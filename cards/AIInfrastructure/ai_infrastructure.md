# GPU Architecture

Q: What is a Streaming Multiprocessor (SM) and what does it contain?
A: The fundamental compute unit of an NVIDIA GPU. Each SM contains: multiple CUDA cores (FP32/INT32 ALUs), tensor cores (for matrix multiply), warp schedulers, register file (~65k 32-bit registers), L1 cache/shared memory (configurable split), and load/store units. A GPU has tens to hundreds of SMs. All threads in an SM share the register file and L1/shared memory.

---

Q: What is a warp and why does it matter for performance?
A: A warp is a group of 32 threads that execute the same instruction simultaneously (SIMT — Single Instruction Multiple Threads). The warp is the unit of scheduling. If threads in a warp diverge on a branch (different conditions), both paths are serialized — warp divergence kills throughput. Minimizing divergence is critical for GPU kernel efficiency.

---

Q: What is the CUDA thread hierarchy?
A: Thread → Warp (32 threads, hardware unit) → Block (user-defined, up to 1024 threads, runs on one SM, shares shared memory and barrier sync) → Grid (all blocks for one kernel launch). Threads within a block can synchronize with `__syncthreads()`. Blocks are independent — no ordering guarantees between blocks.

---

Q: What is the GPU memory hierarchy from fastest to slowest?
A: Registers (per-thread, ~1 cycle, ~256KB/SM) → Shared memory / L1 (per-block, ~1-5 cycles, 32-100KB/SM, manually managed) → L2 cache (chip-wide, ~50 cycles, few MB) → Global memory / HBM (off-chip, ~200-800 cycles, GB-scale, high bandwidth). Optimizing means keeping hot data in registers or shared memory and avoiding global memory round trips.

---

Q: What is the difference between shared memory and L1 cache on a GPU?
A: Physically the same SRAM on modern NVIDIA GPUs (Ampere+: 128KB/SM total), but logically distinct. Shared memory is explicitly managed by the programmer — you control what goes in and out. L1 is hardware-managed (automatic caching of global memory accesses). You can configure the split (e.g. 64KB shared / 64KB L1) based on your kernel's needs.

---

Q: What is HBM and why do AI accelerators use it instead of GDDR?
A: High Bandwidth Memory — stacked DRAM dies connected via silicon interposer directly to the GPU die. HBM3e (H100): ~3.35 TB/s bandwidth vs ~1 TB/s for GDDR6X. The wide bus (1024+ bits) enables the high bandwidth needed to feed thousands of CUDA cores. Trade-off: lower total capacity than GDDR at similar cost, but bandwidth is the bottleneck for most ML workloads.

---

Q: What are Tensor Cores and what operation do they accelerate?
A: Specialized hardware units introduced in Volta (2017) that perform matrix-multiply-accumulate (MMA) operations: D = A × B + C on small tiles (e.g. 16×16×16) in a single clock cycle. Dramatically accelerate GEMM (General Matrix Multiplication), which underlies all transformer attention and feedforward operations. Support FP16, BF16, TF32, INT8, FP8 precisions.

---

Q: What is NVLink and how does it differ from PCIe for multi-GPU setups?
A: NVLink is NVIDIA's proprietary high-bandwidth GPU-to-GPU interconnect. NVLink 4.0 (H100): 900 GB/s bidirectional bandwidth between GPUs. PCIe 5.0 x16: ~128 GB/s. NVLink enables GPUs to share memory and communicate at near-memory speeds, critical for model parallelism and AllReduce in training. NVSwitch extends NVLink to all-to-all connectivity across up to 256 GPUs in a DGX SuperPOD.

---

Q: What is occupancy on a GPU and why does maximizing it not always improve performance?
A: Occupancy = active warps per SM / maximum warps per SM. Higher occupancy hides memory latency by having more warps ready to execute when others are waiting. But it's not always better: a kernel with high register usage per thread limits concurrent warps (register pressure reduces occupancy). Sometimes a low-occupancy kernel with high instruction-level parallelism (ILP) per warp outperforms a high-occupancy one.

---

Q: What is the difference between an A100 and an H100 for training workloads?
A: H100 (Hopper, 2022) vs A100 (Ampere, 2020). Key improvements: FP8 training support (2× throughput vs FP16), Transformer Engine (dynamic FP8/FP16 casting per layer), NVLink 4.0 (900 GB/s vs 600 GB/s), HBM3 (3.35 TB/s vs 2 TB/s), 4th-gen Tensor Cores. H100 SXM5 delivers ~3× the training throughput of A100 for large transformer models. FP8 is the key lever — halves memory and doubles compute vs BF16.

