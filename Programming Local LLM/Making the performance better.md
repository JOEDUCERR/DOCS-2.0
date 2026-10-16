Your 11 t/s is well below what this hardware should manage, so there's room. A 1080 Ti has about 484 GB/s of memory bandwidth, and a 27B Q4_K_M is roughly 16-17 GB. With `--split-mode layer` the GPUs work one after another on each token, so the ceiling for a single stream is about 25-30 t/s and a realistic target is 15-18. Going beyond that needs a different approach (speculative decoding, or an MoE model).

**Concurrency is already working for you.** In your log, tasks 0 and 2 generated at the same time at about 10 t/s each, so the aggregate was about 20 t/s. Decoding is bandwidth-bound, so extra streams are nearly free. With 4 slots you could see 35-40 t/s total. If several people share this server, aggregate throughput is already better than the single-stream number suggests.

I'd try these in this order:

1. **Set the CPU governor to performance.** Your `lscpu` shows the cores at 31% of max clock. Decode on a multi-GPU setup is partly kernel-launch bound, and an i5-7400 is a weak single-thread CPU.
    
    ```
    sudo cpupower frequency-set -g performance
    ```
    
    Also run `sudo nvidia-smi -pm 1` for persistence mode.
    
2. **Reduce `--threads`.** With every layer on the GPU, the CPU threads mostly just feed kernels. You have 4 cores shared between two server processes, so `--threads 4` on the big model and the default on the 8B means they compete. Try `--threads 2` on the 27B and `--threads 1` or `2` on the 8B.
    
3. **Benchmark the KV cache and flash-attn choices.** On Pascal, llama.cpp uses its vector attention kernels (no tensor cores), and quantized KV adds dequantization work. I can't tell from the log how much it costs you, so measure it. Stop the server first, since it holds the VRAM, then run:
    
    ```
    CUDA_VISIBLE_DEVICES=0,1,2 llama-bench -m <model.gguf> -ngl 99 -sm layer -p 512 -n 128 -fa 1 -ctk q4_0 -ctv q4_0
    CUDA_VISIBLE_DEVICES=0,1,2 llama-bench -m <model.gguf> -ngl 99 -sm layer -p 512 -n 128 -fa 1 -ctk q8_0 -ctv q8_0
    CUDA_VISIBLE_DEVICES=0,1,2 llama-bench -m <model.gguf> -ngl 99 -sm layer -p 512 -n 128 -fa 0 -ctk f16 -ctv f16
    ```
    
    If q8_0 matches q4_0, switch to it. It has noticeably better quality for long contexts and tool calling. Quantizing the KV cache saves VRAM but isn't free for speed on this generation of card.
    
4. **Try 2 GPUs instead of 3.** Each extra GPU in a layer split adds a hop and a synchronization per token. The model is about 16.5 GB, so two 11 GB cards can hold it with a smaller context, for example `CUDA_VISIBLE_DEVICES=0,1 --ctx-size 32768`. Your three cards currently use about 22.7 GB combined, so check that it fits. If it does and it's faster, GPU 2 is free for something else.
    
5. **Experiment with split modes.** Run `llama-server --help | grep -A5 split-mode`. Your build is recent, so it may offer `tensor` alongside `layer` and `row`. Tensor parallelism makes the GPUs work simultaneously instead of in turn, but without NVLink the PCIe traffic may cancel the gain. `row` has historically helped on P40/Pascal cards but doesn't combine well with a quantized KV cache. Both are worth one benchmark run each, and I wouldn't expect miracles.
    
6. **Speculative decoding.** This is the only route to a big single-stream jump. Verifying several draft tokens costs about the same as decoding one when you're bandwidth-bound. It needs either a draft model with the same tokenizer, which is probably not your Qwen3-8B, or an n-gram mode that needs no draft model and works well for code edits where text repeats. Check `llama-server --help | grep -i spec` for what your build supports. I'd start with n-gram.
    

Two smaller things:

- Most perceived latency is thinking tokens. `--reasoning-budget 1024` already caps this, but for simple requests turning thinking off entirely will beat any hardware tweak.
- Add `-ngl 99` explicitly to the 8B command rather than relying on the default.

Make one change at a time and compare the steady-state `tg` figure, since a changed variable makes the numbers hard to read otherwise. If you paste the llama-bench results, I can help you interpret them.