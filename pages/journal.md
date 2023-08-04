# Journal

This page contains my day-to-day unstructured and unfiltered notes.

## Machine Learning Compilers

### General Theory

**Instruction selection** [[wikipedia](https://en.wikipedia.org/wiki/Instruction_selection)]

- macro expansion (inefficient)
- graph covering
- output from instruction selection is pseudo-registers (temporaries) then register allocation

**Primitive Tensor Functions**

```python
def add(a, b, c):
    for i in range(128):
        c[i] = a[i] + b[i]
```

```cpp
void add(float* a, float* b, float* c) {
    for (int i = 0; i < 128; i++) {
        c[i] = a[i] + b[i];
    }
}
```

- linear, add, relu, softmax
- implemented in low-level languages (C/C++) or assembly
  - can be hardware-specific to utilize parallelism

Primitive tensor function in `tvm.script` (tensor program abstraction):

```python
@T.prim_func
def main(A: T.Buffer[128, "float32"], B: T.Buffer[128, "float32"], C: T.Buffer[128, "float32"]):
  for i in range(128):
    with T.block("C"):
      vi = T.axis.spatial(128, i)
      C[vi] = A[vi] + B[vi]
```

### Deep Learning Compiler Stack

![dl_compiler_stack.png](https://d3i71xaburhd42.cloudfront.net/69046519775ca6ac40c7d577887149525df2ee5d/10-Figure2-1.png)

[The Deep Learning Compiler: A Comprehensive Survey](https://arxiv.org/pdf/2002.03794.pdf)

[Machine Learning Compilation (MLC)](https://mlc.ai/index.html)

[MLC notes and videos](https://mlc.ai/summer22/schedule)

**Front-end**

- "frontend": parsing models (ONNX, pytorch, tensorflow)
	- [Protocol Buffers](https://protobuf.dev/)
- graph optimization
	- constant folding, operator fusion, pruning (dead code elimination)
	- [Graph Transform Tool](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/tools/graph_transforms/README.md)
	- [Computational Graph Optimization](https://mlc.ai/chapter_graph_optimization/index.html)

**Back-end**

- kernel selection
	- pick best kernel for each operation
	- [cuDNN](https://developer.nvidia.com/cudnn)
	- quantized kernels (computing on lower-precision data types)
- auto-tuning
	- find best kernel parameters for each operation (GA, RL)
	- [GA tuner](https://github.com/apache/tvm/blob/main/python/tvm/autotvm/tuner/ga_tuner.py)
- code generation
	- machine code or device-specific GPU instructions (LLVM, XLA, WASM, WebGPU)
	- [Compiling Machine Learning to WASM and WebGPU with Apache TVM](https://tvm.apache.org/2020/05/14/compiling-machine-learning-to-webassembly-and-webgpu)
- "backend": runtime environment and hardware-specific details (CUDA, OpenCL)

