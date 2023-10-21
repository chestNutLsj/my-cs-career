## Case 1: each arrays has the same length

Given **K** sorted arrays of size **N** each, merge them and print the sorted output.

```
Input: K = 3, N = 4, arr = { {1, 3, 5, 7}, {2, 4, 6, 8}, {0, 9, 10, 11}}
Output: 0 1 2 3 4 5 6 7 8 9 10 11 
Explanation: The output array is a sorted array that contains all the elements of the input matrix. 

Input: k = 4, n = 4, arr = { {1, 5, 6, 8}, {2, 4, 10, 12}, {3, 7, 9, 11}, {13, 14, 15, 16}} 
Output: 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 
Explanation: The output array is a sorted array that contains all the elements of the input matrix. 
```

### Approach 1: naive / BF

Create an output array of size (N * K) and then copy all the elements into the output array followed by sorting:
- Create an output array of size N * K. 
- Traverse the matrix from start to end and insert all the elements in the output array.
- Sort and print the output array.

#### Implementation

```cpp
// C++ program to merge K sorted arrays of size n each. 

#include <bits/stdc++.h> 
using namespace std; 
#define N 4 

// A utility function to print array elements 
void printArray(int arr[], int size) 
{ 
	for (int i = 0; i < size; i++) 
		cout << arr[i] << " "; 
} 

// This function takes an array of arrays as an argument and 
// All arrays are assumed to be sorted. It merges them 
// together and prints the final sorted output. 
void mergeKArrays(int arr[][N], int a, int output[]) 
{ 
	int c = 0; 

	// traverse the matrix 
	for (int i = 0; i < a; i++) { 
		for (int j = 0; j < N; j++) 
			output = arr[i][j]; 
	} 

	// sort the array 
	sort(output, output + N * a); 
} 

// Driver's code 
int main() 
{ 
	// Change N at the top to change number of elements 
	// in an array 
	int arr[][N] = { { 2, 6, 12, 34 }, 
					{ 1, 9, 20, 1000 }, 
					{ 23, 34, 90, 2000 } }; 
	int K = sizeof(arr) / sizeof(arr[0]); 

	int output[N * K]; 

	// Function call 
	mergeKArrays(arr, 3, output); 

	cout << "Merged array is " << endl; 
	printArray(output, N * K); 

	return 0; 
}

```

```python
# Python3 program to merge k sorted arrays of size n each. 

# This function takes an array of arrays as an argument 
# and 
# All arrays are assumed to be sorted. It merges them 
# together and prints the final sorted output. 


def mergeKArrays(arr, a, output): 
	c = 0

	# traverse the matrix 
	for i in range(a): 
		for j in range(4): 
			output = arr[i][j] 
			c += 1

	# sort the array 
	output.sort() 

# A utility function to print array elements 


def printArray(arr, size): 
	for i in range(size): 
		print(arr[i], end=" ") 


# Driver's code 
if __name__ == '__main__': 
	arr = [[2, 6, 12, 34], [1, 9, 20, 1000], [23, 34, 90, 2000]] 
	K = 4
	N = 3
	output = [0 for i in range(N * K)] 

	# Function call 
	mergeKArrays(arr, N, output) 

	print("Merged array is ") 
	printArray(output, N * K) 

# This code is contributed by umadevi9616 

```

#### Complexity

- **Time Complexity:** `O(N * K * log (N*K))`, Since the resulting array is of size N * K.  
- **Space Complexity:** `O(N * K)`, The output array is of size N * K.

### Approach 2: devide and conquer

The process begins with merging arrays into groups of two. After the first merge, there will be K/2 arrays remaining. Again merge arrays in groups, now K/4 arrays will be remaining. This is similar to merge sort. Divide K arrays into two halves containing an equal number of arrays until there are two arrays in a group. This is followed by merging the arrays in a bottom-up manner.

Follow the given steps to solve the problem:

- Create a recursive function that takes K arrays and returns the output array.
- In the recursive function, if the value of K is 1 then return the array else if the value of K is 2 then merge the two arrays in linear time and return the array.
- If the value of K is greater than 2 then divide the group of k elements into two equal halves and recursively call the function, i.e 0 to K/2 array in one recursive function and K/2 to K array in another recursive function.
- Print the output array.

#### Implementation

