## 基本逻辑
```cpp
#include <iostream>
#include <cmath>
#include <vector>

class BuddySystem {
private:
    int memorySize;
    int minBlockSize;
    int maxLevels;
    std::vector<std::vector<int>> freeList;

public:
    BuddySystem(int size, int minBlockSize) : memorySize(size), minBlockSize(minBlockSize) {
        maxLevels = log2(memorySize) - log2(minBlockSize) + 1;
        freeList.resize(maxLevels);

        for (int i = 0; i < maxLevels; ++i) {
            int blockSize = minBlockSize * (1 << i);
            int numBlocks = memorySize / blockSize;
            freeList[i].resize(numBlocks, 1);  // 1 represents free block
        }
    }

    int allocate(int size) {
        if (size > memorySize) {
            std::cout << "Requested size exceeds memory capacity." << std::endl;
            return -1;
        }

        int level = log2(size);
        for (int i = level; i < maxLevels; ++i) {
            if (!freeList[i].empty()) {
                int blockIndex = freeList[i].size() - 1;
                freeList[i][blockIndex] = 0;  // 0 represents allocated block
                return blockIndex * minBlockSize * (1 << i);
            }
        }

        std::cout << "No suitable block found for allocation." << std::endl;
        return -1;
    }

    void deallocate(int address) {
        int level = -1;
        int blockSize = minBlockSize;

        // Find the level of the block based on its address
        for (int i = 0; i < maxLevels; ++i) {
            if (address % blockSize == 0) {
                level = i;
                break;
            }
            blockSize *= 2;
        }

        if (level == -1 || level >= maxLevels) {
            std::cout << "Invalid address for deallocation." << std::endl;
            return;
        }

        int blockIndex = address / (minBlockSize * (1 << level));
        freeList[level][blockIndex] = 1;  // Mark block as free

        // Merge buddies if possible
        while (level < maxLevels - 1 && freeList[level][blockIndex] && freeList[level][blockIndex ^ 1]) {
            blockIndex /= 2;
            ++level;
            freeList[level][blockIndex] = 1;  // Mark merged block as free
        }
    }

    void printFreeList () {
        for (int i = 0; i < maxLevels; ++i) {
            std:: cout << "Level " << i << ": ";
            for (int j = 0; j < freeList[i]. size (); ++j) {
                std:: cout << freeList[i][j] << " ";
            }
            std:: cout << std:: endl;
        }
    }
};

int main () {
    int memorySize = 1024;  // Total memory size in bytes
    int minBlockSize = 16;  // Minimum block size in bytes

    BuddySystem buddy (memorySize, minBlockSize);

    int allocatedBlock = buddy.allocate (64);
    if (allocatedBlock != -1) {
        std:: cout << "Allocated block at address: " << allocatedBlock << std:: endl;
    }

    buddy.printFreeList ();

    buddy.deallocate (allocatedBlock);

    buddy.printFreeList ();

    return 0;
}

```