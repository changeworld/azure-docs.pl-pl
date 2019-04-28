---
title: Samouczek — konfigurowanie klastrów usługi Azure Kubernetes Service (AKS) w systemie Azure za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą rozwiązania Ansible tworzyć klastry usługi Azure Kubernetes Service i zarządzać nimi na platformie Azure
keywords: ansible, azure, metodyki devops, bash, cloudshell, element playbook, aks, kontener, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 64b8eb12c755f41cde28067b5c145c4e95066886
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766066"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Samouczek: Konfigurowanie klastrów usługi Azure Kubernetes Service (AKS) w systemie Azure za pomocą rozwiązania Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS, może być skonfigurowana do używania [usługi Azure Active Directory (AD)](/azure/active-directory/) do uwierzytelniania użytkowników. Po skonfigurowaniu używasz token uwierzytelniania usługi Azure AD do logowania do klastra usługi AKS. RBAC mogą być oparte na tożsamości użytkownika lub członkostwa w grupie katalogu.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS
> * Konfigurowanie klastra usługi AKS

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Tworzenie zarządzanego klastra usługi AKS

Element playbook przykładowy tworzy grupę zasobów i klaster usługi AKS w taki sposób, w grupie zasobów.

Zapisz następujący podręcznik jako `azure_create_aks.yml`:

```yml
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

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:

- Pierwsza sekcja w ramach `tasks` definiuje grupę zasobów o nazwie `myResourceGroup` w ramach `eastus` lokalizacji.
- Druga sekcja w ramach `tasks` definiuje klaster AKS, o nazwie `myAKSCluster` w ramach `myResourceGroup` grupy zasobów.
- Dla symbolu zastępczego `your_ssh_key` wprowadź swój klucz publiczny RSA w formacie jednowierszowym — rozpoczynając od „ssh-rsa” (bez cudzysłowu).

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook azure_create_aks.yml
```

Uruchamianie elementu playbook zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>Skalowanie węzłów usługi AKS

Przykładowy podręcznik w poprzedniej sekcji definiuje dwa węzły. Liczba węzłów można dostosować, modyfikując `count` wartość w `agent_pool_profiles` bloku.

Zapisz następujący podręcznik jako `azure_configure_aks.yml`:

```yml
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

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:

- Dla symbolu zastępczego `your_ssh_key` wprowadź swój klucz publiczny RSA w formacie jednowierszowym — rozpoczynając od „ssh-rsa” (bez cudzysłowu).

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook azure_configure_aks.yml
```

Uruchamianie elementu playbook zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Usuwanie zarządzanego klastra usługi AKS

Przykładowy element playbook usuwa klaster usługi AKS.

Zapisz następujący podręcznik jako `azure_delete_aks.yml`:


```yml
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

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook azure_delete_aks.yml
```

Uruchamianie elementu playbook zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Skalowanie aplikacji w usłudze Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)