---

# CUDA Programming Model

Q: What is a CUDA kernel and how is it launched?
A: A function that runs on the GPU, defined with `__global__` qualifier. Launched from host (CPU) with `kernel<<<gridDim, blockDim, sharedMem, stream>>>(args)`. gridDim specifies the number of blocks (3D), blockDim the number of threads per block (3D, max 1024 total). Each thread computes its global index from `blockIdx`, `blockDim`, `threadIdx`. The GPU runs all blocks asynchronously; the CPU continues unless explicitly synchronized.

---

Q: What is memory coalescing and why is it critical for global memory access?
A: When threads in a warp access global memory, the hardware coalesces consecutive accesses into a single memory transaction (cache line = 128 bytes). If thread i accesses address base + i*4 (stride-1), all 32 threads are served in one transaction. If accesses are strided or random, each thread needs a separate transaction — 32× more memory traffic. Layout data so that adjacent threads access adjacent addresses.

---

Q: What are shared memory bank conflicts?
A: Shared memory is divided into 32 banks (one per warp lane). If multiple threads in a warp access different addresses in the same bank simultaneously, accesses are serialized — a bank conflict. Stride-1 access is conflict-free. Stride-2 halves effective bandwidth (2-way conflict). Padding arrays by one element per row (e.g. `[N][32+1]`) eliminates conflicts in matrix transpose kernels.

---

Q: What is the difference between `__syncthreads()` and `__threadfence()`?
A: `__syncthreads()` is a barrier — all threads in a block must reach it before any proceeds. Ensures shared memory writes are visible to all threads in the block. `__threadfence()` is a memory fence — ensures all previous writes by the calling thread are visible to all other threads (global scope), but does NOT block execution. Use `__syncthreads()` for producer-consumer within a block; `__threadfence()` for global visibility without synchronization.

---

Q: What is CUDA stream and why does it enable overlap?
A: A stream is a sequence of GPU operations (kernels, memory copies) that execute in order relative to each other. Operations in different streams can overlap — a kernel in stream 1 can run concurrently with a H2D memory copy in stream 2. Critical for hiding PCIe transfer latency in inference pipelines (copy next batch while current batch is executing). Default stream is stream 0 — synchronizes with all other streams.

---

Q: What is pinned (page-locked) memory and when should you use it?
A: Host memory allocated with `cudaMallocHost()` is pinned — the OS cannot page it out. Allows the GPU DMA engine to transfer it directly without an intermediate copy, achieving near-PCIe peak bandwidth. Unpinned transfers must first copy to a pinned staging buffer (invisible to user, done by the driver) — roughly 2× slower. Use for data that is frequently transferred; over-pinning starves the OS of pageable memory.

---

Q: What is occupancy and how do you calculate it for a kernel?
A: Active warps / max warps per SM. Determined by three resource limits: (1) register usage per thread × threads per block / registers per SM, (2) shared memory per block / shared memory per SM, (3) max blocks per SM × threads per block / max threads per SM. The binding constraint determines occupancy. NVIDIA Occupancy Calculator or `cudaOccupancyMaxActiveBlocksPerMultiprocessor()` computes this at runtime.

---

Q: What is kernel fusion and what problem does it solve?
A: Combining multiple kernels (e.g. layer norm + activation + dropout) into a single kernel that processes data in one pass. Without fusion, each kernel writes its output to global memory and the next reads it back — memory round trips dominate. Fused kernels keep intermediate data in registers or shared memory. This is the primary optimization in frameworks like FlashAttention and torch.compile.

---

Q: What is warp divergence and how does it affect throughput?
A: When threads in a warp take different branches of an if/else, the GPU must execute both paths — threads that don't take a path are masked (disabled) while the other path executes. Effectively serializes the two paths, halving throughput in a 50/50 split. Avoid by restructuring code so all threads in a warp take the same branch, or use predication for small bodies.

---

Q: What is the CUDA programming model's consistency guarantee?
A: Within a warp: instructions execute in lockstep, results visible immediately. Within a block: `__syncthreads()` provides a full barrier. Between blocks: no ordering guarantee during kernel execution — blocks may run in any order, possibly concurrently. Between kernels: default stream enforces order; different streams may overlap. This means inter-block communication during a kernel is unsafe without atomic operations and `__threadfence()`.

---

