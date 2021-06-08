# Delphi-Tasks
 Small and simple: Thread Pools with Tasks

I needed some better constructs than what was available in Delphi 2009, to be more productive with one of my major programs (this runs as a critical service 7x24, with hundreds of threads, but also short-living parallel activities to manage timeouts and some monitoring).
I felt that I needed some better program constructs than Delphi's TThread class, a better way of handling threads as also a built-in and safe way to wait for completion of started tasks.
As for keeping the implementation as small and fast as possible, this is relying on pre-existing Windows constructs all the way (Slim RW Locks, Condition Variables, Events).

### Kind of objects:

* ITask: Reference to an action passed to a thread pool for asynchronous execution.

* ICancel: Reference to an object that serves as an cancellation flag.

* TThreadPool: Implements a configurable thread pool and provides a default thread pool. You can create any number of thread pools.

* TGuiThread: Allows any thread to inject calls into the GUI thread.


### Implemention concept:

The heart of each thread pool is a thread-safe queue for task objects. The application adds tasks to the queue. Threads are created automatically to drain the queue. Idle threads terminate after a configurable timeout. There are parameters to control the three main aspects of the model: Maximum number of threads, Maximum idle time per thread, Maximum queue length.

To enable non-GUI threads to delegate calls to the GUI thread, a Windows messaage hook is used. This has the advantage that the processing is not blocked by non-Delphi modal message loops, neither by the standard Windows message box nor by moving or resizing a window.

There is *no* heuristic to "tune" the thread pool(s): It is up to the application to perform "correct" threading for its use-case. If your tasks are CPU-bound, then put them all in a specfic thread pool, sized to run only as much threads in parallel as desired. If your tasks are I/O-bound (like print spooling or network communication, for example), just use the default thread pool.

Also note that Windows only schedules threads within a single, static group of CPU cores, assigned to the process at process startup. (https://docs.microsoft.com/en-us/windows/win32/procthread/processor-groups)


Tested with:
- Delphi 2009
- Delphi 10.1.2 Berlin: 32bit and 64bit
