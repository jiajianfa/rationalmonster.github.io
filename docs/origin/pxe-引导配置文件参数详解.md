
```yaml
default ks 　　　　#默认启动的是 'label ks' 中标记的启动内核
prompt 1          #显示 'boot: ' 提示符。为 '0' 时则不提示，将会直接启动 'default' 参数中指定的内容。
timeout 6 　　　　 #在用户输入之前的超时时间，单位为 1/10 秒。

display boot.msg  #显示某个文件的内容，注意文件的路径。默认是在/var/lib/tftpboot/ 目录下。也可以指定位类似 '/install/boot.msg'这样的，路径+文件名。

F1 boot.msg 　　　 #按下 'F1' 这样的键后显示的文件。
F2 options.msg 
F3 general.msg 
F4 param.msg 
F5 rescue.msg 


label linux       #'label' 指定你在 'boot:' 提示符下输入的关键字，比如boot: linux[ENTER]，这个会启动'label linux' 下标记的kernel 和initrd.img 文件。
  kernel vmlinuz  #kernel 参数指定要启动的内核。
  append initrd=initrd.img #append 指定追加给内核的参数，能够在grub 里使用的追加给内核的参数，在这里也都可以使用。
  
label text 
  kernel vmlinuz 
  append initrd=initrd.img text 
  
label ks 
  kernel vmlinuz 
  append ks=http://192.168.111.130/ks.cfg initrd=initrd.img    #告诉系统，从哪里获取ks.cfg文件 
  
label local 
  localboot 1 
  
label memtest86 
  kernel memtest 
  append -
```

```yaml
default menu.c32
prompt 1
timeout 10
 
menu title ########## PXE Boot Menu ##########
 
label 1
menu label ^1) Install CentOS 7 x64 with Local Repo
menudefault
kernel centos7/vmlinuz
append initrd=centos7/initrd.img text ks=ftp://192.168.100.1/pub/ks.cfg
 
label 2
menu label ^2) Install CentOS 7 x64 with http://mirror.centos.org Repo
kernel centos7/vmlinuz
append initrd=centos7/initrd.img method=http://mirror.centos.org/centos/7/os/x86_64/ devfs=nomount ip=dhcp
 
label 3
menu label ^3) Install CentOS 7 x64 with Local Repo using VNC
kernel centos7/vmlinuz
append initrd=centos7/initrd.img method=ftp://192.168.100.1/pub devfs=nomount inst.vnc inst.vncpassword=password 
```