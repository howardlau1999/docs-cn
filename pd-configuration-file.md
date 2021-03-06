---
title: PD 配置文件描述
aliases: ['/docs-cn/dev/pd-configuration-file/','/docs-cn/dev/reference/configuration/pd-server/configuration-file/']
---

# PD 配置文件描述

<!-- markdownlint-disable MD001 -->

PD 配置文件比命令行参数支持更多的选项。你可以在 [conf/config.toml](https://github.com/pingcap/pd/blob/master/conf/config.toml) 找到默认的配置文件。

本文档只阐述未包含在命令行参数中的参数，命令行参数参见 [PD 配置参数](/command-line-flags-for-pd-configuration.md)。

### `name`

+ PD 节点名称。
+ 默认：`"pd"`
+ 如果你需要启动多个 PD，一定要给 PD 使用不同的名字。

### `data-dir`

+ PD 存储数据路径。
+ 默认：`"default.${name}"`

### `client-urls`

+ PD 监听的客户端 URL 列表。
+ 默认：`"http://127.0.0.1:2379"`
+ 如果部署一个集群，client URLs 必须指定当前主机的 IP 地址，例如 `"http://192.168.100.113:2379"`，如果是运行在 Docker 则需要指定为 `"http://0.0.0.0:2379"`。

### `advertise-client-urls`

+ 用于外部访问 PD 的 URL 列表。
+ 默认：`"${client-urls}"`
+ 在某些情况下，例如 Docker 或者 NAT 网络环境，客户端并不能通过 PD 自己监听的 client URLs 来访问到 PD，这时候，你就可以设置 advertise URLs 来让客户端访问。
+ 例如，Docker 内部 IP 地址为 `172.17.0.1`，而宿主机的 IP 地址为 `192.168.100.113` 并且设置了端口映射 `-p 2379:2379`，那么可以设置为 `advertise-client-urls="http://192.168.100.113:2379"`，客户端可以通过 `http://192.168.100.113:2379` 来找到这个服务。

### `peer-urls`

+ PD 节点监听其他 PD 节点的 URL 列表。
+ 默认: `"http://127.0.0.1:2380"`
+ 如果部署一个集群，peer URLs 必须指定当前主机的 IP 地址，例如 `"http://192.168.100.113:2380"`，如果是运行在 Docker 则需要指定为 `"http://0.0.0.0:2380"`。

### `advertise-peer-urls`

+ 用于其他 PD 节点访问某个 PD 节点的 URL 列表。
+ 默认：`"${peer-urls}"`
+ 在某些情况下，例如 Docker 或者 NAT 网络环境，其他节点并不能通过 PD 自己监听的 peer URLs 来访问到 PD，这时候，你就可以设置 advertise URLs 来让其他节点访问
+ 例如，Docker 内部 IP 地址为 `172.17.0.1`，而宿主机的 IP 地址为 `192.168.100.113` 并且设置了端口映射 `-p 2380:2380`，那么可以设置为 `advertise-peer-urls="http://192.168.100.113:2380"`，其他 PD 节点可以通过 `http://192.168.100.113:2380` 来找到这个服务。

### `initial-cluster`

+ 初始化 PD 集群配置。
+ 默认：`"{name}=http://{advertise-peer-url}"`
+ 例如，如果 name 是 "pd"，并且 `advertise-peer-urls` 是 `"http://192.168.100.113:2380"`，那么 `initial-cluster` 就是 `"pd=http://192.168.100.113:2380"`。
+ 如果启动三台 PD，那么 `initial-cluster` 可能就是
  `pd1=http://192.168.100.113:2380, pd2=http://192.168.100.114:2380, pd3=192.168.100.115:2380`。

### `initial-cluster-state`

+ 集群初始状态
+ 默认："new"

### `initial-cluster-token`

+ 用于在集群初始化阶段标识不同的集群。
+ 默认："pd-cluster"
+ 如果先后部署多个集群，且多个集群有相同配置的节点，应指定不同的 token 来隔离不同的集群。

### `lease`

+ PD Leader Key 租约超时时间，超时系统重新选举 Leader。
+ 默认：3
+ 单位：秒

### `tso-save-interval`

+ TSO 分配的时间窗口,实时持久存储。
+ 默认：3s

### `enable-prevote`

+ 开启 raft prevote 的开关。
+ 默认：true

### `quota-backend-bytes`

+ 元信息数据库存储空间的大小，默认 8GiB。
+ 默认：8589934592

### `auto-compaction-mod`

+ 元信息数据库自动压缩的模式，可选项为 periodic（按周期），revision（按版本数）。
+ 默认：periodic

### `auto-compaction-retention`

+ compaction-mode 为 periodic 时为元信息数据库自动压缩的间隔时间；compaction-mode 设置为 revision 时为自动压缩的版本数。
+ 默认：1h

### `force-new-cluster`

+ 强制让该 PD 以一个新集群启动，且修改 raft 成员数为 1。
+ 默认：false

### `tick-interval`

+ etcd raft 的 tick 周期。
+ 默认：100ms

### `election-interval`

+ etcd leader 选举的超时时间。
+ 默认：3s

### `use-region-storage`

+ 开启独立的 region 存储。
+ 默认：false

## security

安全相关配置项。

### `cacert-path`

+ CA 文件路径
+ 默认：""

### `cert-path`

+ 包含 X509 证书的 PEM 文件路径
+ 默认：""

### `key-path`

+ 包含 X509 key 的 PEM 文件路径
+ 默认：""

## log

日志相关的配置项。

### `format`

+ 日志格式，可指定为"text"，"json"， "console"。
+ 默认：text

### `disable-timestamp`

+ 是否禁用日志中自动生成的时间戳。
+ 默认：false

## log.file

日志文件相关的配置项。

### `max-size`

+ 单个日志文件最大大小，超过该值系统自动切分成多个文件。
+ 默认：300
+ 单位：MiB
+ 最小值为 1

### `max-days`

+ 日志保留的最长天数。
+ 默认: 28
+ 最小值为 1

### `max-backups`

+ 日志文件保留的最大个数。
+ 默认: 7
+ 最小值为 1

## metric

监控相关的配置项。

### `interval`

+ 向 promethus 推送监控指标数据的间隔时间。
+ 默认: 15s

## schedule

调度相关的配置项。

### `max-merge-region-size`

+ 控制 Region Merge 的 size 上限，当 Region Size 大于指定值时 PD 不会将其与相邻的 Region 合并。
+ 默认: 20

### `max-merge-region-keys`

+ 控制 Region Merge 的 key 上限，当 Region key 大于指定值时 PD 不会将其与相邻的 Region 合并。
+ 默认: 200000

### `patrol-region-interval`

+ 控制 replicaChecker 检查 Region 健康状态的运行频率，越短则运行越快，通常状况不需要调整
+ 默认: 100ms

### `split-merge-interval`

+ 控制对同一个 Region 做 split 和 merge 操作的间隔，即对于新 split 的 Region 一段时间内不会被 merge。
+ 默认: 1h

### `max-snapshot-count`

+ 控制单个 store 最多同时接收或发送的 snapshot 数量，调度受制于这个配置来防止抢占正常业务的资源。
+ 默认: 3

### `max-pending-peer-count`

+ 控制单个 store 的 pending peer 上限，调度受制于这个配置来防止在部分节点产生大量日志落后的 Region。
+ 默认：16

### `max-store-down-time`

+ PD 认为失联 store 无法恢复的时间，当超过指定的时间没有收到 store 的心跳后，PD 会在其他节点补充副本。
+ 默认：30m

### `leader-schedule-limit`

+ 同时进行 leader 调度的任务个数。
+ 默认：4

### `region-schedule-limit`

+ 同时进行 Region 调度的任务个数
+ 默认：2048

### `replica-schedule-limit`

+ 同时进行 replica 调度的任务个数。
+ 默认：64

### `merge-schedule-limit`

+ 同时进行的 Region Merge 调度的任务，设置为 0 则关闭 Region Merge。
+ 默认：8

### `high-space-ratio`

+ 设置 store 空间充裕的阈值。
+ 默认：0.7
+ 最小值：大于 0
+ 最大值：小于 1

### `low-space-ratio`

+ 设置 store 空间不足的阈值。
+ 默认：0.8
+ 最小值：大于 0
+ 最大值：小于 1

### `tolerant-size-ratio`

+ 控制 balance 缓冲区大小。
+ 默认：0 (为 0 为自动调整缓冲区大小)
+ 最小值：0

### `disable-remove-down-replica`

+ 关闭自动删除 DownReplica 的特性的开关，当设置为 true 时，PD 不会自动清理宕机状态的副本。
+ 默认：false

### `disable-replace-offline-replica`

+ 关闭迁移 OfflineReplica 的特性的开关，当设置为 true 时，PD 不会迁移下线状态的副本。
+ 默认：false

### `disable-make-up-replica`

+ 关闭补充副本的特性的开关，当设置为 true 时，PD 不会为副本数不足的 Region 补充副本。
+ 默认：false

### `disable-remove-extra-replica`

+ 关闭删除多余副本的特性开关，当设置为 true 时，PD 不会为副本数过多的 Region 删除多余副本。
+ 默认：false

### `disable-location-replacement`

+ 关闭隔离级别检查的开关，当设置为 true 时，PD 不会通过调度来提升 Region 副本的隔离级别。
+ 默认：false

### `store-balance-rate`

+ 控制 TiKV 每分钟最多允许做 add peer 相关操作的次数。
+ 默认：15

## replication

副本相关的配置项。

### `max-replicas`

+ 所有副本数量，即 leader 与 follower 数量之和。默认为 `3`，即 1 个 leader 和 2 个 follower。
+ 默认：3

### `location-labels`

+ TiKV 集群的拓扑信息。
+ 默认：[]
+ [配置集群拓扑](/schedule-replicas-by-topology-labels.md)

### `isolation-level`

+ TiKV 集群的最小强制拓扑隔离级别。
+ 默认：""
+ [配置集群拓扑](/schedule-replicas-by-topology-labels.md)

### `strictly-match-label`

+ 打开强制 TiKV Label 和 PD 的 localtion-labels 是否匹配的检查
+ 默认：false

### `enable-placement-rules`

+ 打开 `placement-rules`
+ 默认：false
+ 参考[Placement Rules 使用文档](/configure-placement-rules.md)
+ 4.0 实验性特性

## label-property

标签相关的配置项。

### `key`

+ 拒绝 leader 的 store 带有的 label key。
+ 默认：""

### `value`

+ 拒绝 leader 的 store 带有的 label value。
+ 默认：""

## dashboard

PD 中内置的 [TiDB Dashboard](/dashboard/dashboard-intro.md) 相关配置项。

### `tidb-cacert-path`

+ CA 根证书文件路径。可配置该路径来使用 TLS 连接 TiDB 的 SQL 服务。
+ 默认值：""

### `tidb-cert-path`

+ SSL 证书文件路径。可配置该路径来使用 TLS 连接 TiDB 的 SQL 服务。
+ 默认值：""

### `tidb-key-path`

+ SSL 私钥文件路径。可配置该路径来使用 TLS 连接 TiDB 的 SQL 服务。
+ 默认值：""

### `public-path-prefix`

+ 通过反向代理访问 TiDB Dashboard 时，配置反向代理提供服务的路径前缀。
+ 默认："/dashboard"
+ 若不通过反向代理访问 TiDB Dashboard，**请勿配置该项**，否则可能导致 TiDB Dashboard 无法正常访问。关于该配置的详细使用场景，参见[通过反向代理使用 TiDB Dashboard](/dashboard/dashboard-ops-reverse-proxy.md)。

### `enable-telemetry`

+ 是否启用 TiDB Dashboard 遥测功能。
+ 默认：true
+ 参阅[遥测](/telemetry.md)了解该功能详情。
