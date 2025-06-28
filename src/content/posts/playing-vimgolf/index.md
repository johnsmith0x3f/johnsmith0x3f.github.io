---
title: Playing VimGolf
published: 2025-06-22
description: ''
image: ''
tags: [ 'Vim' ]
category: ''
draft: true
lang: ''
---

## [Add quotes to ansible playbook](https://www.vimgolf.com/challenges/5f0f5fbe280fbf000c233304)

You created an ansible playbook, but forgot to add quotes. Can you fix it?

```yaml title="Start.yaml"
---
- hosts: all
  vars:
    ssh_state: True
  tasks:
    - name: Manage openssh
      package:
        name: openssh
        state: {{ ssh_state }}
```

```yaml title="Goal.yaml"
---
- hosts: all
  vars:
    ssh_state: True
  tasks:
    - name: Manage openssh
      package:
        name: openssh
        state: "{{ ssh_state }}"
```

```vim
:%s/\({.*}\)/"\1"<CR>ZZ
```

```vim
GWi"<Esc>$a"<Esc>ZZ
```

```vim
GWi"<Esc>A"<Esc>ZZ
```

```vim
GWi"<End>"<Esc>ZZ
```

```vim
GWi"<End><C-@>ZZ
```

## [Com(m)a Trouble](https://www.vimgolf.com/challenges/5ba020f91abf2d000951055c)

Someone was real stupid when placing his commas. Can you fix it?

```txt title="Start.txt"
,0,1,2,3,4,5,6,7,89
,1,2,3,4,5,6,7,8,90
,2,3,4,5,6,7,8,9,01
,3,4,5,6,7,8,9,0,12
,4,5,6,7,8,9,0,1,23
56,7,8,9,0,1,2,3,4,
67,8,9,0,1,2,3,4,5,
78,9,0,1,2,3,4,5,6,
89,0,1,2,3,4,5,6,7,
90,1,2,3,4,5,6,7,8,
```

```txt title="Goal.txt"
0,1,2,3,4,5,6,7,8,9
1,2,3,4,5,6,7,8,9,0
2,3,4,5,6,7,8,9,0,1
3,4,5,6,7,8,9,0,1,2
4,5,6,7,8,9,0,1,2,3
5,6,7,8,9,0,1,2,3,4
6,7,8,9,0,1,2,3,4,5
7,8,9,0,1,2,3,4,5,6
8,9,0,1,2,3,4,5,6,7
9,0,1,2,3,4,5,6,7,8
```

```vim
<C-V>4jd$PG$<C-V>4kd0pZZ
```

## [YAML to dotenv](https://www.vimgolf.com/challenges/9v00674f1bfb00000000063d)

Convert this YAML config file to a .env file.

```yaml title="Start.yaml"
vimgolf:
  logging:
    level: INFO
app:
  postgres:
    host: !ENV {POSTGRES_HOST}
    port: !ENV {POSTGRES_PORT}
  pulsar:
    host: !ENV ${PULSAR_HOST}
    port: !ENV ${PULSAR_PORT}
    namespace: vimgolf
    topic: !ENV ${PULSAR_TOPIC}
```

```sh frame="code" title="Goal.env"
POSTGRES_HOST=
POSTGRES_PORT=
PULSAR_HOST=
PULSAR_PORT=
PULSAR_TOPIC=
```

```vim
:v/{/d|%s/.*{\(.*\)}/\1=<CR>ZZ
```

```vim
:v/{/d|%norm df{$r=<CR>ZZ
```

```vim
qqd/{<CR>x$r=j0q4@qZZ
```

```vim
qqd/P<CR>$r=j0q4@qZZ
```

```vim
qqd/P<CR>$r= q4@qZZ
```

## [Create json from a .env file](https://www.vimgolf.com/challenges/9v00680e54330000000006c0)

A simple everyday task.

```sh frame="code" title="Start.env"
# API Settings
JOBS_API_URL=http://localhost:5000
JOBS_BASE_URL=http://localhost:8000
SCRAPERS_BASE_URL=http://localhost:9900

# Database Settings
JOBS_DATABASE_URI=mongodb://mongouser:mongopassword@127.0.0.1:27017/app

# Redis Settings
JOBS_REDIS_DSN=redis://127.0.0.1:6000

# Data API
DATA_BASE_URL=http://127.0.0.1:8900

# Minio config
JOBS_MINIO_SECURE=false
JOBS_MINIO_ACCESS_KEY=miniouser
JOBS_MINIO_SECRET_KEY=miniosecret
JOBS_MINIO_HOST=127.0.0.1:9500
JOBS_MINIO_DEFAULT_BUCKET=jobs
JOBS_MINIO_REGION=us-west-1
JOBS_MINIO_CREATE_BUCKETS=false
JOBS_MINIO_RESULTS_FORMAT=results/{job.id}.json
JOBS_MINIO_ARGUMENTS_FORMAT=arguments/{job.id}.json
JOBS_MINIO_SERVICES_PATH=services/{job.service.bucket_name}
JOBS_MINIO_LOGS_BUCKET=

# RabbitMQ Settings
JOBS_RABBITMQ_URI=amqp://rabbitmquser:rabbitmqpassword@127.0.0.1:5672

# Package registry
REGISTRY_TOKEN=g_dka000111222333444

LOG_FORMAT=text

# Slack notifications
SLACK_TOKEN=
LOGGING_CHANNEL=

# Metadata API
TEST_METADATA_BASE_URL=http://127.0.0.1:8801
```

```json title="Goal.json"
{
    "JOBS_API_URL": "http://localhost:5000",
    "JOBS_BASE_URL": "http://localhost:8000",
    "SCRAPERS_BASE_URL": "http://localhost:9900",
    "JOBS_DATABASE_URI": "mongodb://mongouser:mongopassword@127.0.0.1:27017/app",
    "JOBS_REDIS_DSN": "redis://127.0.0.1:6000",
    "DATA_BASE_URL": "http://127.0.0.1:8900",
    "JOBS_MINIO_SECURE": "false",
    "JOBS_MINIO_ACCESS_KEY": "miniouser",
    "JOBS_MINIO_SECRET_KEY": "miniosecret",
    "JOBS_MINIO_HOST": "127.0.0.1:9500",
    "JOBS_MINIO_DEFAULT_BUCKET": "jobs",
    "JOBS_MINIO_REGION": "us-west-1",
    "JOBS_MINIO_CREATE_BUCKETS": "false",
    "JOBS_MINIO_RESULTS_FORMAT": "results/{job.id}.json",
    "JOBS_MINIO_ARGUMENTS_FORMAT": "arguments/{job.id}.json",
    "JOBS_MINIO_SERVICES_PATH": "services/{job.service.bucket_name}",
    "JOBS_MINIO_LOGS_BUCKET": "",
    "JOBS_RABBITMQ_URI": "amqp://rabbitmquser:rabbitmqpassword@127.0.0.1:5672",
    "REGISTRY_TOKEN": "g_dka000111222333444",
    "LOG_FORMAT": "text",
    "SLACK_TOKEN": "",
    "LOGGING_CHANNEL": "",
    "TEST_METADATA_BASE_URL": "http://127.0.0.1:8801"
}
```
