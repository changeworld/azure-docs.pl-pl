---
title: Tworzenie i konfigurowanie serwera usługi Azure Database for MySQL za pomocą rozwiązania Ansible (wersja zapoznawcza)
description: Dowiedz się, jak można użyć rozwiązania Ansible do utworzenia i skonfigurowania serwera usługi Azure Database for MySQL
ms.service: ansible
keywords: ansible, azure, devops, bash, element playbook, mysql, baza danych
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: b549aeaf24bd774245ee1f2ff6924ac1f6dbeee3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427900"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible-preview"></a>Tworzenie i konfigurowanie serwera usługi Azure Database for MySQL za pomocą rozwiązania Ansible (wersja zapoznawcza)
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. 

W tym przewodniku Szybki start pokazano, jak za pomocą rozwiązania Ansible można utworzyć serwer usługi Azure Database for MySQL i skonfigurować jego regułę zapory. Te zadania możesz wykonać w ciągu okołu pięciu minut za pomocą witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Rozwiązanie Ansible 2.7 jest wymagane do uruchamiania następujących przykładowych elementów playbook w ramach tego samouczka. Rozwiązanie Ansible 2.7 RC możesz zainstalować, uruchamiając polecenie `sudo pip install ansible[azure]==2.7.0rc2`. Po wydaniu wersji 2.7 rozwiązania Ansible nie trzeba będzie tutaj określać wersji, ponieważ wersja 2.7 będzie wersją domyślną.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.  

W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie **myResourceGroup** w lokalizacji **eastus**:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Zapisz powyższy element playbook jako plik **rg.yml**. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Tworzenie serwera MySQL i bazy danych
Poniższy przykład umożliwia utworzenie serwera MySQL o nazwie **mysqlserveransible** i wystąpienia usługi Azure Database for MySQL o nazwie **mysqldbansible**. Jest to serwer ogólnego przeznaczenia 5. generacji z jednym rdzeniem wirtualnym. 

Wartość zmiennej **mysqlserver_name** musi być unikatowa. Aby ustalić prawidłowe wartości dla poszczególnych regionów i warstw, zapoznaj się z [dokumentacją warstw cenowych](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Zastąp wartość `<server_admin_password>` przy użyciu hasła.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Zapisz powyższy element playbook jako plik **mysql_create.yml**. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory
Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej na nawiązywanie połączeń z Twoim serwerem przez zaporę usługi Azure MySQL. Przykładem aplikacji zewnętrznej jest narzędzie wiersza polecenia **mysql** lub program MySQL Workbench.
Poniższy przykład umożliwia utworzenie reguły zapory o nazwie **externalaccess**, która zezwala na połączenia z dowolnego zewnętrznego adresu IP. 

Wprowadź własne wartości właściwości **startIpAddress** i **endIpAddress**. Użyj zakresów adresów IP odpowiednich dla urządzeń, z których będzie nawiązywane połączenie. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

> [!NOTE]
> Połączenia z usługą Azure Database for MySQL korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.
> 

Tutaj w celu wykonania tego zadania jest używany moduł **azure_rm_resource**. Umożliwia on bezpośrednie korzystanie z interfejsu API REST.

Zapisz powyższy element playbook jako plik **mysql_firewall.yml**. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>Nawiązywanie połączenia z serwerem przy użyciu narzędzia wiersza polecenia
Program MySQL możesz [pobrać](https://dev.mysql.com/downloads/) i zainstalować na swoim komputerze. Zamiast tego możesz wybrać przycisk **Wypróbuj** przy przykładach kodu lub przycisk **>_** na pasku narzędzi w prawym górnym rogu witryny Azure Portal i otworzyć usługę **Azure Cloud Shell**.

Wprowadź następujące polecenia: 

1. Nawiąż połączenie z serwerem przy użyciu narzędzia wiersza polecenia **mysql**:
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. Wyświetl stan serwera:
```sql
 mysql> status
```

Jeśli wszystko pójdzie dobrze, narzędzie wiersza polecenia powinno zwrócić następujący tekst:

```
demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65233
Server version: 5.6.39.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper

Connection id:          65233
Current database:
Current user:           mysqladmin@13.76.42.93
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 36 min 21 sec

Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
--------------
```

## <a name="using-facts-to-query-mysql-servers"></a>Wykonywanie zapytań względem serwerów MySQL przy użyciu faktów
Poniższy przykład umożliwia wykonanie zapytania względem serwerów MySQL w grupie **myResourceGroup**, co oznacza wszystkie bazy danych na serwerach:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Zapisz powyższy element playbook jako plik **mysql_query.yml**. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook mysql_query.yml
```

Zostaną wyświetlone następujące dane wyjściowe dla serwera MySQL: 
```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Zostaną także wyświetlone następujące dane wyjściowe dla bazy danych MySQL:
```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz tych zasobów, możesz je usunąć przez uruchomienie następującego przykładu. Usuwa on grupę zasobów o nazwie **myResourceGroup**. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Zapisz powyższy element playbook jako plik **rg_delete.yml**. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:
```bash
ansible-playbook rg_delete.yml
```

Jeśli chcesz usunąć tylko nowo utworzony serwer MySQL, uruchom następujący przykład:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

Zapisz powyższy element playbook jako plik **mysql_delete.yml**. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](https://docs.microsoft.com/azure/ansible/)