# Memory & Bandwidth

Q: What is the roofline model?
A: A performance model that plots achievable throughput (FLOP/s) vs arithmetic intensity (FLOP per byte of memory traffic). Two ceilings: compute roof (peak FLOP/s) and memory roof (peak bandwidth × arithmetic intensity). A kernel is memory-bound if its arithmetic intensity is below the ridge point (compute/bandwidth); compute-bound if above. Tells you which resource to optimize and what speedup is achievable.

---

Q: What is arithmetic intensity and how do you compute it for a matrix multiply?
A: FLOP/s divided by bytes of memory traffic. For an M×K × K×N matmul: FLOPs = 2MKN (multiply-add). Bytes = (MK + KN + MN) × dtype_size. For large square matrices (M=N=K=4096, FP16): ~2× FLOPs dominate, bytes ≈ 3×N²×2 = small relative to FLOPs → high arithmetic intensity → compute-bound. This is why GEMM saturates tensor cores and not HBM bandwidth.

---

Q: What is memory bandwidth and how do you calculate if a kernel is bottlenecked by it?
A: Peak HBM bandwidth (e.g. H100: 3.35 TB/s). For a kernel: measure bytes read + written, divide by execution time → achieved bandwidth. If achieved ≈ peak, you're memory-bound — optimization should reduce memory traffic (fusion, quantization, caching). If achieved bandwidth << peak but execution time is high, you're compute-bound or have latency issues (kernel launch overhead, low occupancy).

---

Q: What is the difference between BF16 and FP16 for training?
A: Both are 16-bit. FP16: 1 sign, 5 exponent, 10 mantissa bits — higher precision, smaller range (max ~65504, overflow common). BF16: 1 sign, 8 exponent, 7 mantissa bits — same exponent range as FP32 (max ~3.4×10³⁸), lower precision. BF16 is numerically more stable for training (avoids overflow/underflow) and requires no loss scaling. FP16 needs loss scaling. Modern training defaults to BF16 on Ampere+.

---

Q: What is mixed precision training and what is loss scaling?
A: Keeping weights in FP32 for precision, but computing forward/backward passes in FP16/BF16 for speed and memory. FP16 has limited dynamic range — gradients smaller than ~6×10⁻⁸ underflow to 0. Loss scaling multiplies the loss by a large constant before backward pass, scaling gradients up into representable range, then unscales before the optimizer step. BF16 has FP32-equivalent range so doesn't need loss scaling.

---

Q: What is quantization and what are the trade-offs between INT8, FP8, and GPTQ?
A: Representing weights/activations in lower precision to reduce memory and increase throughput. INT8: 2× memory reduction vs FP16, widely supported, some accuracy loss. FP8 (E4M3/E5M2): native on H100 Tensor Cores, ~2× throughput vs BF16, better dynamic range than INT8. GPTQ: post-training quantization to INT4 — 4× memory reduction, significant quality loss mitigated by calibration. INT4 is inference-only; FP8 can be used in training.

---

Q: What is memory-bound vs compute-bound and why does it determine your optimization strategy?
A: Memory-bound: the kernel spends most time waiting for data from HBM — throughput ≤ bandwidth × arithmetic_intensity. Fix: reduce memory traffic (fusion, quantization, reuse data in shared memory). Compute-bound: ALUs are the bottleneck — throughput ≤ peak FLOP/s. Fix: increase arithmetic intensity, use tensor cores, improve occupancy. Most transformer attention is memory-bound; large GEMMs are compute-bound.

---

Q: What is gradient checkpointing (activation recomputation)?
A: During backpropagation, intermediate activations from the forward pass are needed to compute gradients. Storing all activations is O(layers × sequence × batch) — huge. Checkpointing discards activations after the forward pass and recomputes them on-demand during backward. Trade-off: ~33% extra compute for ~√N memory reduction. Essential for training large models that don't fit in GPU memory with full activations stored.

---

# Distributed Training

Q: What is data parallelism and how does it scale?
A: Each GPU holds a complete copy of the model. The batch is split across GPUs — each processes a shard, computes gradients, then gradients are averaged (AllReduce) across all GPUs before the optimizer step. Scales training throughput linearly with the number of GPUs (assuming AllReduce overhead is small). Requires the full model to fit in a single GPU's memory — impossible for models >100B parameters.

---

