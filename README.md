# Содержание
- [Отчет по настройке CI/CD на примере Gitlab](#отчет-по-настройке-ci/cd-на-примере-gitlab)
	- [Описание рабочей среды](#описание-рабочей-среды)
	- [Ход работы](#ход-работы)
- [Отчет по настройке CI/CD на примере Jenkins](#отчет-по-настройке-ci/cd-на-примере-jenkins)
	- [Описание рабочей среды](#описание-рабочей-среды)
	- [Ход работы](#ход-работы)

# Отчет по настройке CI/CD на примере Gitlab
**Цель:** выполнить запуск Ansible-Playbook при помощи GitLab.
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

# Отчет по настройке CI/CD на примере Jenkins
**Цель:** выполнить запуск Ansible-Playbook при помощи Jenkins Pipeline.
## Описание рабочей среды
Имеется 2 виртуальные машины на базе ОС Linux Ubuntu Server 16.04 с IP-адресами 192.168.56.200 и 192.168.56.201.
На машине 192.168.56.201 запущен Jenkins Docker контейнер.
На машине 192.168.56.200 установлен и запущен Jenkins agent и на нее же будет выполнятся установка.

На обоих машинах настроена возможность подключения друг к другу по SSH, добавлены SSH-ключи для подключения без пароля.
## Ход работы
1. Настраиваем jenkins агент с именем `sopo-agent`.
2. Создаем pipeline `sopo-run`.В качестве параметра зададим `sudo-pas` - пароль супер-пользователя, используемый в скрипте выполнения ansible playbook.
3. В репозитории создадим файл [Jenkinsfile](https://github.com/BassinD/sopo/blob/main/Jenkinsfile), в котором опишем следующие шаги для выполнения ansible playbook:
- Первый шаг 'Clone code source' - клонирование данного репозитория
- Второй шаг 'Work with code' - вывод склонированного репозитория (для проверки), выполнение `ansible-galaxy` и `ansible-playbook` скриптов для установки ansible, вывод текущей версии ansible для проверки валидности установки.
В качестве усполнителя данного скрипта укажем агента, созданного в 1-ом шаге `sopo-agent`.
4. Настроим pipeline `sopo-run` так, чтобы в качестве исполняемого скрипта использовался Jenkinsfile файл из данного репозитоия.
5. Запустим pipeline. Результат выполнения представлен ниже:
```
Started by user user
Obtained Jenkinsfile from git https://github.com/BassinD/sopo.git
Running in Durability level: MAX_SURVIVABILITY
[Pipeline] Start of Pipeline
[Pipeline] node
Running on sopo-agent in /home/jenkins/workspace/sopo-run
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Clone code source)
[Pipeline] git
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
using credential GitHub-acc
Fetching changes from the remote Git repository
 > git rev-parse --is-inside-work-tree # timeout=10
 > git config remote.origin.url https://github.com/BassinD/sopo.git # timeout=10
Fetching upstream changes from https://github.com/BassinD/sopo.git
 > git --version # timeout=10
 > git --version # 'git version 2.7.4'
using GIT_ASKPASS to set credentials 
 > git fetch --tags --progress https://github.com/BassinD/sopo.git +refs/heads/*:refs/remotes/origin/* # timeout=10
Checking out Revision f5cf7f5a7564e57140bb60404c57b751b53eb95d (refs/remotes/origin/main)
Commit message: "Create Jenkinsfile"
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
 > git config core.sparsecheckout # timeout=10
 > git checkout -f f5cf7f5a7564e57140bb60404c57b751b53eb95d # timeout=10
 > git branch -a -v --no-abbrev # timeout=10
 > git branch -D main # timeout=10
 > git checkout -b main f5cf7f5a7564e57140bb60404c57b751b53eb95d # timeout=10
 > git rev-list --no-walk 863db4e2f9b0d4496d8d54b63689c316399c450b # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Work with code)
[Pipeline] sh
+ ls .
ansible
docker
gitlab-passed.gif
Jenkinsfile
README.md
[Pipeline] sh
+ ansible-galaxy install -r ansible/roles/requirements.yml --force
/usr/local/lib/python2.7/dist-packages/ansible/parsing/vault/__init__.py:44: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in a future release.
  from cryptography.exceptions import InvalidSignature
Starting galaxy role install process
- changing role ansible-role from main to main
- extracting ansible-role to /home/jenkins/.ansible/roles/ansible-role
- ansible-role (main) was installed successfully
[Pipeline] sh
+ ansible-playbook -i ansible/environments/vbox/inventory ansible/playbooks/ansible.yml --extra-vars ansible_sudo_pass=Serverg0d! -D
/usr/local/lib/python2.7/dist-packages/ansible/parsing/vault/__init__.py:44: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in a future release.
  from cryptography.exceptions import InvalidSignature

PLAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
[DEPRECATION WARNING]: Distribution Ubuntu 16.04 on host bassind.ubuntu.vbox 
should use /usr/bin/python3, but is using /usr/bin/python for backward 
compatibility with prior Ansible releases. A future Ansible release will 
default to using the discovered platform python for this host. See https://docs
.ansible.com/ansible/2.10/reference_appendices/interpreter_discovery.html for 
more information. This feature will be removed in version 2.12. Deprecation 
warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [bassind.ubuntu.vbox]

TASK [ansible-role : Install python-apt - need move to deps or pre_tasks] ******
ok: [bassind.ubuntu.vbox]

TASK [ansible-role : Install python-pip] ***************************************
ok: [bassind.ubuntu.vbox]

TASK [ansible-role : Install ansible] ******************************************
[WARNING]: The value "{'major': 2, 'full': '2.10.3', 'string': '2.10.3',
'minor': 10, 'revision': 3}" (type dict) was converted to "u"{'major': 2,
'full': '2.10.3', 'string': '2.10.3', 'minor': 10, 'revision': 3}"" (type
string). If this does not look like what you expect, quote the entire value to
ensure it does not change.
ok: [bassind.ubuntu.vbox]

PLAY RECAP *********************************************************************
bassind.ubuntu.vbox        : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[Pipeline] sh
+ ansible --version
/usr/local/lib/python2.7/dist-packages/ansible/parsing/vault/__init__.py:44: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in a future release.
  from cryptography.exceptions import InvalidSignature
ansible 2.10.3
  config file = None
  configured module search path = [u'/home/jenkins/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python2.7/dist-packages/ansible
  executable location = /usr/local/bin/ansible
  python version = 2.7.12 (default, Oct  5 2020, 13:56:01) [GCC 5.4.0 20160609]
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
```

