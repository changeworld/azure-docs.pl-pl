---
title: Samouczek — Konfigurowanie tabel tras platformy Azure przy użyciu rozwiązania ansible
description: Dowiedz się, jak tworzyć i usuwać tabele tras platformy Azure oraz zarządzać nimi za pomocą rozwiązania ansible. Dowiedz się również, jak tworzyć i usuwać trasy.
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 0ffc00606eac4cf57cdf19072986373f5602aafa
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614337"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Samouczek: Konfigurowanie tabel tras platformy Azure przy użyciu rozwiązania ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli potrzebujesz większej kontroli nad routingiem środowiska, możesz utworzyć [tabelę tras](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Tworzenie tabeli tras tworzenie sieci wirtualnej i podsieci kojarzenie tabeli tras z podsiecią Usuń skojarzenie tabeli tras z podsieci tworzenie i usuwanie tras kwerenda tabeli tras Usuwanie tabeli tras

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Kod element PlayBook w tej sekcji tworzy tabelę tras. Aby uzyskać informacje na temat limitów tabel tras, zobacz [limity platformy Azure](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Kod element PlayBook w tej sekcji:

* Tworzy sieć wirtualną
* Tworzy podsieć w sieci wirtualnej
* Kojarzy tabelę tras z podsiecią

Tabele tras nie są skojarzone z sieciami wirtualnymi. Zamiast tego tabele tras są skojarzone z podsiecią sieci wirtualnej.

Sieć wirtualna i tabela tras muszą współistnieć w tej samej lokalizacji i subskrypcji platformy Azure.

Podsieci i tabele tras mają relację "jeden do wielu". Podsieć może być zdefiniowana bez skojarzonej tabeli tras lub jednej tabeli tras. Tabele tras można kojarzyć z brakmi, jedną lub wieloma podsieciami. 

Ruch z podsieci jest kierowany na podstawie:

- Trasy zdefiniowane w tabelach tras
- [trasy domyślne](/azure/virtual-network/virtual-networks-udr-overview#default)
- trasy propagowane z sieci lokalnej

Sieć wirtualna musi być połączona z bramą sieci wirtualnej platformy Azure. Brama może być ExpressRoute lub sieci VPN w przypadku używania protokołu BGP z bramą sieci VPN.

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuwanie skojarzenia tabeli tras z podsiecią

Kod element PlayBook w tej sekcji tworzy skojarzenie tabeli tras z podsieci.

W przypadku usuwania skojarzenia tabeli tras z podsieci Ustaw `route_table` podsieci do `None`. 

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Tworzenie trasy

Kod element PlayBook w tej sekcji jest trasą w tabeli tras. 

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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

* `virtual_network_gateway` jest zdefiniowany jako `next_hop_type`. Aby uzyskać więcej informacji na temat sposobu wybierania tras przez platformę Azure, zobacz [Omówienie routingu](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` jest zdefiniowany jako `10.1.0.0/16`. Nie można zduplikować prefiksu w tabeli tras.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Usuwanie trasy

Kod element PlayBook w tej sekcji usuwa trasę z tabeli tras.

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Pobierz informacje o tabeli tras

Kod element PlayBook w tej sekcji używa `azure_rm_routetable_facts` modułu rozwiązania ansible do pobierania informacji o tabeli tras.

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras

Kod element PlayBook w tej sekcji a tabeli tras.

Po usunięciu tabeli tras wszystkie jej trasy również są usuwane.

Nie można usunąć tabeli tras, jeśli jest ona skojarzona z podsiecią. Usuń [skojarzenie tabeli tras z dowolnych podsieci](#dissociate-a-route-table-from-a-subnet) przed podjęciem próby usunięcia tabeli tras. 

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)