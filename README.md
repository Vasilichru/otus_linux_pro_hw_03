# otus_linux_pro_hw_03
Подготовить стенд на Vagrant как минимум с одним сервером.
На этом сервере используя Ansible необходимо развернуть nginx со следующими условиями:

    необходимо использовать модуль yum/apt;
    конфигурационные файлы должны быть взяты из шаблона jinja2 с перемененными;
    после установки nginx должен быть в режиме enabled в systemd;
    должен быть использован notify для старта nginx после установки;
    сайт должен слушать на нестандартном порту - 8080, для этого использовать переменные в Ansible.

Критерии оценки:

Статус "Принято" ставится, если:

    предоставлен Vagrantfile и готовый playbook/роль (инструкция по запуску стенда, если посчитаете необходимым);
    после запуска стенда nginx доступен на порту 8080;
    при написании playbook/роли соблюдены перечисленные в задании условия.


1. все необходимые файлы содежраться в папке ansible
2. запустить vagrant - он создаст вм в частной сети с адресом 192.168.56.150
3. запустить плейбук
ansible-playbook playbook.yaml 

PLAY [NGINX | install and configure nginx] **********************************************************

TASK [Gathering Facts] ******************************************************************************
ok: [nginx]

TASK [update] ***************************************************************************************
changed: [nginx]

TASK [NGINX | install nginx] ************************************************************************
changed: [nginx]

TASK [NGINX | Create NGINX config file from template] ***********************************************
changed: [nginx]

TASK [firewall stop] ********************************************************************************
changed: [nginx]

RUNNING HANDLER [restart nginx] *********************************************************************
changed: [nginx]

RUNNING HANDLER [reload nginx] **********************************************************************
changed: [nginx]

PLAY RECAP ******************************************************************************************
nginx                      : ok=7    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   



4. для проверки работы nginx подключаемся по ссш (тк сеть частная) 
vagrant ssh
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-71-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun May 19 18:46:24 UTC 2024

  System load:  0.291015625       Processes:               102
  Usage of /:   4.3% of 38.70GB   Users logged in:         0
  Memory usage: 30%               IPv4 address for enp0s3: 10.0.2.15
  Swap usage:   0%                IPv4 address for enp0s8: 192.168.56.150


Expanded Security Maintenance for Applications is not enabled.

193 updates can be applied immediately.
118 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


Last login: Sun May 19 18:45:55 2024 from 10.0.2.2

5. и запускаем curl
url 192.168.56.150:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>


задание выполнено

6. можно проверить работу хэндлеров, просто изменив конфиг nginx (напр. поменяв порт подключения) и перезапустик плейбук
# {{ ansible_managed }}
events {
    worker_connections 1024;
}

http {
    server {
        listen       8081 default_server;
        server_name  default_server;
        root         /usr/share/nginx/html;

        location / {
        }
    }
}


ansible-playbook playbook.yaml 

PLAY [NGINX | install and configure nginx] **********************************************************

TASK [Gathering Facts] ******************************************************************************
ok: [nginx]

TASK [update] ***************************************************************************************
changed: [nginx]

TASK [NGINX | install nginx] ************************************************************************
ok: [nginx]

TASK [NGINX | Create NGINX config file from template] ***********************************************
changed: [nginx]

TASK [firewall stop] ********************************************************************************
ok: [nginx]

RUNNING HANDLER [reload nginx] **********************************************************************
changed: [nginx]

PLAY RECAP ******************************************************************************************
nginx                      : ok=6    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


vagrant@nginx:~$ curl 192.168.56.150:8080
curl: (7) Failed to connect to 192.168.56.150 port 8080 after 0 ms: Connection refused

vagrant@nginx:~$ curl 192.168.56.150:8081
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>


