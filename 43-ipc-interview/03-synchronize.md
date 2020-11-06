# 43.3 Synchronization Facilities

The synchronization facilities allow processes to coordinate their actions.
Synchronization allows processes to avoid doing things such as simultaneously
updating a shared memory region or the same part of a file.

UNIX systems provide the following synchronization facilities:
1. *Semaphores*: A semaphore is a kernel-maintained integer whose value is never
   permitted to fall below 0. A process can decrease or increase the value of a
   semaphore. If an attempt is made to decrease the value of the semaphore below
   0, the the kernel blocks the operation until the semaphore's value increase
   to a level that permits the operation to be performed. (Alternatively, the
   process can request a nonblocking operation; then, instead of blocking, the
   kernel causes the operation to return immediately with an error indicating
   that the operation can't be performed immediately.) The meaning of a
   semaphore is determined by the application. A process decrements a semaphore
   (from, say 1 to 0) in order to reserve exclusive access to some shared
   resource, and after completing work on the resource, increments the semaphore
   so that the shared resource is released for use by some other process. The
   use of a binary semaphore - a semaphore whose value is limited to 0 or 1 - is
   common. However, an application that deals with multiple instances of a
   shared resource would employ a semaphore whos maximum values equals the
   number of shared resources. Linux provides both System V semaphores and POSIX
   semaphores, which have essentially similar functionality.
2. *File locks*: Are a synchronization method explicitly designed to coordinate
   the actions of multiple processes operating on the same file. They can also
   be used to coordinate access to other shared resources. File locks come into
   two flavors: read (shared) locks and write (execlusive) locks. Linux provides
   file-locking facilities via the *flock()* and *fcntl()* system calls.
3. *Mutexes and condition variables*: These synchronization facilities are
   normally used with POSIX threads.
>Some UNIX implementations, including Linux systems with glibc that provides the
>NPTL threading implementation, also allow mutexes and condition variables to be
>shared between processes.
