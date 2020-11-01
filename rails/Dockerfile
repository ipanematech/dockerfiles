FROM ruby:2.7.1-slim AS project_base

RUN apt-get update && apt-get install -yqq --no-install-recommends curl gnupg build-essential lsof git && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb [arch=amd64] http://dl.yarnpkg.com/debian/ stable main" >> /etc/apt/sources.list.d/yarn.list && \
    curl -sL https://deb.nodesource.com/setup_10.x | bash - && \
    apt-get install -y libpq-dev nodejs yarn && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN mkdir -p /project

WORKDIR /project

ENV BUNDLE_PATH /project_gems
ENV PATH "$BUNDLE_PATH/bin:$PATH"
ENV BUNDLER_VERSION 2.1.4
RUN gem install bundler -v 2.1.4
RUN bundle config --global path "$BUNDLE_PATH" && bundle config --global bin "$BUNDLE_PATH/bin"

FROM project_base AS project_development

COPY . .

RUN mkdir -p log && touch log/development.log && touch log/test.log

FROM project_base AS project_test

COPY Gemfile Gemfile
COPY Gemfile.lock Gemfile.lock
COPY .gemrc .gemrc
COPY .npmrc .npmrc
COPY package.json package.json
COPY yarn.lock yarn.lock

RUN bundle install --jobs 40 --retry 5
RUN yarn install --check-files

COPY . .

RUN mkdir -p log && touch log/development.log && touch log/test.log
