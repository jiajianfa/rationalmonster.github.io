# 1、Linux SSH安全设置

## 只允许某用户从指定IP地址登陆

    sed -i '$a AllowUsers CR@192.168.1.12 root@192.168.1.12' /etc/ssh/sshd_config ;\
    systemctl restart sshd

## 修改会话保持时间

    #ClientAliveInterval 0
    #ClientAliveCountMax 3
    修改成
    ClientAliveInterval 30    #（每30秒往客户端发送会话请求，保持连接）
    ClientAliveCountMax 3     #（去掉注释即可，3表示重连3次失败后，重启SSH会话）

## 增加ssh登陆的验证次数

    MaxAuthTries 20

## 允许root用户登录

    sed -i 's/PermitRootLogin no/PermitRootLogin yes/g'  /etc/ssh/sshd_config ;\
    systemctl restart sshd

## 设置登录方式

    #AuthorizedKeysFile   .ssh/authorized_keys   //公钥公钥认证文件
    #PubkeyAuthentication yes   //可以使用公钥登录
    #PasswordAuthentication no  //不允许使用密码登录

# 2、bash不显示路径

命令行会变成-bash-3.2$主要原因可能是用户主目录下的配置文件丢失

    # 方式一
    cp -a /etc/skel/. ~
    
    # 方式二
    echo "export PS1='[\u@\h \W]\$'" >> ~/.bash_profile ;\
    source ~/.bash_profile

# 3、同时监控多个文件

    tail -f file1 file2

# 4、查看网卡

    # 方式一
    ifconfig -a
        
    # 方式二
    cat /proc/net/dev

