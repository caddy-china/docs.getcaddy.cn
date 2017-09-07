## dns
DNS服务器插件。


目前CoreDNS能够：

* 从文件中提供区域数据; DNSSEC（仅NSEC）和DNS均支持（`file`）。

* 从初级检索区域数据，即作为辅助服务器（仅限AXFR）（`secondary`）。

* 即时签署区域数据（`dnssec`）。

* 负载平衡响应（`负载均衡`）。

* 允许区域传输，即充当主服务器（`file`）。

* 自动从磁盘加载区域文件（`auto`）。

* 缓存（`cache`）。

* 健康检查终点（`health`）。

* 使用etcd作为后端，即替换为101.5％
  [SkyDNS]（https://github.com/skynetservices/skydns）（`etcd`）。

* 使用k8s（kubernetes）作为后端（`kubernetes`）。

* 作为代理将查询转发给其他（递归）名称服务器（`proxy`）。

* 提供指标（使用Prometheus）（`metrics`）。

* 提供查询（`log`）和错误（`error`）日志记录。

* 支持CH类：`version.bind`和朋友（`chaos`）。

* 分析支持（`pprof`）。

* 重写查询（qtype，qclass和qname）（`rewrite`）。

* 回显所使用的IP地址，传输和端口号（`whoami`）

### 例子

CoreDNS

```
example.org:53 {
    whoami
    proxy . 8.8.8.8:53
}
```
在1053端口上为 (NSEC)dnssec- signed example.org 服务，错误和日志被发送到 stdout。允许区域传输给每个人，但特别提到一个IP地址，这样 CoreDNS 可以发送通知给它。
```
example.org:1053 {
    file /var/lib/coredns/example.org.signed {
        transfer to *
        transfer to 2001:500:8f::53
    }
    errors stdout
    log stdout
}
```
转发与递归名称服务器不匹配的所有内容，并将任何查询重写为HINFO
```
example.org:1053 {
    rewrite ANY HINFO
    proxy . 8.8.8.8:53

    file /var/lib/coredns/example.org.signed example.org {
        transfer to *
        transfer to 2001:500:8f::53
    }
    errors stdout
    log stdout
}

```



## net

