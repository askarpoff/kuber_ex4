# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 1»

### Задание 1. Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера

1. Создать Deployment приложения, состоящего из двух контейнеров (nginx и multitool), с количеством реплик 3 шт.
2. Создать Service, который обеспечит доступ внутри кластера до контейнеров приложения из п.1 по порту 9001 — nginx 80, по 9002 — multitool 8080.
3. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложения из п.1 по разным портам в разные контейнеры.
4. Продемонстрировать доступ с помощью `curl` по доменному имени сервиса.
5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

------

### Задание 2. Создать Service и обеспечить доступ к приложениям снаружи кластера

1. Создать отдельный Service приложения из Задания 1 с возможностью доступа снаружи кластера к nginx, используя тип NodePort.
2. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.
3. Предоставить манифест и Service в решении, а также скриншоты или вывод команды п.2.

------
## Ответ:

### Задание 1. Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера

1. Создать Deployment приложения, состоящего из двух контейнеров (nginx и multitool), с количеством реплик 3 шт.
```yaml
apiVersion : apps/v1
kind: Deployment
metadata:
  name: deployment1
  labels:
    app: deployment1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deployment1
  template:
    metadata:
      labels:
        app: deployment1
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
        - name: multitool
          image: wbitt/network-multitool
          ports:
            - containerPort: 8080
          env:
            - name: HTTP_PORT
              value: "8080"
```
2. Создать Service, который обеспечит доступ внутри кластера до контейнеров приложения из п.1 по порту 9001 — nginx 80, по 9002 — multitool 8080.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: service1
spec:
  ports:
  - name: nginx
    port: 9001
    targetPort: 80
  - name: multitoolhttp
    port: 9002
    targetPort: 8080

```
```bash
root@learning-k8s:~/kuber_ex4/task1# kubectl apply -f deployment.yaml
deployment.apps/deployment1 created
root@learning-k8s:~/kuber_ex4/task1# kubectl apply -f service.yaml
service/service1 created
root@learning-k8s:~/kuber_ex4/task1# kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
deployment1-59896ddb88-p9dcq   2/2     Running   0          40s
deployment1-59896ddb88-dwd8r   2/2     Running   0          40s
deployment1-59896ddb88-2qmkn   2/2     Running   0          40s
root@learning-k8s:~/kuber_ex4/task1# kubectl get services
NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service1   ClusterIP   10.152.183.240   <none>        9001/TCP,9002/TCP   28s
```
3. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложения из п.1 по разным портам в разные контейнеры.
![image](https://github.com/askarpoff/kuber_ex4/assets/108946489/31a8b114-ccaa-46d5-a511-965733857efa)

4. Продемонстрировать доступ с помощью `curl` по доменному имени сервиса.
![image](https://github.com/askarpoff/kuber_ex4/assets/108946489/a5776b7c-1b0e-47a3-a7a8-f529d106723f)
6. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

<a href='https://github.com/askarpoff/kuber_ex4/blob/main/task1/deployment.yaml'>task1/deployment.yaml</a>

<a href='https://github.com/askarpoff/kuber_ex4/blob/main/task1/service.yaml'>task1/service.yaml</a>

------

### Задание 2. Создать Service и обеспечить доступ к приложениям снаружи кластера

1. Создать отдельный Service приложения из Задания 1 с возможностью доступа снаружи кластера к nginx, используя тип NodePort.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: service2
spec:
  ports:
  - name: nginx
    port: 9001
    targetPort: 80
  type: NodePort
  selector:
    app: deployment1
```
![image](https://github.com/askarpoff/kuber_ex4/assets/108946489/3906570d-3a4e-4a79-a3ba-5eb6d30d22d6)

3. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.
 ![image](https://github.com/askarpoff/kuber_ex4/assets/108946489/68fec045-d32d-4563-908c-0b6e46841c55)
  
4. Предоставить манифест и Service в решении, а также скриншоты или вывод команды п.2.
<a href='https://github.com/askarpoff/kuber_ex4/blob/main/task2/service.yaml'>task2/service.yaml</a>
См.выше.
