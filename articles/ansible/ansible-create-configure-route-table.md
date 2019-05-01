---
title: Samouczek — Konfigurowanie tabel tras platformy Azure za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, zmienianie i usuwanie tabel tras platformy Azure za pomocą rozwiązania Ansible
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3d20a7bb98ba266850baa0512f5b767f8b649767
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707877"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Samouczek: Konfigurowanie tabel tras platformy Azure za pomocą rozwiązania Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli potrzebujesz większej kontroli nad routingu w danym środowisku, można utworzyć [tabeli tras](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Utwórz tabelę tras Utwórz sieć wirtualną i podsieć kojarzenie tabeli tras z podsiecią usuwanie skojarzenia tabelę tras z podsiecią, tworzenie i usuwanie kieruje zapytania tabeli tras, usuwanie tabeli tras

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Kod elementu playbook w tej sekcji tworzy tabelę tras. Aby uzyskać informacji na temat limitów tabeli tras, zobacz [limity platformy Azure](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Zapisz następujący podręcznik jako `route_table_create.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Kod elementu playbook w tej sekcji:

* Tworzy sieć wirtualną
* Tworzy podsieć w sieci wirtualnej
* Kojarzy tabelę tras z podsiecią

Tabele tras nie są skojarzone z sieciami wirtualnymi. Przeciwnie tabele tras są skojarzone z podsiecią sieci wirtualnej.

Tabeli wirtualnej sieci i trasy musi współistnieć w tej samej lokalizacji platformy Azure i subskrypcji.

Podsieci i tabel tras relacją jeden do wielu. Można zdefiniować podsieci bez tabeli tras skojarzone lub tabeli tras. Tabele tras może być skojarzony z Brak, jeden lub wiele podsieci. 

Ruch z podsieci jest kierowany na podstawie:

- trasy zdefiniowane tabele tras
- [Trasy domyślne](/azure/virtual-network/virtual-networks-udr-overview#default)
- propagowane trasy z sieci lokalnej

Sieć wirtualna muszą być podłączone do bramy sieci wirtualnej platformy Azure. Brama może być ExpressRoute lub sieci VPN, jeśli przy użyciu protokołu BGP z bramą sieci VPN.

Zapisz następujący podręcznik jako `route_table_associate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuwanie skojarzenia tabeli tras z podsiecią

Kod elementu playbook w tej sekcji dissociates tabelę tras z podsiecią.

Usuwanie skojarzenia tabelę tras z podsiecią, ustawić `route_table` podsieci do `None`. 

Zapisz następujący podręcznik jako `route_table_dissociate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Tworzenie trasy

Kod elementu playbook w tej sekcji trasy w tabeli tras. 

Zapisz następujący podręcznik jako `route_create.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:

* `virtual_network_gateway` jest zdefiniowany jako `next_hop_type`. Aby uzyskać więcej informacji na temat jak platforma Azure wybiera trasy, zobacz [Omówienie routingu](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` jest zdefiniowany jako `10.1.0.0/16`. Nie można zduplikować prefiksu w ramach tabeli tras.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Usuwanie trasy

Kod elementu playbook w tej sekcji usuwa trasę z tabeli tras.

Zapisz następujący podręcznik jako `route_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Pobieranie informacji o tabeli tras

Kod elementu playbook w tej sekcji używa modułu Ansible `azure_rm_routetable_facts` można pobrać informacji o tabeli tras.

Zapisz następujący podręcznik jako `route_table_facts.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras

Kod elementu playbook w tej sekcji tabeli tras.

Po usunięciu tabeli tras wszystkich jego tras również zostaną usunięte.

Nie można usunąć tabeli tras, jeśli jest ona skojarzona z podsiecią. [Usuń skojarzenie tabeli tras z dowolnej podsieci](#dissociate-a-route-table-from-a-subnet) przed podjęciem próby wykonania można usunąć tabeli tras. 

Zapisz następujący podręcznik jako `route_table_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)