

This fuser 8080/tcp will print you PID of process bound on that port.

And this fuser -k 8080/tcp will kill that process.

Works on Linux only. More universal is use of lsof -i4 (or 6 for IPv6).

Source:

[https://stackoverflow.com/questions/11583562/how-to-kill-a-process-running-on-particular-port-in-linux](Stackoverflow source)
