---
title: Samouczek — Konfigurowanie komunikacji równorzędnej sieci wirtualnej platformy Azure za pomocą rozwiązania ansible
description: Dowiedz się, jak używać programu rozwiązania ansible do łączenia sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych.
keywords: rozwiązania ansible, Azure, DevOps, bash, element PlayBook, sieci, Komunikacja równorzędna
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 180bdff48a2ace4dfee1d1cb10eb75a33d360f4c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241236"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Samouczek: Konfigurowanie komunikacji równorzędnej sieci wirtualnej platformy Azure za pomocą rozwiązania ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Komunikacja równorzędna sieci wirtualnych (VNET)](/azure/virtual-network/virtual-network-peering-overview) umożliwia bezproblemowe łączenie dwóch sieci wirtualnych platformy Azure. Po nawiązaniu połączenia równorzędnego dwie sieci wirtualne są wyświetlane jako jedna do celów związanych z łącznością. 

Ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej za pośrednictwem prywatnych adresów IP. Podobnie ruch między maszynami wirtualnymi w równorzędnej sieci wirtualnej jest kierowany za pośrednictwem infrastruktury szkieletowej firmy Microsoft. W związku z tym maszyny wirtualne w różnych sieciach wirtualnych mogą komunikować się ze sobą.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie dwóch sieci wirtualnych
> * Równorzędne dwie sieci wirtualne
> * Usuwanie komunikacji równorzędnej między dwiema sieciami

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Utwórz dwie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Przykładowy kod element PlayBook w tej sekcji jest używany do:

- Utwórz dwie grupy zasobów 

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

Przykładowy kod element PlayBook w tej sekcji jest używany do:

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

Przykładowy kod element PlayBook w tej sekcji jest używany do:

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

## <a name="peer-the-two-virtual-networks"></a>Równorzędne dwie sieci wirtualne

Przykładowy kod element PlayBook w tej sekcji jest używany do:

- Inicjowanie komunikacji równorzędnej sieci wirtualnej
- Dwie wcześniej utworzone sieci wirtualne

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

## <a name="delete-the-virtual-network-peering"></a>Usuń komunikację równorzędną sieci wirtualnej

Przykładowy kod element PlayBook w tej sekcji jest używany do:

- Usuń komunikację równorzędną między dwiema utworzonymi wcześniej sieciami wirtualnymi

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Pobierz przykładową element PlayBook

Istnieją dwa sposoby uzyskania kompletnej przykładowej element PlayBook:

- [Pobierz element PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) i Zapisz go w `vnet_peering.yml`.
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

## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowej element PlayBook

Przykładowy kod element PlayBook w tej sekcji jest używany do testowania różnych funkcji przedstawionych w tym samouczku.

Poniżej przedstawiono niektóre kluczowe uwagi, które należy wziąć pod uwagę podczas pracy z przykładową element PlayBook:

- W sekcji `vars` Zastąp symbol zastępczy `{{ resource_group_name }}` nazwą grupy zasobów.

Uruchom element PlayBook za pomocą polecenia rozwiązania ansible-element PlayBook:

```bash
ansible-playbook vnet_peering.yml
```

Po uruchomieniu element PlayBook są wyświetlane dane wyjściowe podobne do następujących:

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

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Przykładowy kod element PlayBook w tej sekcji jest używany do:

- Usuń dwie utworzone wcześniej grupy zasobów

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

Poniżej przedstawiono niektóre kluczowe uwagi, które należy wziąć pod uwagę podczas pracy z przykładową element PlayBook:

- Zastąp symbol zastępczy `{{ resource_group_name-1 }}` nazwą pierwszej utworzonej grupy zasobów.
- Zastąp symbol zastępczy `{{ resource_group_name-2 }}` nazwą drugiej utworzonej grupy zasobów.
- Wszystkie zasoby w ramach dwóch określonych grup zasobów zostaną usunięte.

Uruchom element PlayBook za pomocą polecenia rozwiązania ansible-element PlayBook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)