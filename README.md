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
This experiment benchmarks GPU compute shader performance by measuring execution times across different buffer sizes using the WebGPU API.

### What It Does
- Runs a compute shader written in WGSL (WebGPU Shading Language) that performs a heavy workload loop on buffers of varying sizes:  
  `1024`, `8192`, `65536`, and `524288` elements.
- Measures average execution time over multiple iterations to approximate GPU cache and memory latency.
- Reports execution time trends to help understand GPU compute and memory behavior under increasing workload sizes.

### How It Works
- The compute shader processes each element in the buffer with a heavy arithmetic loop (10,000 iterations).
- For each buffer size:
  - Initializes a GPU storage buffer filled with zeros.
  - Dispatches compute shader workgroups to process the buffer.
  - Runs the computation 10 times to calculate an average execution time.
- Uses browser timing APIs to measure GPU command execution durations, synchronizing by mapping GPU buffers back to the CPU.

### Key Insights
- Smaller buffers typically fit within GPU caches, resulting in faster execution.
- Larger buffers may exceed cache capacity, causing increased latency due to slower memory access.
- The test illustrates GPU compute scalability and memory bottlenecks in a web environment.

### Requirements
- A modern browser with WebGPU support (e.g., Chrome Canary, Edge Dev).
- WebGPU features may need to be enabled via browser flags (`chrome://flags/#enable-unsafe-webgpu`).

---

## How to Run

1. Clone or download the repository.
2. Open the respective HTML files in a compatible browser:
   - `gpu_test.html` for Task 1 (WebGL GPU Utilization).
   - `webgpu_cache_latency.html` for Task 2 (WebGPU Performance Test).
3. Monitor GPU activity using system tools such as Task Manager or NVIDIA Control Panel.
4. For Task 2, observe the printed timing results directly on the webpage.

---

## Notes

- Task 1 validates GPU engagement for WebGL workloads in browsers configured to use dedicated GPUs.
- Task 2 explores GPU compute workload performance and memory latency effects using WebGPU.
- Both tasks help deepen understanding of browser-based GPU capabilities and performance characteristics.

---

Feel free to reach out if you have questions or suggestions!
