FROM ruby:3.2.2
RUN apt-get update -qq && apt-get install -y nodejs postgresql-client
ENV RAILS_ROOT /app
WORKDIR $RAILS_ROOT
ARG RAILS_ENV
ENV RAILS_ENV=$RAILS_ENV
ENV RAKE_ENV=$RAILS_ENV
ENV RACK_ENV=$RAILS_ENV
COPY Gemfile Gemfile.lock .
RUN if [["$RAILS_ENV" == "production"]]; then bundle install --jobs 20 --retry 5 --without development test; else bundle install --jobs 20 --retry 5; fi
COPY ./docker/app/entrypoint.sh /usr/bin/
RUN chmod +x /usr/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]
CMD ["rails", "server", "-b", "0.0.0.0"]
