---
title: Tworzenie zestawów skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible
description: Dowiedz się, jak utworzyć i skonfigurować maszyny wirtualnej zestawu skalowania w usłudze Azure za pomocą rozwiązania Ansible
ms.service: ansible
keywords: rozwiązanie ansible, azure, devops, bash, elementu playbook, maszyny wirtualnej, zestawu skalowania maszyn wirtualnych, zestawu skalowania maszyn wirtualnych
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 5f915f7b1b425a3bd6e5d62eb70bb3f633b7eda8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009007"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tworzenie zestawów skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible
Rozwiązanie Ansible pozwala zautomatyzować procesy wdrażania i konfiguracji zasobów w danym środowisku. Rozwiązanie Ansible służy do zarządzania zestaw skalowania maszyn wirtualnych (zestawu skalowania maszyn wirtualnych) na platformie Azure, takie same, jak będzie zarządzać innych zasobów platformy Azure. W tym artykule przedstawiono sposób tworzenia i skalowania w poziomie zestawu skalowania maszyn wirtualnych za pomocą rozwiązania Ansible. 

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed przystąpieniem do wykonywania.
- **Konfigurowanie rozwiązania Ansible** - [Azure Utwórz poświadczenia i konfigurowanie rozwiązania Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Rozwiązanie Ansible i moduły zestawu Azure Python SDK** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 Z DODATKIEM SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)

> [!Note]
> Ansible 2.6 jest wymagana do uruchamiania następujących przykładowych elementów playbook w ramach tego samouczka. 

## <a name="create-a-vmss"></a>Tworzenie zestawu skalowania maszyn wirtualnych
W tej sekcji przedstawiono przykładowy playbook rozwiązania Ansible, który definiuje następujące zasoby:
- **Grupa zasobów** do którego zostaną wszystkie zasoby wdrożone
- **Sieć wirtualna** przestrzeni adresów 10.0.0.0/16
- **Podsieci** w ramach sieci wirtualnej
- **Publiczny adres IP** tego wllows możesz uzyskać dostęp do zasobów w Internecie
- **Sieciowa grupa zabezpieczeń** sterującą przepływem ruchu sieciowego i z zestawu skalowania maszyn wirtualnych
- **Moduł równoważenia obciążenia** , rozkłada ruch na zestawu zdefiniowanych maszyn wirtualnych przy użyciu reguł usługi load balancer
- **Zestaw skalowania maszyn wirtualnych** używającej utworzonych zasobów

Wprowadź hasło dla *admin_password* wartości.

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

Aby utworzyć środowiska zestaw skali maszyny wirtualnej za pomocą rozwiązania Ansible, zapisać poprzedni element playbook jako `vmss-create.yml`, lub [Pobierz przykładowe playbook rozwiązania Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Aby uruchomić element playbook rozwiązania Ansible, użyj **playbook rozwiązania ansible** polecenia w następujący sposób:

  ```bash
  ansible-playbook vmss-create.yml
  ```

Po uruchomieniu elementu playbook, dane wyjściowe podobne do poniższego przykładu pokazuje, że skalowania maszyn wirtualnych została pomyślnie utworzona zestawu:

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

## <a name="scale-out-a-vmss"></a>Skalowanie zestawu skalowania maszyn wirtualnych
Zestaw skalowania utworzono maszyn wirtualnych ma dwa wystąpienia. Jeśli przejdziesz do maszyny wirtualnej zestawu skalowania w witrynie Azure portal, zobacz **standardowa_ds1_v2 (2 wystąpienia)**. Można również użyć [usługi Azure Cloud Shell](https://shell.azure.com/) , uruchamiając następujące polecenie w usłudze Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Dane wyjściowe będą podobne do następujących:

  ```bash
  {
    "capacity": 2,
  }
  ```

Teraz możemy skalować z dwóch wystąpień do trzech wystąpień. Poniższy kod elementu playbook rozwiązania Ansible pobiera informacji na temat skalowania maszyn wirtualnych i zmienia jego pojemność od dwóch do trzech. 

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

Aby skalować zestaw skalowania maszyn wirtualnych, został utworzony, Zapisz poprzedni element playbook jako `vmss-scale-out.yml` lub [Pobierz przykładowe playbook rozwiązania Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)). 

Następujące polecenie spowoduje uruchomienie elementu playbook:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Dane wyjściowe z uruchomieniu elementu playbook rozwiązania Ansible pokazuje, że zestaw skalowania maszyn wirtualnych została pomyślnie skalowanie:

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

Jeśli przejdź do zestawu skalowania maszyn wirtualnych, które są skonfigurowane w witrynie Azure portal, zostanie wyświetlony **standardowa_ds1_v2 (wystąpienia 3)**. Można również sprawdzić zmiany z [usługi Azure Cloud Shell](https://shell.azure.com/) , uruchamiając następujące polecenie:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Wyniki działania polecenia w usłudze Cloud Shell pokazuje, że trzy wystąpienia istnieją teraz. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible przykładowe Podręcznik dla zestawu skalowania maszyn wirtualnych](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)