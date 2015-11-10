## Parallel R

* Can R be parallelized?


#### R Memory and Structure

* Memory is in the form of a **heap** and a **stack**.
* A heap is reserved memory that can be dynamically allocated (e.g., size of array determined at runtime).
* A stack is a fixed space of statically allocated memory (e.g., size of array fixed to a certain size at compiletime).
* Memory in R is allocated onto the heap.
* In Linux, [everything is a file](https://en.wikipedia.org/wiki/Everything_is_a_file).  In R, everything is an [S-expression](https://en.wikipedia.org/wiki/S-expression) (see ["Expressions" chapter of Advanced R](http://adv-r.had.co.nz/Expressions.html)).
* R expressions are a series of **promises** which are not evaluated until they are explicitly forced to be evaluated (**lazy evaluation**).  Promises are stored on the stack as a **call stack**.  R has full access to the call stack through `substitute`, `sys.calls`, `sys.frames`, etc.  Evaluating a promise moves it from the stack to the heap.
* R uses **environments** for storing information, determining namespaces, allowing mutable data structures, etc.


#### Problems for Parallelization

* Environments are difficult to parallelize because they have an unstructured pointer topology.  We kinda rushed through this one.
