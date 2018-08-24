
# 配置从如下项目copy并已放在gitlab文件夹下(有删改)，感谢:
[github.com/sameersbn/docker-gitlab](https://github.com/sameersbn/docker-gitlab)
> **注**:生产环境请使用持久化存储部署
---
1.部署Namespace
---
```bash
kubectl apply -f gitlab-ns.yaml 
```
---
2.部署Redis
---
```bash
kubectl create -f redis-svc.yaml 
kubectl create -f redis-rc.yaml 
```
---
4.部署PostgreSQL
---
```bash
kubectl create -f postgresql-svc.yaml 
kubectl create -f postgresql-rc.yaml 
```
---
5.部署GitLab
---
+ 创建 ConfigMap
> **注**:[sameersbn]项目中的关于gitlab.rb的变量并不能载入Pod，只能使用configmap载入pod
---
```bash
kubectl -n gitlab create configmap gitlab-rb --from-file=gitlab.rb
kubectl -n gitlab describe configmap gitlab-rb
```
---
+ 部署
- `GITLAB_SECRETS_OTP_KEY_BASE` 用于加密数据库中的2FA秘密。如果您丢失或轮换此密码，则您的所有用户都无法使用2FA登录。
- `GITLAB_SECRETS_DB_KEY_BASE` 用于加密数据库中的CI密钥变量以及导入凭证。如果丢失或轮换此秘密，您将无法使用现有的CI秘密。
- `GITLAB_SECRETS_SECRET_KEY_BASE` 用于密码重置链接和其他“标准”身份验证功能。如果丢失或轮换此密码，电子邮件中的密码重置令牌将重置。

> **注**: 可以使用`pwgen -Bsv1 64`生成随机字符串并将其指定为如上值.

---
```bash
# kubectl create -f gitlab-svc.yaml 
# kubectl create -f gitlab-rc.yaml
# kubectl -n gitlab get pod
NAME               READY     STATUS    RESTARTS   AGE
gitlab-jmwbr       1/1       Running   0          1h
postgresql-k9p4s   1/1       Running   0          1h
redis-rxlqc        1/1       Running   0          1h
# kubectl -n gitlab get services
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)      AGE
gitlab       LoadBalancer   10.244.85.204    <pending>     80:41008/TCP,22:30270/TCP,9090:34210/TCP   1h
postgresql   ClusterIP      10.244.130.245   <none>        5432/TCP                                   1h
redis        ClusterIP      10.244.158.130   <none>        6379/TCP                                   1h
```
