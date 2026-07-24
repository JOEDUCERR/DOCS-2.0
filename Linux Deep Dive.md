Linux Deep Dive

## Process
* Process: running instance of a program. Any command that will start a new task.

* command: 'ps' or 'Top'. -A or -e shows all processes. -r shows only running processes. H shows threads as if they were processes. -f shows full information. 'ps <ID>' shows the single process info. -e shows extended info.
* in 'ps' output is static and not in realtime unlike top which gives realtime output.
* >>>ps -eaf | head (helps us see the init process with PID1)
* 'PID1' is known as 'init' process. It is the parent process to all other child processes. It cannot be killed manually.
* Child processes can be found in the /proc file system.
* Foreground process: are which depend on input from users. E.g. command prompt running a command which cannot be overruled by any other process in the terminal.
* Background process: are which run independent of any input. It can be made by adding '&' to any program or command. E.d. pwd &
* you can check process state using 'ps aux'. can be R, T, Z, S, D. In aux, a: used to display all running process, u: used to display user/owner and x: used to display all processes without terminals.

* To change priority of a process we use 'nice' and 'renice' command. 'nice' starts a new process and assigns it a priority. 'renice' changes the priority already running.
* command 'kill -9 <PID>' kills a process which is not responding to the normal kill command.
* 'bg <job>' resumes suspended jobs in the background.
* 'fg <JID>' continues a stopped job in the foreground.
* zombie: when a child process has finished execution but its exit status is not read by its parent yet.
* orphan: if the parent process if terminated before child completes process.
* daemon: background system process running continuously. Automatically run with root privileges.

## Systemd

* Coming after the POST when switching on the PC, it manages system resources and daemon handling components called 'units'. It also maintains journal log system. It is responsible for running the basic process with PID1.
* during startup it checks for hardware changes, mounts disks, launches bg services, handles login and loads up desktop.
* located in /etc/systemd/system or /run/systemd/system (has runtime system units) or /lib/systemd/system (has service files with least priority)
* systemctl: start, enable, disable, status, etc.
* systemctl list-units --type service --all (shows all services with state and desc).

## Cron

* time-based job scheduler to schedule and run daemon processes automatically at predefined times without intervention. Basically used to automate repetitive tasks.
* cron keeps checking constantly the time of the system and its own time fields and runs commands defined when it reaches specified time.
* commands or scripts are specified at crontab (cron table) where users can add, modify or remove tasks.
* you can use 'crontab' directly to edit cronjobs. -e edit, -l view current crons and -r to remove all cronjobs.
* location of crontab /var/spool/cron/crontabs/joeducer

## rsync

* Remote sync for file sharing and syncing between two different machines. This can happen over ssh or through rsync daemon using TCP.
* It's specaility is that it only copies the difference between already synced files.
* rsync local-file user@remote-host:remote-file

## tar

* used to manage archived files.
* extract: tar -xvf file.tar
* create a tar: tar -cvf file.tar*.c
* -c creates a new archive, -f specifies filename of archive
