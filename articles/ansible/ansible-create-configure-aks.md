---
title: Tworzenie i konfigurowanie klastrów usługi Azure Kubernetes Service na platformie Azure za pomocą rozwiązania Ansible
description: Dowiedz się, jak za pomocą rozwiązania Ansible tworzyć klastry usługi Azure Kubernetes Service i zarządzać nimi na platformie Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, Kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/23/2018
ms.openlocfilehash: df1efc1506fbbe51ba5afb03f147c51a57d9bbdb
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727062"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Tworzenie i konfigurowanie klastrów usługi Azure Kubernetes Service na platformie Azure za pomocą rozwiązania Ansible
Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. Rozwiązanie Ansible służy do zarządzania usługą Azure Kubernetes Service (AKS). W tym artykule pokazano, jak za pomocą rozwiązania Ansible tworzyć i konfigurować klastry usługi Azure Kubernetes Service.

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- **Jednostka usługi platformy Azure** — podczas [tworzenia jednostki usługi](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal) zwróć uwagę na następujące wartości: **appId**, **displayName**, **password** i **tenant**.

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Rozwiązanie Ansible 2.6 jest wymagane do uruchamiania następujących przykładowych podręczników w ramach tego samouczka.

## <a name="create-a-managed-aks-cluster"></a>Tworzenie zarządzanego klastra usługi AKS
Kod w tej sekcji przedstawia przykładowy podręcznik rozwiązania Ansible, który tworzy grupę zasobów i znajdujący się w niej klaster usługi AKS.

> [!Tip]
> Dla symbolu zastępczego `your_ssh_key` wprowadź swój klucz publiczny RSA w formacie jednowierszowym — rozpoczynając od „ssh-rsa” (bez cudzysłowu).

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

Następujące punkty wyjaśniają przedstawiony powyżej kod podręcznika rozwiązania Ansible:
- Pierwsza sekcja w obszarze **tasks** definiuje grupę zasobów o nazwie **myResourceGroup**, której lokalizacją jest **eastus**.
- Druga sekcja w obszarze **tasks** definiuje klaster usługi AKS o nazwie **myAKSCluster** w ramach grupy zasobów **myResourceGroup**.

Aby utworzyć klaster usługi AKS za pomocą rozwiązania Ansible, zapisz poprzedni przykładowy podręcznik jako `azure_create_aks.yml` i uruchom go za pomocą następującego polecenia:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

Dane wyjściowe z polecenia **ansible-playbook* przypominają poniższe dane wskazujące na pomyślne utworzenie klastra usługi AKS:

  ```Output
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

Przykładowy podręcznik w poprzedniej sekcji definiuje dwa węzły. Jeśli chcesz zmniejszyć lub zwiększyć liczbę obciążeń kontenerów w klastrze, możesz łatwo dostosować liczbę węzłów. Przykładowy podręcznik w tej sekcji zwiększa liczbę węzłów z dwóch do trzech. Modyfikowanie liczby węzłów odbywa się przez zmianę wartości **count** w bloku **agent_pool_profiles**.

> [!Tip]
> Dla symbolu zastępczego `your_ssh_key` wprowadź swój klucz publiczny RSA w formacie jednowierszowym — rozpoczynając od „ssh-rsa” (bez cudzysłowu).

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

Aby usunąć klaster usługi Azure Kubernetes Service za pomocą rozwiązania Ansible, zapisz poprzedni podręcznik jako *azure_configure_aks.yml* i uruchom go w następujący sposób:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

Następujące dane wyjściowe pokazują, że klaster usługi AKS został pomyślnie utworzony:

  ```Output
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>Usuwanie zarządzanego klastra usługi AKS

W poniższej sekcji przykładowego podręcznika rozwiązania Ansible pokazano, jak usunąć klaster usługi AKS:

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name:
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

Aby usunąć klaster Azure Kubernetes Service za pomocą rozwiązania Ansible, zapisz poprzedni podręcznik jako *azure_delete_aks.yml* i uruchom go w następujący sposób:

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

Następujące dane wyjściowe pokazują, że klaster usługi AKS został pomyślnie usunięty:
  ```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Samouczek: Skalowanie aplikacji w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
