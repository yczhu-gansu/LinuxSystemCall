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
