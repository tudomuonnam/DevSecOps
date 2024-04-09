

Your .service file should look like this:
```vi
[Unit]
Description=Spark service

[Service]
ExecStart=/path/to/spark/sbin/start-all.sh

[Install]
WantedBy=multi-user.target
```
Now, take a few more steps to enable and use the .service file:

Place it in /etc/systemd/system folder with a name like myfirst.service.

Make sure that your script is executable with:
```terminal
chmod u+x /path/to/spark/sbin/start-all.sh
```
 Start it:
```terminal
     sudo systemctl start myfirst
```
    Enable it to run at boot:
```terminal
     sudo systemctl enable myfirst
```
    Stop it:
```terminal
     sudo systemctl stop myfirst
```
Notes

    You don't need to launch Spark with sudo in your service, as the default service user is already root.

    Look at the links below for more systemd options.

Moreover

Now what we have above is just rudimentary, here is a complete setup for spark:
```vi
[Unit]
Description=Apache Spark Master and Slave Servers
After=network.target
After=systemd-user-sessions.service
After=network-online.target
 
[Service]
User=spark
Type=forking
ExecStart=/opt/spark-1.6.1-bin-hadoop2.6/sbin/start-all.sh
ExecStop=/opt/spark-1.6.1-bin-hadoop2.6/sbin/stop-all.sh
TimeoutSec=30
Restart=on-failure
RestartSec=30
StartLimitInterval=350
StartLimitBurst=10
 
[Install]
WantedBy=multi-user.target
```
To setup the service:
```terminal
sudo systemctl start spark.service
sudo systemctl stop spark.service
sudo systemctl enable spark.service
```
Further reading

Please read through the following links. Spark is a complex setup, so you should understand how it integrates with Ubuntu's init service.

    https://datasciencenovice.wordpress.com/2016/11/30/spark-stand-alone-cluster-as-a-systemd-service-ubuntu-16-04centos-7/
    https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files
    https://www.freedesktop.org/software/systemd/man/systemd.unit.html




Source:
[Ask-Ubuntu](https://askubuntu.com/questions/919054/how-do-i-run-a-single-command-at-startup-using-systemd)
[Better-souce-I-use-it-for-open-vpn](https://www.funoracleapps.com/2022/02/run-shell-script-as-systemd-service-in.html)
