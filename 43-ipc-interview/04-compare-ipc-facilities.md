# 43.4 Comparing IPC Failities

## IPC object identification and handles for open objects
In order to access an IPC object, a process must have some means of identifying
the object, and once the object has been "opened," the process must use some
type of handle to refer to the open object.

Identifiers and handles for various types of IPC facilities

| **Facility type** | **Name used to identify object** | **Handle used to refer to object in programs** |
| --- | --- | --- |
| Pipe<br />FIFO | no name<br />pathname | file descriptor<br/>file descriptor |
| UNIX domain socket<br />Internet domain socket | pathname<br />IP address + port number | file descriptor<br />file descriptor|
|System V message queue<br />System V semaphore<br />System V shared memory | System V IPC key<br />System V IPC key<br />System V IPC key<br /> | System V identifier <br />System V identifier <br />System V identifier|
