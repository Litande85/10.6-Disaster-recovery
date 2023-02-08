# Домашнее задание к занятию 10.6 «Disaster recovery» -  `Елена Махота`

- [Ответ к Заданию 1](#1)
- [Ответ к Заданиям 2 - 3*](#2)

---

### Задание 1

В чём разница между DRaaS, BaaS, Active-Active, Active-Passive?

*Приведите ответ в свободной форме.*


### *<a name="1">Ответ к Заданию 1</a>*


Сравнение DRaaS, BaaS, Active-Active, Active-Passive

| **Решения для восстановления** | **DRaaS**                                           | **BaaS**                                                                                                                                   | **Active-Active**                                                | **Active-Passive**                                                                                          |
|--------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| **Параметры**                  |                                                     |                                                                                                                                            |                                                                  |                                                                                                              |
| **Конфигурация**               | Поддержка кластеров любого размера в облаке         | Облачный ресурс для хранения резервных копий  - хранятся в сжатом состоянии и не требуют сложной инфраструктуры, достаточно одного сервера | НА-кластер, в котором все серверы активны (включены)             | НА-кластер, в котором есть активные узлы (включены) и есть узлы пассивные (выключены или в режиме ожидания)  |
| **Время DR**                   | моментальное восстановление функционирования        | более длинное время восстановления, RTO и RPO                                                                                              | Моментальное (время на переключение на другой работающий сервер) | Может потребоваться некоторое время для включения в работу резервного сервера                                |
| **Результат**                  | Резервирование всего сервиса на горячую             | Копия данных                                                                                                                               | Резервирование всего сервиса на горячую                          | Копия данных                                                                                                 |
| **Стоимость**                  | Дорого                                              | Не дорого                                                                                                                                  | Дорого                                                           | Дешевле active                                                                                               |
| **Потеря данных**             | Не допустима                                        | Допустима                                                                                                                                  | Не допустима                                                     | Допустима                                                                                                    |
| **Частота копирования**        | Непрерывно                                          | По расписанию                                                                                                                              | Непрерывно                                                       | Непрерывно                                                                                                   |
| **Актуальность данных**       | На момент аварии, краткосрочное хранение данных     | На любой момент времени в пределах хранения, долгосрочное хранение данных                                                                  | На момент аварии, краткосрочное хранение данных                  | На момент аварии или незадолго до аварии, краткосрочное хранение данных                                                              |
| **Аспекты управления**         | Управляются третьей стороной - облачным провайдером | Управляются третьей стороной - облачным провайдером                                                                                        | Могут управляться владельцем данных или привлеченными лицами     | Могут управляться владельцем данных или привлеченными лицами                                                 |



Использованные источники:

\- [Кратко о HA моделях Active-Active и Active-Passive]( https://forum.huawei.com/enterprise/ru/%D0%BA%D1%80%D0%B0%D1%82%D0%BA%D0%BE-%D0%BE-ha-%D0%BC%D0%BE%D0%B4%D0%B5%D0%BB%D1%8F%D1%85-active-active-%D0%B8-active-passive/thread/882067-100129)

\- https://www.rusonyx.ru/blog/post/chto-optimalnee-rezervnoe-kopirovanie-baas-ili-avariynoe-vosstanovlenie-draas/ 

\- https://oncloud.ru/blog/disaster-recovery-i-backUp-bratya-bliznecy

\- https://intervision.com/baas-vs-draas-3-key-differences-know/


---

### Задание 2

Компании нужно составить план восстановления в случае Disaster recovery. Сервер состоит из системного диска и диска с данными. 
Требуется копировать два логических диска на один физический: 
- системный диск (C:) (20 гигабайт);
- диск с данными (D:) (256 гигабайт). 

В требованиях говорится: 
- данные критичны в течение 24 часов после аварии;
- сеть критична к большим потокам данных в рабочее время;
- рабочее время с 9.00 до 18.00, пять дней (понедельник – пятница);
- план резервирования должен быть реализован для диска C и для диска D. В случае Linux-систем /dev/sda1, /dev/sda4 или /dev/sdb1-данные;
- считается, что для этой задачи может быть: 1) поставлен второй сервер или 2) выбрана облачная инфраструктура с определённой услугой;
- компания готова платить за 10 терабайт места как в одном, так и в другом случае.
 
*Приведите ответ в форме плана востановления с выбранным механизмом и получившейся топологией.*


### Задание 3*

Используя программу R-sync, составьте конфигурацию для выполнения прошлой задачи.

*Пришлите файл конфигурации.*


### *<a name="2">Ответ к Заданиям 2 - 3*</a>*

### План аварийного восстановления (Disaster Recovery)

***Раздел 1. Исходная информация.***

1.1. Тип аварийной ситуации - недоступен **Основной сервер**:

```bash 
makhota@nodeone:~$ hostnamectl
   Static hostname: nodeone
         Icon name: computer-vm
           Chassis: vm
        Machine ID: d5271009be984d698409bc288664dce0
           Boot ID: 4264b16590d24eee84d657efd34fe8c0
    Virtualization: oracle
  Operating System: Debian GNU/Linux 11 (bullseye)
            Kernel: Linux 5.10.0-16-686-pae
      Architecture: x86

makhota@nodeone:~$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   20G  0 disk
├─sda1   8:1    0   19G  0 part /
├─sda2   8:2    0    1K  0 part 
└─sda5   8:5    0  975M  0 part [SWAP]
sdb      8:16   0    256G  0 disk 
└─sdb1   8:17   0    256G  0 part /home/makhota/Общедоступные
sr0     11:0    1 58,7M  0 rom
```

1.2. Настроено резервное копирование основного сервера по типу с помощью `rsync` на **Сервер для хранения резервных копий** 10 терабайт места:

Файл конфигурации на обоих серверах `/etc/rsyncd.conf:

```bash
pid file = /var/run/rsyncd.pid
log file = /var/log/rsyncd.log
transfer logging = true
munge symlinks = yes
# папка источник для бэкапа
[datasda1]
path = /
uid = root
read only = yes
list = yes
comment = Data backup Dir
auth users = backup
secrets file = /etc/rsyncd.scrt
[datasdb1]
path = /home/makhota/Общедоступные
uid = root
read only = yes
list = yes
comment = Data backup Dir
auth users = backup
secrets file = /etc/rsyncd.scrt   

```

Файл `/etc/rsyncd.scrt` на `Основном сервере`:

```bash
backup:12345
```
Файл `/etc/rsyncd.scrt` на `Сервере для хранения резервных копий`:

```bash
12345
```

Копирование осуществляется с помощью скрипта `backup-nodeone.sh`

```bash
#!/bin/bash
echo "***"

date
# Папка, куда будем складывать архивы — ее либо сразу создать либо не создавать а положить в уже существующие
syst_dir=/backup/
# Имя сервера, который архивируем
srv_name=nodeone
# Адрес сервера, который архивируем
srv_ip=192.168.1.39
# Пользователь rsync на сервере, который архивируем
srv_user=backup
# Ресурс на сервере для бэкапа
srv_dir1=datasda1
srv_dir2=datasdb1


#Вывод в консоль информации о запуске бекапа

echo "Start backup ${srv_name}"
# Создаем папку для инкрементных бэкапов
sudo mkdir -p ${syst_dir}${srv_name}/increment/

# Запускаем копирование sda1

#-a, --archive Эквивалентно набору -rlptgoD. Это быстрый способ указать, что Вам нужна рекурсия 
# и Вы хотите сохранить почти все. -a не сохраняет жесткие ссылки, для этого отдельно указывать -H.
#-v, --verbose увеличить уровень подробностей

#-z, --compress С этим параметром rsync сжимает все передаваемые данные файлов.
#-H, --hard-links Указывает пересоздать жесткие ссылки на конечной стороне в соответствии с тем, что имеется на исходной. Без этого параметра жесткие ссылки обрабат>#-x, --one-file-system Требует не переходить границ файловой системы при рекурсивном копировании. Это полезно при необходимости копирования только одной файловой си>#-S, --sparse Пытаться эффективнее обработать фрагментацию файлов, чтобы сэкономить пространство на приемной стороне.
#--delete Удалять любые файлы на приемной стороне, которых нет на передающей

#--password-file Позволяет Вам предоставить пароль для доступа к rsync-серверу, сохранив его в файле.

#-b, --backup создавать резервную копию (см. --suffix и --backup-dir)
# --backup-dir создавать резервную копию в этом каталоге

/usr/bin/rsync -aHAXSz --progress --delete --password-file=/etc/rsyncd.scrt --rsync-path="sudo rsync" \
--exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/etc/fstab","/etc/udev/rules.d/*","/etc/network/*","/lib/modprobe.d/*", "/home/makhota/Общедоступные/*"} \${srv_user}@${srv_ip}::${srv_dir1} ${syst_dir}${srv_name}/current/sda1/ --backup \
--backup-dir=${syst_dir}${srv_name}/increment/sda1/`date +%Y-%m-%d`/

# Запускаем копирование sdb1

/usr/bin/rsync -aHAXSz --progress --delete --password-file=/etc/rsyncd.scrt --rsync-path="sudo rsync" \
--exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/etc/fstab","/etc/udev/rules.d/*","/etc/network/*","/lib/modprobe.d/*"} \${srv_user}@${srv_ip}::${srv_dir2} ${syst_dir}${srv_name}/current/sdb1/ --backup \
--backup-dir=${syst_dir}${srv_name}/increment/sdb1/`date +%Y-%m-%d`/

#Найти и удалить файлы старше 30 дней

# -type — тип искомого: f=файл, d=каталог, l=ссылка (link).
# -mtime — время последнего изменения файла.
# -exec command {} \; — выполняет над найденным файлом указанную команду; обратите внимание на синтаксис.

# -maxdepth 1 ограничить глубину поиска значением «1». 
#Так вы ограничитесь поиском в текущей папке, не залезая в подпапки.


/usr/bin/find ${syst_dir}${srv_name}/increment/ -maxdepth 1 -type d -mtime +30 -exec rm -rf {} \;

#Вывести дату в консоль

date

#Вывести в консоль информацию о завершении бекапа на конкретной машине
echo "Finish backup ${srv_name} ip ${srv_ip}"
echo "***"
```

Копирование осуществляется ежедневно шедулером, поскольку данные критичны 24 часа, а на восстановление сервера требуется от 2 до 4 часов, то резервное копирование осуществляется два раза в день в нерабочее время в 21:01 и 6:01, в файл `/etc/crontab` добавлены строки:

```bash

01 21 * * * root /root/scripts/backup-nodeone.sh 2>&1 >> /home/makhota/rsync-recovery/logrsync-nodeone.txt
01 06 * * * root /root/scripts/backup-nodeone.sh 2>&1 >> /home/makhota/rsync-recovery/logrsync-nodeone.txt

```


***Раздел 2. Ответственное за аварийное восстановление лицо.***

Махота Елена 
Телефон +7000000000
e-mail: help@server.ru

***Раздел 3. Сроки аварийного восстановления.***
От 2 до 4 часов на перенос данных Основного сервера на временный сервер/
Максимальный срок на восстановление от момента последней резервной копии - не более 24 часов.

***Раздел 4. Порядок аварийного восстановления на новый временный сервер в облаке.***

4.1. До устранения неполадок или замены Основного сервера будет использоваться машина в облаке Yandex Cloud c Debian 10

```
Имя nodeone
Дата создания 08.02.2023, в 21:02
Внутренний FQDN nodeone.ru-central1.internal
Зона доступности ru-central1-a
Сервисный аккаунт makhota2
Ресурсы 
латформа Intel Ice Lake
Гарантированная доля vCPU 100%
vCPU 2
RAM 4 ГБ
Объём дискового пространства 20 ГБ
Объём дополнительного дискового пространства 256 ГБ
Прерываемая да
Сеть Сетевой интерфейс
Внутренний IPv4 10.128.0.10
Публичный IPv4 51.250.93.48

```


4.2. Создать точку монтирования на каждый раздел на новом временном сервере

```bash
sudo mkdir -p /mnt/new_server/sda1
sudo mkdir -p /mnt/new_server/sdb1

sudo mount /dev/vda2 /mnt/new_server/sda1
sudo mount /dev/vdb1 /mnt/new_server/sda1

makhota@nodeone:~$ lsblk
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
vda    254:0    0  30G  0 disk
├─vda1 254:1    0   1M  0 part
└─vda2 254:2    0  30G  0 part /mnt/new_server/sda1
vdb    254:16   0   260G  0 disk
└─vdb1 254:17   0   260G  0 part /mnt/new_server/sdb1
```

4.3. Скопировать резервную копию на новый временный сервер

```
rsync -aHAXSz --delete --info=progress2 --numeric-ids -e "ssh" --rsync-path="sudo rsync" --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/etc/fstab","/etc/udev/rules.d/*","/etc/network/*","/lib/modprobe.d/*"} YOUR_USERNAME@IP_Сервера_для_хранения_резервных копий:/backup/nodeone/current/sda1/ /new_server/sda1

rsync -aHAXSz --delete --info=progress2 --numeric-ids -e "ssh" --rsync-path="sudo rsync" --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/etc/fstab","/etc/udev/rules.d/*","/etc/network/*","/lib/modprobe.d/*"} YOUR_USERNAME@IP_Сервера_для_хранения_резервных копий:/backup/nodeone/current/sdb1/ /new_server/sdb1
```
или если машина за натом, можно использовать `scp`.

4.5. Настроить резервное копирование на **Сервер для хранения резервных копий** с нового временного сервера.


***Раздел 5. Порядок переноса сервера с временного на восстановленный Основной сервер.***

После устранения неполадок либо покупки нового **Основного сервера** выполнить:

5.1. Подготовка временного сервера к переносу на **Основной сервер**
5.1.1. Сохранить образ диска.
5.1.2. Создаем точку монтирования для каждого раздела
```bash
sudo mount -o ro /dev/vda2 /mnt/new_server/sda1
sudo mount -o ro /dev/vdb1 /mnt/new_server/sdb1
```

Примечание: монтаж только для чтения, чтобы предотвратить стирание вещей по ошибке.

5.1.3. Внести правки в файл
`nano /etc/sudoers`
Добавить в конце файла (заменить YOUR_USERNAME локальным именем пользователя):
YOUR_USERNAME ALL= NOPASSWD:/usr/bin/rsync
Это позволит запустить rsync от root через ssh позже.

5.2. Подготовка нового **Основного сервера**
5.2.1. Установить Debian 10, разметить диски, разделы.
5.2.2. Запустить новый сервер в режиме восстановления.
5.2.3. Проверить соединение ssh с новым Основным сервером (в режиме аварийного восстановления)
5.2.4. Создаем точки монтирования
sudo su
```bash
sudo mkdir -p /mnt/new_server/sda1
sudo mkdir -p /mnt/new_server/sdb1

sudo mount /dev/sda1 /mnt/new_server/sda1
sudo mount /dev/sdb1 /mnt/new_server/sda1
```

5.2.5. Копируем файлы.
```bash
rsync -aHAXSz --delete --info=progress2 --numeric-ids -e "ssh" --rsync-path="sudo rsync" --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/etc/fstab","/etc/udev/rules.d/*","/etc/network/*","/lib/modprobe.d/*"} YOUR_USERNAME@YOUR_OLD_SERVER_IP:/mnt/new_server/sda1/ /mnt/new_server/sda1/

rsync -aHAXSz --delete --info=progress2 --numeric-ids -e "ssh" --rsync-path="sudo rsync" --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/etc/fstab","/etc/udev/rules.d/*","/etc/network/*","/lib/modprobe.d/*"} YOUR_USERNAME@YOUR_OLD_SERVER_IP:/mnt/new_server/sda1/ /mnt/new_server/sda1/
```

5.2.6. Настроить новый Основной сервер.
5.2.6.1. Заменить сетевой интерфейс
5.2.6.2. Отредактировать файл.
`nano /mnt/new_server/sda1/etc/hosts`
Если IP-адрес старого сервера присутствует в файле, замените его IP-адресом нового сервера.

5.2.7. Установить grub на новый сервер

```bash
mount --bind /proc /mnt/new_server/proc
mount --bind /sys /mnt/new_server/sys
mount --bind /dev /mnt/new_server/dev
mount --bind /run /mnt/new_server/run
chroot /mnt/new_server
grub-install /dev/sda
update-grub

5.2.8. Перезагрузить новый Основной сервер и протестировать.
5.2.9. Проверить журналы, чтобы увидеть, все ли в порядке `/var/log/syslog`





Использованные источники:

- https://www.man42.net/blog/2017/07/how-to-migrate-a-debian-server/

- https://www.man42.net/blog/2017/07/how-to-migrate-a-debian-server/

- https://habr.com/ru/company/ruvds/blog/523570/

- https://habr.com/ru/company/servermall/blog/655127/ 