FROM ruby:latest

RUN gem install rails sqlite3 bundler:1.15.1

# https://nodejs.org/en/download/package-manager/
RUN curl -sL https://deb.nodesource.com/setup_13.x | bash -

# https://stackoverflow.com/a/58613322
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

RUN apt-get update && apt-get install -y nodejs yarn

# the following command would make much more sense, but I guess the exercise
# is supposed to familiarize the student with the "ONBUILD" instruction...
# RUN rails new blog

ONBUILD WORKDIR /opt/app
ONBUILD COPY blog/ /opt/app

ONBUILD RUN bundle install

# https://stackoverflow.com/a/14864024
ONBUILD RUN bundle exec rake db:migrate
ONBUILD RUN bundle exec rake db:seed


# docker login
# docker build -t ft-rails:on-build .
# docker tag ft-rails:on-build [username]/[user's repo]
# docker push [username]/[user's repo]
