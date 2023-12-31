# Rails + PostgreSQL + Docker

准备

方法1：

```
mkdir rails_demo
cd rails_demo
git init
git remote add compose_rails git@github.com:kyvision/compose_rails.git
git fetch compose_rails main
git merge compose_rails/main
```

或者方法2：

```
git clone git@github.com:kyvision/compose_rails.git
cd compose_rails
git archive  --output "../compose_rails.tar.gz" main
cd ..
mkdir rails_demo
tar zxvf compose_rails.tar.gz -C rails_demo
```

创建Rails项目

```
docker compose run --rm --no-deps app rails new . --force --database=postgresql

```

Linux系统需要修复一下文件权限

```
sudo chown -R $USER:$USER .

```

安装Redis和Sidekiq

```
docker compose run --rm --no-deps app bundle add redis
docker compose run --rm --no-deps app bundle add hiredis
docker compose run --rm --no-deps app bundle add sidekiq

```

FILE: config/sidekiq.yml

```
development:
  :concurrency: 5

production:
  :concurrency: 10

:max_retries: 1

:queues:
  - default
```

FILE: config/database.yml

```
default: &default
  adapter: <%= ENV.fetch('DB_ADAPTER') { 'postgresql' } %>
  encoding: unicode
  host: <%= ENV.fetch('DB_HOST', 'localhost') %>
  username: <%= ENV.fetch('DB_USER', 'postgres') %>
  port: <%= ENV.fetch('DB_PORT', '5432') %>

development:
  <<: *default
  database: app_development

test:
  <<: *default
  database: app_test

production:
  <<: *default
  database: app_production
```

FILE: config/application.rb

```
config.active_job.queue_adapter = :sidekiq
config.cache_store = :redis_cache_store, {  url: ENV.fetch('REDIS_URL', 'redis://localhost:6379/1')}
```

FILE: config/initializers/sidekiq.rb

```
Sidekiq.configure_server do |config|
  config.redis = { url: ENV.fetch('REDIS_URL', 'redis://localhost:6379/1') }
end

Sidekiq.configure_client do |config|
  config.redis = { url: ENV.fetch('REDIS_URL', 'redis://localhost:6379/1') }
end
```

创建镜像

```
docker compose build

```

启动


```
docker compose up
```

创建数据库文件


```
docker compose run -rm app rails db:create
```
