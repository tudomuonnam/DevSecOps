Cài đặt docker trong centos -stream 7

Uninstall podman trước

``` bash
sudo yum erase podman buildah

sudo yum install docker-ce docker-ce-cli containerd.io

# Dùng scripts để cài đặt docker 

curl -fsSL https://get.docker.com -o get-docker.sh

sudo sh ./get-docker.sh --dry-run

#Check docker version

docker version

#Cài đặt docker-rootless

dockerd-rootless-setuptool.sh install

# Cài đặt để máy khởi động với c2 group

sudo vi /etc/default/grub 

Thêm `systemd.unified_cgroup_hierarchy=1` vào dòng `GRUB_CMDLINE_LINUX`

#update grub trên centos

sudo grub2-mkconfig -o /boot/grub2/grub.cfg 

#reboot máy tính

sudo reboot


```

Sau khi máy khởi động lại check lại docker đã chạy trên c2 group chưa 

``` bash
docker info

```

