# Advanced-Cpp

## Basic Lambda Function
```
auto greet = []() {
    cout << "Hello from Lambda function!" << endl;
};
greet();
```

```
// Lambda Function with parameters Parameters
auto add = [](int a, int b) {
    return a + b;
};
cout << "Sum is : " << add(5, 3) << endl;
```

```
// Lambdas can "capture" variables from the surrounding scope.
// [=] → Capture all local variables by value.
// [&] → Capture all local variables by reference.
// Capture by Value (=)
int x = 10;
auto printX = [x]() {
    cout << "Print x : " << x << endl;
};
x = 20; // will not affect printX()
printX();
```

```
// Capture by Reference (&)
int x = 10;
auto printX = [&x]() {
    x += 5;
};
printX();
cout << "Print x : " << x << endl;
```

```
// Returning Values from a Lambda
// Implicit Return Type
auto multiply = [](int a, int b) {
    return a * b;  // The compiler infers return type
};
```

```
// Explicit Return Type
auto divide = [](double a, double b) -> double {
    return a / b;
};
```

## Memory Management & Smart Pointers

```
/*
    Why Use Smart Pointers?
    Problems with raw pointers (new and delete):
    
    1. Memory leaks – Forgetting delete causes memory not to be freed.
    2. Dangling pointers – Using a pointer after freeing memory.
    3. Double deletion – Freeing memory twice causes undefined behavior.
    Smart pointers (std::unique_ptr, std::shared_ptr, std::weak_ptr) automatically manage memory and ensure proper cleanup.
*/
```

```
/*
std::unique_ptr – Exclusive Ownership
    1. Only one unique_ptr can own an object at a time.
    2. Automatically deletes memory when it goes out of scope.
    3. Cannot be copied, only moved.
*/
#include <iostream>
#include <memory>  // Required for smart pointers

class Widget {
public:
    Widget() { std::cout << "Widget Created\n"; }
    ~Widget() { std::cout << "Widget Destroyed\n"; }
    void show() { std::cout << "Widget Function\n"; }
};

int main() {
    std::unique_ptr<Widget> ptr1 = std::make_unique<Widget>();  // Creates a Widget
    ptr1->show(); // Using the object

    // std::unique_ptr<Widget> ptr2 = ptr1;  // ❌ Error! Copying not allowed
    std::unique_ptr<Widget> ptr2 = std::move(ptr1);  // ✅ Transfer ownership

    if (!ptr1) std::cout << "ptr1 is now null\n";

    return 0; // Widget gets automatically destroyed
}
```

```
/*
std::shared_ptr – Shared Ownership
    Multiple shared_ptrs can share the same object.
    When the last shared_ptr goes out of scope, the object is deleted.
*/
#include <iostream>
#include <memory>

class Widget {
public:
    Widget() { std::cout << "Widget Created\n"; }
    ~Widget() { std::cout << "Widget Destroyed\n"; }
};

int main() {
    std::shared_ptr<Widget> ptr1 = std::make_shared<Widget>();
    std::shared_ptr<Widget> ptr2 = ptr1;  // Multiple owners

    std::cout << "Use count: " << ptr1.use_count() << std::endl; // 2 owners

    return 0; // Object is deleted when the last reference is gone
}
```

```
/*
std::weak_ptr – Prevent Cyclic References
    std::weak_ptr is a non-owning reference to a shared_ptr object.
    Prevents circular references, which cause memory leaks.
*/
#include <iostream>
#include <memory>

class B;

class A {
public:
    std::shared_ptr<B> b_ptr;
    A() { std::cout << "A Created\n"; }
    ~A() { std::cout << "A Destroyed\n"; }
};

class B {
public:
    std::weak_ptr<A> a_ptr;  // Weak reference
    B() { std::cout << "B Created\n"; }
    ~B() { std::cout << "B Destroyed\n"; }
};

int main() {
    std::shared_ptr<A> a = std::make_shared<A>();
    std::shared_ptr<B> b = std::make_shared<B>();

    a->b_ptr = b;
    b->a_ptr = a;  // Weak pointer prevents memory leak

    return 0; // Objects get deleted correctly
}
```

## RAII (Resource Acquisition Is Initialization)
RAII (Resource Acquisition Is Initialization) is a fundamental C++ concept that ensures proper resource management. It helps prevent memory leaks, dangling pointers, and manual resource management issues.

```
What is RAII?
    The resource (memory, file, socket, mutex, etc.) is acquired during object initialization (in the constructor).
    The resource is automatically released when the object goes out of scope (in the destructor).
Key Idea:
    "Own resources inside objects, and let destructors clean them up automatically."
```

### RAII for Memory Management
Without RAII:
```
void foo() {
    int* ptr = new int(10);  // Allocating memory
    std::cout << *ptr << std::endl;
    delete ptr;  // Forgetting this = MEMORY LEAK!
}
```

With RAII (using std::unique_ptr):

```
void foo() {
    std::unique_ptr<int> ptr = std::make_unique<int>(10);
    std::cout << *ptr << std::endl;  // Memory is automatically freed
}  // No need to call delete
```

### RAII for Mutex Locking (Thread Safety)
Without RAII:
```
#include <mutex>

std::mutex mtx;

void criticalSection() {
    mtx.lock();
    // Code that must be thread-safe
    mtx.unlock();  // Forgetting this = DEADLOCK!
}
```

With RAII (std::lock_guard):
```
void criticalSection() {
    std::lock_guard<std::mutex> lock(mtx);
    // Mutex is automatically unlocked when `lock` goes out of scope
}
// Here, std::lock_guard ensures the mutex is always released, even if an exception occurs.
```