Q: What is model parallelism (tensor parallelism) and when is it needed?
A: Split individual layers (weight matrices) across multiple GPUs. For a linear layer Y = XW, split W column-wise across GPUs — each GPU computes a partial result, then AllGather to reconstruct the full output. Megatron-LM uses this for transformer MLP and attention layers. Needed when a single layer's weights don't fit on one GPU. Communication overhead: one AllReduce per layer forward and backward.

---

Q: What is pipeline parallelism and what is the micro-batch technique?
A: Split the model layer-wise across GPUs — GPU 0 runs layers 1-4, GPU 1 runs 5-8, etc. Without pipelining, only one GPU is active at a time (pipeline bubble). Micro-batching: split the batch into M micro-batches, feed them through the pipeline one after another — GPUs overlap, reducing bubble fraction to 1/M. Used in GPT-3 training (PipeDream, Megatron-LM). Trade-off: M micro-batches need M times more in-flight activations.

---

Q: What are the three stages of ZeRO optimizer and what does each partition?
A: ZeRO (Zero Redundancy Optimizer) partitions optimizer state, gradients, and parameters across data-parallel GPUs instead of replicating them. Stage 1: partition optimizer states (Adam moments) — 4× memory reduction. Stage 2: + partition gradients — 8× reduction. Stage 3: + partition parameters — linear reduction with GPU count. Each GPU holds only 1/N of each. Communication: AllGather for forward pass parameters, ReduceScatter for gradients. Implemented in DeepSpeed.

---

Q: What is the difference between ReduceScatter and AllReduce in terms of communication volume?
A: AllReduce = ReduceScatter + AllGather. AllReduce sums data across all ranks and gives every rank the full result — 2(N-1)/N × data volume per rank. ReduceScatter sums and scatters — each rank ends up with 1/N of the reduced result — (N-1)/N × data volume. ZeRO-2 uses ReduceScatter (rank only needs its own gradient shard); ZeRO-3 uses both. For large N, both saturate bandwidth at ~2× data volume.

---

Q: What is gradient accumulation and when do you use it?
A: Run multiple forward/backward passes (micro-batches) before calling the optimizer step, accumulating gradients. Simulates a larger effective batch size without needing the full batch in memory at once. Used when the desired batch size exceeds GPU memory capacity. With data parallelism: effective batch = micro_batch × accumulation_steps × num_GPUs.

---

Q: What is the 3D parallelism strategy used to train GPT-3/PaLM-scale models?
A: Combine data parallelism (DP), tensor parallelism (TP), and pipeline parallelism (PP). TP and PP together form "model parallelism" — TP splits within a node (NVLink bandwidth), PP splits across nodes (InfiniBand). DP replicates model slices across node groups. Example: 1024 GPUs = 8-way TP × 16-way PP × 8-way DP. Each dimension addresses a different memory/compute bottleneck.

---

Q: What is the critical batch size and how does it affect distributed training efficiency?
A: The batch size below which larger batches improve gradient signal quality, and above which more data per step doesn't help convergence — you need more steps, not more data per step. Training with very large batches (required for high GPU utilization at scale) may require learning rate warmup, linear/square-root LR scaling rules, and more epochs to converge. OpenAI scaling laws characterize this trade-off.

---

Q: What is FSDP (Fully Sharded Data Parallel) and how does it compare to ZeRO-3?
A: PyTorch's native implementation of ZeRO-3. Model parameters, gradients, and optimizer states are sharded across data-parallel ranks. Before each layer's forward pass, parameters are gathered (AllGather); after backward, gradients are reduced and sharded again. Tightly integrated with PyTorch's autograd — no manual intervention. ZeRO-3 (DeepSpeed) is similar but has more configuration knobs and better support for CPU offloading.

---

Q: What is CPU offloading in ZeRO-Infinity and when does it help?
A: ZeRO-Infinity (DeepSpeed stage 3+) offloads optimizer states and parameters to CPU RAM (and optionally NVMe) when they don't fit in GPU HBM. CPU offload: PCIe bandwidth becomes the bottleneck (~64 GB/s vs 3.35 TB/s HBM). Effective only when the model is so large that GPU memory is the hard constraint and training speed is a secondary concern. Enables training 100B+ parameter models on a single server.

---

Q: What is activation memory and how does it dominate memory during training?
A: Intermediate activations stored during the forward pass for use in the backward pass. For a transformer: O(batch × sequence × hidden × layers). For GPT-3 (96 layers, 12288 hidden, sequence 2048, batch 512): ~2.5 TB of activations at FP16 — far exceeds GPU memory. Solutions: gradient checkpointing (recompute), micro-batching (smaller batch), mixed precision (halve dtype size), activation offloading to CPU.

