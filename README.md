# Домашнее задание к занятию "`10.3. Pacemaker`" - `Барановский Станислав`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

## Задание 1

Опишите основные функции и назначение Pacemaker.

*Приведите ответ в свободной форме.*
```
```
Pacemaker - это менеджер ресурсов кластера со следующими основными функциями:
- позволяет находить и устранять сбои на уровне нод и служб;
- не зависит от подсистемы хранения: общий диск не требуется;
- не зависит от типов ресурсов: все, что можно прописать в скрипты, можно кластеризовать;
- поддерживает STONITH (Shoot-The-Other-Node-In-The-Head), то есть умершая нода изолируется и запросы к ней не поступают, пока нода не отправит сообщение о том, что она снова в рабочем состоянии;
- поддерживает кворумные и ресурсозависимые кластеры любого размера;
- поддерживает практически любую избыточную конфигурацию;
- может автоматически реплицировать конфиг на все узлы кластера - не придется править все вручную;
- можно задать порядок запуска ресурсов, а также их совместимость на одном узле;
- поддерживает расширенные типы ресурсов: клоны (когда ресурс запущен на множестве узлов) и дополнительные состояния (master/slave и подобное) - актуально для СУБД (MySQL, MariaDB, PostgreSQL, Oracle);
- имеет единую кластерную оболочку CRM с поддержкой скриптов.

---

## Задание 2

Опишите основные функции и назначение Corosync.

*Приведите ответ в свободной форме.*
```
```
Corosync (Corosync Cluster Engine) - программный продукт, позволяющий создавать единый кластер из нескольких аппаратных или виртуальных серверов.
Его основное назначение — знать и передавать состояние всех участников кластера.
Основные возможности:
- мониторить статус приложений;
- оповещать приложения о смене активной ноды в кластере;
- отправлять идентичные сообщения процессам на всех нодах;
- предоставлять доступ к общей базе данных с конфигурацией и статистикой;
- отправлять уведомления об изменениях, произведенных в базе.

---

## Задание 3

Соберите модель, состоящую из двух виртуальных машин. Установите Pacemaker, Corosync, Pcs. Настройте HA кластер.

*Пришлите скриншот рабочей конфигурации и состояния сервиса для каждого нода.*
```
sudo apt install -y pacemaker corosync pcs
sudo nano /etc/hosts # Прописываем DNS суфиксы для каждой ноды. Записи представлены ниже
sudo passwd hacluster
sudo pcs host auth nodeone nodetwo # Настраиваем аутентификацию (на одной ноде)
sudo systemctl start pcsd.service
sudo systemctl status pcsd.service
#sudo pcs cluster destroy
#sudo pcs cluster auth
sudo pcs cluster setup newCluster nodeone nodetwo 
sudo service --status-all
sudo pcs cluster enable --all
sudo systemctl enable pcsd.service
sudo systemctl enable corosync.service
sudo systemctl enable pacemaker.service
sudo systemctl start corosync.service
sudo systemctl start pacemaker.service
sudo systemctl stauts pcsd.service
sudo systemctl status corosync.service
sudo systemctl status pacemaker.service
sudo pcs status

```
Добавляем в /etc/hosts DNS суфиксы для каждой ноды и на каждой ноде
```
192.168.56.12 nodeone
192.168.56.11 nodetwo
192.168.57.11 nodeone
192.168.57.12 nodetwo
```
![Скриншот рабочей конфигурации и состояние сервиса для первой ноды](https://github.com/StanislavBaranovskii/10-3-hw-pacemaker/blob/main/img/10-3-3-1.png "Скриншот рабочей конфигурации и состояние сервиса для первой ноды")
![Скриншот рабочей конфигурации и состояние сервиса для второй ноды](https://github.com/StanislavBaranovskii/10-3-hw-pacemaker/blob/main/img/10-3-3-2.png "Скриншот рабочей конфигурации и состояние сервиса для второй ноды")

---

## Задание 4*

Установите и настройте DRBD-сервис для настроенного кластера.

*Пришлите скриншот рабочей конфигурации и состояние сервиса для каждого нода.*
```
sudo apt install drbd-utils
sudo modprobe drbd
sudo nano /etc/modules  # Добаляем строку drbd
sudo lsmod |grep drbd
#Добавляем к ВМ накопитель и подготавливаем диск
ls /dev |grep sd 
sudo fdisk /dev/sdb
pvcreate /dev/sdb1
vgcreate vg0 /dev/sdb1
lvcreate -L3G -n www vg0
lvcreate -L3G -n mysql vg0
#Создаем конфиг файлы /etc/drbd.d/www.res и /etc/drbd.d/mysql.res
#Команды на обоих нодах:
drbdadm create-md www
drbdadm create-md mysql
drbdadm up all
#Команды на первой ноде:
drbdadm primary --force www
drbdadm primary --force mysql
#Команды на второй ноде:
drbdadm secondary www
#Подключаем разделы и проверяем репликацию на обоих нодах
mkdir /mnt/www
mkdir /mnt/mysql
mount /dev/drbd0 /mnt/www
mount /dev/drbd2 /mnt/mysql
```
Содержимое конфиг файлов:
```
resource www {
   protocol C;
   disk {
      fencing resource-only;
   }
   handlers {
      fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
      after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
   }
   syncer {
      rate 110M;
   }
   on nodeTwo
   {
      device /dev/drbd2;
      disk /dev/vg0/www;
      address 192.168.56.2:7794;
      meta-disk internal;
   }
   on nodeOne
   {
      device /dev/drbd2;
      disk /dev/vg0/www;
      address 192.168.56.1:7794;
      meta-disk internal;
   }
}
```
![Скриншот рабочей конфигурации и состояние сервиса для каждого нода](https://github.com/StanislavBaranovskii/10-3-hw-pacemaker/blob/main/img/10-3-4.png "Скриншот рабочей конфигурации и состояние сервиса для каждого нода")

---
