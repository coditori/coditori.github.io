---
title:  "Manage dev/prod config in Python"
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
- put configs in a simple format (JSON, YAML, ...)
- hide unnecessary config information from other envs

### Solution
I will put my configs in "etc" folder in the root of the project then specify the current env inside "config.yml", this is my config.yml file content:
{% highlight yaml %}
profiles:
    active: dev
{% endhighlight %}

And this is my "config-dev.yml" file which I want to read the redis configuration from:
{% highlight yaml %}
redis:
    host: localhost
    port: 6379
    history-sub: history-subscriber
{% endhighlight %}

It's time to read these values inside python, this is my "config.py":
{% highlight python %}
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


Now we can need to load out config code once then use it anywhere we like, add these lines to you main module:
{% highlight python %}
from config import config

def main():
    config.load()
    redis_port = config.get['redis']['port']
    print('redis_port', redis_port)


if __name__ == "__main__":
    main()
{% endhighlight %}