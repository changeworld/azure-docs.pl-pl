---
title: Tworzenie zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania Ansible
description: Dowiedz się, jak utworzyć i skonfigurować zestaw skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: f3b08c41d3bf083c7cca5897cee11a1a4b9c9092
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918579"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tworzenie zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania Ansible
Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. Rozwiązanie Ansible umożliwia zarządzanie zestawem skalowania maszyn wirtualnych na platformie Azure, tak jak ma to miejsce w przypadku każdego innego zasobu platformy Azure. W tym artykule przedstawiono sposób tworzenia i skalowania na zewnątrz zestawu skalowania maszyn wirtualnych za pomocą rozwiązania Ansible. 

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Rozwiązanie Ansible 2.6 jest wymagane do uruchamiania następujących przykładowych podręczników w ramach tego samouczka. 

## <a name="create-a-vmss"></a>Tworzenie zestawu skalowania maszyn wirtualnych
W tej sekcji przedstawiono przykładowy podręcznik rozwiązania Ansible, który definiuje następujące zasoby:
- **Grupę zasobów**, do której zostaną wdrożone wszystkie zasoby
- **Sieć wirtualną** w przestrzeni adresowej 10.0.0.0/16
- **Podsieć** w ramach sieci wirtualnej
- **Publiczny adres IP**, który pozwala uzyskać dostęp do zasobów w Internecie
- **Sieciową grupę zabezpieczeń**, która kontroluje przepływ ruchu do i z zestawu skalowania maszyn wirtualnych
- **Moduł równoważenia obciążenia**, który dystrybuuje ruch w ramach zestawu zdefiniowanych maszyn wirtualnych przy użyciu reguł modułu równoważenia obciążenia
- **Zestaw skalowania maszyn wirtualnych**, który używa wszystkich utworzonych zasobów

Wprowadź własne hasło dla wartości *admin_password*.

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
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

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
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

Aby utworzyć środowisko zestawu skalowania maszyn wirtualnych za pomocą rozwiązania Ansible, zapisz poprzedni podręcznik jako `vmss-create.yml` lub [pobierz przykładowy podręcznik rozwiązania Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Aby uruchomić podręcznik rozwiązania Ansible, użyj polecenia **ansible-playbook** w następujący sposób:

  ```bash
  ansible-playbook vmss-create.yml
  ```

Po uruchomieniu podręcznika dane wyjściowe podobne do poniższego przykładu pokazują, że zestaw skalowania maszyn wirtualnych został pomyślnie utworzony:

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Skalowanie na zewnątrz zestawu skalowania maszyn wirtualnych
Utworzony zestaw skalowania maszyn wirtualnych ma dwa wystąpienia. Jeśli przejdziesz do zestawu skalowania maszyn wirtualnych w witrynie Azure Portal, zobaczysz pozycję **Standardowa_DS1_v2 (2 wystąpienia)**. Można również użyć [usługi Azure Cloud Shell](https://shell.azure.com/), uruchamiając następujące polecenie w usłudze Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

  ```bash
  {
    "capacity": 2,
  }
  ```

Teraz możemy przeprowadzić skalowanie z dwóch do trzech wystąpień. Poniższy kod podręcznika rozwiązania Ansible pobiera informacje na temat skalowania maszyn wirtualnych i zmienia jego pojemność z dwóch do trzech. 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
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

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Aby skalować w poziomie utworzony zestaw skalowania maszyn wirtualnych, zapisz poprzedni podręcznik jako `vmss-scale-out.yml` lub [pobierz przykładowy podręcznik rozwiązania Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml). 

Następujące polecenie uruchomi podręcznik:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Dane wyjściowe z uruchomienia podręcznika rozwiązania Ansible pokazują, że zestaw skalowania maszyn wirtualnych został pomyślnie przeskalowany na zewnątrz:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
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

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Jeśli przejdziesz do zestawu skalowania maszyn wirtualnych skonfigurowanego w witrynie Azure Portal, zobaczysz pozycję **Standardowa_DS1_v2 (3 wystąpienia)**. Możesz również zweryfikować zmianę za pomocą [usługi Azure Cloud Shell](https://shell.azure.com/), uruchamiając następujące polecenie:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Wynik uruchomienia polecenia w usłudze Cloud Shell pokazuje, że istnieją teraz trzy wystąpienia. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Przykładowy podręcznik rozwiązania Ansible dla zestawu skalowania maszyn wirtualnych](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)