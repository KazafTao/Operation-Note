# 搭建openvpn服务

# 服务器端

## 安装相关软件

```shell
yum -y install epel-release
yum -y install openvpn easy-rsa iptables-services
```

## 生成证书和密钥

1. 将 easy-rsa 脚本复制到 /etc/openvpn/
```shell
mkdir /etc/openvpn/easy-rsa/
cp -r /usr/share/easy-rsa/3.0.8/* /etc/openvpn/easy-rsa/
cp /usr/share/doc/easy-rsa-3.0.8/vars.example /etc/openvpn/easy-rsa/vars
```
2. 编辑vars文件：
```shell
cd /etc/openvpn/easy-rsa/
vi vars
```
去掉#注释，编辑以下内容

```ini
set_var EASYRSA_REQ_COUNTRY "CN"
set_var EASYRSA_REQ_PROVINCE "Beijing"
set_var EASYRSA_REQ_CITY "Beijing"
set_var EASYRSA_REQ_ORG "OpenVPN CA"
set_var EASYRSA_REQ_EMAIL "63***59@qq.com"
set_var EASYRSA_REQ_OU  "My VPN"
```
变量生效

```shell
source ./vars   # 使变量生效
```
3. 生成 CA 根证书：
```shell
./easyrsa init-pki
./easyrsa build-ca nopass
```
4. 生成 OpenVPN 服务器证书和密钥

```shell
./easyrsa build-server-full server nopass     #第一个参数 remitProd 为证书名称
./easyrsa gen-dh
openvpn --genkey --secret ta.key
#如果出现警告
#WARNING: Using --genkey --secret filename is DEPRECATED.  Use --genkey secret filename instead.
#则执行下面的命令
openvpn --genkey secret ta.key
```

5. 复制证书及密钥文件
```shell
cd /etc/openvpn/
cp /etc/openvpn/easy-rsa/{pki/dh.pem,pki/ca.crt,ta.key,pki/issued/server.crt,pki/private/server.key} /etc/openvpn/
```
## 配置OpenVPN服务端配置

创建 server.conf 文件：

```shell
vi server.conf
```
```ini
#local 123.57.237.225 #指定监听的本机IP(因为有些计算机具备多个IP地址)，该命令是可选的，默认监听所有IP地址
port 1194 #服务端端口号
proto tcp #通过tcp协议来连接，也可以通过udp
dev tun #路由模式，注意windows下必须使用dev tap
ca ca.crt #ca证书存放位置
cert server.crt #服务器证书存放位置
key server.key  # #服务器密钥存放位置
dh dh.pem #dh.pem存放位置
tls-auth ta.key 0  #ta.key存放位置
server 10.19.0.0 255.255.255.0 #虚拟局域网网段设置
ifconfig-pool-persist ipp.txt
client-to-client                         #开启客户端互访
duplicate-cn                             #支持一个证书多个客户端登录使用，建议不启用
keepalive 10 120
cipher AES-128-CBC
#comp-lzo
max-clients 100                          #最大客户端并发连接数量
user nobody
group nobody
persist-key
persist-tun
status openvpn-status.log
verb 3
log         /var/log/openvpn/openvpn.log
log-append  /var/log/openvpn/openvpn.log
push "route 172.20.208.0 255.255.255.0" #实际内网ip网段
```
## 配置防火墙和SELINUX

1. 关闭 Firewalld 防火墙及 SELINUX

```shell
systemctl stop firewalld && systemctl mask firewalld
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```
2.  启动 iptables 防火墙并配置

```shell
systemctl enable iptables && systemctl start iptables
iptables -F   # 清理所有防火墙规则
iptables -t nat -A POSTROUTING -s 10.19.0.0/16 -j MASQUERADE
iptables-save > /etc/sysconfig/iptables   # iptables 规则持久化保存
```

## 配置地址转发，启动 OPENVPN 服务端

1. 启用地址转发

```shell
echo -e "###OpenVPN ADD\nnet.ipv4.conf.default.accept_source_route = 1\nnet.ipv4.conf.default.rp_filter = 0\nnet.ipv4.ip_forward = 1" >> /etc/sysctl.conf
sysctl -p
```

2. 启动 openvpn-server

