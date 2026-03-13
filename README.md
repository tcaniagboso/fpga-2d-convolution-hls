# FPGA Streaming 2D Convolution Accelerator (Vitis HLS)

This project implements multiple hardware architectures for accelerating **2D image convolution** using **Xilinx Vitis HLS**.  
The goal is to explore how architectural optimizations impact **latency and hardware resource utilization**.

All implementations are verified against a **golden reference convolution model** using a custom C++ testbench.

---

## Configuration

- Platform: **Xilinx Vitis HLS**
- Kernel size: **3×3 convolution**
- Image size: **512×512**

---

## Implementations

The following hardware architectures were implemented:

- **Baseline** – naive nested-loop convolution
- **Pipeline** – loop pipelining applied to improve throughput
- **Line Buffer** – row buffering to reduce redundant memory accesses
- **Sliding Window + Dataflow** – streaming convolution architecture

---

## Performance Results

| Design | Latency (cycles) | BRAM | DSP | LUT |
|------|------|------|------|------|
| Baseline | 2,359,317 | 4 | 9 | 4936 |
| Pipeline | 2,359,316 | 4 | 10 | 4728 |
| Line Buffer | 786,452 | 6 | 20 | 5038 |
| Dataflow | 262,163 | 6 | 30 | 3840 |

The optimized **dataflow streaming architecture** achieves approximately **9× lower latency** compared to the baseline implementation.**

---

## Optimization Techniques

The following hardware optimization techniques were used across the designs:

- **Loop Pipelining** – overlaps iterations of computation to improve throughput
- **Array Partitioning** – enables parallel access to kernel elements
- **Line Buffering** – caches previous rows to reduce redundant external memory accesses
- **Sliding Window** – reuses previously loaded pixels for neighboring convolution windows
- **Dataflow Architecture** – streams data between stages to increase pipeline parallelism
  
---

## Key Insight

Reducing **external memory accesses** through line buffering and sliding window techniques provides significantly larger performance gains than compute-only optimizations such as loop pipelining.

This reflects a common principle in hardware accelerator design: **memory bandwidth is often the primary bottleneck**.

---

## Repository Structure

```markdown
source/
  conv_baseline.cpp
  conv_pipeline.cpp
  conv_linebuffer.cpp
  conv_dataflow.cpp
  conv_kernels.h

testbench/
  conv_kernels_tb.cpp
```

---

## Verification

The testbench:

1. Generates random input images
2. Computes a **golden reference convolution**
3. Runs each hardware implementation
4. Compares results to ensure correctness

---

## How To Run
### 1. Open Vitis HLS

Create a new Vitis HLS project.

### 2. Add Source Files

Add the following files to the **Sources** virtual folder:

```markdown
source/
  conv_baseline.cpp
  conv_pipeline.cpp
  conv_linebuffer.cpp
  conv_dataflow.cpp
  conv_kernels.h
```

### 3. Add Testbench

Add the testbench file to the **Test Bench** folder:

```markdown
testbench/
  conv_kernels_tb.cpp
```

### 4. Set Top Function

Select the desired top-level function depending on the design you want to synthesize:

```markdown
conv_baseline
conv_pipeline
conv_linebuffer
conv_dataflow
```

### 5. Run Simulation

To verify correctness:

```markdown
Run → C Simulation
```

The testbench compares each implementation against a golden reference.

### 6. Run Synthesis

To generate performance reports:

```markdown
Run → C Synthesis
```

The synthesis report will include:

- Latency (cycles)
- Resource utilization (BRAM, DSP, LUT, FF)
