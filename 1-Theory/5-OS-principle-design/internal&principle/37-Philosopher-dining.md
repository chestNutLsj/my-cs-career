## 信号量方案
```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <chrono>
#include <semaphore>

std::array<std::mutex, 5> forks;
std::counting_semaphore<4> room(4);

void think(int philosopherIndex) {
    // Simulate thinking
    std::cout << "Philosopher " << philosopherIndex << " is thinking." << std::endl;
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
}

void eat(int philosopherIndex) {
    // Simulate eating
    std::cout << "Philosopher " << philosopherIndex << " is eating." << std::endl;
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
}

void philosopher(int i) {
    while (true) {
        think(i);
        room.acquire();  // Wait for available seat in the room
        forks[i].lock();  // Wait for left fork
        forks[(i + 1) % 5].lock();  // Wait for right fork
        eat(i);
        forks[(i + 1) % 5].unlock();  // Release right fork
        forks[i].unlock();  // Release left fork
        room.release();  // Leave the room
    }
}

int main() {
    std::array<std::thread, 5> philosopherThreads;

    for (int i = 0; i < 5; ++i) {
        philosopherThreads[i] = std::thread(philosopher, i);
    }

    for (int i = 0; i < 5; ++i) {
        philosopherThreads[i].join();
    }

    return 0;
}

```

## 管程方案
```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <chrono>

class DiningController {
public:
    void getForks(int pid) {
        int left = pid;
        int right = (pid + 1) % 5;

        std::unique_lock<std::mutex> lock(mutex);

        if (!fork[left])
            forkReady[left].wait(lock); // Queue on condition variable

        fork[left] = false;

        if (!fork[right])
            forkReady[right].wait(lock); // Queue on condition variable

        fork[right] = false;
    }

    void releaseForks(int pid) {
        int left = pid;
        int right = (pid + 1) % 5;

        std::unique_lock<std::mutex> lock(mutex);

        if (forkReady[left].empty()) // No one is waiting for this fork
            fork[left] = true;
        else
            forkReady[left].notify_one(); // Awaken a process waiting on this fork

        if (forkReady[right].empty()) // No one is waiting for this fork
            fork[right] = true;
        else
            forkReady[right].notify_one(); // Awaken a process waiting on this fork
    }

private:
    std::mutex mutex;
    std::array<std::condition_variable, 5> forkReady;
    std::array<bool, 5> fork = {true, true, true, true, true};
};

DiningController controller;

void philosopher(int pid) {
    while (true) {
        std::cout << "Philosopher " << pid << " is thinking." << std::endl;
        std::this_thread::sleep_for(std::chrono::milliseconds(100));

        controller.getForks(pid);

        std::cout << "Philosopher " << pid << " is eating." << std::endl;
        std::this_thread::sleep_for(std::chrono::milliseconds(100));

        controller.releaseForks(pid);
    }
}

int main() {
    std::array<std::thread, 5> philosopherThreads;

    for (int i = 0; i < 5; ++i) {
        philosopherThreads[i] = std::thread(philosopher, i);
    }

    for (int i = 0; i < 5; ++i) {
        philosopherThreads[i].join();
    }

    return 0;
}

```