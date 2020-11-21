# Содержание
- [Отчет по настройке CI/CD на примере Gitlab](#отчет-по-настройке-ci/cd-на-примере-gitlab)
	- [Описание рабочей среды](#описание-рабочей-среды)
	- [Ход работы](#ход-работы)

# Отчет по настройке CI/CD на примере Gitlab
**Цель:** используя Ansible на одной виртуальной машине (хосте) развернуть Ansible на другой виртуальной машине.
## Описание рабочей среды
Имеется 2 виртуальные машины на базе ОС Linux Ubuntu Server 16.04 с IP-адресами 192.168.56.200 и 192.168.56.201.
На машине 192.168.56.201 запущен Gitlab Docker контейнер.
На машине 192.168.56.200 установлен и запущен Gitlab runner и на нее же будет выполнятся установка.

На обоих машинах настроена возможность подключения друг к другу по SSH, добавлены SSH-ключи для подключения без пароля.
## Ход работы
1. Импортируем данныйрепозиторий в Gitlab.
2. Создаем файл `.gitlab-ci.yml`, в котором описываем 2 шага (job) для установки зависимостей роли и прокатки роли. 
В скрипте автоматизируются пункты 4-6 из [данного отчета](https://github.com/BassinD/sopo/tree/main/ansible#%D1%85%D0%BE%D0%B4-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B)
3. Регистрируем gitlab-runner на машине 192.168.56.200 для работы с Gitlab.
Результат регистрации представлен ниже:
```
sudo gitlab-runner status
[sudo] password for bassindd:
Sorry, try again.
[sudo] password for bassindd:
Runtime platform                                    arch=amd64 os=linux pid=8527 revision=ece86343 version=13.5.0
gitlab-runner: Service is running!
bassindd@bassid-ubuntu-server:~$ sudo gitlab-runner register
Runtime platform                                    arch=amd64 os=linux pid=8604 revision=ece86343 version=13.5.0
Running in system-mode.

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
http://192.168.56.201/
Please enter the gitlab-ci token for this runner:
8pyS6BP5oaM8B6XwtH3i
Please enter the gitlab-ci description for this runner:
[bassid-ubuntu-server]:
Please enter the gitlab-ci tags for this runner (comma separated):
ansible_ini
Registering runner... succeeded                     runner=8pyS6BP5
Please enter the executor: docker, parallels, shell, virtualbox, kubernetes, custom, docker-ssh, ssh, docker+machine, docker-ssh+machine:
shell
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
```
4. Переходим в раздел CI/CD -> Pipelines и дожидаемся завершения выполнения текущего процесса.
Результат выполнения pipeline:
![gitlab-demo](https://github.com/BassinD/sopo/blob/main/gitlab-passed.gif)
