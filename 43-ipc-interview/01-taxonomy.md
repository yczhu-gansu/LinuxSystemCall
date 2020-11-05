# 43.1. A Taxonomy of IPC Facilities
Unix communication and synchronization facilities can be divided into three
broad functional categories:
1. *Communication*: Concerned with exchanging data between processes.
2. *Synchronization*: Concerned with synchronizing the actions of processes or
   threads.
3. *Signals*: Although signals are intended primarily for other purposes, they
   can be used as a synchronization technique: the signal number itself is a form
   of information, and realtime signals can be accompanied by associated data (an
   integer or a pointer).

Although some of these facilities are concerned with synchronization, the
general *interprocess communication* (IPC) is often used to describe them all.

Why several facilities provide similar IPC functionality?
1. Similar facilities evolved on different UNIX variants, and later came to be
   ported to other UNIX systems. 
2. New facilities have been developed to address design deficiencies in similar
   earlia facilities.

