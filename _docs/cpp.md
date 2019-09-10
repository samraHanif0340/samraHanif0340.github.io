---
title: "C++"
---

## Serial program
+ Mathematically $$\int_{0}^{1}\frac{4}{1+x^2}dx\approx\pi$$. This integral can be approximated as a sum of
rectangles: 

    $$
    \sum_{i=0}^N\frac{4}{1+((i+0.5)\Delta x)^2}\Delta x\approx\pi
    $$

    where each rectangle has width $$\Delta x$$ and height $$\frac{4}{1+((i+0.5)\Delta x)^2}$$ at the middle of interval $$i$$. Here, $$N = 1/\Delta x$$.
+ A serial C++ program to compute $$\pi$$ this is as follows:

    ```c++
    double runPiSerial(int numSteps){
        double x, pi, sum = 0.0;
        double step = 1.0/double(numSteps);

        for (int ii = 0; ii < numSteps; ii++){
            x = (ii + 0.5)*step;
            sum = sum + 4.0/(1.0+x*x);
        }
        pi = step * sum;
        
        printf("computePi.runPiSerial(). Pi = %f.\n", pi);
        return pi;
    }
    ```

Parallel programming in C++ may be achieved using OpenMP, MPI, and multithreading.

## Open MP

+ OpenMP is a multi-threading, shared address model. Threads communicate by sharing variables.
+ To create multiple threads or parallel regions, use compiler directive: `#pragma omp parallel`.
+ To control race conditions, use synchronization to protect data conflicts.
+ Synchronization 
    + `#pragma omp barrier`: Each thread waits until all threads arrive.
    + `#pragma omp critical`: Only one thread at a time can enter a critical region. Threads wait their turn – only one at a time calls code within the block.
    + `#pragma omp atomic`: Provides mutual exclusion but only applies to the update of a memory location.
    + A parallel program with synchornisation to compute $\pi$ is as follows:
    ```c++
    double runPiParallelSync(int numSteps, int nThreadsInput){
        double x;
        double step = 1.0/double(numSteps);
        double sumMaster = 0;
        int nThreadsMaster;

        omp_set_num_threads(nThreadsInput);
        #pragma omp parallel
        {
            int id = omp_get_thread_num(); //Thread ID
            int nThreadsSlave = omp_get_num_threads();
            if (id == 0){
                nThreadsMaster = nThreadsSlave;
            }
            int ii;
            double sumSlave = 0;
            for (ii = id; ii < numSteps; ii=ii+nThreadsSlave){
                x = (ii + 0.5)*step;
                sumSlave = sumSlave + 4.0/(1.0+x*x);
            }
            #pragma omp critical
            {
                sumMaster += sumSlave;
            }
        }
        double pi = sumMaster*step;
        
        printf("computePi.runPiParallelSync(). Pi = %f.\n", pi);
        return pi;
    }
    ```
+ WorkSharing Loop: `#pragma omp for`
    + The loop worksharing construct splits up loop iterations among the threads in a team. This is a simpler parallelization technique compared to using synchronization. 
    + Ensure the loop iterations independent so they can safely execute in any order without loop-carried dependencies.
    + Combining values from different threads into a single accumulation variable is known as reduction. In OpenMP, use the reduction clause: `reduction (op : list)`
    + Inside a work-sharing construct:
       + A local copy of each list variable is made and initialized depending on the `op` (e.g. 0 for `+`).
        + Updates occur on the local copy.
        + Local copies are reduced into a single value and
        combined with the original global value.
        + The variables in `list` must be shared in the  enclosing parallel region.
    + A parallel program with worksharing loop to compute $\pi$ is as follows: 
    ```c++
    double runPiWorkSharing(int numSteps){
        double pi, sum = 0.0;
        double step = 1.0/double(numSteps);

        #pragma omp parallel 
        {
            double x;
            #pragma omp for reduction(+:sum)
            for (int ii = 0; ii < numSteps; ii++){
                x = (ii + 0.5)*step;
                sum = sum + 4.0/(1.0+x*x);
            }

        }
        pi = step * sum;
        
        printf("computePi.runPiWorkSharing(). Pi = %f.\n", pi);
        return pi;
    }
    ```