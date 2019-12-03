# 将Secret和ConfigMap以文件的形式挂载到容器

# 一、Context

ConfigMap或者Secret在默认挂载到容器是以Volumes的形式，如果挂载路径下原有的其他文件，则会覆盖掉。
如果将挂载路径直接写成文件的绝对路径，这会在挂载路径下创建以文件名为名字的文件夹，文件会在这个文件夹下

```yaml
containers:
  - image: 'busybox:latest'
    name: test
    volumeMounts:
      - mountPath: /etc/test/test.txt
        name: test-volume
volumes:
  - name: test-volume
    secret:
      defaultMode: 420
      secretName: test-secret
```

# 二、操作

挂载Secret或Config类型的volume时，添加一个subPath字段即可，可将其以文件的形式挂载，而不是以目录的形式。如下：

```yaml
containers:
  - image: 'busybox:latest'
    name: test
    volumeMounts:
      - mountPath: /etc/test/test.txt
        name: test-volume
        readOnly: true
        subPath: test.txt
volumes:
  - name: test-volume
    secret:
      defaultMode: 420
      secretName: test-secret
```

**secret**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name:test-secret
type: Opaque
data:
 test.txt: >-
    ************************ 
```
此时，secret中的test.txt文件将会单个文件的形式挂载到/etc/test/目录下
