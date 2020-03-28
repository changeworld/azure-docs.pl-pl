---
title: Samouczek — konfigurowanie komunikacji równorzędnej sieci wirtualnej platformy Azure przy użyciu ansible
description: Dowiedz się, jak używać ansible do łączenia sieci wirtualnych z komunikacją równorzędną sieci wirtualnej.
keywords: ansible, azure, devops, bash, playbook, networking, peering
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155730"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Samouczek: Konfigurowanie komunikacji równorzędnej sieci wirtualnej platformy Azure przy użyciu ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Komunikacja równorzędna sieci wirtualnej umożliwia](/azure/virtual-network/virtual-network-peering-overview) bezproblemowe łączenie dwóch sieci wirtualnych platformy Azure. Po wzniebowtęglowane dwie sieci wirtualne są wyświetlane jako jedna do celów łączności. 

Ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej za pośrednictwem prywatnych adresów IP. Podobnie ruch między maszynami wirtualnymi w sieci wirtualnej równorzędnej jest kierowany za pośrednictwem infrastruktury szkieletowej firmy Microsoft. W rezultacie maszyny wirtualne w różnych sieciach wirtualnych mogą komunikować się ze sobą.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie dwóch sieci wirtualnych
> * Równorzędnie dwóch sieci wirtualnych
> * Usuwanie komunikacji równorzędnej między dwiema sieciami

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Tworzenie dwóch grup zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Przykładowy kod podręcznika w tej sekcji jest używany do:

- Tworzenie dwóch grup zasobów 

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

Przykładowy kod podręcznika w tej sekcji jest używany do:

- Tworzenie sieci wirtualnej
- Tworzenie podsieci w sieci wirtualnej

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

Przykładowy kod podręcznika w tej sekcji jest używany do:

- Tworzenie sieci wirtualnej
- Tworzenie podsieci w sieci wirtualnej

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

## <a name="peer-the-two-virtual-networks"></a>Równorzędnie dwóch sieci wirtualnych

Przykładowy kod podręcznika w tej sekcji jest używany do:

- Inicjowanie komunikacji równorzędnej w sieci wirtualnej
- Peer dwie wcześniej utworzone sieci wirtualne

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

Przykładowy kod podręcznika w tej sekcji jest używany do:

- Usuwanie komunikacji równorzędnej między dwiema wcześniej utworzonymi sieciami wirtualnymi

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Pobierz przykładowy podręcznik

Istnieją dwa sposoby uzyskania pełnego przykładowego podręcznika:

- [Pobierz podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) i zapisz `vnet_peering.yml`go w pliku .
- Utwórz nowy `vnet_peering.yml` plik o nazwie i skopiuj do niego następującą zawartość:

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

## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowego podręcznika

Przykładowy kod podręcznika w tej sekcji służy do testowania różnych funkcji pokazanych w tym samouczku.

Oto kilka kluczowych uwag, które należy wziąć pod uwagę podczas pracy z przykładowym podręcznikiem:

- W `vars` sekcji zastąp `{{ resource_group_name }}` symbol zastępczy nazwą grupy zasobów.

Uruchom podręcznik za pomocą polecenia ansible-playbook:

```bash
ansible-playbook vnet_peering.yml
```

Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

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

Gdy nie są już potrzebne, usuń zasoby utworzone w tym artykule. 

Przykładowy kod podręcznika w tej sekcji jest używany do:

- Usuwanie dwóch grup zasobów utworzonych wcześniej

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

Oto kilka kluczowych uwag, które należy wziąć pod uwagę podczas pracy z przykładowym podręcznikiem:

- Zastąp `{{ resource_group_name-1 }}` symbol zastępczy nazwą pierwszej utworzonej grupy zasobów.
- Zastąp `{{ resource_group_name-2 }}` symbol zastępczy nazwą drugiej utworzonej grupy zasobów.
- Wszystkie zasoby w ramach dwóch określonych grup zasobów zostaną usunięte.

Uruchom podręcznik za pomocą polecenia ansible-playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)