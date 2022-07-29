---
title: Docker-compose部署openVPN
date: 2022-07-29
tags:
 - docker
 - docker-compose
 - openvpn
---



##### 一. 部署

- 创建 `docker-compose.yml`

```yaml
version: '3'
services:
  openvpn:
    cap_add:
     - NET_ADMIN
    image: kylemanna/openvpn
    container_name: openvpn
    ports:
     - "1194:1194"
    restart: always
    volumes:
     - ./data/conf:/etc/openvpn
```



##### 二. 初始化

- 初始化配置

  ```bash
  docker-compose run --rm openvpn ovpn_genconfig -u tcp://vpn.kaychen.cn
  ```

- 初始化证书

  ```bash
  docker-compose run --rm openvpn ovpn_initpki
  ```

  输出示例

  ```
  init-pki complete; you may now create a CA or requests.
  Your newly created PKI dir is: /etc/openvpn/pki
  Using SSL: openssl OpenSSL 1.1.1g  21 Apr 2020
  
  Enter New CA Key Passphrase: (输入密码)
  Re-Enter New CA Key Passphrase: (输入密码)
  Generating RSA private key, 2048 bit long modulus (2 primes)
  ...............................................+++++
  ..............................................+++++
  You are about to be asked to enter information that will be incorporated
  into your certificate request.
  What you are about to enter is what is called a Distinguished Name or a DN.
  There are quite a few fields but you can leave some blank
  For some fields there will be a default value,
  If you enter '.', the field will be left blank.
  -----
  Common Name (eg: your user, host, or server name) [Easy-RSA CA]:(直接回车)
  DH parameters of size 2048 created at /etc/openvpn/pki/dh.pem
  
  
  Using SSL: openssl OpenSSL 1.1.1g  21 Apr 2020
  Generating a RSA private key
  ..................+++++
  ...........................+++++
  writing new private key to '/etc/openvpn/pki/easy-rsa-72.DKOLbA/tmp.HckbDH'
  -----
  Using configuration from /etc/openvpn/pki/easy-rsa-72.DKOLbA/tmp.glJiIo
  Enter pass phrase for /etc/openvpn/pki/private/ca.key:(输入密码)
  Check that the request matches the signature
  Signature ok
  The Subject's Distinguished Name is as follows
  commonName            :ASN.1 12:'vpn.kaychen.cn'
  Certificate is to be certified until Oct 31 10:23:04 2024 GMT (825 days)
  
  Write out database with 1 new entries
  Data Base Updated
  
  Using SSL: openssl OpenSSL 1.1.1g  21 Apr 2020
  Using configuration from /etc/openvpn/pki/easy-rsa-147.oHBngi/tmp.iaDMkK
  Enter pass phrase for /etc/openvpn/pki/private/ca.key:(输入密码)
  
  An updated CRL has been created.
  CRL file: /etc/openvpn/pki/crl.pem
  ```

- 调整目录权限

  ```bash
  sudo chown -R $(whoami): ./data
  ```

- 生成客户端证书

  ```bash
  # with a passphrase (recommended)
  docker-compose run --rm openvpn easyrsa build-client-full zhengkai.chen
  # without a passphrase (not recommended)
  docker-compose run --rm openvpn easyrsa build-client-full zhengkai.chen nopass
  ```

- 导出客户端配置ovpn

  ```bash
  docker-compose run --rm openvpn ovpn_getclient zhengkai.chen > zhengkai.chen.ovpn
  ```

- 撤销客户端证书

  ```bash
  # Keep the corresponding crt, key and req files.
  docker-compose run --rm openvpn ovpn_revokeclient zhengkai.chen
  # Remove the corresponding crt, key and req files.
  docker-compose run --rm openvpn ovpn_revokeclient zhengkai.chen remove
  ```

- 启动openVPN

  ```bash
  docker-compose up -d
  ```

  

##### 三. 下载客户端

- [MacOS](https://openvpn.net/client-connect-vpn-for-mac-os/)
- [Windows](https://openvpn.net/client-connect-vpn-for-windows/)



##### 参考:

- [openvpn官网](https://openvpn.net)

- [docker-openvpn](https://github.com/kylemanna/docker-openvpn/blob/master/docs/docker-compose.md)
- [openvpn 配置](https://zhuanlan.zhihu.com/p/497357782)