FROM debian:buster

# Install packages and permission setting
RUN apt-get update && \
    apt-get upgrade -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y apache2 && \
    a2enmod cgid && \
    apt-get -y install libtinfo5 && \
    apt-get -y install netcat && \
    apt-get install -y sudo && \
    groupadd wheel && \
    usermod -aG wheel www-data && \
    echo "%wheel ALL=NOPASSWD: ALL" >> /etc/sudoers && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Inject CVE-2014-6271 bash
COPY packages /packages
RUN dpkg -i /packages/*

# Place content
COPY vulnerable /usr/lib/cgi-bin/
COPY danger.html /var/www/html/

RUN chown www-data:www-data /var/www/html/* && \
    chown www-data:www-data /usr/lib/cgi-bin/vulnerable && \
    chmod +x /usr/lib/cgi-bin/vulnerable

EXPOSE 80

CMD ["/usr/sbin/apache2ctl", "-DFOREGROUND"]
