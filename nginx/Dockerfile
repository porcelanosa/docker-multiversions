FROM ubuntu:latest  
RUN apt-get update  
RUN apt-get install -y nginx
RUN apt-get install -y curl
COPY ./hosts/ /etc/nginx/sites-enabled/
COPY ./certs/ /etc/nginx/certs/
WORKDIR /data  
VOLUME /data  
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]