nginx出现问题的解决办法
==

	最近遇到Permission denied这个问题，经过排查，是目录权限的问题，server的root目录必须要有写权限才行，只有读权限还是会出现Permission denied的问题，并且要给nginx的执行用户，比如默认的www-data用户。
	待续

