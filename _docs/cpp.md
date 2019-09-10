# C++

## Serial program
+ The value of $\pi$ can be approximated by the sum of rectangles
$$ 
\sum_{i=0}^N\frac{4}{1+x^2}dx\approx\pi
\sum_{i=0}^NF(x_i)\frac{4}{1+x^2}dx\approx\pi
$$
where each rectangle has width `x` and height F(x i ) at the middle of interval i.


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

Parallel programming in C++ may be achieved using using OpenMP, MPI, and multithreading.

## Open MP

+ OpenMP is a multi-threading, shared address model. Threads communicate by sharing variables.
+ To create multiple threads or parallel regions, use compiler directive: `#pragma omp parallel`.
+ To control race conditions, use synchronization to protect data conflicts.
+ Synchronization 
    + `#pragma omp barrier`: Each thread waits until all threads arrive.
    + `#pragma omp critical`: Only one thread at a time can enter a critical region. Threads wait their turn – only one at a time calls code within the block.
    + `#pragma omp atomic`: Provides mutual exclusion but only applies to the update of a memory location.
+ WorkSharing Loop: `#pragma omp for`
    + The loop worksharing construct splits up loop iterations among the threads in a team. This is a simpler parallelization technique compared to using synchronization. 
    + Ensure the loop iterations independent so they can safely execute in any order without loop-carried dependencies.
    + OpenMP reduction clause: `reduction (op : list)`
    + Inside a work-sharing construct:
       + A local copy of each list variable is made and initialized depending on the `op` (e.g. 0 for `+`).
        + Updates occur on the local copy.
        + Local copies are reduced into a single value and
        combined with the original global value.
        + The variables in `list` must be shared in the  enclosing parallel region.
