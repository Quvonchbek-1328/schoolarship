# Grant Atlas — nginx image serving the static site
FROM nginx:1.27-alpine
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/conf.d/grant-atlas.conf
RUN sed -i 's#server_name example.uz;#server_name _;#; s#root /var/www/grant-atlas;#root /usr/share/nginx/html;#' /etc/nginx/conf.d/grant-atlas.conf
COPY public/ /usr/share/nginx/html/
EXPOSE 80
