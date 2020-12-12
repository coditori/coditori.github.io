---
title:  "Manage dev/prod config in pure Python"
date:   2020-12-12 19:00:40 +0330
tags:
  - deployment
  - python
  - ci
  - cd
toc: true
last_modified_at: 2020-12-12T19:00:40-03:30
---
I'd like to separate dev/test/prod environments :) so let's see the real example:

### Audience
Developers, DevOps Engineers, and SysAdmins.

### Challenges
- Put configs in a simple format (JSON, YAML, ...)
- Hide unnecessary config information from other envs

### Solution
I will put my configs in "etc" folder in the root of the project:
{% highlight yaml %}
├── config-dev.yml
├── config-prod.yml
└── config.yml
{% endhighlight %}

Then specify the current env inside "config.yml", this is my config.yml file content:
{% highlight yaml %}
# config.yml
profiles:
    active: dev
{% endhighlight %}

Now I want to read the redis configuration from "config-dev.yml":
{% highlight yaml %}
# config-dev.yml
redis:
    host: localhost
    port: 6379
    history-sub: history-subscriber
{% endhighlight %}

It's time to read these values inside python, this is my "config.py" file:
{% highlight python %}
# config.py
from loguru import logger

get = None
CONFIG_PATH_FROMAT = 'etc/config%s.yml'

def load():
    logger.add("logs/app.log", rotation="10 MB", compression="zip", level="INFO")
    active_profile = read(load_config(''))['profiles']['active']
    logger.info("Config active profile \"%s\" is loaded..." % active_profile)
    global get
    get = read(load_config(active_profile))


def read(path):
    with open(r'' + path) as file:
        return yaml.load(file, Loader=yaml.FullLoader)


def load_config(active_profile):
    if active_profile == '':
        return CONFIG_PATH_FROMAT % ''
    else:
        return CONFIG_PATH_FROMAT % ('-' + active_profile)
{% endhighlight %}


Now we need to load our config loader once then use it anywhere we like, add these lines to you main module:
{% highlight python %}
# main.py
from config import config

def main():
    config.load()
    redis_port = config.get['redis']['port']
    print('redis_port', redis_port)


if __name__ == "__main__":
    main()
{% endhighlight %}

The only issue is that you can not change the structure of "config.yml", and the other files structures just depend on you.