# In-House Local LLM Infrastructure — Research & Proposal

## 1. Objective

The goal is to build an **in-house, locally hosted LLM platform** that can be accessed by multiple developers over the company's internal network.

The system should:

* Run entirely on company-owned hardware.
* Support multiple developers concurrently.
* Provide strong **coding and reasoning capabilities**.
* Support **agentic workflows**, including tool/function calling.
* Prefer **Mixture-of-Experts (MoE)** models where practical.
* Provide a large context window for working with codebases and documentation.
* Avoid consuming the compute resources of individual developer machines.
* Be expandable as additional GPU resources become available.

The current target is approximately:

* **RAM:** up to 128 GB
* **GPU:** currently evaluating available/expandable VRAM
* **Inference server:** **vLLM**
* **Network:** Internal/LAN access

---

# 2. Why vLLM?

For a single developer running a model locally, tools such as **Ollama** or **llama.cpp** are convenient.

For a company-wide shared inference server, however, **vLLM is more appropriate** because it is designed around serving LLMs through an API and handling multiple simultaneous requests efficiently.

The proposed architecture is:

**Developers → Internal Network → Reverse Proxy/API Gateway → vLLM → GPU(s)**

This means developers don't need to install or run the large model themselves. They simply connect their IDE, agent framework, or internal application to the LLM API.

---

# 3. Important Hardware Consideration

The main limitation is not just the model's parameter count.

GPU memory must accommodate:

**Model weights + KV cache + runtime overhead + concurrent users**

The **KV cache** stores attention information from previous tokens so the model doesn't need to recompute them during generation.

Therefore, increasing any of the following increases memory requirements:

* Model size
* Context length
* Number of simultaneous users
* Number of concurrent requests

This is particularly important for a shared company server.

A model that works perfectly for one developer may become impractical when 10–20 developers are using it simultaneously.

---

# 4. Candidate Models

The following models are being investigated for different hardware tiers.

| Model                      |  Size | Architecture |    Context | Approx. Hardware Requirement   | Intended Use                         |
| -------------------------- | ----: | ------------ | ---------: | ------------------------------ | ------------------------------------ |
| **Qwen3 8B**               |    8B | Dense        |       ~40K | ~6–8 GB GPU                    | Lightweight coding/reasoning         |
| **Qwen3-Coder-Next 80B**   |   80B | MoE          |      Large | ~45+ GB combined memory        | Agentic coding                       |
| **Qwen3.8 27B**            |   27B | Dense        |      Large | ~24 GB for Q4-class use        | General coding/reasoning             |
| **Qwen3.6 35B**            |   35B | MoE          |       250K | ~40 GB VRAM for higher-end use | High-quality coding + long context   |
| **DeepSeek-V4-Flash-0731** | ~284B | MoE          |         1M | ~110+ GB combined memory       | Advanced agentic/coding workloads    |
| **GLM-5.2**                | ~753B | MoE          | Very large | ~411+ GB VRAM                  | Enterprise-scale workloads           |
| **Kimi K3**                | ~2.8T | MoE          |         1M | ~640 GB+ memory minimum        | Extremely large-scale agentic coding |

> Model specifications and memory requirements should be treated as planning estimates until each model/version is benchmarked on the actual hardware.

---

# 5. Recommended Model Tiers

Rather than selecting one model immediately, it makes more sense to divide the infrastructure into tiers.

### Tier 1 — Budget / Development

**Qwen3 8B**

Suitable for:

* Basic coding assistance
* Technical questions
* Lightweight reasoning
* Simple internal agents

Hardware requirement is relatively low.

This is useful as a fallback model but is probably **not the primary company-wide model** if stronger hardware is available.

---

### Tier 2 — Practical Mainstream

**Qwen3-Coder-Next 80B / Qwen3.8 27B / Qwen3.6 35B**

These are more interesting for the company's actual development workload.

They provide a better balance between:

* Coding quality
* Reasoning
* Agentic capabilities
* Context length
* Hardware requirements
* Concurrent users

For a company with approximately **128 GB RAM and a suitable multi-GPU setup**, this is currently the most realistic target range.

---

### Tier 3 — High-End

**DeepSeek-V4-Flash-0731**

This is substantially larger and targets advanced coding and agentic workloads.

Its large context window is particularly interesting for:

* Large repositories
* Long-running agents
* Large documentation sets
* Complex software-engineering workflows

However, the hardware requirement is significantly higher.

A system with around **128 GB total memory** is close to the lower practical boundary for this class, leaving less room for KV cache and concurrent users.

Therefore, it should be treated as an **experimental/high-end deployment**, not the first production target.

---

### Tier 4 — Enterprise GPU Infrastructure

**GLM-5.2 / Kimi K3**

These models are in a completely different hardware category.

They require hundreds of GB to over 1 TB of memory/VRAM.

They are therefore **not realistic for the current infrastructure** and would require dedicated enterprise GPU servers.

They are included in the research mainly to understand the upper end of the model landscape.

---

# 6. MoE Models

Several of the interesting models use **Mixture-of-Experts (MoE)** architecture.

An MoE model may contain hundreds of billions or even trillions of total parameters, but only a small subset of those parameters is activated for each token.

For example:

**80B total parameters → ~3B active parameters/token**

This allows a model to have very large overall capacity without requiring the computational cost of activating the entire model for every token.

However, there is an important distinction:

> **MoE reduces compute requirements, but it does not make the model weights disappear.**

The full model still has to be stored in memory.

Therefore, an 80B MoE model can be computationally efficient while still requiring substantial memory.

---

# 7. Multi-GPU Considerations

If the available GPU VRAM is insufficient for a model, multiple GPUs can be combined.

Two major approaches are:

### Tensor Parallelism

