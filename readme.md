# HW8: Kubernetes Deployment & Service

## **Підготовка середовища**

Перехід у директорію проєкту:
```sh
cd HW8-deployment-service/
ls
```

Зупиняємо Docker та запускаємо MicroK8s:
```sh
sudo systemctl stop docker.service 
sudo microk8s start
```

Перевіряємо статус MicroK8s:
```sh
sudo microk8s status
```

## **Створення та застосування простору імен**

Редагуємо та застосовуємо `namespace`:
```sh
mcedit namespase.yaml
sudo microk8s.kubectl apply -f namespase.yaml
```

Перевіряємо наявність простору імен:
```sh
sudo microk8s.kubectl get namespaces
```
**Вивід:**
```
NAME              STATUS   AGE
cert-manager      Active   22d
default           Active   23d
gitea             Active   22d
hw8-namespace     Active   17s
ingress           Active   22d
kube-node-lease   Active   23d
kube-public       Active   23d
kube-system       Active   23d
```

Детальна інформація про `namespace`:
```sh
sudo microk8s.kubectl describe namespace hw8-namespace
```

## **Створення та розгортання Pod**

Редагуємо та застосовуємо pod:
```sh
mcedit hw8-pod.yaml
sudo microk8s.kubectl apply -f hw8-pod.yaml -n hw8-namespace
```

Перевіряємо pod у просторі імен:
```sh
sudo microk8s.kubectl get pods -n hw8-namespace
```
**Вивід:**
```
NAME      READY   STATUS    RESTARTS   AGE
hw8-pod   1/1     Running   0          13s
```

Опис pod:
```sh
sudo microk8s.kubectl describe pod hw8-pod -n hw8-namespace
```

## **Перевірка доступності pod через curl**
```sh
curl 10.1.243.242
```
**Вивід:**
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</head>
</html>
```

## **Створення та застосування сервісу**

```sh
mcedit service.yaml
sudo microk8s.kubectl apply -f service.yaml -n hw8-namespace
```

Перевіряємо створений сервіс:
```sh
sudo microk8s.kubectl get services -n hw8-namespace
```
**Вивід:**
```
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
hw8-service   ClusterIP   10.152.183.76   <none>        80/TCP    12s
```

Отримуємо `endpoints` сервісу:
```sh
sudo microk8s.kubectl get endpoints hw8-service -n hw8-namespace
```

## **Створення та застосування Deployment**

Редагуємо та застосовуємо deployment:
```sh
mcedit deployment.yaml
sudo microk8s.kubectl apply -f deployment.yaml -n hw8-namespace
```

Перевіряємо створені `deployments`:
```sh
sudo microk8s.kubectl get deployments -n hw8-namespace
```
**Вивід:**
```
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
hw8-deployment   3/3     3            3           11s
```

Опис deployment:
```sh
sudo microk8s.kubectl describe deployment hw8-deployment -n hw8-namespace
```

## **Масштабування Deployment**

### **Збільшення реплік до 5**
```sh
sudo microk8s.kubectl scale deployment hw8-deployment --replicas=5 -n hw8-namespace
```
Перевіряємо pod-и:
```sh
sudo microk8s.kubectl get pods -n hw8-namespace
```
**Вивід:**
```
NAME                              READY   STATUS    RESTARTS   AGE
hw8-deployment-75448f45cf-cfbl9   1/1     Running   0          3s
hw8-deployment-75448f45cf-ggc6l   1/1     Running   0          97s
hw8-deployment-75448f45cf-kwrdn   1/1     Running   0          97s
hw8-deployment-75448f45cf-s9fz5   1/1     Running   0          97s
hw8-deployment-75448f45cf-zjlr2   1/1     Running   0          3s
```

### **Зменшення реплік до 2**
```sh
sudo microk8s.kubectl scale deployment hw8-deployment --replicas=2 -n hw8-namespace
```
Перевіряємо pod-и:
```sh
sudo microk8s.kubectl get pods -n hw8-namespace
```
**Вивід:**
```
NAME                              READY   STATUS    RESTARTS   AGE
hw8-deployment-75448f45cf-ggc6l   1/1     Running   0          116s
hw8-deployment-75448f45cf-kwrdn   1/1     Running   0          116s
