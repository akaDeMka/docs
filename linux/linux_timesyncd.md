# systemd-timesyncd

это служба, которая была добавлена для синхронизации системных часов по сети. По сути, эта служба реализует упрощенный клиент SNTP. В отличие от сложных реализаций NTP, systemd-timesyncd представляет только клиентскую часть, ориентируясь на запрос времени из одного удаленного сервера и синхронизации локальных часов с ним.

## Установка

**Для активации systemd-timesyncd и запуска выполните:**

```bash
timedatectl set-ntp true 
```

**Узнать текущее состояние службы:**

```bash
$ timedatectl status
      Local time: Sat 2015-11-14 22:20:38 MSK
  Universal time: Sat 2015-11-14 19:20:38 UTC
        RTC time: Sat 2015-11-14 19:20:38
       Time zone: Europe/Minsk (MSK, +0300)
Network time on: yes
NTP synchronized: yes
RTC in local TZ: no
```

## Настройка

При запуске systemd-timesyncd будет читать файл конфигурации /etc/systemd/timesyncd.conf, который выглядит так:

```bash
cat /etc/systemd/timesyncd.conf
[Time]
#NTP=
#FallbackNTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org
```

Чтобы добавить сервера времени или изменить предложенные, необходимо раскомментировать соответствующую строку со списком их имен хостов или IP, разделяемых пробелами. Например, вы можете использовать любые серверы, предоставляемые NTP pool project или использовать стандартные для Arch (также предусмотренные NTP pool project):

```bash
/etc/systemd/timesyncd.conf
[Time]
NTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org
FallbackNTP=0.pool.ntp.org 1.pool.ntp.org 0.fr.pool.ntp.org
```

Также NTP сервера могут быть предусмотрены в systemd-networkd конфигурации с опцией NTP= или динамически через DHCP сервер.
Используемый сервер NTP будет определяться по следующим правилам:

- Приоритетно - с любого интерфейса NTP серверов, полученных из конфигурации systemd-networkd.service(8) или через DHCP.
- Сервера NTP, указанные в /etc/systemd/timesyncd.conf будут добавлены в список интерфейса после получения ответа от серверов в процессе соединения с ними.
- Если после выполнения действий выше информация о серверах NTP не будет получена, то будет использоваться имя хоста и IP адреса, указанные в FallbackNTP=

><div style="background-color:pink">Важно: При каждой синхронизации служба перезаписывает файл в /var/lib/systemd/clock, который на данный момент захардкоден и не может быть изменен. В связи с этим могут возникнуть проблемы, если корневой раздел работает в режиме "только для чтения" или при попытки минимизации операций записи на SD-картах.</div>
