# devops-school-k8s-canary-deployment  
Пример работы canary deployment для k8s (тестировался на minikube)  

Применяем конфигурацию из yaml фалйла, отдельный namespace  
**kubectl apply -f canary_namespace.yaml**  

Применяем конфигурацию из yaml фалйла, configman для каждой весрии приложения:  
**kubectl apply -f canary_cm_nginx_v1.yaml**  
**kubectl apply -f canary_cm_nginx_v2.yaml**  

Применяем конфигурацию из yaml фалйла, отдельный depolyment для каждой версии приложения:  
**kubectl apply -f canary_dep_v1.yaml**  
**kubectl apply -f canary_dep_v2.yaml**  

Применяем конфигурацию из yaml фалйла, отдельный service для каждой версии приложения:  
**kubectl apply -f canary_svc_v1.yaml**  
**kubectl apply -f canary_svc_v1.yaml**  

Применяем конфигурацию из yaml фалйла, основной файл ingress:  
**kubectl apply -f canary_ingress-v1.yaml**  

Применяем конфигурацию из yaml фалйла, для работы сanary depoloy:  
По весу, т.е. проценту преселыемых запросов.  
**kubectl apply -f canary_ingress_weight.yaml**  
ИЛИ(OR)  
По заголовку:  
**kubectl apply -f canary_ingress_header.yaml**  
  
Примеры команд для проверки конфигурации.  
**kubectl get all -n k8s-canary -o wide** # Данные о pod, replicaset, service, deployment и service  
**kubectl get ingress -n k8s-canary** # Данные об экземплярах ingress  
**kubectl get configmaps -n k8s-canary -o wide** # Данные об configmap  
**kubectl describe service web-v1 -n k8s-canary** # Подробные данные о конкретной сущности, в данном случае service  с назнавание web-v1  
  
Проверка работы из minikube проводится обращением адрес minikube  
**curl $(minikube ip)**  
**curl $(minikube ip) -H "web: v2"**# по загаловку.
  
**ВАЖНО** 
Основная особенность "архитектуры":  
Есть основной ингресс (без наворотов). #canary_ingress-v1.yaml  
Cоздается второй ингресс с теми же host И path и с аннотациями для кенери. #canary_ingress_weight.yaml или canary_ingress_header.yaml  
Часть трафика направляется в новый кенери ингресс.  
! Если основного ингресса нет - тот, что с кенери аннотациями игнорируется !  
