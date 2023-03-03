---
layout: post
title: Calico에서 Global Network Policy 설정
subtitle: K8s 서비스에 대한 특정 IP접속 제한 
categories: Kubernetes
tags: [K8s, calico]
---
#
## 개요
상용에 구축된 사이트 측에서 서비스(웹 포탈)에 인가된 IP만 접속을 허용하도록 해달라는 요청이 있었다. Calico의 Global Network Policy로 조치를 했는데 내용은 아래와 같다.  
#
##  Global Network Policy 적용하기

특정 서비스에만 정책을 적용할 수 있도록 레이블을 지정한다. 

#### [service-netpol.yaml]
```
apiVersion: projectcalico.org/v3
kind: GlobalNetworkPolicy
metadata:
  name: portal-netpol
spec:
  selector: app.kubernetes.io/name == 'portal'
  types:
    - Ingress
  ingress:
    - action: Allow
      protocol: TCP
      source:
        nets:
          - 192.168.16.50/32
          - 192.168.16.201/32
      destination:
        ports:
          - 8080
```
#### [정책 적용]
```
calicoctl apply -f service-netpol.yaml
```

#### [정책 확인]
```
# calicoctl get GlobalNetworkPolicy
NAME
portal-netpol
 ```
적용 확인 후, 외부에서 허용된 IP에서만 접속되는지 테스트 해본다.


