1. Создадим файл конфигурации [configmap.yaml](./configmap.yaml), где указки значения переменных `REACT_APP_USERNAME` и `REACT_APP_COMPANY_NAME`. 
2. Создадим файл конфигурации [deployment.yaml](./deployment.yaml), в который создает объект `ReplicaSet` с 2 репликами контейнера `ifilyaninitmo/itdt-contained-frontend:master`
3. Запустим minikube
```bash
minikube start
```
4. Добавим созданные файлы в среду 
```bash
minikube kubectl -- apply -f configmap.yaml
```
```bash 
minikube kubectl -- apply -f deployment.yaml
```
5. Для управления входящим трафиком в создаваемом кластере необходимо включить `Ingress`
```bash
minikube addons enable ingress
```
6. Создадим TLS-сертификат, где зададим такие параметры как `rsa` - ключ шифрования, `days` срок действия сертификата в виде количества дней. 
```bash
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out MyCertificate.crt -keyout MyKey.key -subj "/CN=litvinova-itmo.com"
```
7. Добавим созданые файлы для сертификата в среду, поскольку в п.6 данной работы файлы MyKey.key и MyCertificate.crt создаются только в рабочей директории.
```bash
minikube kubectl -- create secret tls app-crt --key MyKey.key --cert MyCertificate.crt
```
8. Для создания объекта `Ingress` необходимо создать сервис, аналогично п.5 лабораторной работы 2
```bash
minikube kubectl -- expose replicaset l3-deployment --port=3000 --name=l3-service --type=ClusterIP
```
9. Создадим файл [ingress.yaml](./ingress.yaml)
10. Добавим созданный файл в среду 
```bash
minikube kubectl -- apply -f ingress.yaml
```
11. Чтобы настроить `hosts` необходимо узнать используемый средой ip-адрес.
```bash 
minikube ip
```
а затем добавить в директорию /etc/host/ своё FQDN-имя и ip-адрес
![image](./images/ip.png)
12. При переходе на `litvinova-itmo.com` получаем следующее:
![image](./images/litvinova-itmo.png)
13. Проверим TLS-сертификат
![image](./images/crt.png)
14. Схема организации контейнеров и сервисов
