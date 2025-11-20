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

![[Observer Pattern - Example]]

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

[^1]: **Busy waiting** is when a thread or process repeatedly checks a condition in a tight loop instead of sleeping, wasting CPU cycles while waiting.

[^2]: **Synchronous blocking:** a programming model where a thread waits for a task for a task to complete before moving to next task.