# 5、cp目录下的带隐藏文件的子目录

    cp -R /home/test/* /tmp/test

/home/test下的隐藏文件都不会被拷贝，子目录下的隐藏文件倒是会的

    cp -R /home/test/. /tmp/test

cp的时候有重复的文件需要覆盖时会让不停的输入yes来确认，可以使用yes|

    yes|cp -r /home/test/. /tmp/test


# 6、查看CPU占用最多的前10个进程

    ps auxw|head -1;ps auxw|sort -rn -k3|head -10 

# 7、查看内存消耗最多的前10个进程

    ps auxw|head -1;ps auxw|sort -rn -k4|head -10 

# 8、查看虚拟内存使用最多的前10个进程

    ps auxw|head -1;ps auxw|sort -rn -k5|head -10

# 9、获取出口IP地址

```bash
curl http://members.3322.org/dyndns/getip
curl https://ip.cn
curl cip.cc
curl myip.ipip.net
curl ifconfig.me
```

# 10、ISO自动挂载

    echo "/mnt/iso/CentOS-7-x86_64-Minimal-1804.iso /mnt/cdrom iso9660 defaults,loop  0 0" >> /etc/fstab && \
    mount -a && \
    df -mh

# 11、查看系统版本号和内核信息

    cat /proc/version
    uname -a
    lsb_release -a
    cat /etc/redhat-release
    cat /etc/issue
    rpm -q redhat-release

# 12、查看物理CPU个数、核数、逻辑CPU个数

CPU总核数 = 物理CPU个数 * 每颗物理CPU的核数 
总逻辑CPU数 = 物理CPU个数 * 每颗物理CPU的核数 * 超线程数

    # 查看CPU信息（型号）
    cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
    
    # 查看物理CPU个数
    cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

    # 查看每个物理CPU中core的个数(即核数)
    cat /proc/cpuinfo| grep "cpu cores"| uniq

    # 查看逻辑CPU的个数
    cat /proc/cpuinfo| grep "processor"| wc -l

# 13、Linux缓存

cached是cpu与内存间的，buffer是内存与磁盘间的，都是为了解决速度不对等的问题。buffer是即将要被写入磁盘的，而cache是被从磁盘中读出来的

- **buff**：作为buffer cache的内存，是块设备的读写缓冲区
- **cache**：作为page cache的内存，文件系统的cache。Buffer cache是针对磁盘块的缓存，也就是在没有文件系统的情况下，直接对磁盘进行操作的数据会缓存到buffer cache中。
- **pagecache**：页面缓存（pagecache）可以包含磁盘块的任何内存映射。这可以是缓冲I/O，内存映射文件，可执行文件的分页区域——操作系统可以从文件保存在内存中的任何内容。Page cache实际上是针对文件系统的，是文件的缓存，在文件层面上的数据会缓存到page cache。
- **dentries**：表示目录的数据结构
- **inodes**：表示文件的数据结构

```bash
#内核配置接口 /proc/sys/vm/drop_caches 可以允许用户手动清理cache来达到释放内存的作用，这个文件有三个值：1、2、3（默认值为0）

#释放pagecache
$> echo 1 > /proc/sys/vm/drop_caches

#释放dentries、inodes
$> echo 2 > /proc/sys/vm/drop_caches

#释放pagecache、dentries、inodes
$> echo 3 > /proc/sys/vm/drop_caches
```

# 14、设置代理

```bash
$> bash -c 'cat >> /etc/profile <<EOF
##HTTP协议使用代理服务器地址
export http_proxy=http://1.2.3.4:3128
##HTTPS协议使用代理服务器地址
export https_proxy=https://1.2.3.4:3128
##FTP协议使用代理服务器地址
export https_proxy=https://1.2.3.4:3128
##不使用代理的IP或主机
export no_proxy=*.abc.com,10.*.*.*,192.168.*.*,*.local,localhost,127.0.0.1
EOF' ;\
   sed -i '/^##/d' /etc/profile ;\
   source /etc/profile
```

# 15、查看网卡UUID

    nmcli con | sed -n '1,2p'

# 16、时间戳与日期

## 日期与时间戳的相互转换

    #将日期转换为Unix时间戳
    date +%s

    #将Unix时间戳转换为指定格式化的日期时间
    date -d @1361542596 +"%Y-%m-%d %H:%M:%S"

## date日期操作

    date +%Y%m%d               #显示前天年月日
    date -d "+1 day" +%Y%m%d   #显示前一天的日期
    date -d "-1 day" +%Y%m%d   #显示后一天的日期
    date -d "-1 month" +%Y%m%d #显示上一月的日期
    date -d "+1 month" +%Y%m%d #显示下一月的日期
    date -d "-1 year" +%Y%m%d  #显示前一年的日期
    date -d "+1 year" +%Y%m%d  #显示下一年的日期

## 获得毫秒级的时间戳

在linux Shell中并没有毫秒级的时间单位，只有秒和纳秒其实这样就足够了，因为纳秒的单位范围是（000000000..999999999），所以从纳秒也是可以的到毫秒的

    current=`date "+%Y-%m-%d %H:%M:%S"`     #获取当前时间，例：2015-03-11 12:33:41
    timeStamp=`date -d "$current" +%s`      #将current转换为时间戳，精确到秒
    currentTimeStamp=$((timeStamp*1000+`date "+%N"`/1000000)) #将current转换为时间戳，精确到毫秒
    echo $currentTimeStamp

# 17、设置时区

    cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 18、生成文件的MD值

在网络传输、设备之间转存、复制大文件等时，可能会出现传输前后数据不一致的情况。这种情况在网络这种相对更不稳定的环境中，容易出现。那么校验文件的完整性，也是势在必行的。

在网络传输时，我们校验源文件获得其md5sum，传输完毕后，校验其目标文件，并对比如果源文件和目标文件md5 一致的话，则表示文件传输无异常。否则说明文件在传输过程中未正确传输。

md5值是一个128位的二进制数据，转换成16进制则是32（128/4）位的进制值。
md5校验，有很小的概率不同的文件生成的md5可能相同。比md5更安全的校验算法还有SHA*系列的。

## **Linux的md5sum命令**

md5sum命令用于生成和校验文件的md5值。它会逐位对文件的内容进行校验。是文件的内容，与文件名无关，也就是文件内容相同，其md5值相同。

```bash
#md5sum命令的详解
$> md5sum --h
Usage: md5sum [OPTION]... [FILE]
With no FILE, or when FILE is -, read standard input.
-b, --binary         二进制模式读取文件
-c, --check          从文件中读取、校验MD5值
      --tag          创建一个BSD-style风格的校验值
-t, --text           文本模式读取文件（默认）
#校验文件MD5值使用的参数
The following four options are useful only when verifying checksums:
      --quiet          don't print OK for each successfully verified file
      --status         don't output anything, status code shows success
      --strict         exit non-zero for improperly formatted checksum lines
  -w, --warn           warn about improperly formatted checksum lines

      --help     display this help and exit
      --version  output version information and exit


#生成的MD5值重定向到文件中
$>md5sum filename > filename.md5
#生成的MD5值重定向追加到文件中
$> md5sum filename >>filename.md5
#多个文件输出到一个md5文件中，这要使用通配符*
$> md5sum *.iso > iso.md5
#同时计算多个文件的MD5值
$> md5sum filetohashA.txt filetohashB.txt filetohashC.txt > hash.md5

#校验MD5:把下载的文件file和该文件的file.md5报文摘要文件放在同一个目录下
$> md5sum -c file.md5
#创建一个BSD风格的校验值
$> md5sum --tag file.md5
MD5 (file.md5) = 9192e127b087ed0ae24bb12070f3051a
```

## **Python生成MD5值**

```bash
# 方式一：使用md5包

import md5

src = 'this is a md5 test.'
m1 = md5.new()
m1.update(src)
print m1.hexdigest()

# 方式二：使用hashlib（推荐）

import hashlib   

m2 = hashlib.md5()   
m2.update(src)   
print m2.hexdigest()

# 加密常见的问题：

1：Unicode-objects must be encoded before hashing

　　解决方案：import hashlib
　　　　　　　m2 = hashlib.md5()
　　　　　　　m2.update(src．encode('utf-8'))
　　　　　　　print m2.hexdigest()
```

## **Java生成MD5值**

```java
import java.security.MessageDigest;
public static void main(String[] args) {  
        String password = "123456";  
        try {  
            MessageDigest instance = MessageDigest.getInstance("MD5");// 获取MD5算法对象  
            byte[] digest = instance.digest(password.getBytes());// 对字符串加密,返回字节数组  
  
            StringBuffer sb = new StringBuffer();  
            for (byte b : digest) {  
                int i = b & 0xff;// 获取字节的低八位有效值  
                String hexString = Integer.toHexString(i);// 将整数转为16进制  
                // System.out.println(hexString);  
  
                if (hexString.length() < 2) {  
                    hexString = "0" + hexString;// 如果是1位的话,补0  
                }  
  
                sb.append(hexString);  
            }  
  
            System.out.println("md5:" + sb.toString());  
            System.out.println("md5 length:" + sb.toString().length());//Md5都是32位  
  
        } catch (NoSuchAlgorithmException e) {  
            e.printStackTrace();  
            // 没有该算法时,抛出异常, 不会走到这里  
        }  
    }  
```

# 19、添加用户

    useradd (选项) （参数）

    #选项
    －c：加上备注文字，备注文字保存在passwd的备注栏中
    －d：指定用户登入时的启始目录
    －D：变更预设值
    －e：指定账号的有效期限，缺省表示永久有效
    －f：指定在密码过期后多少天即关闭该账号
    －g：指定用户所属的起始群组
    －G：指定用户所属的附加群组
    －m：自动建立用户的登入目录
    －M：不要自动建立用户的登入目录
    －n：取消建立以用户名称为名的群组
    －r：建立系统账号
    －s：指定用户登入后所使用的shell
    －u：指定用户ID号

# 20、su 与 sudo

**`su`** : switch to another user 切换用户

**`sudo`** : superuser do 允许用户使用superuser的身份执行命令

```bash
su username ：切换为username，需要输入username密码
su : 切换为root用户，需要输入root密码
su - : 切换为root用户，需要输入root密码，且环境变量也改变
su - -c "command" ：使用root身份执行命令，完成后即退出root身份
sudo command : 与su -c相似，需要输入当前用户（superuser，/etc/sudoers中指定）密码
sudo su -：使用当前用户密码实现root身份的切换
su - hdfs -c command    切换用户并以某用户的身份去执行一条命令
su - hdfs  test.sh  切换用户并以某用户的身份去执行一个shell文件
```

# 21、重新开启SELinux

如果在使用setenforce命令设置selinux状态的时候出现这个提示：`setenforce: SELinux is disabled`。那么说明selinux已经被彻底的关闭了,如果需要重新开启selinux

```bash
vi /etc/selinux/config

更改为：SELINUX=1

必须重启linux，不重启是没办法立刻开启selinux的
```

重启完以后，使用getenforce,setenforce等命令就不会报“setenforce: SELinux is disabled”了。这时，我们就可以用setenforce命令来动态的调整当前是否开启selinux。

# 22、检查软件是否已安装，没有就自动安装

```bash
rpm -qa |grep "jq"
if [ $? -eq 0 ] ;then
    echo "jq hava been installed "
else
    yum -y install epel-release && yum -y install jq
fi
```

# 23、使用privoxy代理http，https流量使用socket连接ShadowSocks服务器

```bash
echo "安装ShadowSocks" && \
yum -y install epel-release && yum -y install python-pip && pip install shadowsocks && \
bash -c 'cat > /etc/shadowsocks.json <<EOF
{
"server": "***.***.***.***",
"server_port": "443",
"local_address": "127.0.0.1",
"local_port":"1080",
"password": "******",
"timeout":300,
"method": "aes-256-cfb",
"fast_open": false
}
EOF' && \
bash -c 'cat > /etc/systemd/system/shadowsocks.service << EOF
[Unit]
Description=Shadowsocks
[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/sslocal -c /etc/shadowsocks.json
[Install]
WantedBy=multi-user.target
EOF' && \
  systemctl daemon-reload  && \
  systemctl enable shadowsocks && \
  systemctl start shadowsocks

yum install -y privoxy && \
sed -i 's/#        forward-socks5t   \/               127.0.0.1:9050 ./        forward-socks5t   \/               127.0.0.1:1080 ./' /etc/privoxy/config && \
privoxy --user privoxy /etc/privoxy/config && \
echo "export http_proxy=http://127.0.0.1:8118" >> /etc/profile && \
echo "export https_proxy=http://127.0.0.1:8118" >> /etc/profile && \
source /etc/profile && \
curl www.google.com
```
