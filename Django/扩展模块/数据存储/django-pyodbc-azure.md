# mac

```
brew install unixodbc
brew install freetds --with-unixodbc
```

## 问题

```
Error: The Command Line Tools header package must be installed on Mojave.
The installer is located at:
  /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg
```

## 解决

```
cd /Library/Developer/CommandLineTools/Packages/
open .
安装macOS_SDK_headers_for_macOS_10.14.pkg
```

```
tsql -C
Compile-time settings (established with the "configure" script)
                            Version: freetds v1.00.94
             freetds.conf directory: /usr/local/etc
     MS db-lib source compatibility: no
        Sybase binary compatibility: no
                      Thread safety: yes
                      iconv library: yes
                        TDS version: 7.3
                              iODBC: no
                           unixodbc: yes
              SSPI "trusted" logins: no
                           Kerberos: no
                            OpenSSL: yes
                             GnuTLS: no
                               MARS: no
```

> vim /usr/local/etc/odbcinst.ini 

```
[FreeTDS]
Description = TD Driver (MSSQL)
Driver = /usr/local/lib/libtdsodbc.so
Setup = /usr/local/lib/libtdsodbc.so
FileUsage = 1
```

