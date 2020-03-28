---
title: Samouczek — konfigurowanie baz danych w bazie danych platformy Azure dla mysql przy użyciu ansible
description: Dowiedz się, jak można użyć rozwiązania Ansible do utworzenia i skonfigurowania serwera usługi Azure Database for MySQL
keywords: ansible, azure, devops, bash, element playbook, mysql, baza danych
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9cd574417733518b993bb242c2c168aba338e34a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78247865"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Samouczek: Konfigurowanie baz danych w bazie danych platformy Azure dla mysql przy użyciu ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Usługa Azure Database for MySQL](/azure/mysql/overview) to relacyjne usługi bazy danych oparte na wersji społeczności MySQL. Usługa Azure Database for MySQL umożliwia zarządzanie bazami danych MySQL w aplikacjach sieci web.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie serwera MySql
> * Tworzenie bazy danych MySql
> * Konfigurowanie reguły zapory w taki sposób, aby aplikacja zewnętrzna mogła łączyć się z serwerem
> * Łączenie się z serwerem MySql z usługi Azure Cloud Shell
> * Zapytanie o dostępne serwery MySQL
> * Wyświetlanie listy wszystkich baz danych na połączonych serwerach

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Kod podręcznika w tej sekcji tworzy grupę zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.  

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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:

* Zostanie utworzona `myResourceGroup` grupa zasobów o nazwie.
* Grupa zasobów jest tworzona w `eastus` lokalizacji:

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Tworzenie serwera MySQL i bazy danych

Kod podręcznika w tej sekcji tworzy serwer MySQL i wystąpienie usługi Azure Database for MySQL. Nowy serwer MySQL jest gen 5 basic purpose serwer z `mysqlserveransible`jednym vCore i nosi nazwę . Wystąpienie bazy `mysqldbansible`danych nosi nazwę .

Aby uzyskać więcej informacji na temat warstw cenowych, zobacz [Warstwy cenowe Usługi Azure Database dla MySQL](/azure/mysql/concepts-pricing-tiers). 

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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:

* W `vars` sekcji wartość musi `mysqlserver_name` być unikatowa.
* W `vars` sekcji zamień `<server_admin_password>` je na hasło.

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory

Reguła zapory na poziomie serwera umożliwia aplikacji zewnętrznej łączenie się z serwerem za pośrednictwem zapory usługi Azure MySQL. Przykładami aplikacji zewnętrznych `mysql` są narzędzie wiersza polecenia i mysql workbench.

Kod podręcznika w tej sekcji tworzy `extenalaccess` regułę zapory o nazwie, która umożliwia połączenia z dowolnego zewnętrznego adresu IP. 

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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:

* W sekcji vars `startIpAddress` zamień i `endIpAddress`. Użyj zakresu adresów IP, które odpowiadają zakresowi, z którego będziesz łączyć.
* Połączenia z usługą Azure Database for MySQL korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.
* Podręcznik wykorzystuje `azure_rm_resource` moduł, który umożliwia bezpośrednie korzystanie z interfejsu API REST.

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Nawiązywanie połączenia z serwerem

W tej sekcji używasz usługi Azure Cloud Shell, aby połączyć się z serwerem utworzonym wcześniej.

1. Otwórz shell.azure.com, wybierając poniżej.

   [![Uruchom osadź](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

1. Wprowadź następujący kod:

    ```sql
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. W wierszu polecenia wprowadź następujące polecenie, aby zbadać stan serwera:

    ```sql
    mysql> status
    ```
    
    Jeśli wszystko pójdzie dobrze, zobaczysz wyjście podobne do następujących wyników:
    
    ```output
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
    
## <a name="query-mysql-servers"></a>Zapytanie o serwery MySQL

Kod podręcznika w tej sekcji wysyła zapytania `myResourceGroup` do serwerów MySQL i wyświetla listę baz danych na znalezionych serwerach.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook mysql_query.yml
```

Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

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

Dla bazy danych MySQL są również widoczne następujące dane wyjściowe:

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

Gdy nie są już potrzebne, usuń zasoby utworzone w tym artykule. 

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)