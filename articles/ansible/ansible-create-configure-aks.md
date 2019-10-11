---
title: Samouczek — konfigurowanie klastrów usługi Azure Kubernetes Service (AKS) na platformie Azure przy użyciu rozwiązania ansible
description: Dowiedz się, jak za pomocą rozwiązania Ansible tworzyć klastry usługi Azure Kubernetes Service i zarządzać nimi na platformie Azure
keywords: rozwiązania ansible, Azure, DevOps, bash, cloudshell, element PlayBook, AKS, kontener, AKS, Kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 9b70a9c364768322a3eae6ef5b92c87b6839c540
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242085"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Samouczek: Konfigurowanie klastrów usługi Azure Kubernetes Service (AKS) na platformie Azure przy użyciu rozwiązania ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS można skonfigurować do korzystania z [Azure Active Directory (AD)](/azure/active-directory/) do uwierzytelniania użytkowników. Po skonfigurowaniu programu Użyj tokenu uwierzytelniania usługi Azure AD, aby zalogować się do klastra AKS. Kontrola RBAC może być oparta na tożsamości użytkownika lub członkostwie w grupie katalogów.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS
> * Konfigurowanie klastra AKS

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Tworzenie zarządzanego klastra usługi AKS

Przykład element PlayBook tworzy grupę zasobów i klaster AKS w grupie zasobów.

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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

- Pierwsza sekcja w `tasks` definiuje grupę zasobów o nazwie `myResourceGroup` w lokalizacji `eastus`.
- Druga sekcja w `tasks` definiuje klaster AKS o nazwie `myAKSCluster` w grupie zasobów `myResourceGroup`.
- Dla symbolu zastępczego `your_ssh_key` wprowadź swój klucz publiczny RSA w formacie jednowierszowym — rozpoczynając od „ssh-rsa” (bez cudzysłowu).

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook azure_create_aks.yml
```

Uruchomienie element PlayBook pokazuje wyniki podobne do następujących:

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

Przykładowy podręcznik w poprzedniej sekcji definiuje dwa węzły. Można dostosować liczbę węzłów, modyfikując wartość `count` w bloku `agent_pool_profiles`.

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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

- Dla symbolu zastępczego `your_ssh_key` wprowadź swój klucz publiczny RSA w formacie jednowierszowym — rozpoczynając od „ssh-rsa” (bez cudzysłowu).

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook azure_configure_aks.yml
```

Uruchomienie element PlayBook pokazuje wyniki podobne do następujących:

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

Przykład element PlayBook usuwa klaster AKS.

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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook azure_delete_aks.yml
```

Uruchomienie element PlayBook pokazuje wyniki podobne do następujących:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: skalowanie aplikacji w usłudze Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)