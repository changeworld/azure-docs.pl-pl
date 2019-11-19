---
title: Samouczek — Konfigurowanie baz danych w Azure Database for MySQL przy użyciu rozwiązania ansible
description: Dowiedz się, jak można użyć rozwiązania Ansible do utworzenia i skonfigurowania serwera usługi Azure Database for MySQL
keywords: ansible, azure, devops, bash, element playbook, mysql, baza danych
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d8c8fd361f37a9cb961012807fe40b905c10c047
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156533"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Samouczek: Konfigurowanie baz danych w Azure Database for MySQL przy użyciu rozwiązania ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) jest usługą relacyjnej bazy danych opartą na programie MySQL Community Edition. Azure Database for MySQL umożliwia zarządzanie bazami danych MySQL w aplikacjach sieci Web.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie serwera MySql
> * Tworzenie bazy danych MySql
> * Konfigurowanie reguły filewall tak, aby aplikacja zewnętrzna mogła łączyć się z serwerem
> * Nawiązywanie połączenia z serwerem MySql za pomocą usługi Azure Cloud Shell
> * Zbadaj dostępne serwery MySQL
> * Wyświetl listę wszystkich baz danych w podłączonych serwerach

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Kod element PlayBook w tej sekcji tworzy grupę zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.  

Zapisz następujący podręcznik jako `rg.yml`:

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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

* Zostanie utworzona grupa zasobów o nazwie `myResourceGroup`.
* Grupa zasobów jest tworzona w lokalizacji `eastus`:

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Tworzenie serwera MySQL i bazy danych

Kod element PlayBook w tej sekcji tworzy serwer MySQL i wystąpienie Azure Database for MySQL. Nowy serwer MySQL to serwer ogólnego przeznaczenia w generacji 5 z jednym rdzeń wirtualny i nosi nazwę `mysqlserveransible`. Wystąpienie bazy danych ma nazwę `mysqldbansible`.

Aby uzyskać więcej informacji na temat warstw cenowych, zobacz [Azure Database for MySQL warstwy cenowe](/azure/mysql/concepts-pricing-tiers). 

Zapisz następujący podręcznik jako `mysql_create.yml`:

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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

* W sekcji `vars` wartość `mysqlserver_name` musi być unikatowa.
* W sekcji `vars` Zastąp `<server_admin_password>` hasłem.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory

Reguła zapory na poziomie serwera umożliwia zewnętrznej aplikacji łączenie się z serwerem za pomocą zapory usługi Azure MySQL. Przykładami aplikacji zewnętrznych są `mysql` narzędzia wiersza polecenia i MySQL Workbench.

Kod element PlayBook w tej sekcji tworzy regułę zapory o nazwie `extenalaccess`, która umożliwia nawiązywanie połączeń z dowolnego zewnętrznego adresu IP. 

Zapisz następujący podręcznik jako `mysql_firewall.yml`:

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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

* W sekcji zmiennych Zastąp `startIpAddress` i `endIpAddress`. Użyj zakresu adresów IP odpowiadającego zakresowi, z którego będziesz się łączyć.
* Połączenia z usługą Azure Database for MySQL korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.
* Element PlayBook używa modułu `azure_rm_resource`, który umożliwia bezpośrednie korzystanie z interfejsu API REST.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Nawiązywanie połączenia z serwerem

W tej sekcji użyjesz usługi Azure Cloud Shell, aby nawiązać połączenie z utworzonym wcześniej serwerem.

1. Wybierz przycisk **Wypróbuj** w poniższym kodzie:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. W wierszu polecenia wprowadź następujące polecenie, aby wykonać zapytanie o stan serwera:

    ```sql
    mysql> status
    ```
    
    Jeśli wszystko przebiegnie prawidłowo, zobaczysz dane wyjściowe podobne do następujących:
    
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
    
## <a name="query-mysql-servers"></a>Kwerenda serwerów MySQL

Kod element PlayBook w tej sekcji wysyła zapytania do serwerów MySQL w `myResourceGroup` i wyświetla bazy danych na znalezionych serwerach.

Zapisz następujący podręcznik jako `mysql_query.yml`:

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook mysql_query.yml
```

Po uruchomieniu element PlayBook są wyświetlane dane wyjściowe podobne do następujących:

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

Zobaczysz również następujące dane wyjściowe dla bazy danych MySQL:

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

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz następujący podręcznik jako `cleanup.yml`:

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)