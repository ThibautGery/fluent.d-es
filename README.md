Fluentd docker image with ES plugin
====================================

This container image is to create endpoint to collect logs on your host.

```
docker run -d -p 24224:24224 -v /data:/fluentd/log thibautgery/fluent.d-es
```

Default configurations are to:

* listen port `24224` for fluentd forward protocol
* store logs with tag `docker.**` into `/fluentd/log/docker.*.log` (and symlink `docker.log`)
* store all other logs into `/fluentd/log/data.*.log` (and symlink data.log)

## Configurable ENV variables

Environment variable below are configurable to control how to execute fluentd process:

### FLUENTD_CONF

It's for configuration file name, specified for `-c`.

If you want to use your own configuration file (without any optional plugins), you can use it over this ENV variable and -v option.

1. write configuration file with filename `yours.conf`
2. execute `docker run` with `-v /path/to/dir:/fluentd/etc` to share `/path/to/dir/yours.conf` in container, and `-e FLUENTD_CONF=yours.conf` to read it

### FLUENTD_OPT

Use this variable to specify other options, like `-v` or `-q`.

### conf example

```
<source>
  @type forward
</source>

<match nginx.docker.**>
  type parser
  format nginx
  remove_prefix nginx
  key_name log
  reserve_data yes
</match>

<match docker.**>
  type elasticsearch
  hosts http://your.host:9200
  logstash_format true
</match>

```
