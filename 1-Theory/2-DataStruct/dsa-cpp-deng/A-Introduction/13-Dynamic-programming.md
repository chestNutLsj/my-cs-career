
Dynamic programming is defined as a computer programming technique where an algorithmic problem is first broken down into sub-problems, the results are saved, and then the sub-problems are optimized to find the overall solution — which usually has to do with finding the maximum and minimum range of the algorithmic query. This article closely examines how dynamic programming works, with examples. 

## What Is Dynamic Programming?

Richard Bellman was the one who came up with the idea for dynamic programming in the 1950s. It is a method of mathematical optimization as well as a methodology for computer programming. **It applies to issues one can break down into either overlapping subproblems or optimum substructures**.

When a more extensive set of equations is broken down into smaller groups of equations, overlapping subproblems are referred to as equations that reuse portions of the smaller equations several times to arrive at a solution.

On the other hand, optimum substructures locate the best solution to an issue, then build the solution that provides the best results overall. This is how they solve problems. When a vast issue is split down into its constituent parts, a computer will apply a mathematical algorithm to determine which elements have the most desirable solution. Then, it takes the solutions to the more minor problems and utilizes them to get the optimal solution to the initial, more involved issue.

This technique solves problems by breaking them into smaller, overlapping subproblems. The results are then stored in a table to be reused so the same problem will not have to be computed again. 

For example, when using the dynamic programming technique to figure out all possible results from a set of numbers, the first time the results are calculated, they are saved and put into the equation later instead of being calculated again. So, when dealing with long, complicated equations and processes, it saves time and makes solutions faster by doing less work.

**The dynamic programming algorithm tries to find the shortest way to a solution when solving a problem**. It does this by going from the top down or the bottom up. The top-down method solves equations by breaking them into smaller ones and reusing the answers when needed. The bottom-up approach solves equations by breaking them up into smaller ones, then tries to solve the equation with the smallest mathematical value, and then works its way up to the equation with the biggest value.

Using dynamic programming to solve problems is more effective than just trying things until they work. But it only helps with problems that one can break up into smaller equations that will be used again at some point.

### Recursion vs. dynamic programming

In computer science, recursion is a crucial concept in which the solution to a problem depends on solutions to its smaller subproblems. 

Meanwhile, dynamic programming is an optimization technique for recursive solutions. It is the preferred technique for solving recursive functions that make repeated calls to the same inputs. A function is known as recursive if it calls itself during execution. This process can repeat itself several times before the solution is computed and can repeat forever if it lacks a base case to enable it to fulfill its computation and stop the execution. 

However, not all problems that use recursion can be solved by dynamic programming. Unless solutions to the subproblems overlap, a recursion solution can only be arrived at using a divide-and-conquer method.

For example, problems like merge, sort, and quick sort are not considered dynamic programming problems. This is because they involve putting together the best answers to subproblems that don’t overlap.

#### **Drawbacks of recursion**

Recursion uses memory space less efficiently. Repeated function calls create entries for all the variables and constants in the function stack. As the values are kept there until the function returns, there is always a limited amount of stack space in the system, thus making less efficient use of memory space. Additionally, a stack overflow error occurs if the recursive function requires more memory than is available in the stack. 

Recursion is also relatively slow in comparison to iteration, which uses loops. When a function is called, there is an overhead of allocating space for the function and all its data in the function stack in recursion. This causes a slight delay in recursive functions. 

### Where should dynamic programming be used?

Dynamic programming is used when one can break a problem into more minor issues that they can break down even further, into even more minor problems. Additionally, these subproblems have overlapped. That is, they require previously calculated values to be recomputed. With dynamic programming, the computed values are stored, thus reducing the need for repeated calculations and saving time and providing faster solutions. 

How Does Dynamic Programming Work?
----------------------------------

Dynamic programming works by breaking down complex problems into simpler subproblems. Then, finding optimal solutions to these subproblems. Memorization is a method that saves the outcomes of these processes so that the corresponding answers do not need to be computed when they are later needed. Saving solutions save time on the computation of subproblems that have already been encountered. 

