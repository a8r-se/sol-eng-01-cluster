# sol-eng-01-cluster
Ambassador Edge Stack is deployed via Helm 3

**Resources**
Hostname:    sol-eng-01
Capacity:
  cpu:                8
  ephemeral-storage:  98868448Ki
  hugepages-2Mi:      0
  memory:             32888700Ki
  pods:               255
Allocatable:
  cpu:                8
  ephemeral-storage:  96179226139
  hugepages-2Mi:      0
  memory:             32888700Ki
  pods:               255


**Online Boutique and Telepresence**
Please note that this cluster is leveraging ArgoCD and Argo Rollouts.
All Online Boutique deployments are configured to use the Telepresence Mutating Webhook  

**Namespaces**
~~~
ambassador
boutique
emojivoto
test
argocd
argo-rollouts
monitoring
~~~

**Hosts**
~~~
NAMESPACE    NAME                                 HOSTNAME                             STATE
ambassador   emojivoto-dev.se1.mturner.k736.net   emojivoto-dev.se1.mturner.k736.net   Ready 
ambassador   emojivoto.se1.mturner.k736.net       emojivoto.se1.mturner.k736.net       Ready
ambassador   monitoring.se1.mturner.k736.net      monitoring.se1.mturner.k736.net      Ready
ambassador   argocd.se1.mturner.k736.net          argocd.se1.mturner.k736.net          Ready
ambassador   boutique-dev.se1.mturner.k736.net    boutique-dev.se1.mturner.k736.net    Ready
ambassador   boutique.se1.mturner.k736.net        boutique.se1.mturner.k736.net        Ready
ambassador   se1.mturner.k736.net                 se1.mturner.k736.net                 Ready
~~~


**Mappings**
~~~
NAMESPACE    NAME                          SOURCE HOST                      SOURCE PREFIX                               DEST SERVICE                         
emojivoto    emojivoto-mapping             emojivoto.se1.mturner.k736.net   /                                           web-svc.emojivoto:80                       
argocd       argocd-server                 argocd.se1.mturner.k736.net      /                                           https://argocd-server.argocd:443           
monitoring   prometheus                                                     /prometheus/                                prometheus.monitoring:9090                 
monitoring   grafana                                                        /grafana/                                   grafana.monitoring:80                      
monitoring   jaeger-ui                                                      /jaeger/                                    jaeger-query:16686                         
boutique     frontend                      boutique.se1.mturner.k736.net    /                                           frontend.boutique                          
test         hello                                                          /hello/                                     hello.test:80                              
test         quote-backend                                                  /backend/                                   quote-backend.test:80                      
ambassador   ambassador-devportal                                           /documentation/                             127.0.0.1:8500                             
ambassador   ambassador-devportal-assets                                    /documentation/(assets|styles)/(.*)(.css)   127.0.0.1:8500                             
ambassador   ambassador-devportal-demo                                      /docs/                                      127.0.0.1:8500                             
ambassador   ambassador-devportal-api                                       /openapi/                                   127.0.0.1:8500                             
~~~



**Deployments**
~~~
NAMESPACE      NAME                   CONTAINERS              IMAGES                                             SELECTOR
test           hello                  hello                   docker.io/datawire/hello-world:latest              app=hello
monitoring     prometheus-operator    prometheus-operator     quay.io/coreos/prometheus-operator:v0.41.0 app.kubernetes.io/component=controller,app.kubernetes.io/name=prometheus-operator  

emojivoto      web                    web-svc                 docker.l5d.io/buoyantio/emojivoto-web:v11         app=web-svc,version=v11
kube-system    local-path-provisioner local-path-provisioner  rancher/local-path-provisioner:v0.0.11            app=local-path-provisioner
kube-system    metrics-server          metrics-server         rancher/metrics-server:v0.3.6                     k8s-app=metrics-server
emojivoto      vote-bot                vote-bot               docker.l5d.io/buoyantio/emojivoto-web:v11         app=vote-bot,version=v11
test           quote-backend           backend                docker.io/datawire/quote:0.4.1                    app=quote
emojivoto      emoji                   emoji-svc              docker.l5d.io/buoyantio/emojivoto-emoji-svc:v11   pp=emoji-svc,version=v11
argo-rollouts  argo-rollouts           argo-rollouts          quay.io/argoproj/argo-rollouts:v1.0.1             app.kubernetes.io/name=argo-rollouts
monitoring     jaeger-operator          aeger-operator        jaegertracing/jaeger-operator:1.19.0              name=jaeger-operator
ambassador     ambassador-agent        agent                  docker.io/datawire/aes:1.14.1           app.kubernetes.io/instance=ambassador,app.kubernetes.io/name=ambassador-agent  

