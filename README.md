# 🔍 Engineering Audit & Codebase Study: Distributed Llama

> **Study Objective:** Reverse-engineering bare-metal C/C++ LLM inference, SIMD optimizations (AVX2/ARM), and distributed tensor parallelism over standard TCP/IP sockets without heavy Python dependencies.

## 🏗️ Architecture Breakdown (Static Analysis)

Through a line-by-line audit of `src/`, I mapped out the core execution pipeline:

1. **Memory Mapping & Quantization (`src/quants.cpp`):** 
   Weights are loaded in quantized formats (`Q4_0`, `Q8_0`) to minimize memory footprint and reduce network payload sizes between the root and worker nodes.
2. **Distributed Tensor Slicing (`src/network.cpp`):** 
   Unlike single-node engines, the root node partitions matrix rows across connected worker nodes via raw TCP sockets, synchronizing execution per layer.
3. **The Inference Loop (`src/app.cpp`):** 
   Uses a zero-allocation runtime buffer (`RunState`) pre-allocated at startup to prevent heap fragmentation during token generation.

## 💡 Key Takeaways for Production Systems
* **Zero-Overhead Abstractions:** Avoiding C++ STL containers (`std::vector`) in the hot loop in favor of raw aligned pointers (`float*`) guarantees predictable cache-line behavior.
* **Network Bottleneck Mitigation:** Synchronizing nodes at every transformer layer requires ultra-compact data packets; quantizing activations before socket transmission is critical.

---
*(Original Project Documentation Below)*

> **Study Objective:** Reverse-engineering bare-metal C/C++ LLM inference, SIMD optimizations (AVX2/ARM), and distributed tensor parallelism over standard TCP/IP sockets without heavy Python dependencies.

## 🏗️ Architecture Breakdown (Static Analysis)

Through a line-by-line audit of `src/`, I mapped out the core execution pipeline:

1. **Memory Mapping & Quantization (`src/quants.cpp`):** 
   Weights are loaded in quantized formats (`Q4_0`, `Q8_0`) to minimize memory footprint and reduce network payload sizes between the root and worker nodes.
2. **Distributed Tensor Slicing (`src/network.cpp`):** 
   Unlike single-node engines, the root node partitions matrix rows across connected worker nodes via raw TCP sockets, synchronizing execution per layer.
3. **The Inference Loop (`src/app.cpp`):** 
   Uses a zero-allocation runtime buffer (`RunState`) pre-allocated at startup to prevent heap fragmentation during token generation.

## 💡 Key Takeaways for Production Systems
* **Zero-Overhead Abstractions:** Avoiding C++ STL containers (`std::vector`) in the hot loop in favor of raw aligned pointers (`float*`) guarantees predictable cache-line behavior.
* **Network Bottleneck Mitigation:** Synchronizing nodes at every transformer layer requires ultra-compact data packets; quantizing activations before socket transmission is critical.

---
*(Original Project Documentation Below)*