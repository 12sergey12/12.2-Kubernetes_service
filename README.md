### Домашнее задание к занятию «Базовые объекты K8S»Баранов Сергей

### Цель задания

В тестовой среде для работы с Kubernetes, установленной в предыдущем ДЗ, необходимо развернуть Pod с приложением и подключиться к нему со своего локального компьютера. 

------

### Чеклист готовности к домашнему заданию

1. Установленное k8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключенным Git-репозиторием.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Описание [Pod](https://kubernetes.io/docs/concepts/workloads/pods/) и примеры манифестов.
2. Описание [Service](https://kubernetes.io/docs/concepts/services-networking/service/).

------

### Задание 1. Создать Pod с именем hello-world

1. Создать манифест (yaml-конфигурацию) Pod.

[pod-echo-default.yaml](https://github.com/12sergey12/12.2-Kubernetes_service/blob/main/images/12.2-1.3_curl.png)

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: echo
  name: pod-hello-world
  namespace: default
spec:
  containers:
  - image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
    imagePullPolicy: IfNotPresent
    name: hello-world

```

2. Использовать image - gcr.io/kubernetes-e2e-test-images/echoserver:2.2.

Создание pod:
```
root@baranov:/home/baranovsa/kube-1.2# kubectl apply -f pod-echo-default.yaml
pod/pod-with-echoserver created
root@baranov:/home/baranovsa/kube-1.2#
```
```
root@baranov:/home/baranovsa/kube-1.2# kubectl get pods
NAME                  READY   STATUS    RESTARTS   AGE
pod-hello-world       1/1     Running   0          18s
root@baranov:/home/baranovsa/kube-1.2#

```

3. Подключиться локально к Pod с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

```
root@baranov:/home/baranovsa/kube-1.2# kubectl port-forward pod/pod-hello-world 30000:8080
Forwarding from 127.0.0.1:30000 -> 8080
Forwarding from [::1]:30000 -> 8080
Handling connection for 30000
Handling connection for 30000

```
```
root@baranov:/home/baranovsa# curl localhost:30000/


Hostname: pod-with-echoserver

Pod Information:
	-no pod information available-

Server values:
	server_version=nginx: 1.12.2 - lua: 10010

Request Information:
	client_address=127.0.0.1
	method=GET
	real path=/
	query=
	request_version=1.1
	request_scheme=http
	request_uri=http://localhost:8080/

Request Headers:
	accept=*/*  
	host=localhost:30000  
	user-agent=curl/7.74.0  

Request Body:
	-no body in request-

root@baranov:/home/baranovsa# 
```
![monitoring](https://github.com/12sergey12/12.2-Kubernetes_service/blob/main/images/12.2-1.3_curl.png)

------

### Задание 2. Создать Service и подключить его к Pod

1. Создать Pod с именем netology-web.

[pod-netology-web-default.yaml](https://github.com/12sergey12/12.2-Kubernetes_service/blob/main/images/12.2-1.3_curl.png)

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: echo
  name: pod-netology-web
  namespace: default
spec:
  containers:
  - image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
    imagePullPolicy: IfNotPresent
    name: hello-world

---
apiVersion: v1
kind: Service
metadata:
  name: netology-svc
spec:
  ports:
    - name: web
      port: 80
      protocol: TCP
      targetPort: 8080
  selector:
    app: echo
```

2. Использовать image — gcr.io/kubernetes-e2e-test-images/echoserver:2.2.

3. Создать Service с именем netology-svc и подключить к netology-web.

```
root@baranov:/home/baranovsa/kube-1.2# kubectl apply -f pod-netology-web-default.yaml
pod/pod-netology-web created
service/netology-svc created
root@baranov:/home/baranovsa/kube-1.2#
```

```
root@baranov:/home/baranovsa/kube-1.2# kubectl get pods
NAME               READY   STATUS    RESTARTS   AGE
pod-hello-world    1/1     Running   0          25m
pod-netology-web   1/1     Running   0          23s
root@baranov:/home/baranovsa/kube-1.2#
```

4. Подключиться локально к Service с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

```
root@baranov:/home/baranovsa/kube-1.2# kubectl port-forward service/netology-svc 30000:80
Forwarding from 127.0.0.1:30000 -> 8080
Forwarding from [::1]:30000 -> 8080
Handling connection for 30000
Handling connection for 30000

```
```
root@baranov:/home/baranovsa/12.2-Kubernetes_service# curl localhost:30000/


Hostname: pod-hello-world

Pod Information:
	-no pod information available-

Server values:
	server_version=nginx: 1.12.2 - lua: 10010

Request Information:
	client_address=127.0.0.1
	method=GET
	real path=/
	query=
	request_version=1.1
	request_scheme=http
	request_uri=http://localhost:8080/

Request Headers:
	accept=*/*  
	host=localhost:30000  
	user-agent=curl/7.74.0  

Request Body:
	-no body in request-

root@baranov:/home/baranovsa/12.2-Kubernetes_service#
```

![monitoring](https://github.com/12sergey12/12.2-Kubernetes_service/blob/main/images/12.2-1.3_curl.png)


```
root@baranov:/home/baranovsa/12.2-Kubernetes_service# curl 10.152.183.209


Hostname: pod-netology-web

Pod Information:
	-no pod information available-

Server values:
	server_version=nginx: 1.12.2 - lua: 10010

Request Information:
	client_address=10.0.2.15
	method=GET
	real path=/
	query=
	request_version=1.1
	request_scheme=http
	request_uri=http://10.152.183.209:8080/

Request Headers:
	accept=*/*  
	host=10.152.183.209  
	user-agent=curl/7.74.0  

Request Body:
	-no body in request-

root@baranov:/home/baranovsa/12.2-Kubernetes_service# curl 10.152.183.209


Hostname: pod-hello-world

Pod Information:
	-no pod information available-

Server values:
	server_version=nginx: 1.12.2 - lua: 10010

Request Information:
	client_address=10.0.2.15
	method=GET
	real path=/
	query=
	request_version=1.1
	request_scheme=http
	request_uri=http://10.152.183.209:8080/

Request Headers:
	accept=*/*  
	host=10.152.183.209  
	user-agent=curl/7.74.0  

Request Body:
	-no body in request-

root@baranov:/home/baranovsa/12.2-Kubernetes_service# 


```

![monitoring](https://github.com/12sergey12/12.2-Kubernetes_service/blob/main/images/12.2-1.3_curl.png)

![monitoring]()



------

### Правила приёма работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода команд `kubectl get pods`, а также скриншот результата подключения.
3. Репозиторий должен содержать файлы манифестов и ссылки на них в файле README.md.

------

### Критерии оценки
Зачёт — выполнены все задания, ответы даны в развернутой форме, приложены соответствующие скриншоты и файлы проекта, в выполненных заданиях нет противоречий и нарушения логики.

На доработку — задание выполнено частично или не выполнено, в логике выполнения заданий есть противоречия, существенные недостатки.
