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
