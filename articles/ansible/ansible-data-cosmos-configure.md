---
title: Samouczek — Konfigurowanie kont Azure Cosmos DB przy użyciu rozwiązania ansible
description: Dowiedz się, jak utworzyć i skonfigurować Azure Cosmos DB przy użyciu programu rozwiązania ansible
keywords: rozwiązania ansible, Azure, DevOps, bash, element PlayBook, Cosmo DB, baza danych
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8c898e3cb0747a442d50b7241ebfcf401148817e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156475"
---
# <a name="tutorial-configure-azure-cosmos-db-accounts-using-ansible"></a>Samouczek: Konfigurowanie kont Azure Cosmos DB przy użyciu rozwiązania ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Azure Cosmos DB](/azure/cosmos-db/) to usługa bazy danych, która obsługuje kilka typów baz danych. Typy tych baz danych obejmują: dokument, klucz-wartość, szeroką kolumnę i Graf. Za pomocą rozwiązania ansible można zautomatyzować wdrażanie i konfigurację zasobów w środowisku.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie konta
> * Pobierz klucze konta
> * Usuń konto

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-random-postfix"></a>Tworzenie losowego przyrostku

Przykładowy fragment kodu element PlayBook tworzy losowy przyrostek. Przyrostk jest używany jako część nazwy konta Azure Cosmos DB.

```yml
  - hosts: localhost
    tasks:
      - name: Prepare random postfix
        set_fact:
          rpfx: "{{ 1000 | random }}"
        run_once: yes
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów 

Przykładowy fragment kodu element PlayBook tworzy grupę zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Poniższy kod tworzy sieć wirtualną i podsieć dla konta Azure Cosmos DB:

```yml
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name }}"
      address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
      dns_servers:
        - 127.0.0.1
        - 127.0.0.3

  - name: Add subnet
    azure_rm_subnet:
      name: "{{ subnet_name }}"
      virtual_network_name: "{{ vnet_name }}"
      resource_group: "{{ resource_group }}"
      address_prefix_cidr: "10.1.0.0/24"
```

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Poniższy kod tworzy konto Cosmos DB:

```yml
  - name: Create instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      location: eastus
      kind: global_document_db
      geo_rep_locations:
        - name: eastus
          failover_priority: 0
        - name: westus
          failover_priority: 1
      database_account_offer_type: Standard
      is_virtual_network_filter_enabled: yes
      virtual_network_rules:
        - subnet:
            resource_group: "{{ resource_group }}"
            virtual_network_name: "{{ vnet_name }}"
            subnet_name: "{{ subnet_name }}"
          ignore_missing_vnet_service_endpoint: yes
      enable_automatic_failover: yes
```

Tworzenie konta może potrwać kilka minut.

## <a name="retrieve-the-keys"></a>Pobierz klucze

Poniższy kod pobiera klucze do użycia w aplikacji.

```yml
  - name: Get Cosmos DB Account facts with keys
    azure_rm_cosmosdbaccount_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      retrieve_keys: all
    register: output

  - name: Display Cosmos DB Acccount facts output
    debug:
      var: output
```

## <a name="delete-the-azure-cosmos-db-account"></a>Usuwanie konta Azure Cosmos DB

Na koniec ostatni fragment kodu przedstawia sposób usuwania konta Azure Cosmos DB.

```yml
  - name: Delete instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Pobierz przykładową element PlayBook

Istnieją dwa sposoby uzyskania kompletnej przykładowej element PlayBook:
- [Pobierz element PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/cosmosdb_create.yml) i Zapisz go w `cosmosdb.yml`.
- Utwórz nowy plik o nazwie `cosmosdb.yml` i skopiuj do niego następującą zawartość:

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- hosts: localhost
#  roles:
#    - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    location: eastus
    vnet_name: myVirtualNetwork
    subnet_name: mySubnet
    cosmosdbaccount_name: cosmos{{ rpfx }}

  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name }}"
        address_prefixes_cidr:
          - 10.1.0.0/16
          - 172.100.0.0/16
        dns_servers:
          - 127.0.0.1
          - 127.0.0.3

    - name: Add subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"

    - name: Create instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        location: eastus
        kind: global_document_db
        geo_rep_locations:
          - name: eastus
            failover_priority: 0
          - name: westus
            failover_priority: 1
        database_account_offer_type: Standard
        is_virtual_network_filter_enabled: yes
        virtual_network_rules:
          - subnet:
              resource_group: "{{ resource_group }}"
              virtual_network_name: "{{ vnet_name }}"
              subnet_name: "{{ subnet_name }}"
            ignore_missing_vnet_service_endpoint: yes
        enable_automatic_failover: yes

    - name: Get Cosmos DB Account facts with keys
      azure_rm_cosmosdbaccount_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        retrieve_keys: all
      register: output

    - name: Display Cosmos DB Account facts output
      debug:
        var: output

    - name: Delete instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        state: absent
```

## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowej element PlayBook

W tej sekcji należy uruchomić element PlayBook w celu przetestowania różnych funkcji przedstawionych w tym artykule.

Przed uruchomieniem element PlayBook wprowadź następujące zmiany:
- W sekcji `vars` Zamień symbol zastępczy `{{ resource_group_name }}` na nazwę grupy zasobów.
- Upewnij się, że element "cosmosdbaccount_name zawiera tylko małe litery i jest globalnie unikatowy.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook cosmosdb.yml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)