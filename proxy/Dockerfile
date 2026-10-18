# Proxy image inherited from the previous team.

# Ori: My changes for the bonus questions are documented in the comments below.

# Changed from "ubuntu:24.04" to "nginx:alpine". (Alpine is a lighter Linux distribution, 
# and this base image comes pre-configured with Nginx).
FROM nginx:alpine
#FROM ubuntu:24.04

# Unnecessary line. Nginx is already included in the base image, 
# and the "apt" command is not relevant for Alpine.
#RUN apt-get update && \
#   apt-get install -y nginx curl htop vim git

# Stays the same.
COPY nginx.conf /etc/nginx/nginx.conf

# Those two instructions are unnecessary. 
# EXPOSE 80 and CMD are already defined in the base nginx:alpine image.
#EXPOSE 80

#CMD ["nginx", "-g", "daemon off;"]
