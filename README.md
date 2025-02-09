
# ***Лабораторная работа № 1 "Виртуальный сервер"***
# *Группа IA2303, Доцен Анна*
## *Дата выполнения: 09.02.2025*
## Описание задачи

## Описание выполнения работы
1. Скачиваем дистрибутив *Debian* и систему виртуализации *QEMU*.
2. Создаем папку *lab01*. В этой папке создаем папкy *dvd* и *файл readme.md*. В папку *dvd* поместите образ *ISO* дистрибутива *Debian*.
3. В консоли создаем в папке *lab01* образ диска для виртуальной машины размером 8 ГБ, формата *qcow2*, используя утилиту *qemu-img*: **qemu-img create -f qcow2 debian.qcow2 8G**
4. Устанавливаем *ОС Debian* на виртуальную машину: **qemu-system-x86_64 -hda debian.qcow2 -cdrom dvd/debian.iso -boot d -m 2G**
5. Перезагружаем виртуальную машину. Для повторного запуска виртуальной машины выполните команду:**qemu-system-x86_64 -hda debian.qcow2 -m 2G -smp 2 -device e1000,netdev=net0 -netdev user,id=net0,hostfwd=tcp::1080-:80,hostfwd=tcp::1022-:22**
6. Проверяем наличие на устройстве *SSH* припомощи команды **ssh -l** и затем подключаемся к виртуальной машине через командную строку, при помощи **ssh -l user localhost -p 1022**
 
7. Устанавливаем *LAMP* в виртуальной машине: 
- su
- apt update -y
- apt install -y apache2 php libapache2-mod-php php-mysql - mariadb-server mariadb-client unzip
8. Скачиваем **СУБД PhpMyAdmin**: *wget https://files.phpmyadmin.net/phpMyAdmin/5.2.2/phpMyAdmin-5.2.2-all-languages.zip*
9. Скачиваем **CMS Drupal**:*wget https://ftp.drupal.org/files/projects/drupal-11.1.1.zip*
10. Проводим распаковку файлов: 
- mkdir /var/www
- unzip phpMyAdmin-5.2.2-all-languages.zip
- mv phpMyAdmin-5.2.2-all-languages /var/www/phpmyadmin
- unzip drupal-11.1.1.zip
- mv drupal-11.1.1 /var/www/drupal
11. Создаем через командную строку для *CMS* базу данных *drupal_db* и пользователя базы данных с нашим именемя: **mysql -u root**
***Содержимое базы дынных:***  
_CREATE DATABASE drupal_db;  
CREATE USER 'user'@'localhost'  IDENTIFIED BY 'password';  
GRANT ALL PRIVILEGES ON drupal_db.* TO 'user'@'localhost';  
FLUSH PRIVILEGES;  
EXIT;_  
12. Создаем файл в папке: ***nano /etc/apache2/sites-available/01-phpmyadmin.conf***, содержимое файла:  
_<VirtualHost *:80>  
    ServerAdmin webmaster@localhost  
    DocumentRoot "/var/www/phpmyadmin"  
    ServerName phpmyadmin.localhost  
    ServerAlias www.phpmyadmin.localhost  
    ErrorLog "/var/log/apache2/phpmyadmin.localhost-error.log"  
    CustomLog "/var/log/apache2/phpmyadmin.localhost-access.log"   common  
</VirtualHost>_  
13. Создаем файл в папке: ***nano /etc/apache2/sites-available/02-drupal.conf***, содержимое файла:  
_<VirtualHost *:80>  
    ServerAdmin webmaster@localhost  
    DocumentRoot "/var/www/drupal"  
    ServerName drupal.localhost  
    ServerAlias www.drupal.localhost  
    ErrorLog "/var/log/apache2/drupal.localhost-error.log"  
    CustomLog "/var/log/apache2/drupal.localhost-access.log" common  
</VirtualHost>_  
14. Регистрируем конфинурацию выполнив следующие команды:  
_/usr/sbin/a2ensite 01-phpmyadmin  
/usr/sbin/a2ensite 02-drupal_  
15. Перезапускаем **Apache HTTP Server:** *systemctl reload apache2*