argocd         argocd-dex-server       dex                    ghcr.io/dexidp/dex:v2.27.0                        app.kubernetes.io/name=argocd-dex-server
boutique       checkoutservice         server                 agervais/checkoutservice:v0.3.1                   app=checkoutservice
ambassador     ambassador-redis        redis                  redis:5.0.1         app.kubernetes.io/instance=ambassador,app.kubernetes.io/name=ambassador-redis  

ambassador     traffic-manager         traffic-manager        docker.io/datawire/tel2:2.4.2                     app=traffic-manager,telepresence=manager
argocd         argocd-repo-server      argocd-repo-server     quay.io/argoproj/argocd:v2.0.4                    app.kubernetes.io/name=argocd-repo-server
monitoring     jaeger                  jaeger                 jaegertracing/all-in-one:1.19.2                   app=jaeger,app.kubernetes.io/component=all-in-one,app.kubernetes.io/instance=jaeger,app.kubernetes.io/managed-by=jaeger-operator,app.kubernetes.io/name=jaeger,app.kubernetes.io/part-of=jaeger  

boutique       productcatalogservice   server                 agervais/productcatalogservice:v0.3.1             app=productcatalogservice
emojivoto      voting                  voting-svc             docker.l5d.io/buoyantio/emojivoto-voting-svc:v11  app=voting-svc,version=v11
kube-system    coredns                 coredns                rancher/coredns-coredns:1.6.9                     k8s-app=kube-dns
argocd         argocd-redis            redis                  redis:6.2.4-alpine                                app.kubernetes.io/name=argocd-redis
boutique       shippingservice         server                 agervais/shippingservice:v0.3.1                   app=shippingservice
boutique       redis-cart              redis                  redis:alpine                                      app=redis-cart
argocd         argocd-server           argocd-server          quay.io/argoproj/argocd:v2.0.4                    app.kubernetes.io/name=argocd-server
boutique       emailservice             erver                 agervais/emailservice:v0.3.1                      app=emailservice
boutique       recommendationservice   server                 agervais/recommendationservice:v0.3.1             app=recommendationservice
boutique       frontend                server                 agervais/frontend:v0.3.1                          app=frontend
boutique       paymentservice          server                 agervais/paymentservice:v0.3.1                    app=paymentservice
monitoring     grafana                 grafana-core           grafana/grafana:5.0.4                             app=grafana,component=core
boutique       currencyservice         server                 agervais/currencyservice:v0.3.1                   app=currencyservice
ambassador     ambassador              ambassador             docker.io/datawire/aes:1.14.1                      app.kubernetes.io/instance=ambassador,app.kubernetes.io/name=ambassador  

monitoring     otel-collector          otel-collector         otel/opentelemetry-collector-contrib:0.11.0       app=opentelemetry,component=otel-collector
boutique       adservice               server                 agervais/adservice:v0.3.1                         app=adservice
boutique       cartservice             server                 agervais/cartservice:v0.3.1                       app=cartservice
~~~

**Service**
~~~
NAMESPACE       NAME                        TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                           SELECTOR
default         kubernetes                  ClusterIP      10.43.0.1       <none>          443/TCP                                           <none>
kube-system     kube-dns                    ClusterIP      10.43.0.10      <none>          53/UDP,53/TCP,9153/TCP                            k8s-app=kube-dns
kube-system     metrics-server              ClusterIP      10.43.220.93    <none>          443/TCP                                           k8s-app=metrics-server
test            quote-backend               ClusterIP      10.43.193.25    <none>          80/TCP,443/TCP                                    app=quote
argocd          argocd-dex-server           ClusterIP      10.43.187.230   <none>          5556/TCP,5557/TCP,5558/TCP                           app.kubernetes.io/name=argocd-dex-server
  
argocd          argocd-metrics              ClusterIP      10.43.186.214   <none>          8082/TCP                                              app.kubernetes.io/name=argocd-application-controller
  
argocd          argocd-redis                ClusterIP      10.43.237.95    <none>          6379/TCP                                            app.kubernetes.io/name=argocd-redis
  
argocd          argocd-repo-server          ClusterIP      10.43.217.170   <none>          8081/TCP,8084/TCP                                     app.kubernetes.io/name=argocd-repo-server
  
argocd          argocd-server-metrics       ClusterIP      10.43.50.42     <none>          8083/TCP                                        app.kubernetes.io/name=argocd-server
  
argo-rollouts   argo-rollouts-metrics       ClusterIP      10.43.155.16    <none>          8090/TCP                                      app.kubernetes.io/name=argo-rollouts
  
