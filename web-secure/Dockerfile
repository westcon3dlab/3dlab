# Official Image of Apache HTTP Server Project
FROM httpd:2.4.48-alpine

# Place content
COPY index.html /usr/local/apache2/htdocs/
RUN chown www-data:www-data /usr/local/apache2/htdocs/index.html