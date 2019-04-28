---
title: Samouczek — Konfigurowanie zestawów skalowania maszyn wirtualnych na platformie Azure, za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować zestawy skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 13829d114f38f9a4cc490f46bb8c4b560b58e774
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764409"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Samouczek: Konfigurowanie zestawów skalowania maszyn wirtualnych na platformie Azure, za pomocą rozwiązania Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurowanie zasobów dla maszyny Wirtualnej
> * Konfigurowanie zestawu skalowania
> * Skalowanie zestawu, zwiększając jego wystąpień maszyn wirtualnych skalowania 

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Konfigurowanie zestawu skalowania

Kod elementu playbook w tej sekcji definiuje następujące zasoby:

* **Grupa zasobów** do którego zostaną wszystkie zasoby wdrożone.
* **Sieć wirtualną** w przestrzeni adresowej 10.0.0.0/16
* **Podsieć** w ramach sieci wirtualnej
* **Publiczny adres IP**, który pozwala uzyskać dostęp do zasobów w Internecie
* **Sieciowa grupa zabezpieczeń** sterującą przepływem ruchu sieciowego i z zestawu skalowania
* **Moduł równoważenia obciążenia**, który dystrybuuje ruch w ramach zestawu zdefiniowanych maszyn wirtualnych przy użyciu reguł modułu równoważenia obciążenia
* **Zestaw skalowania maszyn wirtualnych**, który używa wszystkich utworzonych zasobów

Istnieją dwa sposoby pobrania Podręcznik próbki:

* [Pobierz Podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) i zapisać go w celu `vmss-create.yml`.
* Utwórz nowy plik o nazwie `vmss-create.yml` i skopiuj do niego następującą zawartość:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:

* W `vars` sekcji i Zastąp `{{ admin_password }}` symbol zastępczy przy użyciu własnego hasła.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook vmss-create.yml
```

Po uruchomieniu elementu playbook, zobaczysz dane wyjściowe podobne do następujących wyników:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Wyświetlanie liczby wystąpień maszyn wirtualnych

[Skonfigurowany zestaw skalowania](#configure-a-scale-set) obecnie ma dwa wystąpienia. Aby upewnić się, że wartość używane są następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Przejdź do zestawu skalowania, które zostały skonfigurowane.

1. Zostanie wyświetlony zestaw skalowania nazwę z liczbą wystąpień w nawiasach: `Standard_DS1_v2 (2 instances)`

1. Można też sprawdzić liczbę wystąpień z [usługi Azure Cloud Shell](https://shell.azure.com/) , uruchamiając następujące polecenie:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Wyniki działania polecenia wiersza polecenia platformy Azure w usłudze Cloud Shell pokazują, że trzy wystąpienia istnieją teraz: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Skalowanie zestawu skalowania

Kod elementu playbook w tej sekcji pobiera informacje o zestawie skalowania i zmienia jego pojemność od dwóch do trzech.

Istnieją dwa sposoby pobrania Podręcznik próbki:

* [Pobierz Podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) i zapisać go w celu `vmss-scale-out.yml`.
* Utwórz nowy plik o nazwie `vmss-scale-out.yml` i skopiuj do niego następującą zawartość:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook vmss-scale-out.yml
```

Po uruchomieniu elementu playbook, zobaczysz dane wyjściowe podobne do następujących wyników:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>Sprawdź wyniki

Sprawdź wyniki pracy w witrynie Azure portal:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Przejdź do zestawu skalowania, które zostały skonfigurowane.

1. Zostanie wyświetlony zestaw skalowania nazwę z liczbą wystąpień w nawiasach: `Standard_DS1_v2 (3 instances)` 

1. Możesz również zweryfikować zmianę za pomocą [usługi Azure Cloud Shell](https://shell.azure.com/), uruchamiając następujące polecenie:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Wyniki działania polecenia wiersza polecenia platformy Azure w usłudze Cloud Shell pokazują, że trzy wystąpienia istnieją teraz: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Samouczek: Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible](./ansible-deploy-app-vmss.md)