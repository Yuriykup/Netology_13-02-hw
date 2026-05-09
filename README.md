 Домашнее задание к занятию  «Защита хоста» - Куприянов Ю.В.

-----

### Задание 1

1. Установите **eCryptfs**.
2. Добавьте пользователя cryptouser.
3. Зашифруйте домашний каталог пользователя с помощью eCryptfs.


*В качестве ответа  пришлите снимки экрана домашнего каталога пользователя с исходными и зашифрованными данными.*  

---
### ОТВЕТ на Задание 1

`1.1 Устанваливаем ecryptfs-utils.`

```
sudo apt install ecryptfs-utils
ecryptfsd --version
```
![Скриншот-1](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img1.png)

`1.2 Создаём пользователя cryptouser`

```
sudo adduser cryptouser
```
![Скриншот-2](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img2.png)

`1.3 Шифрование домашнего каталога пользователя cryptouser.`

```
sudo ecryptfs-migrate-home -u cryptouser
``` 
![Скриншот-3](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img3.png)

`1.4 Создаём файлы от имени пользователя cryptouser для проверки шифрования`

```
su cryptouser
cd /home/cryptouser
touch 123.txt 456.txt
```
![Скриншот-4](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img4.png)

`1.5 Проверка шифрования от локального пользователя kupriyanov`

```
ls -l /home/cryptouser
sudo ls -l /home/cryptouser
```
![Скриншот-5](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img5.png)

---

### Задание 2

1. Установите поддержку **LUKS**.
2. Создайте небольшой раздел, например, 100 Мб.
3. Зашифруйте созданный раздел с помощью LUKS.

*В качестве ответа пришлите снимки экрана с поэтапным выполнением задания.*

---
### ОТВЕТ на Задание 2 

`2.1 Устанваливаем cryptsetup`

```
sudo apt install cryptsetup -y
cryptsetup --version
```
![Скриншот-21](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img21.png)

`2.2 Создаём файл‑образ размером 250 МБ`

```
sudo dd if=/dev/zero of=/tmp/luks_partition.img bs=1M count=250

```
Где:

- if=/dev/zero — `источник нулей для заполнения;`
- of=/tmp/luks_partition.img — `путь к файлу‑образу;`
- bs=1M — `размер блока в 1 МБ;`
- count=250 — `количество блоков (итого 250 МБ).`

![Скриншот-22](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img22.png)

`2.3 Привязываем файл к loop‑устройству`

```
sudo losetup --find --show /tmp/luks_partition.img
```
![Скриншот-23](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img23.png)

`2.4 Создаём таблицу разделов (GPT или MBR) и один раздел`

```
sudo fdisk /dev/loop21
```
![Скриншот-24](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img24.png)

`2.5 Инициализируем LUKS‑контейнер на разделе`

```
sudo cryptsetup luksFormat /dev/loop21p1
```
![Скриншот-25](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img25.png)


`2.6 Открываем (расшифровываем) LUKS‑раздел и присваиваем ему имя.`

```
sudo cryptsetup open /dev/loop21p1 luks_volume
```
![Скриншот-26](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img26.png)


`2.7 Создаём файловую систему на расшифрованном устройстве`

```
sudo mkfs.ext4 /dev/mapper/luks_volume
```

![Скриншот-27](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img27.png)


`2.8 Создаём точку монтирования и монтируем раздел`

```
sudo mkdir /mnt/luks_mount
sudo mount /dev/mapper/luks_volume /mnt/luks_mount
```
![Скриншот-28](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img28.png)

### Проверка работы

`2.9 Проверка монтирования`

```
df -h | grep luks
```
![Скриншот-29](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img29.png)

`2.10 Проверяем статус LUKS`

```
sudo cryptsetup status luks_volume
```

![Скриншот-210](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img210.png)

`2.11 Создаём тестовый файл и проверяем содержимое`
```
echo "Это тестовый файл на зашифрованном разделе" | sudo tee /mnt/luks_mount/test-luks-ntlg.txt
cat /mnt/luks_mount/test-luks-ntlg.txt
```

![Скриншот-211](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img211.png)

### Размонтирование и закрытие LUKS‑раздела

`2.12 Размонтирование и закрытие LUKS‑раздела (завершение работы)`
```
sudo umount /mnt/luks_mount
sudo cryptsetup close luks_volume
```

![Скриншот-212](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img212.png)

---

## Дополнительные задания (со звёздочкой*)

Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале

### Задание 3 *

1. Установите **apparmor**.
2. Повторите эксперимент, указанный в лекции.
3. Отключите (удалите) apparmor.


*В качестве ответа пришлите снимки экрана с поэтапным выполнением задания.*

---
### ОТВЕТ на Задание 3

`3.1 Устанавливаем AppArmor`

```
sudo apt install apparmor apparmor-utils -y
sudo aa-status
``` 
![Скриншот-31](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img31.png)

### Замена программы

`3.2 Создаём тестовую замену для Ping`

```
sudo vim /usr/local/bin/fake_ping
```
` Пишем минипрограмму
#!/bin/bash
echo "Это поддельная команда ping!"
echo "Аргументы: $@"`

![Скриншот-32](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img32.png)


`3.3 Делаем файл минипрограммы исполняемым`

```
sudo chmod +x /usr/local/bin/fake_ping
```
![Скриншот-33](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img33.png)

`3.4 Копируем оригиналный Ping для восстановления`

```
sudo mv /bin/ping /bin/ping.original
```

![Скриншот-34](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img34.png)

`3.5 Создадим символическую ссылку на нашу поддельную версию`

```
sudo ln -s /usr/local/bin/fake_ping /bin/ping
```

`3.6 Проверяем замену`

```
ping -c 3 8.8.8.8
```
![Скриншот-35](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img35.png)

### Отключение и полное удаление 

`3.7 Остановка службы и восстановление оригинального Ping`

```
sudo service apparmor stop
```
![Скриншот-36](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img36.png)

`3.8 Проверка восстановления Ping`

```
ping -c 3 8.8.8.8
```

![Скриншот-37](https://github.com/Yuriykup/Netology_13-02-hw/blob/main/img/img37.png)

---
