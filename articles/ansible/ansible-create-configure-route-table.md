---
title: Tworzenie, zmienianie lub usuwanie tabeli tras platformy Azure za pomocą rozwiązania Ansible
description: Dowiedz się, jak za pomocą rozwiązania Ansible utworzyć, zmienić lub usunąć tabelę tras
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 025a8182d32a7d0d00a48795c848d356eb1c3d4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60396822"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Tworzenie, zmienianie lub usuwanie tabeli tras platformy Azure za pomocą rozwiązania Ansible
Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz dokonać zmian w domyślnym routingu na platformie Azure, możesz to zrobić, tworząc [tabelę tras](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. W tym artykule pokazano, jak utworzyć, zmodyfikować lub usunąć tabelę tras platformy Azure, a także jak dołączyć tabelę tras do podsieci. 

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Rozwiązanie Ansible 2.7 jest wymagane do uruchamiania następujących przykładowych podręczników w ramach tego samouczka.

## <a name="create-a-route-table"></a>Tworzenie tabeli tras
W tej sekcji przedstawiono przykładowy podręcznik rozwiązania Ansible, który tworzy tabelę tras. Istnieje limit liczby tabel tras, które można utworzyć dla lokalizacji i subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

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

Zapisz ten podręcznik jako plik `route_table_create.yml`. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią
Podsieć może mieć skojarzoną jedną tabelę tras lub żadną. Tabela tras może być skojarzona z wieloma podsieciami lub żadną. Ponieważ tabele tras nie są skojarzone z sieciami wirtualnymi, musisz skojarzyć tabelę tras z każdą podsiecią, która ma mieć skojarzoną tabelę tras. Cały ruch opuszczający podsieć jest kierowany w oparciu o trasy utworzone w tabelach tras, [trasy domyślne](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default) i trasy propagowane z sieci lokalnej, jeśli sieć wirtualna jest połączona z bramą sieci wirtualnej platformy Azure (usługa ExpressRoute albo sieć VPN, jeśli używany jest protokół BGP z bramą sieci VPN). Tabelę tras można skojarzyć tylko z podsieciami w sieciach wirtualnych, które istnieją w tej samej lokalizacji i subskrypcji platformy Azure co tabela tras.

W tej sekcji przedstawiono przykładowy podręcznik rozwiązania Ansible, który tworzy sieć wirtualną i podsieć, a następnie kojarzy tabelę tras z podsiecią.

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

Zapisz ten podręcznik jako plik `route_table_associate.yml`. Aby uruchomić podręcznik rozwiązania Ansible, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuwanie skojarzenia tabeli tras z podsiecią
Usuwając skojarzenie tabeli tras z podsiecią, wystarczy ustawić właściwość `route_table` podsieci na wartość `None`. Poniżej znajduje się przykładowy podręcznik rozwiązania Ansible. 

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

Zapisz ten podręcznik jako plik `route_table_dissociate.yml`. Aby uruchomić podręcznik rozwiązania Ansible, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Tworzenie trasy
W tej sekcji przedstawiono przykładowy podręcznik rozwiązania Ansible, który tworzy trasę w tabeli tras. Definiuje on element `virtual_network_gateway` jako `next_hop_type` i adres `10.1.0.0/16` jako element `address_prefix`. Prefiksu nie można zduplikować w więcej niż jednej trasie w tabeli tras, chociaż sam prefiks może być zawarty w innym prefiksie. Aby dowiedzieć się, jak platforma Azure wybiera trasy, i zapoznać się ze szczegółowym opisem wszystkich typów następnego przeskoku, zobacz [Omówienie routingu](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

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
Zapisz ten podręcznik jako plik `route_create.yml`. Aby uruchomić podręcznik rozwiązania Ansible, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Usuwanie trasy
W tej sekcji przedstawiono przykładowy podręcznik rozwiązania Ansible, który usuwa trasę z tabeli tras.

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

Zapisz ten podręcznik jako plik `route_delete.yml`. Aby uruchomić podręcznik rozwiązania Ansible, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>Uzyskiwanie informacji o tabeli tras
Szczegółowe informacje o tabeli route_table można uzyskać za pomocą modułu rozwiązania Ansible o nazwie `azure_rm_routetable_facts`. Moduł faktów zwróci informacje o tabeli tras wraz z dołączonymi do niej trasami.
Poniżej znajduje się przykładowy podręcznik rozwiązania Ansible. 

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

Zapisz ten podręcznik jako plik `route_table_facts.yml`. Aby uruchomić podręcznik rozwiązania Ansible, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras
Jeśli tabela tras jest skojarzona z podsieciami, nie można jej usunąć. [Usuń skojarzenie](#dissociate-a-route-table-from-a-subnet) tabeli tras z wszystkimi podsieciami, zanim spróbujesz ją usunąć.

Tabelę tras możesz usunąć wraz ze wszystkimi trasami. Poniżej znajduje się przykładowy podręcznik rozwiązania Ansible. 

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

Zapisz ten podręcznik jako plik `route_table_delete.yml`. Aby uruchomić podręcznik rozwiązania Ansible, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](https://docs.microsoft.com/azure/ansible/)