---

# Communication & Networking

Q: What is AllReduce and what algorithms implement it?
A: A collective operation where every rank starts with a tensor; after AllReduce, every rank has the element-wise sum (or other reduction). Ring-AllReduce: each rank sends to the next in a ring — optimal bandwidth utilization, O(2(N-1)/N × data), latency O(N). Recursive halving/doubling: O(log N) steps, better for small messages. NCCL selects the algorithm based on message size and topology.

---

Q: What is NCCL and what does it optimize?
A: NVIDIA Collective Communications Library — implements AllReduce, AllGather, ReduceScatter, Broadcast, Reduce for multi-GPU and multi-node training. Optimizes for the physical topology: within a node uses NVLink (high bandwidth, low latency), across nodes uses InfiniBand/RoCE. Automatically selects communication algorithms (ring, tree, etc.) and uses GPUDirect RDMA to transfer data from GPU memory directly over the network without CPU involvement.

---

Q: What is GPUDirect RDMA and why does it matter?
A: Allows a network adapter (InfiniBand HCA) to read/write GPU HBM directly, bypassing the CPU and system memory entirely. Without GPUDirect: GPU → PCIe → CPU RAM → PCIe → NIC → network. With GPUDirect: GPU → PCIe → NIC → network. Eliminates two PCIe traversals and a CPU memcpy, reducing latency by ~10μs and bandwidth by ~50% for inter-node transfers. Critical for reducing AllReduce overhead in multi-node training.

---

Q: What is InfiniBand and how does it differ from Ethernet for ML clusters?
A: A high-performance interconnect designed for HPC/ML clusters. HDR InfiniBand: 200 Gb/s per port, ~1μs latency. Ethernet (400GbE): similar bandwidth but 5-10× higher latency and higher CPU overhead for RDMA. InfiniBand has native RDMA support (no TCP overhead), congestion control designed for collective operations, and better bisection bandwidth in fat-tree topologies. Yandex, Meta, Google use InfiniBand or custom fabrics (e.g. Google's Jupiter, Meta's RoCEv2).

---

