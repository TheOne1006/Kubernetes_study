# kubectrl 用法概述

```bash
$ kubectrl [command] [TYPE] [NAME] [flags]
```
1. command 子命令, 用于操作 Kubernetes 集群资源对象的命令, 例如: create/delete/describe/get/apply 等
2. TYPE 资源对象类型， 区分小大小写，能以单数形式,复数形式或者简写表示
3. NAME 资源对象名称，区分大小写, 如不指定， 则返回所有属于 TYPE 的全部对象。