Dynamic programming can be achieved using two approaches:

### 1. Top-down approach

In computer science, problems are resolved by recursively formulating solutions, employing the answers to the problems’ subproblems. If the answers to the subproblems overlap, they may be memoized or kept in a table for later use. The top-down approach follows the strategy of memorization. The memoization process is equivalent to adding the recursion and caching steps. The difference between recursion and caching is that recursion requires calling the function directly, whereas caching requires preserving the intermediate results.

The top-down strategy has many benefits, including the following:

*   The top-down approach is easy to understand and implement. In this approach, problems are broken down into smaller parts, which help users identify what needs to be done. With each step, more significant, more complex problems become smaller, less complicated, and, therefore, easier to solve. Some parts may even be reusable for the same problem.
*   It allows for subproblems to be solved upon request. The top-down approach will enable problems to be broken down into smaller parts and their solutions stored for reuse. Users can then query solutions for each part. 
*   It is also easier to debug. Segmenting problems into small parts allows users to follow the solution quickly and determine where an error might have occurred. 

Disadvantages of the top-down approach include:

*   The top-down approach uses the recursion technique, which occupies more memory in the call stack. This leads to reduced overall performance. Additionally, when the recursion is too deep, a stack overflow occurs. 

### 2. Bottom-up approach

In the bottom-up method, once a solution to a problem is written in terms of its subproblems in a way that loops back on itself, users can rewrite the problem by solving the smaller subproblems first and then using their solutions to solve the larger subproblems. 

Unlike the top-down approach, the bottom-up approach removes the recursion. Thus, there is neither stack overflow nor overhead from the recursive functions. It also allows for saving memory space. Removing recursion decreases the time complexity of recursion due to recalculating the same values. 

The advantages of the bottom-up approach include the following:

*   It makes decisions about small reusable subproblems and then decides how they will be put together to create a large problem. 
*   It removes recursion, thus promoting the efficient use of memory space. Additionally, this also leads to a reduction in timing complexity. 