monitoring      prometheus                  ClusterIP      10.43.193.34    <none>          9090/TCP                                          prometheus=prometheus
monitoring      otel-collector              ClusterIP      10.43.146.253   <none>          55680/TCP,14250/TCP,14268/TCP,9411/TCP,8888/TCP   component=otel-collector
kube-system     kubelet                     ClusterIP      None            <none>          10250/TCP,10255/TCP,4194/TCP                      <none>
monitoring      jaeger-operator-metrics     ClusterIP      10.43.108.63    <none>          8383/TCP,8686/TCP                                 name=jaeger-operator
monitoring      prometheus-operated         ClusterIP      None            <none>          9090/TCP                                          app=prometheus
monitoring      jaeger-agent                ClusterIP      None            <none>          5775/UDP,5778/TCP,6831/UDP,6832/UDP                   app.kubernetes.io/component=all-in-one,app.kubernetes.io/instance=jaeger,app.kubernetes.io/managed-by=jaeger-operator,app.kubernetes.io/name=jaeger,app.kubernetes.io/part-of=jaeger,app=jaeger
  
monitoring      jaeger-collector-headless   ClusterIP      None            <none>          9411/TCP,14250/TCP,14267/TCP,14268/TCP               app.kubernetes.io/component=all-in-one,app.kubernetes.io/instance=jaeger,app.kubernetes.io/managed-by=jaeger-operator,app.kubernetes.io/name=jaeger,app.kubernetes.io/part-of=jaeger,app=jaeger
  
monitoring      jaeger-collector            ClusterIP      10.43.247.232   <none>          9411/TCP,14250/TCP,14267/TCP,14268/TCP               app.kubernetes.io/component=all-in-one,app.kubernetes.io/instance=jaeger,app.kubernetes.io/managed-by=jaeger-operator,app.kubernetes.io/name=jaeger,app.kubernetes.io/part-of=jaeger,app=jaeger
  
monitoring      jaeger-query                ClusterIP      10.43.227.206   <none>          16686/TCP                                             app.kubernetes.io/component=all-in-one,app.kubernetes.io/instance=jaeger,app.kubernetes.io/managed-by=jaeger-operator,app.kubernetes.io/name=jaeger,app.kubernetes.io/part-of=jaeger,app=jaeger
  
monitoring      prometheus-operator         ClusterIP      None            <none>          8080/TCP                                             app.kubernetes.io/component=controller,app.kubernetes.io/name=prometheus-operator
  
monitoring      grafana                     ClusterIP      10.43.103.58    <none>          80/TCP                                                app=grafana,component=core
argocd          argocd-server               LoadBalancer   10.43.227.40    35.226.19.56    80:31531/TCP,443:30936/TCP                            app.kubernetes.io/name=argocd-server
  
test            hello                       ClusterIP      10.43.104.126   <none>          80/TCP                                            app=hello
ambassador      traffic-manager             ClusterIP      None            <none>          8081/TCP                                          app=traffic-manager,telepresence=manager
ambassador      agent-injector              ClusterIP      10.43.177.103   <none>          443/TCP                                           app=traffic-manager,telepresence=manager
boutique        adservice                   ClusterIP      10.43.68.54     <none>          9555/TCP                                          app=adservice
boutique        cartservice                 ClusterIP      10.43.177.8     <none>          7070/TCP                                          app=cartservice
boutique        checkoutservice             ClusterIP      10.43.235.58    <none>          5050/TCP                                          app=checkoutservice
boutique        currencyservice             ClusterIP      10.43.205.230   <none>          7000/TCP                                          app=currencyservice
boutique        emailservice                ClusterIP      10.43.28.79     <none>          5000/TCP                                          app=emailservice
boutique        frontend                    ClusterIP      10.43.40.19     <none>          80/TCP                                            app=frontend
boutique        paymentservice              ClusterIP      10.43.228.97    <none>          50051/TCP                                         app=paymentservice
boutique        productcatalogservice       ClusterIP      10.43.15.26     <none>          3550/TCP                                             app=productcatalogservice
  
boutique        recommendationservice       ClusterIP      10.43.147.181   <none>          8080/TCP                                             app=recommendationservice
  
boutique        redis-cart                  ClusterIP      10.43.91.120    <none>          6379/TCP                                          app=redis-cart
boutique        shippingservice             ClusterIP      10.43.14.84     <none>          50051/TCP                                         app=shippingservice
emojivoto       voting-svc                  ClusterIP      10.43.1.6       <none>          8080/TCP,8801/TCP                                 app=voting-svc
emojivoto       emoji-svc                   ClusterIP      10.43.223.139   <none>          8080/TCP,8801/TCP                                 app=emoji-svc
emojivoto       web-svc                     ClusterIP      10.43.228.81    <none>          80/TCP                                            app=web-svc
ambassador      ambassador-admin            ClusterIP      10.43.75.80     <none>          8877/TCP,8005/TCP                                    app.kubernetes.io/instance=ambassador,app.kubernetes.io/name=ambassador
  
ambassador      ambassador-redis            ClusterIP      10.43.114.156   <none>          6379/TCP                                              app.kubernetes.io/instance=ambassador,app.kubernetes.io/name=ambassador-redis
  
ambassador      ambassador                  LoadBalancer   10.43.142.202   35.224.39.220   80:32623/TCP,443:31221/TCP                          app.kubernetes.io/instance=ambassador,app.kubernete
~~~
