# Rails + PostgreSQL + Docker

准备

```
mkdir my_project
cd my_project
git init
git remote add compose_rails git@github.com:kyvision/compose_rails.git
git fetch compose_rails main
git merge compose_rails/main
```

创建Rails项目

`RAILS_ENV='development' docker compose run --no-deps app rails new . --force --database=postgresql`

Linux系统需要修复一下文件权限

`sudo chown -R $USER:$USER .`

创建镜像

`RAILS_ENV='development' docker compose build`

修改数据库配置

```
default: &default
  adapter: postgresql
  encoding: unicode
  host: db
  username: postgres
  password: password
  pool: 5

development:
  <<: *default
  database: my_project_development


test:
  <<: *default
  database: my_project_test
```

启动

`docker compose up`

创建数据库文件

`docker compose run app rake db:create`
