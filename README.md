# prostef_microservices
prostef microservices repository

## Docker-2
- Инфициализирован репозиторий и Travis CI
- Установлен Docker
- Протестировано создание и запуск контейнеров
- Протестирован запуск команд внутри контейнеров
- Протестировано создание образов из контейнеров
- При помощи docker-machine создан экземпляр ВМ в связке с GCE
- Создан файл Dockerfile с описанием образа с необходимыми приложениями
- Создан образ и запушен в Docker-hub под учеткой prostef

## Docker-3
- Приложение разбито на 3 компонента: сервис для постов, сервис для комментариев и веб-интерфейс, работающий с другими сервисами
- Так же для приложения была добавлена БД MongoDB
- Собраны образы всех сервисов
- Запущены все сервисы и протестирована их работа
- Для избежания потери данных создан том reddit_db и последущем запуске сервиса БД этот том был подключен к нему
- Протестирована окончальная работа всего приложения

## Docker-4
- Рассмотрена и протестировна работа с разными драйверами сети Docker
- Установлен Docker-compose, при помощи которого можно легко на yml описать запуск нескольких контейнеров по определенному сценацию
- Создан и параметаризирован docker-compose.yml
- Значения параметров заданы в файле .env в этой же директории
- Базовое имя проекта тоже можно переопределить при помщи переменной COMPOSE_PROJECT_NAME в файле .env

## GitLab CI
- При помощи gcloud создан инстанс: gcloud compute instances create "gitlab-ci" \
        --image-family="ubuntu-1604-lts" \
        --image-project=ubuntu-os-cloud \
        --machine-type="n1-standard-1" \
        --boot-disk-size="100" \
        --zone="europe-west1-b" \
        --tags="default-allow-http,default-allow-ssh,http-server,https-server"
- Установлен Docker на инстнс
- Подготовлено окружение и при помощи Docker-compose поднят GitLab на инстансе
- Произведена регистрация аккаунта в GitLab
- Локальная ветка gitlab-ci-1 связана с удаленным репозиторием на инстансе и запушена туда
- В GitLab добавлен Runner для запуска пайплайнов
- В пайплайн добавлено тестирование и разделение по окружениям
- Проверена работа пайплайна

## Monitoring-1
- Созданы правила фаервола для Prometheus и Puma
- Создан новый экземпляр ВМ docker-host
- На docker-host установлен и проверен Prometheus
- Переупорядочена структура репозитория и убраны инструкции build из docker-compose.yml
- Создан образ Prometheus
- Пересозбраны образы микросервисов ui, post-py и comment
- Подняты все сервисы и протестирована их работоспособность
- Протестирована обратная связь Prometheus если останавливать/запускать отдельные сервисы
- Добавлен новый сервис node-exporter и протестирована его работа
- Все образы запушины в DockerHub https://hub.docker.com/u/prostef

## Monitoring-2
- Добавлен cAdvisor и выделен в отдельную сборку сервисов наблюдения вместе с Prometheus
- Создано правило файервола cadvior-default открывающее порт 8080 для сервиса cAdvisor
- Протестирована работа cAdvisor
- Добавлена Grafana и выделена в отдельную сборку сервисов наблюдения вместе с Prometheus
- Создано правило файервола grafana-default открывающее порт 3000 для сервиса Grafana
- Протестирована работа в Grafana
- Созданы дашборды в Grafana и экспортированы в виде json файлов в проект
- Добавлен Alertmanager и для него сделана интеграция со Slack
- Пересобраны образы с запуском сервиса алертинга
- Проверена работоспособность пушей в случае если какой то из сервисов остановить
- Все образы запушины в DockerHub https://hub.docker.com/u/prostef

## Logging-1
- Обновлены и пересобраны микросервисы ui, post-py и comment
- Собран и сконфигурирован образ fluentd
- Протестирована работа в веб интерфейсе kibana
- Протестирован парсинг неструктурированных логов
- Добавлен и опубликован сервис Zipkin
- Протестирована работа в Zipkin

