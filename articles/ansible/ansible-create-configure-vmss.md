---
title: Samouczek — konfigurowanie zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu ansible
description: Dowiedz się, jak używać ansible do tworzenia i konfigurowania zestawów skalowania maszyn wirtualnych na platformie Azure
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e1cc40459988fb9bc38e3dbbcde563cebb531e3d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156553"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Samouczek: Konfigurowanie zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurowanie zasobów dla maszyny Wirtualnej
> * Konfigurowanie zestawu skalowania
> * Skaluj zestaw skalowania, zwiększając jego wystąpienia maszyn wirtualnych 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Konfigurowanie zestawu skalowania

Kod podręcznika w tej sekcji definiuje następujące zasoby:

* **Grupa zasobów,** w której zostaną wdrożone wszystkie zasoby.
* **Sieć wirtualną** w przestrzeni adresowej 10.0.0.0/16
* **Podsieć** w ramach sieci wirtualnej
* **Publiczny adres IP**, który pozwala uzyskać dostęp do zasobów w Internecie
* **Sieciowa grupa zabezpieczeń,** która steruje przepływem ruchu sieciowego do i z zestawu skalowania
* **Moduł równoważenia obciążenia**, który dystrybuuje ruch w ramach zestawu zdefiniowanych maszyn wirtualnych przy użyciu reguł modułu równoważenia obciążenia
* **Zestaw skalowania maszyn wirtualnych**, który używa wszystkich utworzonych zasobów

Przykładowy podręcznik można uzyskać na dwa sposoby:

* [Pobierz podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) i zapisz `vmss-create.yml`go w pliku .
* Utwórz nowy `vmss-create.yml` plik o nazwie i skopiuj do niego następującą zawartość:

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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:

* W `vars` sekcji zastąp symbol zastępczy `{{ admin_password }}` własnym hasłem.

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook vmss-create.yml
```

Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

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

[Skonfigurowany zestaw skalowania](#configure-a-scale-set) ma obecnie dwa wystąpienia. Następujące kroki są używane do potwierdzenia tej wartości:

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Przejdź do skonfigurowanych zestawów skalowania.

1. Zobaczysz nazwę zestawu skalowania z liczbą wystąpień w nawiasie:`Standard_DS1_v2 (2 instances)`

1. Można również sprawdzić liczbę wystąpień za pomocą [usługi Azure Cloud Shell,](https://shell.azure.com/) uruchamiając następujące polecenie:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Wyniki uruchamiania polecenia interfejsu wiersza polecenia platformy Azure w usłudze Cloud Shell pokazują, że obecnie istnieją trzy wystąpienia: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Skalowanie w poziomie zestawu skalowania

Kod podręcznika w tej sekcji pobiera informacje o zestawie skalowania i zmienia jego pojemność z dwóch do trzech.

Przykładowy podręcznik można uzyskać na dwa sposoby:

* [Pobierz podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) i zapisz `vmss-scale-out.yml`go w pliku .
* Utwórz nowy `vmss-scale-out.yml` plik o nazwie i skopiuj do niego następującą zawartość:

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook vmss-scale-out.yml
```

Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

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

## <a name="verify-the-results"></a>Weryfikowanie wyników

Zweryfikuj wyniki swojej pracy za pośrednictwem portalu Azure:

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Przejdź do skonfigurowanych zestawów skalowania.

1. Zobaczysz nazwę zestawu skalowania z liczbą wystąpień w nawiasie:`Standard_DS1_v2 (3 instances)` 

1. Możesz również zweryfikować zmianę za pomocą [usługi Azure Cloud Shell](https://shell.azure.com/), uruchamiając następujące polecenie:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Wyniki uruchamiania polecenia interfejsu wiersza polecenia platformy Azure w usłudze Cloud Shell pokazują, że obecnie istnieją trzy wystąpienia: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Samouczek: Wdrażanie aplikacji do zestawów skalowania maszyny wirtualnej na platformie Azure przy użyciu ansible](./ansible-deploy-app-vmss.md)