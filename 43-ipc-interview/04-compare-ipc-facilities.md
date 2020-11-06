# 43.4 Comparing IPC Failities

## IPC object identification and handles for open objects
In order to access an IPC object, a process must have some means of identifying
the object, and once the object has been "opened," the process must use some
type of handle to refer to the open object.

Identifiers and handles for various types of IPC facilities

| **Facility type** | **Name used to identify object** | **Handle used to refer to object in programs** |
| --- | --- | --- |
| Pipe/FIFO | no name/pathname | file descriptor/file descriptor |
| UNIX domain socket/Internet domain socket | pathname/IP address + port number | file descriptor/file descriptor|
