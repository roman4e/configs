# apache2 configuration

This configuration is for using PHP as apache module and fastcgi or proxy_fastcgi

create in the user1 home directory ~/Hosting
```
mkdir /home/user1/Hosting
chmod 0700 /home/user1/Hosting
```
There you will create hosting groups like 
* Hosting/group1
* Hosting/group2

Each group is a place where sites are groupped with the same rights and php handle socket
In each new group you _must_ create few subdirs:
* .config
* .tmp
* .sess
* .logs

So next create in the Apache sites-available a new file 001-vhosts-user1.conf and links it to the sites-enabled
Remember, you also must add any hostname into /etc/hosts file to access it locally if you do not use any configure DNS for the .local domain
Rules file .htaccess is not dynamic and if you change it, reload apache2
No recursive htaccess

Available ${variables} for Hosting
as Global
* curuser - current username
* suffix  - site domain suffix
* hostingdir  - top hosting dir
* APACHE2_HTTP_PORT
* APACHE2_HTTPS_PORT


in Group
* curgroup    - group name
* curpath     - path to group
* logdir      - path to logs
* PHPFCGISocketID - name of php socket
* 

inside .conf file
* curname    - a name of host (without suffix if it used)
* docroot    - host document root

```
# username for the virtual hosts
Use BeginHosting user1

# Adding group (described above)
Use VHostGroup group1
  # Add simple host "hostname.local"
  Use VHost1 "hostname.local"
  # Add simple host with related config file where you can add more stuff
  # and you _must_ add Hosting/group1/.config/otherhost.local.conf file
  Use VHost1conf "otherhost.local"
  
  # if you need map any subdir as docroot use VHost2
  Use VHost2 "symfony.local" "symfony.local/www"
  
  # The mix of config and subdir usage
  Use VHost2conf "supersite.local" "supersite.local/public/www"
  
  # If just an html site without any php handling
  Define DisablePHP
  Use VHost1 "static.local"
  UnDefine DisablePHP
Use EndVhostGroup

# if you want group hostings with domain
Use DomainSuffix "dom"
Use VHostGroup group2
  # Note, a directory name is absolutely same as hosting name even suffix used
  # Hosting/group2/blabla but site is abailable as blabla.dom
  Use VHost1 "blabla"
Use EndVhostGroup

# If you want to reuse php socket
Use PFGroupSocket ${curuser} group1
Use VHostGroup group3
  Use VHost1 "reusing.local"
Use EndVHostGroup

Use EndHosting
# No More Lines There!
```

Inside .conf file
```
# if want to add SSL host
# put cert and key files into .config/cert directory
Use VHostUseSSL

Use XDebugEnable "anyidekey"
```
