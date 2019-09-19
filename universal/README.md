

数据平面地址
```
# export IP=$(hostname -I | awk '{print $1}')
# export CP_URL=http://$IP:5682
# kuma-cp run -c kuma/conf/kuma-cp.conf
# echo "type: Mesh
name: bookinfo
mtls:
  enabled: false" | kumactl apply -f -
# kumactl get meshes
NAME       mTLS   DP ACCESS LOGS
default    off    off
bookinfo   off    off
```








服务ratings的端口配置信息如下。
```
ratings-v1: 
  inbound:
    ratings:
      port:   10000
      proxy:  10001
```

执行如下命令配置ratings-v1服务。
```
# # 以容器方式启动ratings-v1服务，并将内部9080端口映射为主机的10000端口。
# docker run -it -d -p 10000:9080 harbor.waret.net/bookinfo/examples-bookinfo-ratings-v1:0.1
# # 测试服务是否正常
# curl localhost:10000/ratings/0
# # 创建数据平面配置
# echo "type: Dataplane
mesh: bookinfo
name: ratings-v1
networking:
  inbound:
  - interface: $IP:10001:10000
    tags:
      app: bookinfo
      service: ratings
      version: v1" | kumactl apply -f -
# # 检查数据平面配置是否创建成功
# kumactl --mesh bookinfo inspect dataplanes
# # 启动数据平面代理
# export IP=$(hostname -I | awk '{print $1}')
# export CP_URL=http://$IP:5682
# KUMA_CONTROL_PLANE_BOOTSTRAP_SERVER_URL=$CP_URL \
    KUMA_DATAPLANE_MESH=bookinfo \
    KUMA_DATAPLANE_NAME=ratings-v1 \
    kuma-dp run --admin-port 9901
# curl $IP:10001/ratings/0
```

```
details-v1:
  inbound:
    detail:
      port:   10100
      proxy:  10101
```

执行如下命令配置details-v1服务。
```
# # 以容器方式启动details-v1服务，并将内部9080端口映射为主机的10100端口。
# docker run -it -d -p 10100:9080 harbor.waret.net/bookinfo/examples-bookinfo-details-v1:0.1
# # 测试服务是否正常
# curl $IP:10100/details/0
# # 创建数据平面配置
# echo "type: Dataplane
mesh: bookinfo
name: details-v1
networking:
  inbound:
  - interface: $IP:10101:10100
    tags:
      app: bookinfo
      service: details
      version: v1" | kumactl apply -f -
# # 检查数据平面配置是否创建成功
# kumactl --mesh bookinfo inspect dataplanes
# # 启动数据平面代理
# export IP=$(hostname -I | awk '{print $1}')
# export CP_URL=http://$IP:5682
# KUMA_CONTROL_PLANE_BOOTSTRAP_SERVER_URL=$CP_URL \
    KUMA_DATAPLANE_MESH=bookinfo \
    KUMA_DATAPLANE_NAME=details-v1 \
    kuma-dp run --admin-port 9902
# curl $IP:10101/details/0
```

```
reviews-v1:
  inbound:
    reviews:
      port:   10200
      proxy:  10201
  outbound:
    ratings:  10202
```

执行如下命令配置reviews-v1服务。
```
# # 以容器方式启动reviews-v1服务，并将内部9080端口映射为主机的10200端口。
# docker run -it -d -p 10200:9080 \
    -e RATINGS_HOSTNAME=$IP -e RATINGS_PORT=10202 \
    harbor.waret.net/bookinfo/examples-bookinfo-reviews-v1:0.1
# # 测试服务是否正常
# curl $IP:10200/reviews/0
# # 创建数据平面配置
# echo "type: Dataplane
mesh: bookinfo
name: reviews-v1
networking:
  inbound:
  - interface: $IP:10201:10200
    tags:
      app: bookinfo
      service: reviews 
      version: v1
  outbound:
  - interface: $IP:10202
    app: bookinfo
    service: ratings" | kumactl apply -f -
# # 检查数据平面配置是否创建成功
# kumactl --mesh bookinfo inspect dataplanes
# # 启动数据平面代理
# export IP=$(hostname -I | awk '{print $1}')
# export CP_URL=http://$IP:5682
# KUMA_CONTROL_PLANE_BOOTSTRAP_SERVER_URL=$CP_URL \
    KUMA_DATAPLANE_MESH=bookinfo \
    KUMA_DATAPLANE_NAME=reviews-v1 \
    kuma-dp run --admin-port 9903
# curl $IP:10201/reviews/0
```

