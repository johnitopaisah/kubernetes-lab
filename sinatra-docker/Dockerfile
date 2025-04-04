FROM ruby:3.1.0

WORKDIR /app

# Copy Gemfile first to use Docker cache
COPY Gemfile Gemfile.lock ./

# Install dependencies and ensure binstubs are accessible
RUN gem install bundler && \
    bundle config set --local path 'vendor/bundle' && \
    bundle install && \
    ln -s /app/vendor/bundle/ruby/3.1.0/bin/rackup /usr/local/bin/rackup

COPY . .

EXPOSE 4567

CMD ["bundle", "exec", "rackup", "--host", "0.0.0.0", "--port", "4567"]
