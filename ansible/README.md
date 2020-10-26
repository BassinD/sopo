# Отчет по настройке среды через Ansible
**Цель:** используя Ansible на одной виртуальной машине (хосте) развернуть Ansible на другой виртуальной машине.
## Описание рабочей среды
Имеется 2 виртуальные машины на базе ОС Linux Ubuntu Server 16.04 с IP-адресами 192.168.56.200 (выступает в роли хоcта) и 192.168.56.201.

На обоих машинах настроена возможность подключения друг к другу по SSH, добавлены SSH-ключи для подключения без пароля.

На хост-машине установлен пакет Ansible, через который будет выполняться настройка среды на второй машине.
## Ход работы

 1. Создадим файл с описанием машин, с которыми будет осуществляться соединение [./environments/vbox/inventory](./environments/vbox/inventory). Добавим в файл `
bassind.ubuntu.vbox ansible_ssh_host=192.168.56.201 ansible_ssh_user=bassind`
2. Выполним проверку соединения с указаной машиной
```
bassindd@bassid-ubuntu-server:~/task/sopo/ansible$ ansible -m ping all -i ./environments/vbox/inventory
bassind.ubuntu.vbox | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
3. Создадим плейбук для установки Ansible на всех узлах [./playbooks/ansible](./playbooks/ansible).
Добавим в файл инструкции по запуску роли *ansible-role* :
```
---
- hosts: all
  become: yes
  roles:
    - ansible-role
```
**Шаги 4 и 5 были изменены, т.к. роль вынесена в отдельный репозиторий https://github.com/BassinD/ansible-role.
Для установки роли перед шагом 6 выполняется следующая команда:**
```
$ ansible-galaxy install -r roles/requirements.yml
- extracting ansible-role to /home/bassindd/.ansible/roles/ansible-role
- ansible-role (main) was installed successfully
```
~~4. Создадим директорию роли *ansible*: ./playbooks/roles/ansible-role. И в ней создадим директорию с задачами *tasks* и задачу *main.yml*. 
В инструкции задачи добавим следующее для установки python-pip и ansible необходимой версии:~~
```
~/task/sopo/ansible$ cat ./playbooks/roles/ansible-role/tasks/main.yml
---
- name: Install python-apt - need move to deps or pre_tasks
  apt:
    name:
      - python-apt
    state: present
- name: Install python-pip
  apt:
    name: python-pip
    update_cache: yes
    state: present
- name: Install ansible
  pip:
    name: ansible=={{ ansible_version }}
    virtualenv_command: virtualenv-2.7
    state: present


```
~~5. Дополнительно создадим файл с переменными ./playbooks/roles/ansible-role/defaults/main.yml, где укажем требуемую версию ansible:~~
```
~/task/sopo/ansible$ cat ./playbooks/roles/ansible-role/defaults/main.yml
---
ansible_version: 2.10.2
```
6. Выполним инструкции плейбука с созданной на шае 1 конфигурацией узлов командой: `ansible-playbook -i ./environments/vbox/inventory ./playbooks/ansible.yml --ask-become-pass`
Результат выполнения команды представлен ниже:
```
~/task/sopo/ansible$ ansible-playbook -i ./environments/vbox/inventory ./playbooks/ansible.yml --ask-become-pass
/home/bassindd/.local/lib/python2.7/site-packages/ansible/parsing/vault/__init__.py:44: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in a future release.
  from cryptography.exceptions import InvalidSignature
BECOME password:

PLAY [all] *************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************
[DEPRECATION WARNING]: Distribution Ubuntu 16.04 on host bassind.ubuntu.vbox should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with prior Ansible releases.
A future Ansible release will default to using the discovered platform python for this host. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information. This feature will be removed in version 2.12. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [bassind.ubuntu.vbox]

TASK [ansible-role : Install python-apt - need move to deps or pre_tasks] **********************************************************************************************************************
ok: [bassind.ubuntu.vbox]

TASK [ansible-role : Install python-pip] *******************************************************************************************************************************************************
ok: [bassind.ubuntu.vbox]

TASK [ansible-role : Install ansible] **********************************************************************************************************************************************************
[WARNING]: The value {'major': 2, 'full': '2.9.12', 'string': '2.9.12', 'minor': 9, 'revision': 12} (type dict) in a string field was converted to u"{'major': 2, 'full': '2.9.12', 'string':
'2.9.12', 'minor': 9, 'revision': 12}" (type string). If this does not look like what you expect, quote the entire value to ensure it does not change.
changed: [bassind.ubuntu.vbox]

PLAY RECAP *************************************************************************************************************************************************************************************
bassind.ubuntu.vbox        : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
7. Проверим версию Ansible на подчиненной машине:
```
ansible --version
ansible 2.10.2
  config file = None
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python2.7/dist-packages/ansible
  executable location = /usr/local/bin/ansible
  python version = 2.7.12 (default, Jul 21 2020, 15:19:50) [GCC 5.4.0 20160609]
```
