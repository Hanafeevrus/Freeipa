# Freeipa   
##### Задача.   
1. Установить FreeIPA;
2. Написать Ansible playbook для конфигурации клиента;    
3. Firewall должен быть включен на сервере и на клиенте.    
##### Результат.    
После развертывания необходимо добавить в hosts на хостовой машине строку `192.168.111.40 ipa.comp.local`   
Открыть в браузере [Freeipa](https://ipa.comp.local/ipa/ui) и авторизоваться    
login: `admin`    
password: `Qwerty123`   
В разделе [Identity - Hosts](https://github.com/Hanafeevrus/Freeipa/blob/master/photoeditorsdk-export.png) присутствует запись клиента `ipaclient.comp.local`
##### 1) Решение.       
* [`dep_ansible_playbook`](https://github.com/Hanafeevrus/Freeipa/tree/master/dep_ansible_playbook) - разворачивает и настраивает с помощью `ansible playbook`       
* [`dep_ansible_roles`](https://github.com/Hanafeevrus/Freeipa/tree/master/dep_ansible_roles) - разворачивает и настраивает с помощью `ansible role`        
Установка Freeipa server   
* установка Freeipa server выполняется в автоматическом режиме средствами Ansible playbook.   
Требование к инфраструктуре- имя машин клиента и сервера должно быть в формате FQDN, в данном случае `ipa.comp.local` и `ipaclient.comp.local`    
переменные для развертывания Freeipa сервера:   
```   
vars:
    ipa_domain: comp.local
    ipa_realm: COMP.LOCAL
    ipa_admin: admin
    ipa_admin_pass: Qwerty123
    ipa_server: ipa.comp.local    
```   
* команда настройки сервиса Freeipa   
```   
shell: ipa-server-install -U
       --hostname={{ ipa_server }}
       --domain={{ ipa_domain }}
       --realm={{ ipa_realm }}
       --ds-password={{ ipa_admin_pass }}
       --admin-password={{ ipa_admin_pass }}    
```   
##### 2) Решение. Установка Freeipa client   
установка Freeipa client выполняется в автоматическом режиме средствами Ansible playbook.   
* основная команда для настройки клиента где прописаны данные домена, имени машины, пользователя, пароля, и т.д.   
```
shell: ipa-client-install   
--mkhomedir   
--domain=comp.local   
--server=ipa.comp.local   
--realm=COMP.LOCAL    
--principal=admin   
--password=Qwerty123    
--hostname=ipaclient.comp.local   
--no-ntp    
--unattended
```
##### 3) Решение. Включение Filrewall   
В Vagrantfile прописываем строки:    
* для сервера   
```
sudo systemctl enable firewalld
sudo systemctl start firewalld
sudo firewall-cmd --add-service={dns,freeipa-ldap,freeipa-ldaps} --permanent
sudo firewall-cmd --reload    
```
* для клиента   
```
sudo systemctl enable firewalld
sudo systemctl start firewalld
```
