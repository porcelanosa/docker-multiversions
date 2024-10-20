FROM ubuntu:latest  
RUN apt-get update  
RUN apt-get install -y nginx  
COPY ./hosts/ /etc/nginx/sites-enabled/
WORKDIR /data  
VOLUME /data  
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]