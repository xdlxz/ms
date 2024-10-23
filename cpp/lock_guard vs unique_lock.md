# `unique_lock` 和 `lock_guard` 的使用场景区别

`std::unique_lock` 和 `std::lock_guard` 都是 C++ 提供的用于管理互斥锁的 RAII 工具，确保在加锁的同时可以自动解锁。它们的功能和灵活性不同，适用于不同的场景。

## 1. `std::lock_guard` 的使用场景

`std::lock_guard` 是一个简单且高效的锁管理工具。它在构造时自动加锁，析构时自动解锁，无法手动控制锁的解锁与重新加锁。这使得它特别适用于简单的加锁和解锁场景。

### 使用场景

- **简单的加锁和解锁**：适合需要在一个作用域中加锁并自动解锁的操作。
- **高效轻量**：它的实现非常轻量，适合性能敏感的地方。

### 示例

```cpp
#include <iostream>
#include <mutex>

std::mutex mtx;

void safe_function() {
    std::lock_guard<std::mutex> guard(mtx);  // 自动加锁
    std::cout << "Thread-safe operation" << std::endl;
    // 离开作用域时自动解锁
}
```

## 2. std::unique_lock 的使用场景

std::unique_lock 提供了更大的灵活性，允许对锁的手动控制。它可以延迟加锁、提前解锁、重新加锁，甚至支持尝试加锁（try_lock）。此外，std::unique_lock 常用于与条件变量（std::condition_variable）结合的场景。

使用场景
	- **需要灵活的锁控制**：适合需要在函数中手动控制加锁、解锁和重新加锁的场景。
	- **条件变量配合**：std::unique_lock 可以与条件变量一起使用，便于在等待时释放锁。
	- **尝试加锁**：可以使用 try_lock() 函数来尝试加锁，而不是阻塞等待。

```cpp
#include <iostream>
#include <mutex>

std::mutex mtx;

void flexible_function() {
    std::unique_lock<std::mutex> lock(mtx, std::defer_lock);  // 延迟加锁
    // 手动加锁
    lock.lock();
    std::cout << "Thread-safe operation" << std::endl;
    lock.unlock();  // 手动解锁
}

```
## 3.区别总结
## 3. 区别总结

| **特性**               | **`std::lock_guard`**                               | **`std::unique_lock`**                             |
|------------------------|-----------------------------------------------------|----------------------------------------------------|
| **锁的灵活控制**       | 不支持手动控制加锁和解锁                            | 支持手动加锁、解锁、延迟加锁、尝试加锁等操作        |
| **性能**               | 轻量，高效                                          | 功能更强大，但性能略低                             |
| **适用场景**           | 简单的加锁和解锁场景                                | 需要灵活锁管理，或需要与条件变量配合使用的复杂场景  |
| **与条件变量配合**     | 不支持                                               | 支持，与 `std::condition_variable` 配合使用         |
| **尝试加锁**           | 不支持                                               | 支持 `try_lock`                                     |

## 4. 总结
std::lock_guard：适合简单的加锁和解锁场景，轻量高效。
std::unique_lock：适合需要灵活锁控制的场景，特别是在需要手动加锁、解锁或条件变量配合时使用
