*Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.*

Observer pattern. Or, I like to call it sleeping employees pattern
#### Why do I like call it sleeping employees pattern ?
Imagine a number of employee(s) (Observer) who like to sleep. They take order from manager(s) (Subjects). 
Manager comes and notifies about an event and they do the work accordingly and go back to sleep.

They aren't actual observers. They aren't constantly observing a data/global state. So, no busy waiting[^1] i.e. no wasted CPU Cycles.

## How Does it work ?
1. Observers subscribe to one or many subjects.
2. Subject stores data about it's subscribers (not as copies).
3. When something happens with the subject it sends that notification to all the observers.
4. The observers are responsible for the further action based on the notification.

![[Observer Pattern - Classic Example]]

### Advantages
1. **Loose Coupling:** Subject doesn’t need to know concrete observer types.
2. **Dynamic relationships:** Observers can be added or removed at runtime.
3. **Flexibility:** One subject can notify multiple observers in different ways.
4. **Event-driven design:** Efficient for UI, plugins, or reactive systems.
5. **Promotes separation of concerns:** Subjects focus on core logic and observers handle reactions
### Disadvantages
1. **Synchronous blocking[^2]:** slow observers can block subject.
2. **Memory Management:** subjects store pointers to observers; dangling pointers can occur if not careful.
3. **Harder to debug:** many observers reacting independently can create unpredictable behavior.
4. **Potential performance issues:** if notifications are very frequent or observer count is very high.

### Myth 1: "It's Too Slow"
**Reality**: The Observer pattern is actually quite efficient:

- **Simple implementation**: Just walking a list and calling virtual methods
- **Minimal overhead**: No message queuing or dynamic allocation during notifications
- **Synchronous calls**: Direct method invocation without intermediate processing
- **Suitable for most cases**: The performance cost is negligible outside of performance-critical code paths

### Myth 2: "It Does Too Much Dynamic Allocation"
**Reality**: Memory concerns are often overstated:

- **Allocation timing**: Memory allocation only happens when observers are added/removed, not during notifications
- **Controllable**: Can be implemented with fixed arrays or object pools to avoid heap fragmentation
- **No allocation during use**: Sending notifications is just method calls with zero memory overhead
### Important Caveats:
1. **Synchronous Nature**:
    It must wait before all observers RETURN not just get invoked.
    - Observers are called directly and synchronously
    - Slow observers can block the subject
    - Follow UI programming wisdom: "stay off the UI thread" - return quickly or push heavy work elsewhere
2. **Threading Risks**:
    - Potential for deadlocks if observers try to acquire locks the subject holds
    - In highly threaded systems, consider asynchronous Event Queue instead
### Destroying Subjects and Observers
We don't always have to keep observers and subjects. We can create more and destroy them. There will be a huge bug if we are not careful about it.
**Deleting Subject:** It is much simpler to do even in classical example. We can just delete them. Since, the observers don't know about subjects and deleting the subject will just work. Observers might think that they are observers but they never get notified from that subject so nothing happens. 
**Deleting Observer:** It is the problematic part.
	What happens when an observer is deleted ?
	Subject will have reference to the deallocated memory and when subject notifies. Use after free error[^3]. 
#### Does Garbage collector[^4] fix it ?
NO
 **In GC languages, objects are destroyed only when nothing references them.**
But in the Observer pattern:
- The **subject stores references to observers**.
- So if an observer is no longer needed (e.g., a UI screen you closed),  
    **it cannot be collected** as long as the subject still has it in its observer list.
Therefore the observer:
- stays in memory
- keeps receiving notifications
- keeps executing code
- even though the user can’t see it.
This is called the [lapsed listener problem](https://en.wikipedia.org/wiki/Lapsed_listener_problem). This will cause bugs like:
- memory leaks *Yes even in GC languages*
- performance drain
- incorrect behavior

### How to fix it ?
It is not that hard. Hardest part is to remember it. We can send a dying message when destroyed. 
![[Dying Breath approach - Observer Pattern]]
Here, We are removing the pointers when observers and subjects are deleted. Letting the deconstructor do the job is much safer.

### Observers then vs now
The classic Observer pattern was created in the 1990s, during the height of OOP hype. Back then:
- Everything was done with classes and interfaces.
- Inheritance was considered sophisticated.
- Observer naturally became a **class-heavy** pattern.

But modern programming is different:
- Developers now prefer **functions**, **callbacks**, and **closures** over deep class hierarchies.
- Implementing an entire Observer interface just to handle one event feels **rigid**.
- A single observer object with only one `onNotify()` method gets awkward when it needs different handlers for different subjects.

### Why this feels outdated
Modern languages allow you to register **functions** directly:
- JavaScript: event listeners are often just functions.
- C#: events use delegates (references to methods).
- Java: lambdas since JDK 8.
- C++: has lambdas and `std::function`.
### Conclusion
Though it might feel outdated but it is **simple**, **fast** and **it works**. It's more complicated in multithreaded, but what isn't ? 

### *Bob Nystrom, author of the reference book Suggests functional approach in some places but I don't know functional programming but you can learn about this.*

[^1]: **Busy waiting** is when a thread or process repeatedly checks a condition in a tight loop instead of sleeping, wasting CPU cycles while waiting.

[^2]: **Synchronous blocking:** a programming model where a thread waits for a task for a task to complete before moving to next task.

[^3]: **Use after free error:** A bug when program references deallocated memory resulting in undefined behavior. [Use after free article by Kaspersky](https://encyclopedia.kaspersky.com/glossary/use-after-free/). TLDR; It's bad

[^4]: **Garbage Collection:** a form of automatic memory management that reclaims memory occupied by objects that are no longer in use by the program. Languages like Java, Python, JS, C#, Go etc use this model. 