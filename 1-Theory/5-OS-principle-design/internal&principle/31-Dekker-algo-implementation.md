
>[!tip] Prerequisite
>- [Process Synchronization](https://www.geeksforgeeks.org/process-synchronization-set-1/),
>- [Inter Process Communication](https://www.geeksforgeeks.org/inter-process-communication/)   

## What is Dekker algorithm?

To obtain such a mutual exclusion, bounded waiting, and progress there have been several algorithms implemented, one of which is **Dekker**’s Algorithm. To understand the algorithm let’s understand the solution to the critical section problem first.

A process is generally represented as:

```
do {
	// entry section
	critical section
	
	// exit section
	reminder section
} while(TRUE);
```

The solution to the critical section problem must ensure the following three conditions:

1.  Mutual Exclusion
2.  Progress
3.  Bounded Waiting

One of the solutions for ensuring above all factors is [Peterson’s solution](https://www.geeksforgeeks.org/petersons-algorithm-for-mutual-exclusion-set-1/).

Another one is **Dekker’s Solution**. Dekker’s algorithm was the first *probably-correct* solution to the critical section problem. It allows two threads to share a single-use resource without conflict, using only shared memory for communication. It avoids the strict alternation of a naive turn-taking algorithm, and was one of the first mutual exclusion algorithms to be invented.

Although there are many versions of Dekker’s Solution, the final or 5th version is the one that satisfies all of the above conditions and is the most efficient of them all.

>[! warning] Here implemetations ensure only two processes!
>**Note –** Dekker’s Solution, mentioned here, ensures mutual exclusion between two processes only, it could be extended to more than two processes with the proper use of arrays and variables.  
>**Algorithm –** It requires both an array of Boolean values and an integer variable:  

```
var flag: array [0..1] of boolean;
turn: 0..1;
repeat

        flag[i] := true;
        while flag[j] do
                if turn = j then
                begin
                        flag[i] := false;
                        while turn = j do no-op;
                        flag[i] := true;
                end;

                critical section

        turn := j;
        flag[i] := false;

                remainder section

until false;
```


## Version 1:

**First Version of Dekker’s Solution –** The idea is to use a common or shared thread number between processes and stop the other process from entering its critical section if the shared thread indicates the former one already running.

### impl in cpp
```cpp
Main()
{
    int thread_number = 1;
    startThreads();
}
 
Thread1()
{
    do {
 
        // entry section
        // wait until threadnumber is 1
        while (threadnumber == 2)
            ;
 
        // critical section
 
        // exit section
        // give access to the other thread
        threadnumber = 2;
 
        // remainder section
 
    } while (completed == false)
}
 
Thread2()
{
 
    do {
 
        // entry section
        // wait until threadnumber is 2
        while (threadnumber == 1)
            ;
 
        // critical section
 
        // exit section
        // give access to the other thread
        threadnumber = 1;
 
        // remainder section
 
    } while (completed == false)
}
```

### impl in java

### impl in py3

### impl in c\#

### impl in js

The problem arising in the above implementation is lockstep synchronization, i.e each thread depends on the other for its execution. If one of the processes completes, then the second process runs, gives access to the completed one, and waits for its turn, however, the former process is already completed and would never run to return the access back to the latter one. Hence, the second process **waits infinitely then**.