先安装自定义资源crd 两个

然后运行




添加仓库 

helm repo add traefik https://helm.traefik.io/traefik
helm repo update



2、将traefik包下载到本地进行管理
$ helm  search repo traefik
NAME           	CHART VERSION	APP VERSION	DESCRIPTION                                  
traefik/traefik	10.6.0       	2.5.3      	A Traefik based Kubernetes ingress controller
$ helm pull traefik/traefik

这个时候已经下载了一份tgz 包
traefik-10.6.2.tgz

配置自定义资源 value.yaml  --> my-value.yaml


部署命令
$ kubectl create ns traefik-ingress
$ helm install traefik -n traefik-ingress -f my-value.yaml  traefik-10.6.2.tgz

默认安装在 default
$ helm install traefik1111 -n default -f my-value.yaml  traefik-10.6.2.tgz
# 命令解释： 安装 traefik   名称是：traefik1111  -n 是指命名空间 default
-f 加载附加配置参数 my-value.yaml   traefik-10.6.2.tgz 代表安装的指定的文件包



开启 dashbard 

使用的是 ingressRoutes 的路由模式


编辑dashboard.yaml
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: traefik-dashboard
spec:
  routes:
  - match: Host(`traefik.ronnie.com`)
    kind: Rule
    services:
    - name: api@internal
      kind: TraefikService


此类参考 使用的是非ingress 方案，是ingressrouters方案

# dashboard.yaml
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: dashboard
spec:
  entryPoints:
    - web
  routes:
    - match: Host(`traefik.localhost`) && (PathPrefix(`/dashboard`) || PathPrefix(`/api`))
      kind: Rule
      services:
        - name: api@internal
          kind: TraefikService




30091 成功版本

## 命名空间要和所需要转发的 部署应用的命名空间一致

apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  # name: simpleingressroute
  name: traefik-ingress
## 命名空间要和所需要转发的 部署应用的命名空间一致
  namespace: default
spec:
  entryPoints:
    - webvue
  routes:
  - match: PathPrefix(`/`)
    kind: Rule
    services:
    ## services 的名字是指服务的名字
    - name: vuetest1128-nodeport
      port: 30090