```
reviews-v2:
  inbound:
    reviews:
      port:   10300
      proxy:  10301
  outbound:
    ratings:  10302
```

执行如下命令配置reviews-v2服务。
```
# # 以容器方式启动reviews-v2服务，并将内部9080端口映射为主机的10300端口。
# docker run -it -d -p 10300:9080 \
    -e RATINGS_HOSTNAME=$IP -e RATINGS_PORT=10302 \
    harbor.waret.net/bookinfo/examples-bookinfo-reviews-v2:0.1
# # 测试服务是否正常
# curl $IP:10300/reviews/0
# # 创建数据平面配置
# echo "type: Dataplane
mesh: bookinfo
name: reviews-v2
networking:
  inbound:
  - interface: $IP:10301:10300
    tags:
      app: bookinfo
      service: reviews 
      version: v2
  outbound:
  - interface: $IP:10302
    app: bookinfo
    service: ratings" | kumactl apply -f -
# # 检查数据平面配置是否创建成功
# kumactl --mesh bookinfo inspect dataplanes
# # 启动数据平面代理
# export IP=$(hostname -I | awk '{print $1}')
# export CP_URL=http://$IP:5682
# KUMA_CONTROL_PLANE_BOOTSTRAP_SERVER_URL=$CP_URL \
    KUMA_DATAPLANE_MESH=bookinfo \
    KUMA_DATAPLANE_NAME=reviews-v2 \
    kuma-dp run --admin-port 9904
# curl $IP:10301/reviews/0
```

```
reviews-v3:
  inbound:
    reviews:
      port:   10400
      proxy:  10401
  outbound:
    ratings:  10402
```

执行如下命令配置reviews-v3服务。
```
# # 以容器方式启动reviews-v3服务，并将内部9080端口映射为主机的10400端口。
# docker run -it -d -p 10400:9080 \
    -e RATINGS_HOSTNAME=$IP -e RATINGS_PORT=10402 \
    harbor.waret.net/bookinfo/examples-bookinfo-reviews-v3:0.1
# # 测试服务是否正常
# curl $IP:10400/reviews/0
# # 创建数据平面配置
# echo "type: Dataplane
mesh: bookinfo
name: reviews-v3
networking:
  inbound:
  - interface: $IP:10401:10400
    tags:
      app: bookinfo
      service: reviews 
      version: v3
  outbound:
  - interface: $IP:10402
    app: bookinfo
    service: ratings" | kumactl apply -f -
# # 检查数据平面配置是否创建成功
# kumactl --mesh bookinfo inspect dataplanes
# # 启动数据平面代理
# export IP=$(hostname -I | awk '{print $1}')
# export CP_URL=http://$IP:5682
# KUMA_CONTROL_PLANE_BOOTSTRAP_SERVER_URL=$CP_URL \
    KUMA_DATAPLANE_MESH=bookinfo \
    KUMA_DATAPLANE_NAME=reviews-v3 \
    kuma-dp run --admin-port 9905
# curl $IP:10401/reviews/0
```




```
productpage-v1:
  inbound:
    productpage:
      port:   10500
      proxy:  10501
  outbound:
    ratings:  10502
    details:  10503
    reviews:  10504
```

执行如下命令配置productpage-v1服务。
```
# # 以容器方式启动productpage-v1服务，并将内部9080端口映射为主机的10500端口。
# docker run -it -d -p 10500:9080 \
    -e RATINGS_HOSTNAME=$IP -e RATINGS_PORT=10502 \
    -e DETAILS_HOSTNAME=$IP -e DETAILS_PORT=10503 \
    -e REVIEWS_HOSTNAME=$IP -e REVIEWS_PORT=10504 \
    harbor.waret.net/bookinfo/examples-bookinfo-productpage-v1:0.1
# # 测试服务是否正常
# curl $IP:10500/productpage
# # 创建数据平面配置
# echo "type: Dataplane
mesh: bookinfo
name: productpage-v1
networking:
  inbound:
  - interface: $IP:10501:10500
    tags:
      app: bookinfo
      service: productpage
      version: v1
  outbound:
  - interface: $IP:10502
    app: bookinfo
    service: ratings
  - interface: $IP:10503
    app: bookinfo
    service: details
  - interface: $IP:10504
    app: bookinfo
    service: reviews" | kumactl apply -f -
# # 检查数据平面配置是否创建成功
# kumactl --mesh bookinfo inspect dataplanes
# # 启动数据平面代理
# export IP=$(hostname -I | awk '{print $1}')
# export CP_URL=http://$IP:5682
# KUMA_CONTROL_PLANE_BOOTSTRAP_SERVER_URL=$CP_URL \
    KUMA_DATAPLANE_MESH=bookinfo \
    KUMA_DATAPLANE_NAME=productpage-v1 \
    kuma-dp run --admin-port 9906
# curl $IP:10501/productpage
```


