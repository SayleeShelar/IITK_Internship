# GPU Performance Experiments with WebGL and WebGPU

This repository contains two web-based GPU performance experiments designed to analyze GPU utilization and compute performance in modern browsers using **WebGL** and **WebGPU**.

---

## Task 1: GPU Utilization Check with WebGL

### Overview
This experiment verifies whether running a simple WebGL rendering task activates the dedicated GPU (e.g., NVIDIA GPU) on systems with multiple GPUs.

### What It Does
- Renders a WebGL canvas that continuously clears the screen with random colors.
- Runs an infinite animation loop to create sustained GPU load.

### How It Was Tested
- Chrome was configured to use the **NVIDIA high-performance GPU**.
- Upon running the test, GPU utilization spiked to approximately **28%**, confirming that the WebGL workload engages the dedicated GPU.

### Purpose
- To confirm that browsers can leverage the dedicated GPU for WebGL rendering.
- To demonstrate how even simple graphical workloads can drive GPU activity.

---

## Task 2: WebGPU Cache Latency Approximation

### Overview
This experiment benchmarks GPU compute shader performance across different buffer sizes using the WebGPU API.

### What It Does
- Executes a compute shader that performs a heavy workload loop on buffers of varying sizes (`1024`, `8192`, `65536`, and `524288` elements).
- Measures average execution time over multiple iterations.
- Reports approximate latency and performance trends as buffer size increases.

### Key Features
- Uses WGSL (WebGPU Shading Language) for the compute shader.
- Provides detailed execution time feedback on the webpage.
- Helps understand GPU memory/cache behavior under load.

### Requirements
- Modern browsers with WebGPU support (e.g., Chrome Canary, Edge Dev).
- WebGPU experimental features may need enabling via browser flags.

---

## How to Run

1. Clone or download the repository.
2. Open the respective HTML files in a compatible browser:
   - `gpu_test.html` for Task 1 (WebGL GPU Utilization).
   - `webgpu_cache_latency.html` for Task 2 (WebGPU Performance Test).
3. Monitor GPU activity using system tools (Task Manager, NVIDIA Control Panel, etc.).
4. For Task 2, observe the printed timing results on the webpage.

---

## Notes

- Task 1 demonstrates GPU utilization using WebGL and simple rendering.
- Task 2 provides insights into GPU compute workload performance via WebGPU.
- Both tasks aim to deepen understanding of GPU behavior in browser environments.

---

