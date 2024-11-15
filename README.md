# Parallel FFT on FPGA
URL: asparticacid211.github.io  
Team members: Lohith Tummala (lctummal), Kavish Purani (kpurani)
## Summary
We plan to implement a parallel version of the Cooley-Tukey Radix-2 Fast Fourier Transform algorithm using SystemVerilog and synthesize the design on the DE2-115 FPGA, initially for 8 points. We want to assess the latency of the computation and the logic utilization on the FPGA as a function of the number of points that we decide to compute the FFT for. We also wish to compare this implementation against an OpenMPI C++ implementation that we will design and will be run on the GHC 8-core computers.  
## Background
The Discrete Fourier transform (DFT) is a very important and widely-used procedure that is used in differential equations, signal processing, physics, etc. When given a function that varies in time and is discrete, taking the DFT will return a function that varies in frequency, displaying the weightings of frequencies that are present in the original function. For example, if the time-valued function is the sum of several sine functions, each with a different frequency, taking the Fourier transform will give a graph with spikes at those frequency values.  
  
The Fast Fourier Transform is an algorithm that performs this process at an O(n log n) time complexity, making it much more efficient than the original O(n^2). The algorithm relies on a divide-and-conquer strategy, where the algorithm is run on two smaller subarrays and then combined.  
  
The algorithm that we plan to use is the Cooley-Tukey Radix-2 algorithm, since it is the most widely used FFT algorithm. For an array a of N values of a time-valued function, the DFT algorithm dictates that we can find the kth element of frequency valued array A as follows,  
$$A_k = \sum_{n=0}^{N-1}a_n \cdot e^{-\frac{2\pi i}{N}nk}$$  
  
We can rewrite this:  
$$A_k = \sum_{n=0}^{N/2-1}a_{2n} \cdot e^{-\frac{2\pi i}{N}(2n)k} + \sum_{n=0}^{N/2-1}a_{2n+1} \cdot e^{-\frac{2\pi i}{N}(2n+1)k} = \sum_{n=0}^{N/2-1}a_{2n} \cdot e^{-\frac{2\pi i}{N/2}nk} + e^{-\frac{2\pi i}{N}k} \cdot\sum_{n=0}^{N/2-1}a_{2n+1} \cdot e^{-\frac{2\pi i}{N/2}nk} = E_k + e^{-\frac{2\pi i}{N}k} \cdot O_k$$  
  
With some more math, we can show that
$$A_{k+N/2} = E_k - e^{-\frac{2\pi i}{N}k} \cdot O_k$$
Thus, we can recursively compute E_k and O_k, and then compute the elements of A in linear time using those subarrays. This algorithm can even be done in place, saving space.  
As is present in many applications, we attempt to compute the FFTs of many data sets. Thus, the input will be a memory of many values, and we will process the FFT on 8 points at a time, sending the resulting array to another memory.  
## Challenges
FFT is inherently computationally intensive, especially when the problem size increases. The benefit, on the other hand, is that it is also a highly parallelizable problem. The main limitations on the FPGA is the number of multipliers and logic/memory components available. The DE2-115 FPGA has 266 multipliers and 3.9 Mbits of RAM. The FPGA also has a very fast memory access times but they are limited in capacity. Thus, the entire problem will rely on pipelining efficiently on the FPGA to utilize as little resources as possible while also maximizing throughput.  
  
The communication overhead will be much more expensive on the parallel CPU implementation as the problem requires multiple iterations and also requires memory accesses from varied locations.  
## Resources
[Here](https://www.terasic.com.tw/attachment/archive/502/DE2_115_User_manual.pdf) is a link to the user manual for the DE2-115 FPGA. We plan to use this to find out how to leverage certain logic/memory blocks for our algorithm.  
  
[Here](https://en.wikipedia.org/wiki/Cooley%E2%80%93Tukey_FFT_algorithm) is a link to an explanation of the Cooley-Tukey FFT algorithm.  
  
## Goals
### What we plan to achieve
* C++ MPI version of the FFT algorithm
* 8-point FPGA version of the FFT algorithm
* Cost and performance metrics of MPI vs FPGA implementations
### What we hope to achieve
* A generalized (N-point) implementation on FPGA (N is a power of 2)  
* Plots of latency, throughput, and logic utilization for various values of N  
## Platform
The devices that we plan to implement this algorithm on are the DE2-115 Altera FPGA and the GHC machines. When we code up the algorithm on the GHC machines, we plan to use the C++ programming language with the OpenMPI language construct. This is because the FFT algorithm is highly recursive, so the process of splitting data and recombining makes the message-passing idea quite intuitive to implement. The GHC machines also have 8 cores, which is why we start off with developing an 8-point algorithm.  
  
As for the FPGA, we intend to use SystemVerilog, a hardware descriptive language designed to write code that can be transformed into utilization of physical logic gates and blocks on the FPGA. We use the DE2-115 FPGA since we have used it for 18-240, thus making it the most accessible device for us.
## Schedule
November 10-16: Develop strategies for MPI and FPGA implementations  
November 17-23: Code the MPI version and benchmark results, start FPGA implementation (for 8 points)  
November 24-30: Finish 8-point FPGA implementation  
December 1-7: Benchmark results, slack time for any delays, and work on generalized point algorithm if time permits  
December 8-13: Write final paper and poster, work on stretch goals if time permits  
## Link to Proposal
[Here](https://docs.google.com/document/d/1yUG169tpelAlSbY27yceWbm1mbr4Q-zQNxzeF_RIxFM/edit?usp=sharing) is a link to our proposal.
