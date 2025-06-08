````markdown
# 🔍 WebGPU Cache Latency Approximation

This project performs an **experimental analysis of GPU cache behavior** using WebGPU in the browser.  
By observing how compute execution time varies with buffer size, we gain insights into **GPU cache hits, misses, and parallelism**—without directly accessing the cache.

---

## 🧠 Objective

To approximate GPU cache latency behavior by:

- Running the same compute task over buffers of increasing sizes
- Measuring how execution time changes
- Inferring cache efficiency and memory access cost indirectly

---

## ⚙️ How It Works

### 1. Buffer Setup

We define four buffer sizes:

```js
const bufferSizes = [1024, 8192, 65536, 524288]; // in u32 elements
````

Each buffer is initialized with zeroes and used as storage for compute threads.

---

### 2. Compute Shader (WGSL)

```wgsl
@compute @workgroup_size(64)
fn main(@builtin(global_invocation_id) gid : vec3<u32>) {
  let idx = gid.x;
  if (idx >= arrayLength(&data)) return;

  var val = data[idx];
  for (var i = 0u; i < 10000u; i = i + 1u) {
    val = val + 1u - 1u;
  }
  data[idx] = val;
}
```

**What it does:**

* Each thread reads one element from the buffer.
* Performs dummy computation (looping 10,000 times).
* Writes the result back.

This simulates realistic workload on GPU cores, while ensuring memory access plays a role in latency.

---

### 3. Dispatching Work

For each buffer:

* Threads are launched with one thread per data element.
* Threads are grouped into `@workgroup_size(64)` for efficient dispatch.
* GPU is instructed to process the buffer in parallel.

---

### 4. Measuring Time

* A timer starts just before submitting commands to the GPU.
* After execution, results are copied to a readable buffer.
* Timer stops once the GPU signals completion.

```js
const startTime = performance.now();
device.queue.submit([gpuCommands]);
await readbackBuffer.mapAsync(GPUMapMode.READ);
const endTime = performance.now();
```

The average time is calculated over 10 iterations per buffer size.

---

## 📈 Sample Output

```
Buffer size: 1024 elements - Average execution time over 10 runs: 6.280 ms
Buffer size: 8192 elements - Average execution time over 10 runs: 3.770 ms
Buffer size: 65536 elements - Average execution time over 10 runs: 4.240 ms
Buffer size: 524288 elements - Average execution time over 10 runs: 8.130 ms
```

---

## 🔍 Interpretation

### ✅ What We See:

| Buffer Size    | Observation                                                        |
| -------------- | ------------------------------------------------------------------ |
| **1024**       | Slow — not enough threads to fully utilize GPU, overhead dominates |
| **8192–65536** | Fast — buffer fits well in cache, threads run in parallel          |
| **524288**     | Slower — buffer exceeds cache, memory latency increases            |

### ⚡ What's Happening:

* When the buffer fits in GPU cache → **cache hits** → fast compute
* When the buffer exceeds cache → **cache misses** → memory fetches → slower compute
* Very small buffers waste compute power due to low parallelism

---

## 🧪 Why This Is Useful

Even without reading hardware counters, we can **observe GPU behavior through timing patterns**. This experiment shows:

* How GPU caches impact compute workloads
* That performance ≠ just buffer size—it depends on memory hierarchy and parallelism
* A simple way to probe GPU characteristics using WebGPU and WGSL

---

## 🖥️ Run It Yourself

1. Open the `index.html` file in a WebGPU-supported browser:

   * Latest **Chrome**, **Edge**, or **Canary**
   * Enable WebGPU via `chrome://flags/#enable-unsafe-webgpu` if needed

2. View results in the browser window.

---

Enjoy exploring the GPU from your browser! 🚀



