---
title: Samouczek — konfigurowanie tabel marszrut platformy Azure przy użyciu ansible
description: Dowiedz się, jak tworzyć tabele tras platformy Azure, zarządzać nimi i usuwać je przy użyciu funkcji Ansible. Dowiedz się również, jak tworzyć i usuwać trasy.
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659801"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Samouczek: Konfigurowanie tabel marszrut platformy Azure przy użyciu ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli potrzebujesz większej kontroli nad routingiem w swoim środowisku, możesz utworzyć [tabelę tras](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Tworzenie tabeli tras Tworzenie sieci wirtualnej i podsieci Kojarzenie tabeli marszruty z podsiecią Usuń skojarzenie tabeli marszruty z podsieci Utwórz i usuń trasy Kwerenda tabela marszruty Usuwanie tabeli marszruty

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Kod podręcznika w tej sekcji tworzy tabelę tras. Aby uzyskać informacje na temat limitów tabeli tras, zobacz [Limity platformy Azure](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Kod podręcznika w tej sekcji:

* Tworzy sieć wirtualną
* Tworzy podsieć w sieci wirtualnej
* Kojarzy tabelę marszruty z podsiecią

Tabele tras nie są skojarzone z sieciami wirtualnymi. Zamiast tego tabele tras są skojarzone z podsiecią sieci wirtualnej.

Tabela sieci wirtualnej i trasy musi współistnieć w tej samej lokalizacji i subskrypcji platformy Azure.

Podsieci i tabele tras mają relację jeden-do-wielu. Podsieci można zdefiniować bez skojarzonej tabeli marszruty lub jednej tabeli marszruty. Tabele tras mogą być skojarzone z żadną, jedną lub wieloma podsieciami. 

Ruch z podsieci jest kierowany na podstawie:

- trasy zdefiniowane w tabelach tras
- [trasy domyślne](/azure/virtual-network/virtual-networks-udr-overview#default)
- trasy propagowane z sieci lokalnej

Sieć wirtualna musi być podłączona do bramy sieci wirtualnej platformy Azure. Bramą może być usługa ExpressRoute lub SIEĆ VPN, jeśli używa się protokołu BGP z bramą sieci VPN.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuwanie skojarzenia tabeli tras z podsiecią

Kod podręcznika w tej sekcji odcina tabelę tras od podsieci.

Podczas odsyłania tabeli marszruty od `route_table` podsieci `None`ustaw podsieć na . 

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Tworzenie trasy

Kod podręcznika w tej sekcji trasę w tabeli tras. 

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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:

* `virtual_network_gateway`jest zdefiniowany jako `next_hop_type`. Aby uzyskać więcej informacji o tym, jak platforma Azure wybiera trasy, zobacz [Omówienie routingu](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix`jest zdefiniowany jako `10.1.0.0/16`. Prefiksu nie można zduplikować w tabeli trasy.

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Usuwanie trasy

Kod podręcznika w tej sekcji usuwa trasę z tabeli tras.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Uzyskaj informacje o tabeli tras

Kod podręcznika w tej sekcji używa modułu `azure_rm_routetable_facts` Ansible do pobierania informacji o tabeli trasy.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras

Kod podręcznika w tej sekcji tabeli trasy.

Po usunięciu tabeli marszruty wszystkie jej trasy są również usuwane.

Tabeli marszruty nie można usunąć, jeśli jest skojarzona z podsiecią. Przed próbą usunięcia [tabeli marszruty należy odłączyć tabelę tras od wszystkich podsieci.](#dissociate-a-route-table-from-a-subnet) 

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)