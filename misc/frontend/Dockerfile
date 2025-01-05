# Use the official NGINX base image
FROM nginx:alpine

# Copy the entire public directory to the NGINX default web root
COPY public /usr/share/nginx/html/

# Expose port 80
EXPOSE 80

# Start NGINX
CMD ["nginx", "-g", "daemon off;"]
