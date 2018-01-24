
```yaml
apiVersion: v1 
  kind: ReplicationController                # 副本控制器 RC
  metadata:
    name: mysql                              # RC 的名称, 全局唯一
  spec:                                    # RC 的相关属相
    replicas: 1                              # Pod 副本的期待数量 1
    selector:
      app: mysql                             # 副本目标的Pod 拥有此标签
    template:
      matedata:
        labels:
          app: mysql                         # Pod 副本拥有的标签
      spec:
        containers:                          # Pod 内容的定义
          - 
            name: myysql
            images: mysql
            ports:
              - 
                containerPort: 3306
            env:
              -
                name: MYSQL_ROOT_PASSWORD
                value: "123456"
```            
  