Q: What is RoCE (RDMA over Converged Ethernet) and when is it used?
A: RDMA semantics over standard Ethernet hardware. RoCEv2 runs over UDP/IP, enabling GPUDirect RDMA without InfiniBand infrastructure. Lower cost than IB but more sensitive to packet loss (RDMA doesn't handle retransmission well — needs lossless Ethernet with PFC/ECN). Used by Meta (RoCEv2 for training clusters), Alibaba, and hyperscalers that own their Ethernet fabric and can guarantee losslessness.

---

Q: What is topology-aware scheduling in a GPU cluster?
A: Placing job ranks on GPUs such that the most communication-intensive GPU pairs have the highest-bandwidth links between them. TP groups should be on the same node (NVLink); PP groups should span as few network switches as possible; DP groups can span the cluster. Scheduler needs GPU topology awareness (NVLink domains, switch hierarchy). Misplaced jobs can be 2-5× slower due to saturating lower-bandwidth links.

---

Q: What is collective communication overhead and how do you overlap it with computation?
A: AllReduce during backward pass serializes compute and communication. Overlap: bucket gradients by layer — as soon as a layer's backward is complete, start AllReducing its gradients while the next layer's backward continues. PyTorch DDP does this automatically with gradient bucketing (default bucket size 25MB). ZeRO-3 overlaps AllGather of next layer's parameters with current layer's compute (prefetching).

---

Q: What is the bandwidth utilization efficiency of ring-AllReduce at scale?
A: Ring-AllReduce sends 2(N-1)/N × data volume per rank, approaching 2× data as N grows. Bus bandwidth utilization = achieved bandwidth / peak link bandwidth. At N=8 (single DGX node, NVLink): efficiency > 90%. At N=1000+ nodes (InfiniBand): latency accumulates per hop, efficiency drops to 60-80%. At very large scale (10k+ GPUs), tree-based or hierarchical algorithms can outperform ring.

---

Q: What is the difference between inter-node and intra-node communication bandwidth in a typical training cluster?
A: Intra-node (same server, NVLink): 900 GB/s bidirectional (H100 NVLink 4.0). Inter-node (between servers, InfiniBand HDR): 200 Gb/s = 25 GB/s per port, typically 8 ports per server = 200 GB/s. ~4.5× difference. This is why tensor parallelism (most communication) is always placed within a node (NVLink), and pipeline/data parallelism (less frequent, larger messages) spans nodes.

---

Q: What is gradient compression and when does it help distributed training?
A: Reducing the size of gradients before AllReduce to save bandwidth. Techniques: TopK sparsification (send only the K largest gradients, ~99% sparse), quantization to INT8/FP16, PowerSGD (low-rank gradient approximation). Helps when: inter-node bandwidth is the bottleneck (large clusters), gradient tensors are large. Risk: compression error accumulates — requires error feedback mechanisms. Less needed with NVLink (high bandwidth) but valuable at 1000+ node scale.

---

# Inference Optimization

Q: What is the difference between static batching and continuous batching for LLM inference?
A: Static batching: wait until a full batch is assembled, run it to completion. GPU underutilized when requests have different lengths — early-finishing requests must wait for the longest. Continuous batching (Orca): dynamically add new requests to the batch as others complete at the token level. Achieves 10-23× higher throughput for LLM serving by keeping the GPU busy. Used in vLLM, TGI, TensorRT-LLM.

---

Q: What is the KV cache in transformer inference and why does it dominate memory?
A: During autoregressive generation, each new token attends to all previous tokens. The key and value projections of previous tokens are cached — the KV cache — so they aren't recomputed. Memory: 2 × num_layers × num_heads × head_dim × sequence_length × batch_size × dtype_size. For Llama-70B at batch=32, seq=4096, FP16: ~150GB — exceeds GPU memory. Managing KV cache memory is the core challenge of LLM serving systems.

---

Q: What is PagedAttention (vLLM) and what problem does it solve?
A: KV cache is typically allocated as a contiguous block per sequence (max sequence length × layer × head). Most memory is wasted for short sequences or fragmented across requests. PagedAttention stores KV cache in fixed-size blocks (pages) mapped via a block table — like virtual memory for KV cache. Allows sharing KV cache pages across requests with the same prefix (prefix caching), near-zero fragmentation, and 3-4× higher throughput than contiguous allocation.

---

Q: What is speculative decoding and when does it help?
A: Use a small draft model to generate K candidate tokens quickly, then verify all K with the large target model in one forward pass (parallel). If the target accepts the draft tokens, K tokens are generated for the cost of ~1 large model step. Speedup: 2-3× for tasks where a small model is often correct (code, continuation). Hurts if the draft model diverges often (creative tasks). Requires a compatible draft/target pair with the same vocabulary.

---

Q: What is FlashAttention and what problem does it solve?
A: Standard attention computes Q×Kᵀ (n×n matrix) and stores it in HBM — O(n²) memory, O(n²) HBM reads/writes. FlashAttention (Dao et al. 2022) tiles Q, K, V to fit in SRAM, computes attention in blocks without materializing the full n×n matrix in HBM. Result: O(n) HBM memory, 2-4× faster than standard attention on A100, identical numerical result (not approximate). FlashAttention-2/3 further optimize for H100 Tensor Cores.

---

Q: What is quantization-aware training (QAT) vs post-training quantization (PTQ)?
A: PTQ: quantize a pretrained model after training — fast, no retraining, but accuracy degrades especially at INT4/INT3. QAT: simulate quantization during training (fake quantization in forward pass, full precision in backward) — model learns to be robust to quantization, better accuracy at low precision. QAT requires full training infrastructure; PTQ only needs calibration data. GPTQ, AWQ, SmoothQuant are PTQ methods for LLMs.

---

Q: What is the prefill vs decode phase distinction in LLM inference?
A: Prefill: process the entire input prompt in parallel — compute-bound, can use large batch sizes efficiently, single forward pass generates the first output token and populates the KV cache. Decode: autoregressive generation, one token at a time — memory-bound (reads entire KV cache per step), low arithmetic intensity. These phases have opposite bottlenecks — systems like Sarathi-Serve and Disaggregated Prefill run them on separate GPU pools.

---

Q: What is tensor parallelism during inference and what is its latency trade-off?
A: Split weight matrices across multiple GPUs to reduce per-GPU memory and increase throughput via parallel compute. Each GPU computes a partial result; one AllReduce per layer recombines. At batch=1 (latency-sensitive inference), the AllReduce latency (~100μs over NVLink) is a significant fraction of total per-layer time — TP helps throughput but hurts single-request latency. Use TP when model doesn't fit on one GPU or throughput > latency is the goal.

---

Q: What is INT4 GEMM and how do frameworks execute it efficiently?
A: INT4 weights are packed two-per-byte, halving memory vs INT8. Execution: dequantize INT4 → FP16 per block just before the GEMM (GPTQ/AWQ), then run FP16 GEMM. Alternatively, custom INT4 GEMM kernels (bitsandbytes, CUTLASS) operate directly on INT4. For memory-bound decode, INT4 reduces HBM traffic 4× vs FP16 → near 4× faster decode. For compute-bound prefill, benefit is smaller (quantization error + dequantization overhead).

---

Q: What is the effect of batch size on inference throughput and latency?
A: Larger batches improve GPU utilization (more parallelism, tensor cores better utilized) → higher tokens/second throughput. But larger batches increase latency (time-to-first-token and queue wait). Optimal batch size is the point where the arithmetic intensity crosses the roofline ridge point — beyond that, you're compute-bound and latency scales linearly. For serving, systems like vLLM use continuous batching to maximize throughput while meeting latency SLOs.

---

# ML Compilers & Runtimes

Q: What is TensorRT and what optimizations does it apply?
A: NVIDIA's inference optimization library. Takes a trained model (ONNX/TorchScript), applies: layer fusion (Conv+BN+ReLU into one kernel), precision calibration (FP32→FP16/INT8), kernel auto-tuning (selects best CUDA kernel per layer/hardware), memory planning (minimize allocation), and graph optimization (dead node elimination, constant folding). Typically 2-5× faster than unoptimized PyTorch inference on NVIDIA hardware.

---

Q: What is Triton (OpenAI) and how does it differ from CUDA?
A: A Python DSL for writing GPU kernels that abstracts warp-level programming. In CUDA you manage threads and warps explicitly. In Triton you write block-level programs — the compiler handles warp scheduling, memory coalescing, and shared memory management. Enables ML researchers to write custom GPU kernels (custom attention, quantized ops) without deep CUDA expertise. FlashAttention-2 is implemented in Triton.

---

Q: What is XLA (Accelerated Linear Algebra) and how does it relate to JAX/TensorFlow?
A: A compiler for linear algebra that takes a computation graph and produces highly optimized machine code for CPUs, GPUs, and TPUs. JAX and TensorFlow use XLA as their backend. Key optimizations: operation fusion (eliminate intermediate buffers), loop fusion, memory layout optimization, and auto-parallelization. XLA's ability to JIT-compile entire training steps (no Python overhead per step) enables TPU performance.

---

Q: What does `torch.compile()` do and how does it work?
A: Introduced in PyTorch 2.0 — compiles a Python function/model using TorchDynamo (captures the computation graph by intercepting Python bytecode), then passes it to a backend (Inductor by default). Inductor generates optimized Triton kernels for GPU (or C++ for CPU), applying kernel fusion and memory planning. Typical speedup: 30-100% on training/inference with no code changes. Falls back to eager mode for unsupported operations.

---

Q: What is operator fusion in ML compilers and what types exist?
A: Combining multiple operations into one kernel to eliminate intermediate HBM reads/writes. Types: vertical fusion (elementwise ops chained: ReLU + dropout + add), horizontal fusion (same op applied to independent tensors in parallel), and kernel fusion with a compute-heavy op (Conv+BN fusion uses BN stats computed during Conv). Compilers (TensorRT, XLA, Inductor) do this automatically; manual fusion is possible with Triton.

---

Q: What is the difference between eager mode and graph mode execution in PyTorch?
A: Eager mode: operations execute immediately as Python code runs — easy to debug (standard Python debugger works), but high Python overhead (each op invocation has interpreter cost), no cross-op optimization. Graph mode: capture the full computation graph first (via `torch.compile`, `torch.jit.trace`, or `torch.fx`), then optimize and execute as a unit. Graph mode enables fusion, memory planning, and operator reordering — at the cost of a compilation step and reduced debuggability.

---

Q: What is ONNX and what role does it play in the inference stack?
A: Open Neural Network Exchange — a standard format for representing ML models as a computation graph with standardized ops. Enables: exporting models from PyTorch/TensorFlow and importing them into inference runtimes (TensorRT, ONNX Runtime, CoreML, TFLite). Acts as the interchange layer between training frameworks and deployment targets. Limitation: ONNX opset must support the ops used — custom ops require extensions.

---

Q: What is the FlashAttention algorithm at a high level?
A: Tiles the Q, K, V matrices into blocks that fit in SRAM. For each tile of Q, iterates over K and V tiles, computing the attention output incrementally using the online softmax trick (maintains running max and sum for numerical stability). Never writes the full N×N attention matrix to HBM. Forward pass: O(N) HBM reads/writes vs O(N²) for standard attention. Backward pass: recomputes attention from saved Q, K, V rather than storing the N×N matrix.

---

# Cluster Infrastructure

Q: What is Kubernetes' role in GPU cluster management and what are its limitations?
A: Kubernetes schedules GPU workloads via `resources: nvidia.com/gpu`, enabling gang scheduling (all pods of a job start together) via plugins like Volcano or Yunikorn. Limitations: default scheduler doesn't understand GPU topology (NVLink domains), doesn't support fractional GPU allocation (MIG partially helps), and doesn't handle distributed training job lifecycle (preemption, fault recovery, elastic scaling) — these require additional controllers (Kubeflow's MPI Operator, PyTorch Operator).

