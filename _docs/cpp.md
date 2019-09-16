---
title: "C++"
---

## Serial program
1. Mathematically $$\int_{0}^{1}\frac{4}{1+x^2}dx\approx\pi$$. This integral can be approximated as a sum of
rectangles: 

    $$
    \sum_{i=0}^N\frac{4}{1+((i+0.5)\Delta x)^2}\Delta x\approx\pi
    $$

    where each rectangle has width $$\Delta x$$ and height $$\frac{4}{1+((i+0.5)\Delta x)^2}$$ at the middle of interval $$i$$. Here, $$N = 1/\Delta x$$.
1. A serial C++ program to compute $$\pi$$ is as follows:

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

1. OpenMP is a multi-threading, shared address model. Threads communicate by sharing variables.
1. To create multiple threads or parallel regions, use compiler directive: `#pragma omp parallel`.
1. To control race conditions, use synchronization to protect data conflicts.
1. Synchronization 
    + `#pragma omp barrier`: Each thread waits until all threads arrive.
    + `#pragma omp critical`: Only one thread at a time can enter a critical region. Threads wait their turn – only one at a time calls code within the block.
    + `#pragma omp atomic`: Provides mutual exclusion but only applies to the update of a memory location.
1. A parallel program with synchronization to compute $$\pi$$ is as follows:
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
1. WorkSharing Loop: `#pragma omp for`
    + The loop worksharing construct splits up loop iterations (i.e., `for` loops) among the threads in a team. This essentially simplifies parallelization compared to using synchronization. 
    + Ensure the loop iterations independent so they can safely execute in any order without loop-carried dependencies.
    + Combining values from different threads into a single accumulation variable is known as reduction. In OpenMP, use the reduction clause: `reduction (op : list)`
    + Inside a work-sharing construct:
       + A local copy of each list variable is made and initialized depending on the `op` (e.g. 0 for `+`).
        + Updates occur on the local copy.
        + Local copies are reduced into a single value and
        combined with the original global value.
        + The variables in `list` must be shared in the  enclosing parallel region.
1. A parallel program with worksharing loop to compute $$\pi$$ is as follows: 
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
1. Major OpenMP constructs
    + To create a team of threads
        ```c
        #pragma omp parallel
        ```
    + To share work between threads:
        ```c
        #pragma omp for
        #pragma omp single
        #pragma omp sections
        #pragma omp section
        ```
    + To prevent conflicts (prevent races)
        ```c
        #pragma omp critical
        #pragma omp atomic
        #pragma omp barrier
        #pragma omp master
        ```
    + Data environment clauses
        ```c
        private (variable_list)
        firstprivate (variable_list)
        lastprivate (variable_list)
        reduction(+:variable_list)   
        ``` 
1. Presence of implicit barriers at the end of `OMP` directives.
    <style type="text/css">
    .tg  {border-collapse:collapse;border-spacing:0;border-color:#aaa;}
    .tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#aaa;color:#333;background-color:#fff;}
    .tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#aaa;color:#fff;background-color:#f38630;}
    .tg .tg-baqh{text-align:center;vertical-align:top}
    .tg .tg-dg7a{background-color:#FCFBE3;text-align:left;vertical-align:top}
    .tg .tg-yq6s{background-color:#FCFBE3;text-align:center;vertical-align:top}
    .tg .tg-0lax{text-align:left;vertical-align:top}
    </style>
    <table class="tg" style="undefined;table-layout: fixed; width: 306px">
    <colgroup>
    <col style="width: 153px">
    <col style="width: 153px">
    </colgroup>
    <tr>
        <th class="tg-baqh">Directive</th>
        <th class="tg-baqh">Barrier at the end</th>
    </tr>
    <tr>
        <td class="tg-dg7a">#pragma omp parallel</td>
        <td class="tg-yq6s">implicit</td>
    </tr>
    <tr>
        <td class="tg-0lax">#pragma omp master</td>
        <td class="tg-baqh">none</td>
    </tr>
    <tr>
        <td class="tg-dg7a">#pragma omp single</td>
        <td class="tg-yq6s">implicit</td>
    </tr>
    <tr>
        <td class="tg-0lax">#pragma omp task</td>
        <td class="tg-baqh">none</td>
    </tr>
    </table>