```shell
systemctl start openvpn@server  && systemctl enable openvpn@server
```

3. 执行以下命令，确认1194端口正在监听，说明OpenVPN正在运行。

```shell
netstat -ano | grep 1194
```

4. 重启 openvpn-server

```shell
systemctl start openvpn@server #启动OpenVPN服务
systemctl restart openvpn@server #重启OpenVPN服务
systemctl stop openvpn@server #停止OpenVPN服务
```

或者

```shell
service openvpn restart  #重启OpenVPN服务
```

## 创建用户端配置文件及生成证书文件

1. 创建一个客户端配置模板文件 sample.ovpn，该文件在脚本中会用到，放到 /etc/openvpn/client/ 目录：

```shell
vi /etc/openvpn/client/sample.ovpn
```
```ini
client
remote OPENVPN服务端公网IP 1194    #例：remote 106.12.1.1 1194
dev tun
proto tcp
ca ca.crt
cert client.crt
key client.key
tls-auth ta.key 1
remote-cert-tls server
resolv-retry infinite
nobind
persist-key
persist-tun
#comp-lzo
verb 3
mute-replay-warnings
```

2. 创建添加用户脚本：

```shell
vi ovpn_user.sh
```
```shell
#! /bin/bash
 
set -e
 
OVPN_USER_KEYS_DIR=/etc/openvpn/client/keys
EASY_RSA_DIR=/etc/openvpn/easy-rsa/
PKI_DIR=$EASY_RSA_DIR/pki
 
for user in "$@"
do
  if [ -d "$OVPN_USER_KEYS_DIR/$user" ]; then
    rm -rf $OVPN_USER_KEYS_DIR/$user
    rm -rf  $PKI_DIR/reqs/$user.req
    sed -i '/'"$user"'/d' $PKI_DIR/index.txt
  fi
  cd $EASY_RSA_DIR
  # 生成客户端SSL证书文件
  ./easyrsa build-client-full $user nopass
  # 整理下生成的文件
  mkdir -p  $OVPN_USER_KEYS_DIR/$user
  cp $PKI_DIR/ca.crt $OVPN_USER_KEYS_DIR/$user/   # CA 根证书
  cp $PKI_DIR/issued/$user.crt $OVPN_USER_KEYS_DIR/$user/   # 客户端证书
  cp $PKI_DIR/private/$user.key $OVPN_USER_KEYS_DIR/$user/  # 客户端证书密钥
  cp /etc/openvpn/client/sample.ovpn $OVPN_USER_KEYS_DIR/$user/$user.ovpn # 客户端配置文件
  sed -i 's/client.crt/'"$user".crt'/g' $OVPN_USER_KEYS_DIR/$user/$user.ovpn
  sed -i 's/client.key/'"$user".key'/g' $OVPN_USER_KEYS_DIR/$user/$user.ovpn
  cp $EASY_RSA_DIR/ta.key $OVPN_USER_KEYS_DIR/$user/ta.key  # auth-tls 文件
  cd $OVPN_USER_KEYS_DIR
  zip -r $user.zip $user
done
exit 0
```
执行上面脚本创建一个用户：`sh ovpn_user.sh <username>`，改为你要添加的用户名，会在 /etc/openvpn/client/keys 目录下生成以用户名命名的 zip 打包文件，将该压缩包下载到本地解压，即可加载到客户端使用。

2. 删除一个 OpenVPN 用户

创建删除用户的脚本文件

```shell
vim del_ovpn_user.sh
```
```shell
#! /bin/bash
 
set -e
OVPN_USER_KEYS_DIR=/etc/openvpn/client/keys
EASY_RSA_DIR=/etc/openvpn/easy-rsa/
for user in "$@"
do
  cd $EASY_RSA_DIR
  echo -e 'yes\n' | ./easyrsa revoke $user
  ./easyrsa gen-crl
  # 吊销掉证书后清理客户端相关文件
  if [ -d "$OVPN_USER_KEYS_DIR/$user" ]; then
    rm -rf $OVPN_USER_KEYS_DIR/${user}*
  fi
  systemctl restart openvpn@server
done
exit 0
```
执行上面脚本即可删除一个用户：sh del_ovpn_user.sh  <username>，改为你要删除的用户名。