**See More:** [**DevOps Roadmap: 7-Step Complete Guide**](https://www.spiceworks.com/tech/devops/articles/devops-roadmap-guide/)

### Signs of dynamic programming suitability

Dynamic programming solves complex problems by breaking them up into smaller ones using recursion and storing the answers so they don’t have to be worked out again. It isn’t practical when there aren’t any problems that overlap because it doesn’t make sense to store solutions to the issues that won’t be needed again.

Two main signs are that one can solve a problem with dynamic programming: subproblems that overlap and the best possible substructure.

**Overlapping subproblems**

When the answers to the same subproblem are needed more than once to solve the main problem, we say that the subproblems overlap. In overlapping issues, solutions are put into a table so developers can use them repeatedly instead of recalculating them. The recursive program for the Fibonacci numbers has several subproblems that overlap, but a binary search doesn’t have any subproblems that overlap.

A binary search is solved using the divide and conquer technique. Every time, the subproblems have a unique array to find the value. Thus, binary search lacks the overlapping property. 

For example, when finding the nth Fibonacci number, the problem F (n) is broken down into finding F (n-1) and F. (n-2). You can break down F (n-1) even further into a subproblem that has to do with F. (n-2). In this scenario, F (n-2) is reused, and thus, the Fibonacci sequence can be said to exhibit overlapping properties. 

**Optimal substructure**

The optimal substructure property of a problem says that you can find the best answer to the problem by taking the best solutions to its subproblems and putting them together. Most of the time, recursion explains how these optimal substructures work.

This property is not exclusive to dynamic programming alone, as several problems consist of optimal substructures. However, most of them lack overlapping issues. So, they can’t be called problems with dynamic programming.

You can use it to find the shortest route between two points. For example, if a node p is on the shortest path from a source node t to a destination node w, then the shortest path from t to w is the sum of the shortest paths from t to p and from p to w.

Examples of problems with optimal substructures include the longest increasing subsequence, longest palindromic substring, and longest common subsequence problem. Examples of problems without optimal substructures include the most extended path problem and the addition-chain exponentiation. 

### Understanding the Longest Common Subsequence concept in dynamic programming

In dynamic programming, the phrase “largest common subsequence” (LCS) refers to the subsequence that is shared by all of the supplied sequences and is the one that is the longest. It is different from the challenge of finding the longest common substring in that the components of the LCS do not need to occupy consecutive locations within the original sequences to be considered part of that problem.

The LCS is characterized by an optimal substructure and overlapping subproblem properties. This indicates that the issue may be split into many less complex sub-issues and worked on individually until a solution is found. The solutions to higher-level subproblems are often reused in lower-level subproblems, thus, overlapping subproblems. 

Therefore, when solving an LCS problem, it is more efficient to use a dynamic algorithm than a recursive algorithm. Dynamic programming stores the results of each function call so that it can be used in future calls, thus minimizing the need for redundant calls. 

For instance, consider the sequences (MNOP) and (MONMP). They have five length-2 common subsequences (MN), (MO), (MP), (NP), and (OP); two length-3 common subsequences (MNP) and (MOP); MNP and no longer frequent subsequences (MOP). Consequently, (MNP) and (MOP) are the largest shared subsequences. LCS can be applied in bioinformatics to the process of genome sequencing.

**See More:** [**What Is Jenkins? Working, Uses, Pipelines, and Features**](https://www.spiceworks.com/tech/devops/articles/what-is-jenkins/)

Dynamic Programming Algorithms
------------------------------

When dynamic programming algorithms are executed, they solve a problem by segmenting it into smaller parts until a solution arrives. They perform these tasks by finding the shortest path. Some of the primary dynamic programming algorithms in use are:

### 1. Greedy algorithms

An example of dynamic programming algorithms, greedy algorithms are also optimization tools. The method solves a challenge by searching for optimum solutions to the subproblems and combining the findings of these subproblems to get the most optimal answer. 

Conversely, when greedy algorithms solve a problem, they look for a locally optimum solution to find a global optimum. They make a guess that looks optimum at the time but does not guarantee a globally optimum solution. This could end up becoming costly down the road. 

### 2. Floyd-Warshall algorithm

The Floyd-Warshall method uses a technique of dynamic programming to locate the shortest pathways. It determines the shortest route across all pairings of vertices in a graph with weights. Both directed and undirected weighted graphs can use it.

This program compares each pair of vertices’ potential routes through the graph. It gradually optimizes an estimate of the shortest route between two vertices to determine the shortest distance between two vertices in a chart. With simple modifications to it, one can reconstruct the paths. 

This method for dynamic programming contains two subtypes: 

*   **Behavior with negative cycles:** Users can use the Floyd-Warshall algorithm to find negative cycles. You can do this by inspecting the diagonal path matrix for a negative number that would indicate the graph contains one negative cycle. In a negative cycle, the sum of the edges is a negative value; thus, there cannot be a shortest path between any pair of vertices. Exponentially huge numbers are generated if a negative cycle occurs during algorithm execution.
*   **Time complexity:** The Floyd-Warshall algorithm has three loops, each with constant complexity. As a result, the Floyd-Warshall complexity has a time complexity of O (n3). Wherein n represents the number of network nodes. 

### 3. Bellman Ford algorithm

The Bellman-Ford Algorithm determines the shortest route from a particular source vertex to every other weighted digraph vertices. The Bellman-Ford algorithm can handle graphs where some of the edge weights are negative numbers and produce a correct answer, unlike Dijkstra’s algorithm, which does not confirm whether it makes the correct answer. However, it is much slower than Dijkstra’s algorithm. 

The Bellman-Ford algorithm works by relaxation; that is, it gives approximate distances that better ones continuously replace until a solution is reached. The approximate distances are usually overestimated compared to the distance between the vertices. The replacement values reflect the minimum old value and the length of a newly found path. 

This algorithm terminates upon finding a negative cycle and thus can be applied to cycle-canceling techniques in network flow analysis. 

**See More:** [**Top 10 DevOps Automation Tools in 2021**](https://www.spiceworks.com/tech/devops/articles/devops-automation-tools/)

Examples of Dynamic Programming 
--------------------------------

Here are a few examples of how one may use dynamic programming:

### 1. Identifying the number of ways to cover a distance

Some recursive functions are invoked three times in the recursion technique, indicating the overlapping subproblem characteristic required to calculate issues that use the dynamic programming methodology.

Using the top-down technique, just store the value in a HashMap while retaining the recursive structure, then return the value store without calculating each time the function is invoked. Utilize an extra space of dimension n when employing the bottom-up method and compute the values of states beginning with 1, 2,…, n, i.e., compute the values of I i+1 and i+2 and then apply them to determine the value of i+3. 

### 2. Identifying the optimal strategy of a game

To identify the optimal strategy of a game or [gamified experience,](https://www.spiceworks.com/tech/devops/articles/what-is-gamification/) let’s consider the “coins in a line” game. The memoization technique is used to compute the maximum value of coins taken by player A for coins numbered h to k, assuming player B plays optimally (Mh, k). To find out each player’s strategy, assign values to the coin they pick and the value of the opponent’s coin. After computation, the optimal design for the game is determined by observing the Mh, k value for both players if player A chooses coin h or k. 

### 3. Counting the number of possible outcomes of a particular die roll 

With an integer M, the aim is to determine the number of approaches to obtain the sum M by tossing dice repeatedly. The partial recursion tree, where M=8, provides overlapping subproblems when using the recursion method. By using dynamic programming, one can optimize the recursive method. One can use an array to store values after computation for reuse. In this way, the algorithm takes significantly less time to run with time complex: O (t * n * m), with t being the number of faces, n being the number of dice, and m being the given sum.

**See More:** [**DevOps vs. Agile Methodology: Key Differences and Similarities**](https://www.spiceworks.com/tech/devops/articles/devops-vs-agile/) 

### Takeaway

Dynamic programming is among the more advanced skills one must learn as a programmer or [DevOps engineer](https://www.spiceworks.com/tech/devops/articles/devops-engineer/), mainly if you specialize in [Python](https://www.spiceworks.com/tech/artificial-intelligence/articles/top-python-machine-learning-libraries/). It is a relatively simple way to solve complex algorithmic problems and a skill you can apply to virtually any language or use case. For example, the viral game, Wordle, follows dynamic programming principles, and users can train an algorithm to resolve it by finding the most optimal combination of alphabets. In other words, the skill has versatile applications and must be part of every DevOps learning kit. 

**_Did this article help you understand how dynamic programming works? Tell us on_** [**_Facebook_**Opens a new window]( https://www.facebook.com/SpiceworksNews/ "Opens a new window") **_,_** [**_Twitter_**Opens a new window]( https://twitter.com/SpiceworksNews "Opens a new window") **_, and_** [**_LinkedIn_**Opens a new window]( https://www.linkedin.com/company/spiceworksdotcom/ "Opens a new window") **_. We’d love to hear from you!_** 

### **MORE ON DEVOPS**

*   [Top 10 Azure DevOps Certifications in 2022](https://www.spiceworks.com/tech/devops/articles/azure-devops-certifications/)
*   [What Is DevOps Lifecycle? Definition, Key Components, and Management Best Practices](https://www.spiceworks.com/tech/devops/articles/what-is-devops-lifecycle/)
*   [Top 10 Scrum Master Certifications in 2022](https://www.spiceworks.com/tech/devops/articles/scrum-master-certifications/)
*   [Top 18 Azure DevOps Interview Questions in 2022](https://www.spiceworks.com/tech/devops/articles/azure-devops-interview-questions/)
*   [Top 10 DevOps Certifications and Courses in 2022](https://www.spiceworks.com/tech/devops/articles/devops-certifications-and-courses/)