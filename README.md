# Домашнее задание к занятию "08.02 Работа с Playbook"

## Подготовка к выполнению

1. Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.
2. Скачайте [playbook](https://github.com/netology-code/mnt-homeworks/blob/master/08-ansible-02-playbook/playbook) из репозитория с домашним заданием и перенесите его в свой репозиторий.
3. Подготовьте хосты в соотвтествии с группами из предподготовленного playbook.
4. Скачайте дистрибутив [java](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) и положите его в директорию `playbook/files/`.

## Основная часть

(1) Приготовьте свой собственный inventory файл `prod.yml`.

*Ответ:* сделано.

(2) Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает kibana.

Ответ:* сделано.

(3) При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.

Ответ:* сделано.

(4) Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, сгенерировать конфигурацию с параметрами.

Ответ:* сделано.

(5) Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
*Ответ:* ошибок не было.

(6) Попробуйте запустить playbook на этом окружении с флагом `--check`.

*Ответ:*

```
root@cadcixztkv:~/hw_ansible_2/playbook# ansible-playbook site.yml --check

PLAY [Install Java] ********************************************************************************

TASK [Gathering Facts] *****************************************************************************
ok: [node_elasticsearch]
ok: [node_kibana]

TASK [Set facts for Java 11 vars] ******************************************************************
ok: [node_elasticsearch]
ok: [node_kibana]

TASK [Upload .tar.gz file containing binaries from local storage] **********************************
changed: [node_kibana]
changed: [node_elasticsearch]

TASK [Ensure installation dir exists] **************************************************************
changed: [node_elasticsearch]
changed: [node_kibana]

TASK [Extract java in the installation directory] **************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: NoneType: None
fatal: [node_elasticsearch]: FAILED! => {"changed": false, "msg": "dest '/opt/jdk/18' must be an existing dir"}
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: NoneType: None
fatal: [node_kibana]: FAILED! => {"changed": false, "msg": "dest '/opt/jdk/18' must be an existing dir"}

PLAY RECAP *****************************************************************************************
node_elasticsearch         : ok=4    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
node_kibana                : ok=4    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

```


(7) Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.

*Ответ:*

root@cadcixztkv:~/hw_ansible_2/playbook# ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Java] ********************************************************************************

TASK [Gathering Facts] *****************************************************************************
ok: [node_kibana]
ok: [node_elasticsearch]

TASK [Set facts for Java 11 vars] ******************************************************************
ok: [node_elasticsearch]
ok: [node_kibana]

TASK [Upload .tar.gz file containing binaries from local storage] **********************************
diff skipped: source file size is greater than 104448
changed: [node_elasticsearch]
diff skipped: source file size is greater than 104448
changed: [node_kibana]

TASK [Ensure installation dir exists] **************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "/opt/jdk/18",
-    "state": "absent"
+    "state": "directory"
 }

changed: [node_elasticsearch]
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "/opt/jdk/18",
-    "state": "absent"
+    "state": "directory"
 }

changed: [node_kibana]

TASK [Extract java in the installation directory] **************************************************
changed: [node_kibana]
changed: [node_elasticsearch]

TASK [Export environment variables] ****************************************************************
--- before
+++ after: /root/.ansible/tmp/ansible-local-114541866nwdk0o/tmp10m4ptua/jdk.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export JAVA_HOME=/opt/jdk/18
+export PATH=$PATH:$JAVA_HOME/bin
\ No newline at end of file

changed: [node_elasticsearch]
--- before
+++ after: /root/.ansible/tmp/ansible-local-114541866nwdk0o/tmpoaeqvjfe/jdk.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export JAVA_HOME=/opt/jdk/18
+export PATH=$PATH:$JAVA_HOME/bin
\ No newline at end of file

changed: [node_kibana]
[WARNING]: Could not match supplied host pattern, ignoring: elasticsearch

PLAY [Install Elasticsearch] ***********************************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: kibana

PLAY [Install Kibana] ******************************************************************************
skipping: no hosts matched

PLAY RECAP *****************************************************************************************
node_elasticsearch         : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node_kibana                : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


(8) Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.

*Ответ:*

```
root@cadcixztkv:~/hw_ansible_2/playbook# ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Java] ********************************************************************************

TASK [Gathering Facts] *****************************************************************************
ok: [node_elasticsearch]
ok: [node_kibana]

TASK [Set facts for Java 11 vars] ******************************************************************
ok: [node_elasticsearch]
ok: [node_kibana]

TASK [Upload .tar.gz file containing binaries from local storage] **********************************
ok: [node_elasticsearch]
ok: [node_kibana]

TASK [Ensure installation dir exists] **************************************************************
ok: [node_elasticsearch]
ok: [node_kibana]

TASK [Extract java in the installation directory] **************************************************
skipping: [node_elasticsearch]
skipping: [node_kibana]

TASK [Export environment variables] ****************************************************************
ok: [node_elasticsearch]
ok: [node_kibana]
[WARNING]: Could not match supplied host pattern, ignoring: elasticsearch

PLAY [Install Elasticsearch] ***********************************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: kibana

PLAY [Install Kibana] ******************************************************************************
skipping: no hosts matched

PLAY RECAP *****************************************************************************************
node_elasticsearch         : ok=5    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
node_kibana                : ok=5    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

```

(9) Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.

(10) Готовый playbook выложите в свой репозиторий, в ответ предоставьте ссылку на него.
