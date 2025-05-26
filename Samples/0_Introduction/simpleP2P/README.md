# simpleP2P - Simple Peer-to-Peer Transfers with Multi-GPU

## Description

This application demonstrates CUDA APIs that support Peer-To-Peer (P2P) copies, Peer-To-Peer (P2P) addressing, and Unified Virtual Memory Addressing (UVA) between multiple GPUs. In general, P2P is supported between two same GPUs with some exceptions, such as some Tesla and Quadro GPUs.

## Key Concepts

Performance Strategies, Asynchronous Data Transfers, Unified Virtual Address Space, Peer to Peer Data Transfers, Multi-GPU

## Supported SM Architectures

[SM 5.0 ](https://developer.nvidia.com/cuda-gpus)  [SM 5.2 ](https://developer.nvidia.com/cuda-gpus)  [SM 5.3 ](https://developer.nvidia.com/cuda-gpus)  [SM 6.0 ](https://developer.nvidia.com/cuda-gpus)  [SM 6.1 ](https://developer.nvidia.com/cuda-gpus)  [SM 7.0 ](https://developer.nvidia.com/cuda-gpus)  [SM 7.2 ](https://developer.nvidia.com/cuda-gpus)  [SM 7.5 ](https://developer.nvidia.com/cuda-gpus)  [SM 8.0 ](https://developer.nvidia.com/cuda-gpus)  [SM 8.6 ](https://developer.nvidia.com/cuda-gpus)  [SM 8.7 ](https://developer.nvidia.com/cuda-gpus)  [SM 8.9 ](https://developer.nvidia.com/cuda-gpus)  [SM 9.0 ](https://developer.nvidia.com/cuda-gpus)

## Supported OSes

Linux, Windows

## Supported CPU Architecture

x86_64, ppc64le

## CUDA APIs involved

### [CUDA Runtime API](http://docs.nvidia.com/cuda/cuda-runtime-api/index.html)
cudaMemcpy, cudaMalloc, cudaFree, cudaMallocHost, cudaEventCreateWithFlags, cudaSetDevice, cudaEventSynchronize, cudaDeviceDisablePeerAccess, cudaGetDeviceCount, cudaDeviceSynchronize, cudaEventRecord, cudaFreeHost, cudaGetDeviceProperties, cudaDeviceEnablePeerAccess, cudaEventDestroy, cudaEventElapsedTime, cudaDeviceCanAccessPeer

## Dependencies needed to build/run
[only-64-bit](../../../README.md#only-64-bit)

## Prerequisites

Download and install the [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads) for your corresponding platform.
Make sure the dependencies mentioned in [Dependencies]() section above are installed.

## References (for more details)

## Adding some extra checks

This version of simpleP2P.cu has minor modifications to check exactly how the
arrays used to check P2P communication are initialised and to check their values
at more stages during the run.

Changes are on a branch `p2p` in [a fork of `cuda-samples`](https://github.com/gusgw/cuda-samples.git)

The code was compiled on `tooarrana2` after

```
module load gcc/13.3.0
module load cuda/12.6.0
```

using

```
nvcc  -I/home/agray/src/cas/hpc/cuda-samples/Common simpleP2P.cu -o simpleP2P.x
```

Compile with `-DBOUNDS_CHECK` to put bounds checking into the kernel, and with `-DERROR_PEEK` to check for a CUDA error from the kernels.

The output I see is:

```
[agray@gina13 simpleP2P]$ CUDA_VISIBLE_DEVICES=0,1 ./simpleP2P.x
[./simpleP2P.x] - Starting...
Checking for multiple GPUs...
CUDA-capable device count: 2

Checking GPU(s) for support of peer to peer memory access...
> Peer access from NVIDIA A100-SXM4-80GB (GPU0) -> NVIDIA A100-SXM4-80GB (GPU1) : Yes
> Peer access from NVIDIA A100-SXM4-80GB (GPU1) -> NVIDIA A100-SXM4-80GB (GPU0) : Yes
Enabling peer access between GPU0 and GPU1...
Allocating buffers (64MB on GPU0, GPU1 and CPU Host)...

---> Array on g0 initialised to 0.0

---> Array on g1 initialised to 0.0

Creating event handles...
cudaMemcpyPeer / cudaMemcpy between GPU0 and GPU1: 85.91GB/s
Preparing host buffer and memcpy to GPU0...
Run kernel on GPU1, taking source data from GPU0 and writing to GPU1...

---> Verification of g1 after copy passed

Run kernel on GPU0, taking source data from GPU1 and writing to GPU0...
Copy data back to host from GPU0 and verify results...

---> Verification of g0 after copy back passed

Disabling peer access...
Shutting down...
Test passed
```