更新数据平面对象，修改bookinfo.reviews服务的本地端口。
```
# echo "type: Dataplane
mesh: bookinfo
name: productpage-v1
networking:
  inbound:
  - interface: $IP:10501:10500
    tags:
      app: bookinfo
      service: productpage
      version: v1
  outbound:
  - interface: $IP:10502
    app: bookinfo
    service: ratings
  - interface: $IP:10503
    app: bookinfo
    service: details
  - interface: $IP:10505
    app: bookinfo
    service: reviews" | kumactl apply -f -
```

查看对应数据平面服务的日志，可以看到新的配置更新生效。
```
[2019-09-18 17:16:10.988][32263][info][upstream] [external/envoy/source/server/lds_api.cc:42] lds: remove listener 'outbound:10.20.1.77:10504'
[2019-09-18 17:16:10.988][32263][info][upstream] [external/envoy/source/server/lds_api.cc:59] lds: add/update listener 'outbound:10.20.1.77:10505'
```



优势：
1. 轻量
2. 支持多个Mesh

未解决的问题
1. 不支持TrafficRoute、TrafficTracing等重要的功能，所以基本上Kuma还处于不可用状态；
2. 双向认证只支持内建的自签名证书，且只能是在Mesh范围内进行配置。
3. 由于不支持类似Istio中的Ingress、Egress等功能，当开启双向认证时，无法将服务对外发布出去，内部服务也无法访问外部的服务。
4. 为了支持在Universal模式下同时启动多个Envoy，不支持Envoy的热重启。不过，由于xDS配置都是热更新，所以影响并不大。
5. 在Universal模式下，不支持服务注册和发现，用户需要逐个为服务配置依赖应用的outbound入口，而且由于没有集成DNS，服务间访问需要指定到IP:Port，而不是像Istio一样指定Service Name即可。在Kubernetes模式下，则是依赖了Service的机制，可以将Hostname或Service Name解析为ClusterIP，随后发起的HTTP/TCP请求进入到Sidecar中以后再进行转发。
6. 服务启动的过程中尽量不要访问依赖的服务，此时可能由于Sidecar及数据平面未配置好，导致服务启动失败。
7. 查看Envoy的config_dump可以看到，当前都是以TCP连接的模式进行管理，完全没有发挥出Envoy的强大能力。

总结
当前阻碍服务网格技术应用的原因之一是对历史遗留系统的支持。通常来说，我们需要对老的系统经过两次改造，第一次是容器化，使之能够运行在Kubernetes上，第二次则是对RPC框架的拆解或完全替换。
如果服务网格能够支持非容器场景，则至少工作还能减少一半。我们知道Istio在从v1.3版本开始，开始支持网格扩展，也即将虚拟机或裸金属主机集成到部署在Kubernetes中的Isito集群中。当前支持两种方式，一种是单网络方式，也即虚拟机或裸金属主机通过VPN或VPC连接至Kubernetes内部，另外一种是多网络下通过入口网关实现通讯集成。目前来看，由于Istio本身对Kubernetes的依赖比较重，再加上Istio本身的其它功能都已经相对比较完善，要想增加网格扩展的功能，工作量是比较大的，所以这两种方式都还处于开发状态。
相对来说，Kuma则提供了一种在虚拟机或裸金属主机场景下使用服务网格的新思路，虽然当前功能完成度相对太低，不过还是值得大家持续关注。












```
# echo "type: TrafficRoute
name: route-1
mesh: bookinfo
rules:
  - sources:
      - match:
          app: bookinfo
          service: productpage
    destinations:
      - match:
          app: bookinfo
          service: reviews
    conf:
      - weight: 80
        destination:
          - service: reviews
            version: v1
      - weight: 10
        destination:
          - service: reviews
            version: v2
      - weight: 10
        destination:
          - service: backend
            version: v3" | kumactl apply -f -

```

