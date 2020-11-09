# 43.4 Comparing IPC Failities

## IPC object identification and handles for open objects
In order to access an IPC object, a process must have some means of identifying
the object, and once the object has been "opened," the process must use some
type of handle to refer to the open object.

Identifiers and handles for various types of IPC facilities

| **Facility type** | **Name used to**<br>**identify object** | **Handle used to refer to**<br>**object in programs** |
| --- | --- | --- |
| Pipe<br>FIFO | no name<br>pathname | file descriptor<br>file descriptor |
| UNIX domain socket<br>Internet domain socket | pathname<br>IP address + port number | file descriptor<br>file descriptor|
|System V message queue<br>System V semaphore<br>System V shared memory | System V IPC key<br>System V IPC key<br>System V IPC key| System V identifier <br>System V identifier <br>System V identifier|
|POSIX message queue<br>POSIX named semaphore<br>POSIX unnamed semaphore<br>POSIX shared memory | POSIX IPC pathname<br>POSIX IPC pathname<br>no name<br>POSIX IPC pathname | mqd\_t(message queue descriptor) <br>sem\_t \*(semaphore pointer)<br>sem\_t \*(semaphore pointer)<br>file descriptor|
| Anonymous mapping<br>Memory-mapped file | no name<br>pathname | none<br>file descriptor |
| flock() lock<br>fcntl() lock| pathname<br>pathname | file descriptor<br>file descriptor |

## Functionality
The differences between data-transfer facilities and shared memory:
1. Data-transfer facilities involve read and write operations, with transferred
   data being consumable by just one reader process. Flow control between writer
   and reader, as well as synchronization (so that a reader is blocked when
   trying to read data from a facility that is currently empty) is automatically
   handled by the kernel. This model fits well with many application designs.
2. Shared memory allows one process to make data visible to any number of other
   processes sharing the same memory region. Communication "operations" are
   simple -- a process can access data in shared memory in the same manner as it
   accesses any other memory in its virtual address space. On the other hand,
   the need to handle synchronization (and perhaps flow control) can add to the
   complexity of a shared-memory design. This model fits well with application
   designs that need to maintain shared state (e.g., a shared data structure).

With respect to the various data-transfer facilities, the following points are
worth noting:
1. Some data-transfer facilities transfer data as a byte stream (pipes, FIFOs,
   and stream sockets); others are message-oriented (message queues and datagram
   sockets).
2. A distinctive feature of System V and POSIX message queues, compared with
   other data-transfer facilities, is the ability to assign a numeric type or
   priority to a message, so that messages can be delivered in a different order
   from that in which they were sent.
3. Pipes, FIFOs, and sockets are implemented using file descriptors. These IPC
   facilities all support a range of alternative I/O models: I/O multiplexing
   (the *select()* and *poll()* system calls), signal-driven I/O, and the
   Linux-specific *epoll* API. The primary benefit of these techniques is that
   they allow an application to simultaneously monitor multiple file descriptors
   to see whether I/O is possible on any of them. By contrast, System V message
   queues don't employ file descriptors and don't support these techniques.
>On Linux, POSIX message queues are also implemented using file descriptors and
>support the alternative I/O techniques described above. However, this behavior
>is not specified in SUSv3.
4. POSIX message queues provide a notification facility that can send a signal
   to a process, or instantiate a new thread, when a message arives on a
   previously empty queue.
5. UNIX domain sockets provide a feature that allows a file descriptor to be
   passed from one process to another. This allows one process to open a file
   and make it available to another process that otherwise might not be able to
   access the file.
6. UDP (Internet domain datagram) sockets allow a sender to broadcast or
   multicast a message to multiple recipients.

With respect to process-synchronization facilities, the following points are
worth noting:
1. Record locks placed using *fcntl()* are considered to be owned by the process
   placing the lock. The kernel uses this ownership property to detect deadlocks
   (situations where two or more processes are holding locks that block each
   other's further lock requests). If a deadlock situation occurs, the kernel
   denies the lock request of one of the processes, returning an error from the
   *fcntl()* call to indicate that a deadlock occured. System V and POSIX
   semaphores don't have an ownership property; no deadlock detection occurs for
   semaphores.
2. Record locks placed using *fcntl()* are automatically released when the
   process that owns the locks termiantes. System V semaphores provide a similar
   feature in the form of an "undo" feature, but this feature is not reliable in
   all circumstances. POSIX semaphores don't provide an analog of this feature.

## Network communication
Of all the IPC methods, only sockets permit processes to communicate over a
network. Sockets are generally used in one of two domains: the UNIX domain,
which allows communication between processes on the same system, and the
Internet domain, which allows communication between processes on different hosts
connected via a TCP/IP network.

## Portability
The POSIX IPC facilities (message queues, semaphores, and shared memory) are not
quite as widely available as their System V IPC counterparts, especially on
older UNIX systems. (An implementation of POSIX message queues and full support
for POSIX semaphores have appeared on Linux only in the 2.6.x kernel series).
Therefore, from a portability point of view, System IPC may be preferable to
POSIX IPC.

## System V IPC design issues
The System V IPC facilities were designed independently of the traditional UNIX
I/O model, so their programming interfaces complicated to use. The corresponding
POSIX IPC facilities were designed to address these problems:
1. The System V IPC facilities are connectionless. Namely no notion of handle
   (like a file descriptor) refering to an open IPC object. So:
   * The kernel does not record the process as having "opened" the object
     (unlike other types of IPC objects). 
   * The kernel can't maintain a reference count of the number of processes that
2. Traditional UNIX I/O model integer key values and IPC identifiers instead of
   pathnames and file descriptors (used for System V IPC facilities?). The
   programming interfaces are also overly complex.

The kernel counts open references for POSIX IPC objects. This simplifies
desicions about when an object can be deleted.

POSIX facilities provide an interface that is simpler and more consistant with
the traditional UNIX model.

## Accessiblity
The permissions scheme that governs which process can access the object.
1. Determined according to the associated file permissions mask. (FIFOs and
   sockets, object names live in the file systems)
   Although System V IPC objects have an associated permissions mask whose
   semantics are similar to those for files.
2. Accessible only by related (related via *fork()*) processes. One process
   create the object and then call *fork()*, the child process inherits a handle
   referring to the object, allowing both process to share the object.
3. By the accessibility of the shared memory region containing the semaphore.
   (POSIX unnamed semaphore)
4. Must have permission to open the file (place a lock on a file).
5. There are no restrictions on accessing an Internet domain socket.

Table. Accessiblity and persistence for various types of IPC facilities
| **Facility type** | **Accessiblity** | **Persistence** |
| --- | --- | --- |
|Pipe<br>FIFO | Only by related processes<br>permissions mask | process<br>process |
|UNIX domain socket<br>Internet domain socket| permissions mask<br>by any process| process<br>process |
|System V message queue<br>System V semaphore<br>System V shared memory |permissions mask<br>permissions mask<br>permissions mask | kernel<br>kernel<br>kernel|
|POSIX message queue<br>POSIX named semaphore<br>POSIX unnamed semaphore<br>POSIX shared memory |permissions mask<br>permissions mask<br>permissions of underlying memory<br>permissions mask | kernel<br>kernel<br>depends<br>kernel|
|Anonymous mapping<br>Memory-mapped file| Only by related processes<br>permissions mask | process<br>file system|
|flock() file lock<br>fcntl() file lock| open() of file<br>open() of file| process<br>process |