```cpp
// C++ program to merge K sorted arrays of size n each. 

#include <bits/stdc++.h> 
using namespace std; 
#define N 4 

// Merge arr1[0..N1-1] and arr2[0..N2-1] into 
// arr3[0..N1+N2-1] 
void mergeArrays(int arr1[], int arr2[], int N1, int N2, 
				int arr3[]) 
{ 
	int i = 0, j = 0, k = 0; 

	// Traverse both array 
	while (i < N1 && j < N2) { 
		// Check if current element of first 
		// array is smaller than current element 
		// of second array. If yes, store first 
		// array element and increment first array 
		// index. Otherwise do same with second array 
		if (arr1[i] < arr2[j]) 
			arr3[k++] = arr1[i++]; 
		else
			arr3[k++] = arr2[j++]; 
	} 

	// Store remaining elements of first array 
	while (i < N1) 
		arr3[k++] = arr1[i++]; 

	// Store remaining elements of second array 
	while (j < N2) 
		arr3[k++] = arr2[j++]; 
} 

// A utility function to print array elements 
void printArray(int arr[], int size) 
{ 
	for (int i = 0; i < size; i++) 
		cout << arr[i] << " "; 
} 

// This function takes an array of arrays as an argument and 
// All arrays are assumed to be sorted. It merges them 
// together and prints the final sorted output. 
void mergeKArrays(int arr[][N], int i, int j, int output[]) 
{ 
	// If one array is in range 
	if (i == j) { 
		for (int p = 0; p < N; p++) 
			output[p] = arr[i][p]; 
		return; 
	} 

	// if only two arrays are left them merge them 
	if (j - i == 1) { 
		mergeArrays(arr[i], arr[j], N, N, output); 
		return; 
	} 

	// Output arrays 
	int out1[N * (((i + j) / 2) - i + 1)], 
		out2[N * (j - ((i + j) / 2))]; 

	// Divide the array into halves 
	mergeKArrays(arr, i, (i + j) / 2, out1); 
	mergeKArrays(arr, (i + j) / 2 + 1, j, out2); 

	// Merge the output array 
	mergeArrays(out1, out2, N * (((i + j) / 2) - i + 1), 
				N * (j - ((i + j) / 2)), output); 
} 

// Driver's code 
int main() 
{ 
	// Change N at the top to change number of elements 
	// in an array 
	int arr[][N] = { { 2, 6, 12, 34 }, 
					{ 1, 9, 20, 1000 }, 
					{ 23, 34, 90, 2000 } }; 
	int K = sizeof(arr) / sizeof(arr[0]); 
	int output[N * K]; 
	mergeKArrays(arr, 0, 2, output); 

	// Function call 
	cout << "Merged array is " << endl; 
	printArray(output, N * K); 

	return 0; 
}

```

```python
# Python program to merge K 
# sorted arrays of size n each. 
N = 4

# Merge arr1[0..n1-1] and arr2[0..n2-1] into 
# arr3[0..n1+n2-1] 


def mergeArrays(arr1, arr2, N1, N2, arr3): 

	i, j, k = 0, 0, 0

	# Traverse both array 
	while (i < N1 and j < N2): 

		# Check if current element of first 
		# array is smaller than current element 
		# of second array. If yes, store first 
		# array element and increment first array 
		# index. Otherwise do same with second array 
		if (arr1[i] < arr2[j]): 
			arr3[k] = arr1[i] 
			k += 1
			i += 1
		else: 
			arr3[k] = arr2[j] 
			k += 1
			j += 1

	# Store remaining elements of first array 
	while (i < N1): 
		arr3[k] = arr1[i] 
		k += 1
		i += 1

	# Store remaining elements of second array 
	while (j < N2): 
		arr3[k] = arr2[j] 
		k += 1
		j += 1

# A utility function to print array elements 


def printArray(arr, size): 

	for i in range(size): 
		print(arr[i], end=" ") 

# This function takes an array of arrays 
# as an argument and all arrays are assumed 
# to be sorted. It merges them together 
# and prints the final sorted output. 


def mergeKArrays(arr, i, j, output): 

	global N 

	# If one array is in range 
	if (i == j): 
		for p in range(N): 
			output[p] = arr[i][p] 

		return

	# If only two arrays are left 
	# them merge them 
	if (j - i == 1): 
		mergeArrays(arr[i], arr[j], 
					N, N, output) 
		return

	# Output arrays 
	out1 = [0 for i in range(N * (((i + j) // 2) - i + 1))] 
	out2 = [0 for i in range(N * (j - ((i + j) // 2)))] 

	# Divide the array into halves 
	mergeKArrays(arr, i, (i + j) // 2, out1) 
	mergeKArrays(arr, (i + j) // 2 + 1, j, out2) 

	# Merge the output array 
	mergeArrays(out1, out2, 
				N * (((i + j) / 2) - i + 1), 
				N * (j - ((i + j) / 2)), output) 


# Driver's code 
if __name__ == '__main__': 
	arr = [[2, 6, 12, 34], 
		[1, 9, 20, 1000], 
		[23, 34, 90, 2000]] 

	K = len(arr) 
	output = [0 for i in range(N * K)] 

	# Function call 
	mergeKArrays(arr, 0, 2, output) 

	print("Merged array is ") 
	printArray(output, N * K) 

# This code is contributed by shinjanpatra 

```


#### Complexity

## Case 2: each arrays has random length