---

Q: What is MIG (Multi-Instance GPU) and when is it used?
A: NVIDIA Multi-Instance GPU — partitions an A100/H100 into up to 7 isolated GPU instances, each with dedicated SM, L2, and HBM slice. Hard isolation: one MIG instance cannot access another's memory or affect its performance. Used for: inference workloads that don't need a full GPU, multi-tenant serving where isolation is required. Not useful for training (each instance is too small; training needs full GPU).

---

Q: What is job preemption in a GPU cluster and what are the recovery strategies?
A: Evicting a lower-priority job to free resources for a higher-priority one. Recovery: checkpoint/restart — the job saves state to persistent storage (model weights, optimizer state, dataloader position) and resumes from the last checkpoint. Cost: checkpoint write time (large model = minutes) + wasted compute since last checkpoint. Minimize with frequent checkpointing and fast storage (NVMe, distributed checkpoint to object store in parallel).

---

Q: What is distributed checkpointing and why is it needed for large models?
A: Saving a model's state distributed across multiple processes/GPUs in parallel. A 70B parameter model in BF16 = 140GB — writing from one GPU takes minutes. Distributed checkpointing: each rank saves its own shard (ZeRO partitions, pipeline stage, TP shard) in parallel to a shared storage system (Lustre, AWS EFS, GCS). PyTorch's `torch.distributed.checkpoint` and FSDP's `state_dict` support this. Reduces checkpoint time from O(model_size / single_bandwidth) to O(model_size / aggregate_bandwidth).

