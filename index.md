---
layout: default
---

[Implementation Repository](./another-page.html).
[Midpoint Report](./another-page.html).
[Final Paper](./another-page.html).

# Summary

We are going to be experimenting with different ways to parallelize the beam search algorithm for our project. The actual beam search algorithm would be written in C/C++, and parallelized in 2 different ways: using CUDA and using MPI. We want to compare and contrast how these different forms of parallelism can yield speedup in an easily parallelizable algorithm like beam search. 


# Background

Beam search is a widely used algorithm in natural language processing (NLP) and other search-based applications that can be used to find the optimal sequence of states. By maintaining a fixed number of partial solutions (or beams) while exploring possible states, the algorithm achieves a balance between exhaustive search and computational efficiency, making it especially suitable for real-time applications and constrained resources. In this project, we focus on parallelizing the beam search algorithm to explore the performance benefits of different parallel computing approaches.

Through this project, we aim to deepen our understanding of the trade-offs in parallelizing a tree-based search algorithm like beam search on different architectures. We hope to identify the optimal balance between synchronization, memory management, and workload distribution for each parallelization strategy, gaining insights into the challenges of mapping a workload with high communication demands and divergent execution to both GPU and CPU systems.


# The Challenge

Parallelizing beam search presents several distinct challenges, especially in balancing workloads, memory access patterns, and synchronization. While beam search is quite conceptually simple and inherently parallelizable, implementing it efficiently in both CUDA and MPI will present some challenges.

### Dependencies and Synchronization

Beam search introduces dependencies across beams, since each expansion step depends on evaluating and ranking partial sequences to ensure the highest-scoring beams proceed. Synchronization is necessary after each beam expansion, to make sure that that the beams are consistently ranked and that partial solutions are synchronized before moving to the next depth level. With MPI, maintaining synchronization across distributed memory environments introduces additional latency and complexity, as data must be communicated between processes, and potentially over networked nodes. This makes efficient handling of message-passing synchronization a priority to prevent excessive communication overhead and ensure consistent data across processes.

### Memory Access Patterns

Beam search’s memory access pattern is irregular, as each beam expansion accesses various data points, often with poor locality, which can result in scattered memory reads and writes. In a GPU context with CUDA, this lack of locality can lower cache utilization and increase latency due to frequent global memory accesses. With MPI, where each process has its own memory space, memory management becomes even more complex. As beam size increases, so do the memory requirements, and effectively distributing these memory accesses among MPI processes without excessive data movement or duplication will be key to achieving speedup.

### Communication and Computation Balance

Beam search’s communication-to-computation ratio is relatively high due to frequent sorting and synchronization requirements. Each parallel expansion step generates partial results that need to be gathered, ranked, and redistributed among processes. For MPI, this inter-process communication introduces significant overhead, as it requires data to be sent across nodes, often with high latency. Managing this communication in a way that minimizes bottlenecks while still achieving accurate sorting and state ranking is a primary challenge. CUDA, by comparison, benefits from shared memory within blocks but is constrained by limited memory for larger beams, requiring careful management of communication within GPU threads as well.

### Divergent Execution and Load Balancing

Beam search often exhibits divergent execution due to varying expansion paths, which can cause load imbalance. In CUDA, this divergence leads to inefficiencies, as threads in a warp are forced to wait for the longest-running thread. In MPI, managing load balancing across processes is critical, as each process may encounter a variable amount of work depending on the beam’s branching factor. Assigning beams evenly across MPI processes without overloading or idling specific nodes will be challenging, especially with larger beam sizes and deeper search depths.

# Resources

We will be using Yashika’s Python implementation of beam search done for 10-417/617 (Intermediate Deep Learning) as a starting point for our implementation. This starter code base implements the key functionalities of beam search and will allow us to better focus on the intricacies of the parallelisation of the algorithm.

We plan to run our code on the Gates cluster machines to test parallel performance. However, if possible, it may be beneficial to run additional tests on the PSC machines to analyse how the performance of our implementation scales with higher core counts and more powerful cores.

# Goals & Deliverables 

Plan to Achieve

| Goal  | Deliverable                         | Goal |
|:------|:------------------------------------|:------------------------------------------|
| 1     | Baseline performance analysis       | Set up the initial starter code, write the timing code, and conduct a baseline analysis  |
| 2     | Literature review                   | Perform a literature review to see if there are any relevant papers about beam search parallelisation and explore different techniques that may be applicable to our two selected methods  |
| 3     | CUDA Implementation                 | Implement a parallelised version of beam search using CUDA   |
| 4     | MPI Implementation                  | Implement a parallelised version of beam search using MPI  |
| 5     | Performance analysis & discussion   | Perform a performance analysis of baseline + parallelised versions and create speedup graphs  |


Hope to Achieve

| Goal  | Deliverable                         | Goal |
|:------|:------------------------------------|:------------------------------------------|
| 6     | Further optimizations + parallelisation methods       | We hope to implement 2-3 different optimizations (such as level-of-detail techniques or roving capsule meshes) but may run out of time  |

We plan to show a visualised demo of our parallelised beam search, as well as an example application of beam search for NLP to illustrate the spedups that we have achieved during the final presentation along with speedup graphs illustrating the performance benefits we saw.

# Platform Choice

We have chosen C++ as our programming language for this project because it provides both the necessary expressivity to implement beam search as well as providing support for both CUDA and MPI forms of parallel programming. Furthermore, C++ is significantly more efficient than Python and, thus, more suited for these types of performance-dependent programming applications.

We also chose to use CUDA and MPI to parallelize this workload since they are two completely different methods and will allow us to see which form of parallelization performs better on this type of workload. Our goal through the project is essentially to see why it makes sense to use one of these parallel systems over the other.

# Schedule

Fill in here