## Kubernetes-1
- Создана дирректория для манифестов и дальнейшей работы с kubernetes
- Создано 4 Deployment манифеста приложений
- Начата подготовка и установка kubernetes по технологии the hard way
- Скачены и установлены утилиты cfssl, cfssljson и kubectl
- В GCP создана виртуальная частная облачная сеть и правила файервола
- В GCP создан публичный IP-адрес Кубернетеса
- В GCP созданы инстансы контроллеров и воркеров
- При помощи утилит cfssl, cfssljson созданы дополнительные сертификаты TLS и добавлены в GCP для инстансов
- При помощи утилиты kubectl созданы конфиги для рабочих узлов и добавлены в GCP для инстансов
- Создан ключ шифрования, который потом записан в файл encryption-config.yaml
- encryption-config.yaml добавлен на все контроллеры в GCP
- На каждый контроллер скачены etcd сервер и утилита etcdctl
- На каждом контроллере запущен etcd
- На каждый контрполлер скачан, установлен и настроен kubernetes
- На кадлый контроллер поставлен ngnix для балансировки трафика
- В GCP создан балансер нагрузки внешнего интерфейса Kubernetes
- На каждый из воркеров установлены: socat, conntrack, ipset
- На каждом из воркеров выключен своп
- На каждом из воркеров настроена сеть CNI
- На каждом из воркеров настроены и запущены службы: containerd, kubelet и kube-proxy
- Создан kubeconfig для утилиты kubectl на основе adminучетных данных пользователя
- В GCP созданы сетевые маршруты для каждого из воркеров
- Развернута надстройка DNS, которая обеспечивает обнаружение службы на основе DNS при поддержке CoreDNS для приложений, работающих в кластере Kubernetes
- Выполнен смок тест чтобы убедиться что кластер Kubernetes работает нормально
- Проверено, что kubectl apply -f [ui-deployment.yml / post-deployment.yml / mongo-deployment.yml / comment-deployment.yml] проходит и поды запускаются
- Произведена очистка всех созданых сущностей в GCP

## Kubernetes-2
- Установлен и запущен minikube с --vm-driver=virtualbox
- Созданые отдельные деплойменты и сервисы для разных частей приложения
- Протестирована работа сервиса в другом неймспейсе
- Создан кластер в GCP
- В GCP кластере создан dev namespace
- В GCP кластер задеплоены все компоненты приложения в namespace dev
- В GCP создано правило брандмауэра для доступа приложения во внешнем мире
- Протестирована доступность и работоспособность приложения (сделан скриншот приложения)

## Kubernetes-3
- У ui service задан тип LoadBalancer
- После небольшого ожидания проверена доступность приложения по адресу, на который распределил балансировщик
- Создан Ingress контроллер для сервиса ui
- Проверена работа приложения с использованием Ingress балансировки
- Убран LoadBalancer так как уже используется Ingress
- Сгенерирован сертификат и загружен в кластер kubernetes
- Поставлен доступ на ui только по https
- Для кластера включена Network Policy
- Создана mongo-network-policy
- Создан диск в Google Cloud и он же указан как volume у mongo service
- Протестировано, что удалении и установке mongo сервиса данные никуда не теряются
- Создан PersistentVolume для mongo и он же добавлен в кластер
- Создан запрос на выделения ресурсов PersistentVolumeClaim для PersistentVolume
- Создан StorageClass для динамического выделения volume'ов
- Создан запрос на выделения ресурсов PersistentVolumeClaim для StorageClass
- Для mongo service применено использование StorageClass
- Протестировано, что все работает

## Kubernetes-4
- Установлен Helm
- Создан и исполнен манифест для tiller
- Создан Chart для Heml со структурой приложения
- Шаблонизированы темплейты ui сервиса
-