The model is divided across GPUs and the GPUs work together on the same computation.

Advantages:

* Good for large models
* Can provide high throughput

Disadvantage:

* Requires frequent communication between GPUs.
* Communication over PCIe can become a significant bottleneck.

This is particularly important when using consumer GPUs without high-speed GPU interconnects.

---

### Pipeline Parallelism

Different portions of the model are placed on different GPUs.

Advantages:

* Less communication pressure compared with some tensor-parallel workloads.

Disadvantages:

* Execution becomes more sequential.
* GPUs may spend time waiting for other pipeline stages.
* Utilization can decrease.

Therefore, simply adding GPUs does **not** automatically provide linear performance scaling.

---

# 8. Memory Is the Main Constraint

For this project, three variables must be considered together:

### 1. Model size

Larger models generally provide stronger capabilities but require more memory.

### 2. Context length

A 250K or 1M-token context is attractive for agentic coding, but supporting extremely long contexts requires substantial KV-cache memory.

### 3. Concurrent users

If one model is serving 15 developers simultaneously, every active request may require its own KV cache.

Therefore:

**More users + longer context = significantly more VRAM required**

This is why simply saying:

> "The model fits in 64 GB VRAM"

doesn't necessarily mean:

> "64 GB VRAM can comfortably serve 15 developers."

---

# 9. Context Length Should Be Deliberately Limited

Even if a model supports 250K or 1M tokens, the server should not necessarily expose the entire context window.

For example, an initial deployment could deliberately configure something around:

**32K tokens**

and increase it after benchmarking.

This provides more predictable memory consumption and prevents one request from consuming a disproportionate amount of GPU memory.

The appropriate value should ultimately be determined through real workload testing.

---

# 10. Proposed vLLM Configuration

The vLLM deployment will need to address several important areas.

### Multi-GPU

`--tensor-parallel-size`

Set according to the GPU configuration being used.

### Context limit

`--max-model-len`

Set deliberately rather than automatically using the model's maximum context.

Example initial target:

**32K tokens**

### GPU memory allocation

`--gpu-memory-utilization 0.90`

This allows vLLM to use most of the available GPU memory while leaving some headroom.

The value can be tuned after testing.

### Agentic/tool calling

For agentic models, vLLM needs the appropriate:

* Tool-call parser
* Automatic tool-choice support

These must be tested with the selected model because incorrect configuration can result in tool calls not being interpreted correctly.

---

# 11. Multi-User Access

The LLM server should expose an API rather than allowing developers direct access to the GPU server.

Proposed architecture:

```text
Developer Machines
        │
        │ Internal Network
        ▼
   Nginx / Caddy
        │
        │ Authentication / Logging
        ▼
      vLLM
        │
        ▼
     GPU(s)
```

This provides a central point for:

* Authentication
* API keys
* Request logging
* Rate limiting
* Monitoring
* TLS if required
* Access control

This is important because without controls, a single user could potentially consume the majority of the available GPU resources.

---

# 12. Initial Production Strategy

Rather than immediately purchasing enough hardware for the largest available model, the recommended approach is:

### Phase 1 — Benchmark

Deploy several candidate models on the available hardware.

Measure:

* Tokens/second
* Time to first token
* VRAM usage
* RAM usage
* Context-length impact
* Concurrent-user performance
* Agent/tool-calling reliability
* Coding benchmark performance

### Phase 2 — Simulate Multiple Developers

Test approximately:

**1 → 5 → 10 → 15+ concurrent users**

using realistic coding/agent workloads.

This will determine the actual hardware requirement.

### Phase 3 — Select Production Model

Choose the model that provides the best balance between:

**Quality + latency + concurrency + memory usage**

rather than simply choosing the largest model that fits.

### Phase 4 — Productionize

Deploy:

* vLLM
* Reverse proxy
* Authentication/API keys
* Monitoring
* Logging
* Rate limiting
* Model management
* Health checks

---

# 13. Current Recommendation

Given the planned **128 GB RAM** ceiling, the practical target should initially be the **~27B–80B class**, particularly MoE models where the active parameter count is low.

The larger 284B+ models are valuable research targets, but they move the infrastructure into a much more expensive GPU-server category.

The key question is therefore not:

> "What is the largest model we can fit?"

It is:

> **"What model provides the best coding/agentic quality while still serving the required number of developers concurrently?"**

The next step should be a controlled benchmark of the strongest candidate models against the actual development workload.

---

# 14. Current Research Shortlist

### Primary candidates

1. **Qwen3-Coder-Next 80B**

   * Strong candidate for agentic coding
   * MoE
   * Relatively low active parameter count
   * Potentially suitable for a shared coding server

2. **Qwen3.8 27B**

   * More approachable hardware requirements
   * Good balance between capability and deployment cost
   * Potential mainstream production candidate

3. **Qwen3.6 35B**

   * MoE
   * Very large context capability
   * Interesting option for long-context coding/agentic workloads

### High-end experimental candidate

4. **DeepSeek-V4-Flash-0731**

   * Very large MoE model
   * ~1M context
   * Significantly higher hardware requirements
   * Worth benchmarking if the available 128 GB memory configuration can support it

### Reference-only / future enterprise tier

5. **GLM-5.2**
6. **Kimi K3**

These require substantially more GPU infrastructure than the current target.

---

## Bottom Line

The proposed direction is:

**Local GPU server → vLLM → authenticated internal API → multiple developers/agents**

The immediate priority should be **benchmarking Qwen3-Coder-Next 80B, Qwen3.8 27B, and Qwen3.6 35B under concurrent workloads**.

Hardware should then be selected based on the measured requirements for the desired number of simultaneous developers, rather than selecting hardware solely from the model's minimum VRAM requirement.

