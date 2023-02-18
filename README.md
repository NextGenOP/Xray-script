# Xray-TLS+Web build/management script
## Table of contents
[1. Scripting Features](#scripting features)

[2. Precautions] (#Notes)

[3. Installation time description](#Installation time description)

[4. Script usage instructions](#Script usage instructions)

[5. Run screenshot](#run screenshot)

[6. Pretend website description](#Pretend website description)

[7. About TLS handshake, TLS fingerprint and ALPN](#About tls handshake tls fingerprint and alpn)

[8. About gRPC and WebSocket](#About gRPC and WebSocket)

[9. Installation location](#installation location)

[10. Dependency list](#Dependency list)

[11.Note](#Note)
## Script Features
1. Support (VLESS/VMess)-(TCP/gRPC/WebSocket)-(XTLS/TLS) + Web construction/management, and support the coexistence of multiple protocols

2. Integrated multi-version bbr/sharp installation options
 
3. Support multiple systems (Ubuntu CentOS Debian deepin fedora ...)

4. Support multiple instruction sets (x86 x86_64 arm64 ...)

5. Support ipv6only server (need to set dns64 by yourself)

6. Integrate the function of deleting Alibaba Cloud Shield and Tencent Cloud Shield (only valid for Alibaba Cloud and Tencent Cloud servers)

7. Use Nginx as a website service

8. Use Xray as a pre-splitter

9. Use acme.sh to automatically apply for/renew domain name certificates

10. Support the option to build a personal network disk as a camouflaged webpage
## Precautions
1. This script needs a domain name resolved to the server (supports cdn)

2. This script takes a long time to install, see **[Installation Time Description](#Installation Time Description)**

3. This script is designed for personal VPS users, not suitable for airport owners (this script does not have functions such as multi-user management/traffic statistics).

4. It is recommended to use this script on a clean system (VPS console - reset system)
## Installation time instructions
The installation time of this script is relatively long (**[Installation time reference](#Installation time reference)**) , see [here](#Why the script installation time is so long) for the reason.

This script is suitable for long-term use after one installation, and is not suitable for repeatedly resetting the system installation, which will consume a lot of your time. If you need to change the configuration and domain name, etc., there are corresponding options in the management interface.

If there is a need for quick installation, it is recommended to choose other scripts in **[Xray-core#Installation](https://github.com/XTLS/Xray-core#Installation)**
### Installation time reference
Installation process:

`[Upgrade system components]->[install bbr]->[install php]->install Nginx->install Xray->apply for a certificate->configuration file->[configure fake website]`

The part wrapped by `[]` is optional.

**This is the average installation time of a single-core 1G server, for reference only:**
|Project|Duration|
|-|-|
|Upgrade installed software|0-10 minutes|
|Upgrade system|10-20 minutes|
|Install bbr|0-3 minutes|
|Install php|Centos8 (gcc8.3 4.18 kernel): 20-60 minutes|
||Ubuntu20.10 (gcc10.2 5.11-rc3 kernel): 15-20 minutes|
||Debian10 (gcc8.3 4.19 kernel): 10-15 minutes|
|Install Nginx|13-15 minutes|
|Install Xray|<half a minute|
|Application certificate|1-2 minutes|
|Profile|<100ms|
|Configure fake website|Nextcloud: 1-3 minutes|
||Cloudreve: 1-2 minutes|
### Why does the script installation take so long?
There are three reasons why the time is longer than other scripts:
```
1. Integrated the function of installing bbr
2. Integrate the function of updating system and software package
3. (Main reason) Nginx and php of the script are compiled from source code, other scripts usually directly obtain the binary program
```
The main reasons for adopting the compiled form are:
```
1. Easy to manage
2. Easy to adapt to various systems
```
The advantages of compiling over installing binaries directly are:
```
1. High operating efficiency (-O3 optimization is used when compiling)
2. The software version is new (you can compare this script with other script Nginx versions)
```
The disadvantage is that it takes a long time to compile
## Script instructions
### 1. Install wget && ca-certificates
Debian base system (including Ubuntu, Debian, deepin):
```bash
apt --no-install-recommends -y install wget ca-certificates || (apt update && apt --no-install-recommends -y install wget ca-certificates)
```
Red Hat base system (including CentOS, fedora):
```bash
dnf -y install wget ca-certificates || yum -y install wget ca-certificates
```
### 2. Get/update script
```bash
wget -O Xray-TLS+Web-setup.sh https://github.com/kirin10000/Xray-script/raw/main/Xray-TLS+Web-setup.sh
```
### 3. Execute the script
```bash
bash Xray-TLS+Web-setup.sh
```
### 4. Complete the installation according to the script prompt
## Run the screenshot
<div>
     <img width="400" src="https://github.com/kirin10000/Xray-script/blob/main/image/menu.jpg">
</div>
<div>
     <img width="600" src="https://github.com/kirin10000/Xray-script/blob/main/image/protocol.jpg">
</div>

## Masquerade website description
### The role of camouflage website
This website is a website built with your domain name. After the construction is completed, you can directly enter your domain name on the browser to visit.

All traffic you proxy with Xray will be disguised as traffic to this website.

Note that fake websites are not a panacea. According to the experience of some people, as long as your monthly traffic exceeds a certain limit, the operator will block you, no matter what your fake website is. That is to say, even if you do not proxy at all, but visit your website normally and visit too much traffic, you may be blocked.
### Pretending to choose a website
The common characteristics of traffic using VPS self-built Xray proxy are **single point**, **large traffic**, **long time**, **GO-TLS fingerprint characteristics**, **same access Sex ** and so on.

* **Single point** means that there are few people who use it, generally only yourself, even if you share it with friends, generally it will not be too much.
* **Long time** not only refers to a long time, but also refers to using the agent every day for a month or a year.
* **GO-TLS fingerprint feature** **On the premise of not disguising the browser fingerprint**, it can be judged from the TLS handshake information that the client is a GO program, see [here] for details (#about tls handshake tls fingerprint and alpn).
* **Sameness of entry and exit** means that the traffic entering and exiting the VPS is almost the same in time and size, such as using Xray proxy to browse `BiliBili`, traffic from `BiliBili` to `VPS(Xray server)`, and The traffic from `VPS` to `Xray Client` is almost the same in time and size. **The sameness of entry and exit** is a common problem of all proxies. There is no good way to disguise it yet, but because the VPS is not located in the mainland, if it is not the object of special attention, it will generally not be censored.

Since all the traffic that is proxied by Xray will be disguised as the traffic to visit this website, then we choose to disguise the website to try to choose the website with the same traffic characteristics as Xray proxy traffic characteristics**.

1. **Cloudreve and Nextcloud**

They are all personal network disks. Personal network disks can be understood as Baidu network disks built using their own VPS. The difference is that files are stored in the VPS, and they are the administrators of the network disks.

Personal network disks have the most matches with the characteristics mentioned above, including **single point**, **large traffic**, **GO-TLS fingerprint characteristics**, **long-term**, etc., it is recommended choose.

Regarding **GO-TLS fingerprint feature**, **under the premise of not disguising the browser fingerprint**, set alpn to http/1.1, which can be disguised as a WebDav client implemented in GO language, see [here] for details (# about tls handshake tls fingerprint and alpn).

The differences between Cloudreve and Nextcloud are as follows:
||Advantages|Disadvantages|
|-|-|-|
|Nextcloud|More functions and more powerful, more users|Need to install php, which takes a lot of extra time (see **[Installation time reference](#Installation time reference)**), and it also takes up more time than Cloudreve Many system resources, so it is not recommended for small computers. |
|Cloudreve|Lightweight, fast installation (no php required), less system resources occupied|fewer functions, less people use|
2. **403 page**

Basically all large websites have a website background. For example, the website of Bilibili is `www.bilibili.com`. But when the video is playing, the video file is provided by another URL. When playing the video, right-click on `Video Statistics`, where `Video Host` is. This type of URL has content only when a specific URL suffix is opened. If the URL is incorrect, an error page will be returned. The 403 page is disguised as a website background.

In other words, it is disguised as a 403 page, and no one knows whether your website has anything except yourself.

3. **Custom static website**

You can place your own static website source code, which is not recommended for Xiaobai.

4. **Custom Reverse Proxy Website**

It is not recommended to choose, because the reverse proxy is often only a few html and js files, and most of the content in the website is still provided by the background of the website. Does not meet the characteristics of large flow.
## About TLS handshake, TLS fingerprint and ALPN
Although TLS is an encryption technology, there will be some plaintext information transmission during the TLS handshake process, including SNI information (specified by the serverName parameter), ALPN, cipher suite, etc.

The current TLS standard does not have strict requirements on these plaintexts, so the formats of these plaintext information can be described as varied under different TLS implementations. The different plaintext features of these different TLS implementations are TLS fingerprints.

Through the TLS fingerprint, you can reverse the TLS implementation you are using, such as Chrome's TLS, FireFox's TLS, and the TLS of the GO language official library.

By default, Xray uses the TLS library officially provided by the GO language, which is also the TLS library used by almost all GO language programs. Xray can also simulate fingerprints of Chrome, FireFox, and Safari, but currently only supported by the TCP protocol.

When using TCP and not masquerading browser fingerprints, ALPN can be freely defined. It is recommended to set it to http/1.1, so that the Xray client can be disguised as a WebDav client implemented in GO language (such as **[gowebdav](https://github.com/studio-b12/gowebdav)**). WebDav is a network-specific protocol, and the protocol is based on HTTP/1.1, see: **[WebDav](https://en.wikipedia.org/wiki/WebDAV)** for details.

If you choose to fake browser fingerprints, the alpn parameter in the client configuration will be invalid, and the ALPN will be fixed to h2, http/1.1. Similarly, when using WebSocket, ALPN will be fixed to http/1.1; when using gRPC, ALPN will be forced to add h2. Therefore, using WebSocket can still pretend to be a GO language WebDav client, but gRPC cannot.
## About gRPC and WebSocket
When the CDN being used supports both gRPC and WebSocket, how to choose between the two? Their main differences are reflected in the following three aspects: ALPN, latency and performance.

For ALPN, see [here] (#about tls handshake tls fingerprint and alpn).

Regarding latency, gRPC comes with mux, so the latency is lower. Note that this refers to the delay of opening the website, mux cannot reduce the delay of the game.

Regarding performance, WebSocket has stronger performance. If your device has weaker performance, such as a common home router, using WebSocket will be faster.
## Installation location
**Nginx:** `/usr/local/nginx`
*php:** `/usr/local/php`

**Cloudreve:** `/usr/local/cloudreve`

**Xray:** See **[Xray-install](https://github.com/XTLS/Xray-install)**
## dependency list
The script may automatically install the following dependencies:
|Use|Debian-based system|Red Hat-based system|
|-|-|-|
|yumdb set (mark package manual installation)||yum-utils|
|dnf config-manager||dnf-plugins-core|
|setenforce/getenforce(close SELinux)|selinux-utils|libselinux-utils|
|ss (check port usage)|iproute2|iproute|
|wget|wget|wget|
|curl|curl|curl|
|wget/curl https|ca-certificates|ca-certificates|
|kill/pkill/ps/sysctl/free|procps|procps-ng|
|epel source||epel-release|
|epel source||epel-next-release|
|remi-source||remi-release|
|do-release-upgrade (upgrade system)|ubuntu-release-upgrader-core||
|unzip|unzip|unzip|
|curl|curl|curl|
|Install bbr kernel|linux-base||
|**Compilation basis:**|||
|Download source file|wget|wget|
|Decompress the tar source file|tar|tar|
|Decompress tar.gz source code file|gzip|gzip|
|Decompress tar.xz source code file|xz-utils|xz|
|gcc|gcc|gcc|
|g++|g++|gcc-c++|
|make|make|make|
|**acme.sh depends on:**|||
||curl|curl|
||openssl|openssl|
||cron|crontabs|
|**Compile openssl:**|||
||perl-base (included in libperl-dev)|perl-IPC-Cmd|
||perl-modules-5.32 (included in libperl-dev)|perl-Getopt-Long|
||libperl5.32 (included in libperl-dev)|perl-Data-Dumper|
|||perl-FindBin|
|**Compile Nginx:**|||
||libpcre2-dev|pcre2-devel|
||zlib1g-dev|zlib-devel|
|--with-http_xslt_module|libxml2-dev|libxml2-devel|
|--with-http_xslt_module|libxslt1-dev|libxslt-devel|
|--with-http_image_filter_module|libgd-dev|gd-devel|
|--with-google_perftools_module|libgoogle-perftools-dev|gperftools-devel|
|--with-http_geoip_module|libgeoip-dev|geoip-devel|
|--with-http_perl_module||perl-ExtUtils-Embed|
||libperl-dev|perl-devel|
|**Compile php:**|||
||pkg-config|pkgconf-pkg-config|
||libxml2-dev|libxml2-devel|
||libsqlite3-dev|sqlite-devel|
|--with-fpm-systemd|libsystemd-dev|systemd-devel|
|--with-fpm-acl|libacl1-dev|libacl-devel|
|--with-fpm-apparmor|libapparmor-dev||
|--with-openssl|libssl-dev|openssl-devel|
|--with-kerberos|libkrb5-dev|krb5-devel|
|--with-external-pcre|libpcre2-dev|pcre2-devel|
|--with-zlib|zlib1g-dev|zlib-devel|
|--with-bz2|libbz2-dev|bzip2-devel|
|--with-curl|libcurl4-openssl-dev|libcurl-devel|
|--with-qdbm|libqdbm-dev||
|--with-gdbm||gdbm-devel|
|--with-db4|libdb-dev|libdb-devel|
|--with-tcadb|libtokyocabinet-dev|tokyocabinet-devel|
|--with-lmdb|liblmdb-dev|lmdb-devel|
|--with-enchant|libenchant-2-dev/libenchant-dev|enchant-devel|
|--with-ffi|libffi-dev|libffi-devel|
|--enable-gd|libpng-dev|libpng-devel|
|--with-external-gd|libgd-dev|gd-devel|
|--with-webp|libwebp-dev|libwebp-devel|
|--with-jpeg|libjpeg-dev|libjpeg-turbo-devel|
|--with-xpm|libxpm-dev|libXpm-devel|
|--with-freetype|libfreetype6-dev|freetype-devel|
|--with-gmp|libgmp-dev|gmp-devel|
|--with-imap|libc-client2007e-dev|uw-imap-devel|
|--enable-intl|libicu-dev|libicu-devel|
|--with-ldap|libldap2-dev|openldap-devel|
|--with-ldap-sasl|libsasl2-dev|openldap-devel|
|--enable-mbstring|libonig-dev|oniguruma-devel|
|--with-unixODBC, --with-pdo-odbc|unixodbc-dev|unixODBC-devel|
|--with-pdo-dblib|freetds-dev|freetds-devel|
|--with-pdo-pgsql, --with-pgsql|libpq-dev|libpq-devel|
|--with-pspell|libpspell-dev|aspell-devel|
|--with-libedit|libedit-dev|libedit-devel|
|--with-mm|libmm-dev||
|--with-snmp|libsnmp-dev|net-snmp-devel|
|--with-sodium|libsodium-dev|libsodium-devel|
|--with-password-argon2|libargon2-dev|libargon2-devel|
|--with-tidy|libtidy-dev|libtidy-devel|
|--with-xsl|libxslt1-dev|libxslt-devel|
|--with-zip|libzip-dev|libzip-devel|
|Compile php-imagick:|||
||autoconf|autoconf|
||git|git|
||libmagickwand-dev|ImageMagick-devel|
## Note
1. Link to this article (official website): https://github.com/kirin10000/Xray-script

2. Reference tutorial: https://www.v2fly.org/config/overview.html https://guide.v2fly.org/ https://docs.nextcloud.com/server/latest/admin_manual/installation/source_installation. html https://docs.cloudreve.org/

3. Domain name certificate application: https://github.com/acmesh-official/acme.sh

4. The bbr script comes from: https://github.com/teddysun/across/blob/master/bbr.sh

5. The bbr2 script comes from: https://github.com/yeyingorg/bbr2.sh (Ubuntu Debian) https://github.com/jackjieYYY/bbr2 (CentOS)

6. The bbrplus script comes from: https://github.com/chiakge/Linux-NetSpeed

#### This script is only for communication and learning, please do not use this script to do illegal things. Where the Internet is illegal, illegal things will be punished by law! !