---

Q: What is elastic training and what problem does it solve?
A: The ability to resize the GPU pool of a running training job (add/remove workers) without restarting from scratch. Needed when: spot/preemptible instances are reclaimed (shrink), new capacity becomes available (grow). Implementations: PyTorch Elastic (`torchrun`), Horovod elastic. Requires re-partitioning data, re-initializing process groups, and redistributing model shards — complex but critical for cost efficiency on spot fleets.

---

Q: What is the difference between SLURM and Kubernetes for ML workloads?
A: SLURM: HPC-native job scheduler, deep GPU topology awareness, efficient gang scheduling, mature MPI integration, batch-only (no long-running services). Kubernetes: designed for microservices, better for serving/inference, supports heterogeneous workloads, but requires significant plugins for HPC-style training. Most large-scale training clusters (Meta, NVIDIA, academic) use SLURM; cloud-native ML platforms (Kubeflow, Vertex AI) use Kubernetes. Hybrid approaches exist.

---

Q: What is the role of a fast distributed file system in a GPU training cluster?
A: Training reads data (tokenized datasets) and writes checkpoints. Bottleneck: with 1000 GPUs each reading tokens at 10 GB/s, aggregate storage bandwidth needed is 10 TB/s. Solutions: streaming from object store (S3/GCS) with data preprocessing on CPU workers, distributed file systems (Lustre, IBM Spectrum Scale), or pre-tokenized shards in shared memory (datasets library memory-mapped files). Checkpoint writes need high sustained write bandwidth — parallel write to object store (multipart upload) or Lustre.

---

Q: What is a training job failure analysis framework and what are the common causes?
A: Large training runs (1000s of GPUs, weeks long) fail regularly. Common causes: hardware failure (GPU uncorrectable ECC error, NVLink failure, NIC failure), NCCL timeout (stragglers causing collective timeout), OOM (memory leak, unexpected batch size), software bugs (NaN in loss, gradient explosion). Best practices: automated job restart from latest checkpoint, hardware health checks before job launch, anomaly detection on loss curves, Weights & Biases / TensorBoard for monitoring, DCGM for GPU health metrics.
