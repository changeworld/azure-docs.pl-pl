---
title: Samouczek — Konfigurowanie sieci wirtualnej platformy Azure komunikację równorzędną za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą rozwiązania Ansible do łączenia sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej.
keywords: rozwiązanie ansible, azure, metodyki devops, bash, elementu playbook, sieć, komunikacja równorzędna
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: d3d1aab17a94e928d113246f280c3391cae85655
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767089"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Samouczek: Skonfiguruj sieć wirtualną platformy Azure komunikację równorzędną za pomocą rozwiązania Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Komunikacja równorzędna sieci wirtualnych (VNet)](/azure/virtual-network/virtual-network-peering-overview) pozwala na bezproblemowe łączenie dwóch sieci wirtualnych platformy Azure. Po nawiązaniu komunikacji równorzędnej, dwie sieci wirtualne są traktowane jako jedna do celów związanych z łącznością. 

Ruch odbywa się między maszynami wirtualnymi w tej samej sieci wirtualnej przy użyciu prywatnych adresów IP. Podobnie ruch między maszynami wirtualnymi w równorzędnych sieci wirtualnych odbywa się za pośrednictwem infrastruktury sieci szkieletowej firmy Microsoft. W rezultacie maszyny wirtualne w różnych sieciach wirtualnych mogą komunikować się ze sobą.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie dwóch sieci wirtualnych
> * Komunikacja równorzędna dwóch sieci wirtualnych
> * Usuwanie komunikacji równorzędnej między dwiema sieciami

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Utworzyć dwie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Przykładowy kod elementu playbook w tej sekcji służy do:

- Utworzyć dwie grupy zasobów 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Tworzenie pierwszej sieci wirtualnej

Przykładowy kod elementu playbook w tej sekcji służy do:

- Tworzenie sieci wirtualnej
- Utwórz podsieć w sieci wirtualnej

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>Tworzenie drugiej sieci wirtualnej

Przykładowy kod elementu playbook w tej sekcji służy do:

- Tworzenie sieci wirtualnej
- Utwórz podsieć w sieci wirtualnej

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>Komunikacja równorzędna dwóch sieci wirtualnych

Przykładowy kod elementu playbook w tej sekcji służy do:

- Inicjowanie, wirtualne sieci równorzędne
- Komunikacja równorzędna dwóch utworzone wcześniej sieciami wirtualnymi

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Usuwanie komunikacji równorzędnej sieci wirtualnej

Przykładowy kod elementu playbook w tej sekcji służy do:

- Usuwanie komunikacji równorzędnej między tymi dwoma utworzone wcześniej sieciami wirtualnymi

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Pobierz Podręcznik próbki

Istnieją dwa sposoby pobrania Podręcznik pełny przykład:

- [Pobierz Podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) i zapisać go w celu `vnet_peering.yml`.
- Utwórz nowy plik o nazwie `vnet_peering.yml` i skopiuj do niego następującą zawartość:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Uruchamianie elementu playbook próbki

Przykładowy kod elementu playbook w tej sekcji służy do testowania różnych funkcji, które przedstawiono w tym samouczku.

Poniżej przedstawiono ważne uwagi należy wziąć pod uwagę podczas pracy z elementu playbook próbki:

- W `vars` sekcji i Zastąp `{{ resource_group_name }}` nazwą grupy zasobów.

Uruchom element playbook przy użyciu polecenia playbook rozwiązania ansible:

```bash
ansible-playbook vnet_peering.yml
```

Po uruchomieniu elementu playbook, zobaczysz dane wyjściowe podobne do następujących wyników:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń zasoby utworzone w tym artykule. 

Przykładowy kod elementu playbook w tej sekcji służy do:

- Usuwanie grupy zasobów utworzonej wcześniej

Zapisz następujący podręcznik jako `cleanup.yml`:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Poniżej przedstawiono ważne uwagi należy wziąć pod uwagę podczas pracy z elementu playbook próbki:

- Zastąp `{{ resource_group_name-1 }}` utworzony symbol zastępczy nazwą pierwszej grupy zasobów.
- Zastąp `{{ resource_group_name-2 }}` utworzony symbol zastępczy nazwą grupy zasobów, do drugiego.
- Zostaną usunięte wszystkie zasoby w dwóch grupach określonego zasobu.

Uruchom element playbook przy użyciu polecenia playbook rozwiązania ansible:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)