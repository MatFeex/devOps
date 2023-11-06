# Use the Apache HTTP Server version 2.4 as the base image.
FROM httpd:2.4

# Copy the 'index.html' file to the '/usr/local/apache2/htdocs/' directory in the container.
COPY index.html /usr/local/apache2/htdocs/

# Copy the 'httpd.conf' file to the '/usr/local/apache2/conf/' directory in the container.
COPY httpd.conf /usr/local/apache2/conf/httpd.conf
