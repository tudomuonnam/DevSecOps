## Start script
#!/bin/bash
## Structure script and write logfile

Depending on the quantity of the logging information you're expecting to produce, it might be worth using the standard logger tool to write it to the user syslog in /var/log:

1 */8 * * * /path/to/myprog 2>&1 | logger -p user.debug -t 'myprog'

Here is an example of the output written to /var/log/debug on my Debian-based system:

Jul 31 00:17:09 myserver myprog: test message with user.debug

There are various facility/level pairs available for use. You might want to consider user.notice or user.info or user.debug. Just be aware that some of these may also get written to /var/log/messages and /var/log/syslog.

If you want to differentiate stdout and stderr in your cron job, sending only stderr to the logger, you can use a construct like this, which I'm sure others will improve upon:

1 */8 * * * ( /path/to/myprog 2>&1 1>&3 | logger -p user.debug -t 'myprog' ) 3>&1

## Source
[stackoverflow](https://unix.stackexchange.com/questions/219191/log-crontab-shell-script-errors-with-error-occurred-time)
