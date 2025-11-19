*Ensure a class has one instance, and provide a global point of access to it.*

There are times when a class cannot perform correctly if there is more than one instance of it. The common case is when the class interacts with an external system that maintains its own global state.

![[Classic Implementation - Singleton]]
![[Modern Implementation - Singleton (Post C++11)]]

*Difference is due to the fact that modern C++ mandates that the fertilizer for local static variable is only run once, even in the presence of concurrency. So, assuming you’ve got a modern C++ compiler, this code is thread-safe where the first example is not.

*Of course, the thread-safety of singleton class itself is an entirely different question! This just ensures that its initialization is.*
### Advantages:
- **It doesn’t create the instance if no one uses it.** 
	Saves memory and CPU
- **Lazy Initialized** 
	Initialized at runtime. If no need to initialize, no initialization.
- We can subclass the singleton #subclass_of_singleton
	![[Singleton Subclass Example (Filesystem)]]

### Disadvantages
- **It’s a GLOBAL VARIABLE.**
- **Lazy initialization takes control away from you**
		Initialization at runtime is more time consuming. If an instance will be created a `static` will be better in performance critical tasks. 
- **Solves two problems instead of one**
		This pattern gives access to all to the global state which might be risky. 
#### Why Global variables are bad ?
- **They make it harder to reason about code.**
		It is easier to reason about pure functions[^1]. When global state arrive changing it can create domino effect through out the code base. It is aldo harder to track bugs.
- **They encourage coupling.**
		The new coder on your team isn’t familiar with your game’s beautifully maintainable loosely coupled architecture, but he’s just been given his first task: make boulders play sounds when they crash onto the ground. You and I know we don’t want the physics code to be coupled to audio of all things, but he’s just trying to get his task done. Unfortunately for us, the instance of our AudioPlayer is globally visible. So, one little `#include` later, and our new guy has compromised a carefully constructed architecture. Without a global instance of the audio player, even if he did `#include` the header, he still wouldn’t be able to do anything with it. That difficulty sends a clear message to him that those two modules should not know about each other and that he needs to find another way to solve his problem. By controlling access to instances, you control coupling.
- **They aren’t concurrency-friendly.**
		When we make something global, we’ve created a chunk of memory that every thread can see and poke at, whether or not they know what other threads are doing to it. That path leads to deadlocks, race conditions, and other hell-to-fix thread-synchronization bugs.

![[Is Singleton Pattern even Necessary]]

### Conclusion
The question remains, where should we use the real Singleton pattern?

Try to avoid if possible as the project grows it can create Readability and *optimization (only in performance critical code)* problem.

[^1]: Pure Functions: Functions which satisfies two conditions. 
		1. No side effects: It does *not* modify anything outside its own scope.
		2. Deterministic output: Given the _same input_, it always returns the _same output_.
	