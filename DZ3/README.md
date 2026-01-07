Створення дистрибутиву. Було обрано Linux Ubuntu. 
Після стандартної процедури встановлення, для завантаження Nginx було запущенно дані команди:
sudo apt update
sudo apt install nginx
Після встановлення, перевірив чи працює.
systemctl status nginx
І через браузер
[скріншот]

Командою 
pidof nginx
Було перевірено кількість процесів і їхні PID.
З них один є Master, а решта Worker процеси.
За допомогою команди 
ls -l /proc/<PID>/fd
Переглянув що у дескрипторах процесу

Master process:
deo@deo-VirtualBox:/home$ sudo ls -l /proc/7412/fd
total 0
lrwx------ 1 root root 64 Jan  7 01:01 0 -> /dev/null
lrwx------ 1 root root 64 Jan  7 01:01 1 -> /dev/null
l-wx------ 1 root root 64 Jan  7 01:01 15 -> /var/log/nginx/error.log
l-wx------ 1 root root 64 Jan  7 01:01 16 -> /var/log/nginx/access.log
lrwx------ 1 root root 64 Jan  7 01:01 17 -> 'socket:[44056]'
lrwx------ 1 root root 64 Jan  7 01:01 18 -> 'socket:[44057]'
lrwx------ 1 root root 64 Jan  7 01:01 19 -> 'socket:[45068]'
l-wx------ 1 root root 64 Jan  7 01:01 2 -> /var/log/nginx/error.log
lrwx------ 1 root root 64 Jan  7 01:01 20 -> 'socket:[45069]'
lrwx------ 1 root root 64 Jan  7 01:01 21 -> 'socket:[45070]'
lrwx------ 1 root root 64 Jan  7 01:01 22 -> 'socket:[45071]'
lrwx------ 1 root root 64 Jan  7 01:01 3 -> 'socket:[44054]'
lrwx------ 1 root root 64 Jan  7 01:01 4 -> 'socket:[44055]'
lrwx------ 1 root root 64 Jan  7 01:01 5 -> 'socket:[42426]'
lrwx------ 1 root root 64 Jan  7 01:01 6 -> 'socket:[42427]'


Worker process:
deo@deo-VirtualBox:/home$ sudo ls -l /proc/7437/fd
total 0
lrwx------ 1 www-data www-data 64 Jan  7 00:53 0 -> /dev/null
lrwx------ 1 www-data www-data 64 Jan  7 00:53 1 -> /dev/null
l-wx------ 1 www-data www-data 64 Jan  7 00:53 15 -> /var/log/nginx/error.log
l-wx------ 1 www-data www-data 64 Jan  7 00:53 16 -> /var/log/nginx/access.log
lrwx------ 1 www-data www-data 64 Jan  7 00:53 17 -> 'socket:[44056]'
lrwx------ 1 www-data www-data 64 Jan  7 00:53 19 -> 'socket:[45068]'
l-wx------ 1 www-data www-data 64 Jan  7 00:53 2 -> /var/log/nginx/error.log
lrwx------ 1 www-data www-data 64 Jan  7 00:53 22 -> 'socket:[45071]'
lrwx------ 1 www-data www-data 64 Jan  7 00:53 23 -> 'anon_inode:[eventpoll]'
lrwx------ 1 www-data www-data 64 Jan  7 00:53 24 -> 'anon_inode:[eventfd]'
lrwx------ 1 www-data www-data 64 Jan  7 00:53 3 -> 'socket:[44054]'
lrwx------ 1 www-data www-data 64 Jan  7 00:53 5 -> 'socket:[42426]'
lrwx------ 1 www-data www-data 64 Jan  7 00:53 6 -> 'socket:[42427]'

Основна різниця це те, що Master - root і має більше сокетів.

Логи зберігаються у два файли. Один, який зберігає усі дії виконанні на сайті, другий для зберігання помилок.
Щоб прочитати логи можна використати команду
sudo cat /var/log/nginx/access.log
sudo cat /var/log/nginx/error.log

або 
tail -f /var/log/nginx/access.log
Для Читання в реальному часі.

Ми можемо змінити конфігурацію веб-сервера, не перезавантажуючи його. Nginx керується ось цими командами
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
Команда restart зупиняє всі процеси і запускає знову
Команда reload перезапускає сервіс без його зупинки.
Тому змінити конфігурацію без перезвантаження серверу — потрібно використати команду  
sudo systemctl reload nginx



