### **System Specs:**
```
CPU: Intel(R) Core(TM) i5-7400 CPU @ 3.00GHz
	Cores: 4
	Threads: 4 (No Hyper Threading)
	Power Draw: 65W
	Socket: LGA 1151
RAM: 46 GB
	16GiB DIMM DDR4 Synchronous 2400 MHz
	8GiB DIMM DDR4 Synchronous 2400 MHz 
	8GiB DIMM DDR4 Synchronous 2400 MHz
	16GiB DIMM DDR4 Synchronous 2400 MHz
GPU: 44 GB VRAM (PCIe x16 - 1, )
	Nvidia GeForce GTX 1080 Ti : 11GB VRAM : 280W
	Nvidia GeForce GTX 1080 Ti : 11GB VRAM : 250W
	Nvidia GeForce GTX 1080 Ti : 11GB VRAM : 250W
	Nvidia GeForce GTX 1080 Ti : 11GB VRAM : 250W
PSU: Corsair RM 1000x
Storage: WD Green 2.5 480GB: sda
```

### Models
```
1. Qwen3.8:27B
	   Model is too slow to start and gives around 11 t/s average.
	   Model also starts hallucinating after multiple requests and needs a restart.
2. Qwen3:8B
	   Model is unable to do deep/long inferencing on larger coding tasks.
	   Fails to reach goal after 4-5 Build mode prompts.
3. Qwen3-Coder-30B-A3B-Instruct
	   Tuned for coding and tool calling, with no thinking phase, so it starts working immediately.
	   It uses standard attention, so prompt caching works normally, which matters most for agent loops.
	   The Q4_K_M is about 18.6 GB. Try this first.
4. Qwen3.6-35B-A3B
		Newer and stronger at agentic work, with switchable thinking.
		It’s the hybrid architecture, so the caching caveat applies, but 3B active parameters make reprocessing cheap.
		The quant is about 23 GB. Skip the “MTP” builds, which need a special llama.cpp branch.
5. GLM-4.7-Flash
	   A 30B MoE built for agentic coding and tool use, about 18 GB at 4-bit.
	   Try it third, since I’m less sure how it behaves on Pascal.
```

### **AI Usage Setup:**
```
1. OpenWebUI
		open-webui-v0.11.4-rollback
		searxng-valkey
		searxng-core
		open-terminal
2. opencode
```