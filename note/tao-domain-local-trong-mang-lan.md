
# Tạo domain local sử dụng trong mạng LAN

Để tạo một địa chỉ trong mạng lan ví dụ `rasberrypi.local` chẳng hạn có thể dùng avahi-daemon.

## Ưu điểm của việc sử dụng địa chỉ mạng lan
- Máy được cài đặt địa chỉ local có thể thay đổi địa chỉ ip mà vẫn có thể truy cập được bằng http, ssh, hay ping
- Tên miền dễ nhớ hơn địa chỉ IP 
## Nhược điểm

Chỉ có máy cài đặt avahi-daemon mới tìm thấy và sử dụng được địa chỉ local này. Ubuntu cài sẵn, Centos phải tự cài

## Cách cái đặt

Dùng Centos

Cài đặt phần mềm 

``` bash
sudo yum update

sudo yum makecache

sudo yum install avahi-daemon 

```
Chỉnh sửa file 

``` bash
sudo vi /etc/avahi/avahi-daemon.conf 

```

Chỉ sửa lại file để có nội dung như sau


``` conf
[server]
host-name=centos #Ten domain
domain-name=local # Duoi của domain
browse-domains=local,0pointer.de, zeroconf.org
use-ipv4=yes
use-ipv6=no
allow-interfaces=enp0s3
#deny-interfaces=eth1
#check-response-ttl=no
#use-iff-running=no
#enable-dbus=yes
#disallow-other-stacks=no
#allow-point-to-point=no
#cache-entries-max=4096
#clients-max=4096
#objects-per-client-max=1024
#entries-per-entry-group-max=32
ratelimit-interval-usec=1000000
ratelimit-burst=1000

[wide-area]
enable-wide-area=yes

[publish]
#disable-publishing=no
#disable-user-service-publishing=no
#add-service-cookie=no
#publish-addresses=yes
publish-hinfo=no
publish-workstation=yes
publish-domain=yes
#publish-dns-servers=192.168.50.1, 192.168.50.2
#publish-resolv-conf-dns-servers=yes
#publish-aaaa-on-ipv4=yes
#publish-a-on-ipv6=no

[reflector]
#enable-reflector=no
#reflect-ipv=no

[rlimits]
#rlimit-as=
#rlimit-core=0
#rlimit-data=8388608
#rlimit-fsize=0
#rlimit-nofile=768
#rlimit-stack=8388608
#rlimit-nproc=3

```
Cho phép ssh sử dụng domain này

```bash
sudo cp /usr/share/doc/avahi/ssh.service /etc/avahi/services/

#Bật avahi-daemon như một dịch vụ và restat máy
sudo avahi-daemon -D
sudo avahi-daemon -r 
sudo reboot

```

Máy sẽ khởi động lại

Từ máy remote (Lưu ý máy remote cũn phải cài avahi-daemon sẵn rồi)

Kiểm tra xem có tên miền nào trong mạng lan không?

``` bash
avahi-discover 

Browsing domain 'local' on -1.-1 ...
Browsing domain 'centos.local' on 3.1 ...
Browsing domain 'centos.local' on 3.0 ...
Browsing for services of type '_ssh._tcp' in domain 'centos.local' on 3.1 ...
Browsing for services of type '_ssh._tcp' in domain 'centos.local' on 3.0 ...
Found service 'centos' of type '_ssh._tcp' in domain 'centos.local' on 3.1.
Found service 'centos' of type '_ssh._tcp' in domain 'centos.local' on 3.0.
Service data for service 'centos' of type '_ssh._tcp' in domain 'centos.local' on 3.0:
	Host centos.centos.local (192.168.1.12), port 22, TXT data: []
Service data for service 'centos' of type '_ssh._tcp' in domain 'centos.local' on 3.0:
	Host centos.centos.local (192.168.1.12), port 22, TXT data: []

```

Như vậy máy cài đặt đã có thể sử dụng được tên miền centos.local trên mạng lan để bạn có thể sử dụng

``` bash
ssh <user>@centos.local
ping centos.local
curl centos.local

```