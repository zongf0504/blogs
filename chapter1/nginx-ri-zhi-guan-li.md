# Nginx 日志管理

> 对Nginx 运维来讲, 日志管理是非常重要的. Nginx 的日志管理是比较强大的, 支持自定义访问日志格式, 自定义错误日志级别(八个日志级别), 支持日志缓存, 支持日志变量等, 但是却不支持日志自动分割, 这难免是nginx 的一个遗憾. 不过我们可以自己写脚本来进行定时切割.

## 1. nginx 日志格式
### 1.1 默认日志格式:
``` bash
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';
```