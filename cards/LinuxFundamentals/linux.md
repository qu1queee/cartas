Q: What is a process in Linux?
A: An instance of a running program with its own PID, memory space, file descriptors, and execution state. Every process (except PID 1) has a parent process.

---

Q: What is PID 1 and why is it special?
A: The first process started by the kernel after boot (traditionally `init`, now usually `systemd`). It is the ancestor of all other processes and is responsible for reaping orphaned child processes.

---

Q: What are the standard file permission bits in Linux?
A: Three sets of read (r=4), write (w=2), execute (x=1) bits for owner, group, and others — represented as octal (e.g., 755 = rwxr-xr-x).

---

Q: What does the sticky bit do on a directory?
A: Only the file owner, directory owner, or root can delete or rename files within that directory — even if others have write permission. Used on `/tmp`.

---

Q: What is the difference between a hard link and a symbolic link?
A: A hard link is a second directory entry pointing to the same inode — deleting one does not remove the data. A symbolic link is a file that contains a path to another file — it breaks if the target is removed.

---

Q: What are Linux signals and how are they used?
A: Asynchronous notifications sent to processes. Common signals: SIGTERM (graceful shutdown request), SIGKILL (immediate kill, cannot be caught), SIGHUP (reload config), SIGINT (Ctrl+C interrupt).

---

Q: What is the difference between SIGTERM and SIGKILL?
A: SIGTERM can be caught, handled, or ignored by the process — allowing graceful cleanup. SIGKILL is sent directly by the kernel and cannot be caught, blocked, or ignored.

---

Q: What is systemd?
A: The init system and service manager used by most modern Linux distributions. It manages service units, handles dependencies between services, and replaces SysV init scripts.

---

Q: What is a systemd unit file?
A: A configuration file (e.g., `myapp.service`) that describes how systemd should start, stop, and monitor a service — including its binary, environment, restart policy, and dependencies.

---

Q: What does `lsof` do?
A: Lists open files — including regular files, sockets, pipes, and devices — for running processes. Used to find which process holds a port or file handle open.

---

Q: What is the `/proc` filesystem?
A: A virtual filesystem that exposes kernel and process information as files — e.g., `/proc/<pid>/status` for process state, `/proc/net/tcp` for open TCP connections, `/proc/cpuinfo` for CPU details.

---

Q: What is the difference between `ps aux` and `top`?
A: `ps aux` is a static snapshot of running processes at the moment it is run. `top` is an interactive, continuously refreshed view of processes sorted by resource usage.

---

Q: What are inodes in Linux?
A: Data structures that store metadata about a file (permissions, owner, size, timestamps, data block pointers) — everything except the filename, which lives in the directory entry.

---

Q: What is `strace` used for?
A: Traces system calls made by a process in real time — essential for debugging why a program is failing, hanging, or behaving unexpectedly without access to source code.

---

Q: What is the difference between a pipe `|` and a redirect `>`?
A: A pipe connects the stdout of one process to the stdin of another (inter-process). A redirect connects stdout (or stderr) to a file — `>` overwrites, `>>` appends.
