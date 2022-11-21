# Домашнее задание к занятию "10.02. Системы мониторинга"

## Обязательные задания

### Задание 1

#### Вопрос 

Опишите основные плюсы и минусы pull и push систем мониторинга.

#### Ответ

***PUSH***

Плюсы: удобство масштабирования, гибкость настройки метрик, экономия трафика за счёт использования более "лёгких" транспортных протоколов.

Минусы: нет гарантии подлинности метрик (потенциальная возможность получения метрик от недостоверного источника), возможность потерь части метрик во время недоступности системы мониторинга.   

***PULL***

Плюсы: контроль подлинности, возможность настройки передачи данных с гарантией безопасности, централизованное управление процессом получения данных с агентов.   

Минусы: большие трудозатраты в настройке при изменении инфраструктуры.

### Задание 2

#### Вопрос

Какие из ниже перечисленных систем относятся к push модели, а какие к pull? А может есть гибридные?

    - Prometheus 
    - TICK
    - Zabbix
    - VictoriaMetrics
    - Nagios

#### Ответ

- Prometheus: pull
- TICK: push
- Zabbix: push
- VictoriaMetrics: push
- Nagios: pull

### Задание 3

#### Вопрос

Склонируйте себе [репозиторий](https://github.com/influxdata/sandbox/tree/master) и запустите TICK-стэк, 
используя технологии docker и docker-compose.(по инструкции ./sandbox up )

В виде решения на это упражнение приведите выводы команд с вашего компьютера (виртуальной машины):

    - curl http://localhost:8086/ping
    - curl http://localhost:8888
    - curl http://localhost:9092/kapacitor/v1/ping

А также скриншот веб-интерфейса ПО chronograf (`http://localhost:8888`). 

P.S.: если при запуске некоторые контейнеры будут падать с ошибкой - проставьте им режим `Z`, например
`./data:/var/lib:Z`

#### Ответ

***Листинг curl***

![Скриншот](src/task3_1.png)

***Скриншот веб-интерфейса ПО chronograf***

![Скриншот](src/task3_2.png)


### Задание 4

#### Вопрос

Изучите список [telegraf inputs](https://github.com/influxdata/telegraf/tree/master/plugins/inputs).
    - Добавьте в конфигурацию telegraf плагин - [disk](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/disk):
    ```
    [[inputs.disk]]
      ignore_fs = ["tmpfs", "devtmpfs", "devfs", "iso9660", "overlay", "aufs", "squashfs"]
    ```
    - Так же добавьте в конфигурацию telegraf плагин - [mem](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mem):
    ```
    [[inputs.mem]]
    ```
    - После настройки перезапустите telegraf.
 
    - Перейдите в веб-интерфейс Chronograf (`http://localhost:8888`) и откройте вкладку `Data explorer`.
    - Нажмите на кнопку `Add a query`
    - Изучите вывод интерфейса и выберите БД `telegraf.autogen`
    - В `measurments` выберите mem->host->telegraf_container_id , а в `fields` выберите used_percent. 
    Внизу появится график утилизации оперативной памяти в контейнере telegraf.
    - Вверху вы можете увидеть запрос, аналогичный SQL-синтаксису. 
    Поэкспериментируйте с запросом, попробуйте изменить группировку и интервал наблюдений.
    - Приведите скриншот с отображением метрик утилизации места на диске (disk->host->telegraf_container_id) из веб-интерфейса.  

#### Ответ

***Скриншот с отображением метрик утилизации места на диске ***

![Скриншот](src/task4.png)

### Задание 5

#### Вопрос

Добавьте в конфигурацию telegraf следующий плагин - [docker](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/docker):
```
[[inputs.docker]]
  endpoint = "unix:///var/run/docker.sock"
```

Дополнительно вам может потребоваться донастройка контейнера telegraf в `docker-compose.yml` дополнительного volume и 
режима privileged:
```
  telegraf:
    image: telegraf:1.4.0
    privileged: true
    volumes:
      - ./etc/telegraf.conf:/etc/telegraf/telegraf.conf:Z
      - /var/run/docker.sock:/var/run/docker.sock:Z
    links:
      - influxdb
    ports:
      - "8092:8092/udp"
      - "8094:8094"
      - "8125:8125/udp"
```

После настройки перезапустите telegraf, обновите веб интерфейс и приведите скриншотом список `measurments` в 
веб-интерфейсе базы telegraf.autogen . Там должны появиться метрики, связанные с docker.

Факультативно можете изучить какие метрики собирает telegraf после выполнения данного задания.

#### Ответ

***Скриншот метрик docker***

![Скриншот](src/task5.png)
