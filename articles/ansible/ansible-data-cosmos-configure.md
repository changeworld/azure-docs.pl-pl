---
title: Samouczek — Konfigurowanie konta usługi Azure Cosmos DB za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować usługi Azure Cosmos DB za pomocą rozwiązania Ansible
keywords: ansible, azure, devops, bash, playbook, cosmo db, database
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 06d416358c1886f09b0b2336cc1ea53ce89947ae
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230803"
---
# <a name="tutorial-configure-azure-cosmos-db-accounts-using-ansible"></a>Samouczek: Konfiguruj konta usługi Azure Cosmos DB za pomocą rozwiązania Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Usługa Azure Cosmos DB](/azure/cosmos-db/) usługa bazy danych, która obsługuje kilka typów bazy danych. Bazy danych należą do dokumentów, pary klucz wartość, szerokokolumnowe i wykres. Za pomocą rozwiązania Ansible, możesz zautomatyzować wdrażanie i konfigurowanie zasobów w danym środowisku.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie konta
> * Pobieranie kluczy konta
> * Usuń konto

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-random-postfix"></a>Tworzenie losowego przyrostka

Fragment kodu z elementu playbook przykładowy tworzy losowe odwrotnej notacji. Przyrostkowa jest używany jako część nazwy konta usługi Azure Cosmos DB.

```yml
  - hosts: localhost
    tasks:
      - name: Prepare random postfix
        set_fact:
          rpfx: "{{ 1000 | random }}"
        run_once: yes
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów 

Fragment kodu z elementów playbook przykładowy tworzy grupę zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Poniższy kod tworzy sieć wirtualną i podsieć dla usługi Azure Cosmos DB:

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

## <a name="create-an-azure-cosmos-db-account"></a>Utwórz konto usługi Azure Cosmos DB

Poniższy kod tworzy konto usługi Cosmos DB:

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

Tworzenie konta potrwa kilka minut.

## <a name="retrieve-the-keys"></a>Pobieranie kluczy

Poniższy kod pobiera kluczy do użycia w aplikacji.

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

## <a name="delete-the-azure-cosmos-db-account"></a>Usuwanie konta usługi Azure Cosmos DB

Na koniec ostatni fragment kodu pokazuje, jak można usunąć konta usługi Azure Cosmos DB.

```yml
  - name: Delete instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Pobierz Podręcznik próbki

Istnieją dwa sposoby pobrania Podręcznik pełny przykład:
- [Pobierz Podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/cosmosdb_create.yml) i zapisać go w celu `cosmosdb.yml`.
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

## <a name="run-the-sample-playbook"></a>Uruchamianie elementu playbook próbki

W tej sekcji Uruchamianie elementu playbook, aby przetestować różne funkcje przedstawione w tym artykule.

Przed uruchomieniem elementu playbook, należy wprowadzić następujące zmiany:
- W `vars` sekcji i Zastąp `{{ resource_group_name }}` nazwą grupy zasobów.
- Upewnij się, że "cosmosdbaccount_name zawiera tylko małe litery oraz jest globalnie unikatowa.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook cosmosdb.yml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz poniższy kod jako `cleanup.yml`:

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

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)