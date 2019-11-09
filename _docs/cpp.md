---
title: "C++"
---

## Code Style
1. Always remember that code interpretability and maintainability trumps everything else.
1. Please conform to the [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html).
1. Avoid using Class Inheritance or Friendship features in C++ codes. Inheritance and Friendship reduces code traceability.
1. In general macros should not be used.
1. Wherever possible, follow C style instead of C++, as it makes the code more aligned with the driving philosophies of Golang.

## Naming
+ C++ files should end in .cc and header files should end in .h.
+ Naming guide:

    | Type                       | Format             | Example                               |
    | :--------------------------|:-------------------| :-------------------------------------|
    | Filename                   | lowercase.cc       | myusefulclass.cc                      |
    | Namespace Names            | lower_with_under   | using namespace websearch::index_util |
    | Typedef                    | CapWords           | typedef struct _MyClass MyClass;      |
    | Enumerator                 | CapWords           | enum Color {red, yellow};             |
    | Aliases                    | CapWords           | using Calc = void (*) (int, int);     |
    | Constants                  | kCapWords          | const int kDaysInWeek = 7;            |
    | Variables                  | lower_with_under   | std::string table_name;               |
    | Function Names             | CapWords           |	void MyFunction(){};                  |
    | Function Parameters        | lower_with_under   | void my_function(int max_count){};    |
    | Structures                 | CapWords           | struct UrlTableProperties{};          |
    | Struct Data Member         | lower_with_under   | int num_entries;                      |
    | Classes                    | CapWords           |	class UrlTableTester{};               |
    | Class Data Member          | lower_with_under_  | std::string table_name_;              |

## Code Documentation - Doxygen 
1. Install Doxygen
    ```bash
    $ apt-get install doxygen
    ```

# Folder structure
The following serves as a representative project folder structure.

```text
cppprocessing                  # Main project directory 
├── apps                       # Folder for executable main files
|   ├── CMakeLists.txt            
|   └── tutorial.cpp           # Executable main file 
├── assets                     # Assets
|   ├── ...
|   ... 
├── bin                        # Binary files
├── build                      # CMake build system files 
├── include                    # Headers
|   ├── computepi.h
|   └── helloworld.h   
├── libs                       # Local libraries
|   ├── computepi
|   |   ├── CMakeLists.txt
|   |   └── computepi.cpp 
|   └── helloworld    
|       ├── CMakeLists.txt
|       └── computepi.cpp 
├── CMakeLists.txt 
├── docker-compose.yml
├── Dockerfile                            
└── README.md                  # Readme file
```
1. `apps` folder may contain one or more main files.    
1. `assets` folder should contain miscellaneous files. For example, it can contain images used for explanation in `Readme.md`.
1. `bin` folder may contain one or more binary files, which correspond to the main files in `apps` folder.
1. `build` folder contain the CMake build system.
1. `include` folder contains the C/C++ headers.
1. `libs` contains the local libraries in individual folders.
1. `CMakeLists.txt` defines the top-level CMake commands.
1. `README.md` file shall contain a brief description of the following:
    + Explanation of what the project is about
    + Instructions to run a sample of the code
    + Desired input and output of the code

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
    .tg .tg-7d57{background-color:#FCFBE3;border-color:inherit;text-align:left;vertical-align:top}
    .tg .tg-g30d{background-color:#FCFBE3;border-color:inherit;text-align:center;vertical-align:top}
    .tg .tg-c3ow{border-color:inherit;text-align:center;vertical-align:top}
    .tg .tg-0pky{border-color:inherit;text-align:left;vertical-align:top}
    .tg .tg-0lax{text-align:left;vertical-align:top}
    .tg .tg-dg7a{background-color:#FCFBE3;text-align:left;vertical-align:top}
    .tg .tg-yq6s{background-color:#FCFBE3;text-align:center;vertical-align:top}
    </style>
    <table class="tg">
        <colgroup>
        <col style="width: 50%">
        <col style="width: 50%">
        </colgroup>
        <tr>
            <th class="tg-c3ow">Directive</th>
            <th class="tg-c3ow">Barrier at the end</th>
        </tr>
        <tr>
            <td class="tg-7d57">#pragma omp parallel</td>
            <td class="tg-g30d">implicit</td>
        </tr>
        <tr>
            <td class="tg-0pky">#pragma omp for</td>
            <td class="tg-c3ow">implicit</td>
        </tr>
        <tr>
            <td class="tg-7d57">#pragma omp master</td>
            <td class="tg-g30d">none</td>
        </tr>
        <tr>
            <td class="tg-0pky">#pragma omp single</td>
            <td class="tg-c3ow">implicit</td>
        </tr>
        <tr>
            <td class="tg-7d57">#pragma omp task</td>
            <td class="tg-g30d">none</td>
        </tr>
        <tr>
            <td class="tg-0pky">#pragma omp barrier</td>
            <td class="tg-c3ow">implicit</td>
        </tr>
        <tr>
            <td class="tg-7d57">#pragma omp for</td>
            <td class="tg-g30d">implicit</td>
        </tr>
        <tr>
            <td class="tg-0pky">#pragma omp sctions</td>
            <td class="tg-c3ow">implicit</td>
        </tr>
        <tr>
            <td class="tg-7d57">#pragma omp section</td>
            <td class="tg-g30d">none</td>
        </tr>
    </table>