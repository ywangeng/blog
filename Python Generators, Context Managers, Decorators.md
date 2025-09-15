## Python Generators, Context Managers, and Decorators — How They Connect

Python has many features that make it both powerful and elegant. Among them, three stand out for their ability to 
simplify complex programming patterns: **generators**, **context managers**, and **decorators**. At first glance, these might seem 
unrelated — one produces sequences, another manages resources, and the third modifies functions. But in fact, they are 
deeply connected.

### 1. Generators: Lazy Iteration
A **generator** is a special function that produces values one at a time using the yield keyword. Unlike lists, which 
generate all values at once and store them in memory, generators are **lazy**: they only produce the next value when asked.

```python
def countdown(n):
    while n > 0:
        yield n
        n -= 1

for num in countdown(5):
    print(num)

```
#### ✅ Key benefits
- Save memory (no need to store all results).
- Useful for infinite or very large sequences.
- Enable streaming-style computation.

### 2. Context Managers: Setup and Cleanup
A **context manager** is a construct that ensures resources are properly acquired and released. We use them with the 
with statement:
```python
with open("data.txt", "r") as f:
    content = f.read()
```
Here:
- The file is opened at the start.
- The block of code runs.
- The file is closed automatically, even if an error occurs.

Technically, a context manager is any object that implements:   \
```__enter__()``` — what happens at the start  \
```__exit__()``` — what happens at the end

### 3. Decorators: Wrapping Functions
A **decorator** is a function that takes another function and returns a new one, adding extra behavior without changing 
the original code.
```python
def my_decorator(func):
    def wrapper():
        print("Before")
        func()
        print("After")
    return wrapper

@my_decorator
def greet():
    print("Hello")

greet()
```

Output:
```python
Before
Hello
After
```

#### ✅ Use cases
- Logging
- Timing
- Authentication
- Caching

### 4. How They Connect
Here’s the interesting part: these three concepts can be combined.

#### A. Context Managers from Generators
Python’s ```contextlib``` module allows we to build context managers using generators:
```python
from contextlib import contextmanager

@contextmanager
def managed_file(name):
    f = open(name, "w")
    try:
        yield f
    finally:
        f.close()

with managed_file("test.txt") as f:
    f.write("Hello!")
```
Here:
- The ```@contextmanager``` decorator transforms a generator function into a context manager.
- Inside, yield hands control back to the block of code.
- When the block finishes, cleanup runs automatically.  \

👉 This single example uses all three: a generator (yield), a context manager (with), and a decorator (@contextmanager).

#### B. Decorators Using Context Managers
Decorators can also take advantage of context managers to manage resources during a function call:

```python
import time
from contextlib import contextmanager

@contextmanager
def timer_block(name):
    start = time.time()
    yield
    end = time.time()
    print(f"{name} took {end:.2f}s")

def timing_decorator(func):
    def wrapper(*args, **kwargs):
        with timer_block(func.__name__):
            return func(*args, **kwargs)
    return wrapper

@timing_decorator
def work():
    time.sleep(1)

work()

```

Output:
```python
work took 1.00s
```
Here, the decorator wraps the function, and inside it a context manager measures execution time.

### 5. Conclusion
- Generators handle lazy iteration.
- Context managers handle setup and cleanup.
- Decorators handle function wrapping.

Each concept solves a different problem, but they share a common philosophy: **wrapping behavior around code in a clean, 
reusable way**. And when combined, they enable developers to write expressive, safe, and highly reusable Python code.
