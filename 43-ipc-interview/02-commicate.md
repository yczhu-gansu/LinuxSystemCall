# 43.2 Communication Facilities
Allow processes to exchange data with one another. (These facilities can also be
used to exchange data between the threads of a single process, but this is
seldom necessary, since threads can exchange information via shared global
variables.)

Break the communication facilities into two categories:
1. *Data-transfer facilities*: The key factor distinguishing these facilities is
   the notio of writing and reading. In order to communicate, one process writes
   data to the IPC facility, and another process reads the data. These facilities
   require two data transfers between user memory and kernel memory: one
   transfer from user memory to kernel memory during writing, and another
   transfer from the kernel memory to user memory during reading.
2. *Shared memory*: Allows processes to exchange information by placing it in a
   region of memory that is shared between the processes. (The kernel
   accomplishes this by making page-table entries in each process point to the
   same pages of RAM.) A process can make data available to other processes by
   placing it in the shared memory region.

## Data transfer
Further break data-transfer facilities into the following subcategories:
1. *Byte stream*: The data exchanged via pipes, FIFOs, and datagram sockets is
   an undelimited byte stream. Each read operation may read an arbitrary number
   of bytes from the IPC facility, regardless of the size of blocks written by
   the writer. This model mirrors the traditional UNIX "file as a sequence of
   bytes" model.
2. *Message*: The data exchanged via System V message queues, POSIX message
   queues, and datagram sockets takes the form of delimited messages. Each read
   operation reads a whole message, as written by the writter process. It is not
   possible to read part of a message, leaving the remainder on the IPC
   facility; nor is it possible to read multiple messages in a single read
   operation.
3. *Pseudoterminals*: A pseudoterminal is a communication facility intended for
   use in specialized situations.

A few general features distinguish data-transfer facilities from shared memory:
1. Although a data-transfer facility may have multiple readers, reads are
   destructive. A read operation consumes data, and that data is not available
   to another othr process.
>The MSG\_PEEK flag can be used to perform a nondestructive read from a socket.
>UDP (Internet domain datagram) sockets allow a single message to be broadcase
>or multicast to multiple recipients.
2. Synchronization between the reader and writer processes is automatic. If a
   reader attempts to fetch data from a data-transfer facility that currently
   has no data, then (by default) the read operation will block until some
   process writes data to the facility.

## Shared memory
Most modern UNIX systems provide three flavors of shared memory:
* System V shared memory;
* POSIX shared memory;
* memory mapping.

Note the following general points about shared memory:
1. Although shared memory provides fast communication, this speed advantage is
   offset by the need to synchronize operations on the shared memory. A
   semaphore is the usual synchronization method used with shared memory.
2. Data placed in shared memory is visible to all of the processes that share
   that memory.
