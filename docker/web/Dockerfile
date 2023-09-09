FROM nginx:1.25.2

RUN apt-get update -qq && apt-get install -y apache2-utils

ENV RAILS_ROOT /app

WORKDIR $RAILS_ROOT

RUN mkdir public
RUN mkdir log

COPY docker/web/nginx.conf /tmp/docker.nginx

RUN envsubst '$RAILS_ROOT' < /tmp/docker.nginx > /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
