```cpp
#include <iostream>
#include <vector>
#include <array>
#include <algorithm>

constexpr int n = /* Number of processes */;
constexpr int m = /* Number of resources */;

struct State {
    std::array<int, m> resource;
    std::array<int, m> available;
    std::array<std::array<int, m>, n> claim;
    std::array<std::array<int, m>, n> alloc;
};

// Function to check if a process can be safely allocated
bool isSafe(const State& s) {
    std::array<int, m> currentAvail = s.available;
    std::vector<int> rest(n);
    for (int i = 0; i < n; ++i) {
        rest[i] = i;
    }

    bool possible = true;
    while (possible) {
        int foundIndex = -1;
        for (int i = 0; i < rest.size(); ++i) {
            int processIndex = rest[i];
            bool canExecute = true;
            for (int j = 0; j < m; ++j) {
                if (s.claim[processIndex][j] - s.alloc[processIndex][j] > currentAvail[j]) {
                    canExecute = false;
                    break;
                }
            }
            if (canExecute) {
                foundIndex = i;
                break;
            }
        }
        
        if (foundIndex != -1) {
            int processIndex = rest[foundIndex];
            for (int j = 0; j < m; ++j) {
                currentAvail[j] += s.alloc[processIndex][j];
            }
            rest.erase(rest.begin() + foundIndex);
        } else {
            possible = false;
        }
    }

    return rest.empty();
}

// Function to simulate resource allocation
bool allocateResources(State& s, int processIndex, const std::array<int, m>& request) {
    for (int j = 0; j < m; ++j) {
        if (s.alloc[processIndex][j] + request[j] > s.claim[processIndex][j]) {
            std::cout << "Error: Total request exceeds claim" << std::endl;
            return false;
        } else if (request[j] > s.available[j]) {
            std::cout << "Suspend process" << std::endl;
            return false;
        }
    }

    // Simulate allocation
    for (int j = 0; j < m; ++j) {
        s.alloc[processIndex][j] += request[j];
        s.available[j] -= request[j];
    }

    State newState = s;
    if (isSafe (newState)) {
        s = newState;
        return true;
    } else {
        // Restore original state
        for (int j = 0; j < m; ++j) {
            s.alloc[processIndex][j] -= request[j];
            s.available[j] += request[j];
        }
        std:: cout << "Suspend process" << std:: endl;
        return false;
    }
}

int main () {
    State state;
    // Initialize state's data members here

    int processIndex = /* Index of the requesting process */;
    std::array<int, m> request = /* Requested resources */;

    if (allocateResources (state, processIndex, request)) {
        std:: cout << "Resources allocated successfully" << std:: endl;
    }

    return 0;
}

```