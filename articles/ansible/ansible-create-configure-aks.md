---
title: Tworzenie i konfigurowanie klastrów w usłudze Azure Kubernetes Service na platformie Azure za pomocą rozwiązania Ansible
description: Dowiedz się, jak tworzyć i zarządzać nimi w klastrze usługi Azure Kubernetes Service na platformie Azure za pomocą rozwiązania Ansible
ms.service: ansible
keywords: rozwiązanie ansible, azure, metodyki devops, bash, cloudshell, element playbook, aks, kontenerów, Kubernetes
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 6d7c5f961256e0ae1831bd76353cadd761f4b8ac
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009203"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Tworzenie i konfigurowanie klastrów w usłudze Azure Kubernetes Service na platformie Azure za pomocą rozwiązania Ansible
Rozwiązanie Ansible pozwala zautomatyzować procesy wdrażania i konfiguracji zasobów w danym środowisku. Rozwiązanie Ansible służy do zarządzania usługi Azure Kubernetes Service (AKS). W tym artykule pokazano, jak za pomocą rozwiązania Ansible utworzyć i skonfigurować klaster Azure Kubernetes Service.

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed przystąpieniem do wykonywania.
- **Konfigurowanie rozwiązania Ansible** - [Azure Utwórz poświadczenia i konfigurowanie rozwiązania Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Rozwiązanie Ansible i moduły zestawu Azure Python SDK** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 Z DODATKIEM SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Jednostka usługi platformy Azure** — po [tworzenia jednostki usługi](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal), zwróć uwagę na następujące wartości: **appId**, **displayName**, **hasła** , i **dzierżawy**.

> [!Note]
> Ansible 2.6 jest wymagana do uruchamiania następujących przykładowych elementów playbook w ramach tego samouczka. 

## <a name="create-a-managed-aks-cluster"></a>Tworzenie zarządzanego klastra AKS
Następujący element playbook rozwiązania Ansible przykładowy tworzy grupę zasobów i klaster AKS, która znajduje się w grupie zasobów:

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

Następujące punktory pomóc wyjaśnić w poprzednim kodzie element playbook rozwiązania Ansible:
- Pierwsza sekcja w ramach **zadania** definiuje grupę zasobów o nazwie **myResourceGroup** w ramach **eastus** lokalizacji. 
- Druga sekcja w ramach **zadania** definiuje klaster AKS, o nazwie **myAKSCluster** w ramach **myResourceGroup** grupy zasobów. 

Aby utworzyć klaster AKS za pomocą rozwiązania Ansible, Zapisz poprzedni element playbook przykładowe jako `azure_create_aks.yml`, i uruchom element playbook przy użyciu następującego polecenia:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

Dane wyjściowe z **playbook rozwiązania ansible* polecenie wygląda podobnie do poniższej przedstawiający pomyślnie utworzono klaster AKS:

  ```bash
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>Skalowanie węzłów usługi AKS

Przykładowy element playbook w poprzedniej sekcji definiuje dwa węzły. Jeśli potrzebujesz co najmniej mniejszej liczby obciążeń kontenerów w klastrze, można łatwo dostosować liczbę węzłów. Przykładowy element playbook w tej sekcji zwiększa liczbę węzłów z uwzględnieniem dwóch węzłów do trzech. Modyfikowanie liczby węzłów odbywa się przez zmianę **liczba** wartość w **agent_pool_profiles** bloku. 

Wprowadź własne `ssh_key`, `client_id`, i `client_secret` w **service_principal** bloku:

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"    
        location: "{{ location }}"
        resource_group: "{{ resource_group }}" 
        dns_prefix: "{{ aks_name }}" 
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Skalowanie klastra usługi Azure Kubernetes Service za pomocą rozwiązania Ansible, Zapisz poprzedni element playbook jako *azure_configure_aks.yml*, i uruchom element playbook w następujący sposób:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

Następujące dane wyjściowe pokazuje, czy klaster AKS został utworzony pomyślnie:

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Samouczek: Skalowanie aplikacji w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-scale)