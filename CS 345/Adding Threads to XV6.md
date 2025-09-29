# Steps
1. Create a lower primitive than a process
	- Have a thread id
	- make `sched` operate on threads rather than processes
2. Make process operate in terms of a thread
	- This means giving a process that has a main thread
	- If the main thread exits, then it should kill the process
3. Add a system call that creates a thread
4. Make it so that all threads die is the main thread exits