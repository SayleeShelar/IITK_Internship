# 🔍 WebGPU Cache Latency Approximation

This project performs an **experimental analysis of GPU cache behavior** using WebGPU in the browser.
By observing how compute execution time varies with buffer size, we gain insights into **GPU cache hits, misses, and parallelism** — without directly accessing the cache.

---

## 🧠 Objective

To approximate GPU cache latency behavior by:

* Running the same compute task over buffers of increasing sizes
* Measuring how execution time changes
* Inferring cache efficiency and memory access cost indirectly

---

## ⚙️ How It Works

### 1. Buffer Setup

We test four buffer sizes (number of 32-bit unsigned integers):

```js
const bufferSizes = [1024, 8192, 65536, 524288];
```

Each buffer is initialized with zeros and sent to the GPU for processing.

---

### 2. Compute Shader and the Heavy Loop

The compute shader runs a function on each buffer element **in parallel**:

```wgsl
@compute @workgroup_size(64)
fn main(@builtin(global_invocation_id) gid : vec3<u32>) {
  let idx = gid.x;
  if (idx >= arrayLength(&data)) return;

  var val = data[idx];

  // Heavy loop: repeated 10,000 times
  for (var i = 0u; i < 10000u; i = i + 1u) {
    val = val + 1u - 1u;
  }

  data[idx] = val;
}
```

**Why run the loop 10,000 times?**

* The loop creates a **meaningful workload** so timing measurements are **significant and reliable**.
* Without this loop, the GPU might finish too quickly, making timing noisy or dominated by setup overhead.
* The loop **does not change the data** (adding and subtracting 1 cancels out) — it just forces repeated computation.
* This repeated operation **stresses the cache and memory system** by causing many memory accesses per thread.
* It helps **amplify differences** caused by whether data fits in GPU cache or spills into slower memory.

---

### 3. Parallel Execution and Thread Groups

* Each GPU thread is assigned a **unique global ID** (`gid.x`) corresponding to a buffer element.
* Threads run in groups of 64 (`@workgroup_size(64)`), processing elements in parallel batches.
* This parallelism helps observe how the GPU manages multiple simultaneous memory accesses with different buffer sizes.

---

### 4. Measuring Time to Approximate Latency

* Time is recorded just before submitting GPU commands.
* The program waits until GPU processing completes and results are mapped back.
* The elapsed time includes both compute and memory access delays.
* Running multiple iterations and averaging ensures stable and meaningful measurements.

---

### 5. Interpreting Results

Here are the average execution times observed over 10 runs for each buffer size in this experiment:

| Buffer Size (elements) | Average Execution Time (ms) |
| ---------------------- | --------------------------- |
| 1024                   | 6.280                       |
| 8192                   | 3.770                       |
| 65536                  | 4.240                       |
| 524288                 | 8.130                       |

**What do these numbers tell us?**

* For **1024 elements (smallest buffer)**, time is relatively high. This is due to fixed overheads in GPU command setup and less efficient parallel utilization for very small workloads.
* For **8192 elements**, execution time decreases significantly. This size likely fits well in GPU cache and allows better parallel utilization, resulting in faster processing.
* At **65536 elements**, time increases slightly but remains efficient, indicating partial cache usage and beginning of data spilling beyond cache.
* At **524288 elements (largest buffer)**, time increases notably, as data exceeds cache size and must be fetched from slower memory, causing cache misses and higher latency.

This pattern reflects how GPU cache hierarchy and memory system affect performance. By measuring execution time across buffer sizes, we approximate cache latency behavior indirectly.

---

## Conclusion

This experiment helps us understand GPU cache effects through observable execution times. While we don’t directly measure cache hits or misses, the timing data combined with buffer size reveals how cache and memory bandwidth impact GPU compute workloads.

---


