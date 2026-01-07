
# Встановлення та аналіз роботи веб-сервера Nginx

## Вибір дистрибутиву

Для виконання завдання було обрано дистрибутив **Linux Ubuntu**.  
Після стандартної процедури встановлення операційної системи було виконано інсталяцію веб-сервера Nginx.

---

## Встановлення Nginx

Для оновлення списку пакетів та встановлення Nginx було використано такі команди:

```bash
sudo apt update
sudo apt install nginx
````

Після завершення інсталяції було перевірено стан сервісу:

```bash
systemctl status nginx
```
![](/DZ3/src/VirtualBoxVM_8069vNVdWj.png)


Також працездатність веб-сервера була перевірена через браузер шляхом відкриття сторінки `localhost`.

![](/DZ3/src/VirtualBoxVM_E68EigTock.png)

---

## Аналіз процесів Nginx

Для перевірки кількості процесів Nginx та їх PID була використана команда:

```bash
pidof nginx
```

Було виявлено, що Nginx складається з одного **Master process** та кількох **Worker process**.

---

## Аналіз файлових дескрипторів процесів

Для перегляду відкритих файлових дескрипторів процесів було використано команду:

```bash
ls -l /proc/<PID>/fd
```

### Master process

```bash
sudo ls -l /proc/7412/fd
```

```text
0  -> /dev/null
1  -> /dev/null
2  -> /var/log/nginx/error.log
15 -> /var/log/nginx/error.log
16 -> /var/log/nginx/access.log
socket:[...]
```

Master process працює від користувача **root** та має більшу кількість сокетів. Він відповідає за керування worker-процесами, відкриття мережевих портів та ініціалізацію логів.

---

### Worker process

```bash
sudo ls -l /proc/7437/fd
```

```text
0  -> /dev/null
1  -> /dev/null
2  -> /var/log/nginx/error.log
15 -> /var/log/nginx/error.log
16 -> /var/log/nginx/access.log
socket:[...]
anon_inode:[eventpoll]
anon_inode:[eventfd]
```

Worker process працює від користувача **www-data** та безпосередньо обробляє HTTP-запити клієнтів.
Він використовує сокети для мережевої взаємодії та механізми `epoll` для ефективної обробки подій.

---

## Робота з логами

Nginx зберігає логи у двох основних файлах:

**access.log** — містить інформацію про всі запити до веб-сервера

**error.log** — містить повідомлення про помилки

Для перегляду логів використовуються команди:

```bash
sudo cat /var/log/nginx/access.log
sudo cat /var/log/nginx/error.log
```

Для перегляду логів у реальному часі:

```bash
tail -f /var/log/nginx/access.log
```

![](/DZ3/src/VirtualBoxVM_SIHTUriEWF.png)

---

## Керування сервісом та оновлення конфігурації

Керування веб-сервером Nginx здійснюється за допомогою таких команд:

```bash
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
```

`restart` — повністю зупиняє всі процеси та запускає їх знову

`reload` — перечитує конфігурацію без повної зупинки сервісу

Таким чином, конфігурацію веб-сервера можна змінювати **без перезавантаження сервера**, використовуючи команду:

```bash
sudo systemctl reload nginx
```
