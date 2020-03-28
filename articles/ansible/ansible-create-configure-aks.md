---
title: Samouczek — konfigurowanie klastrów usługi Azure Kubernetes (AKS) na platformie Azure przy użyciu usługi Ansible
description: Dowiedz się, jak za pomocą rozwiązania Ansible tworzyć klastry usługi Azure Kubernetes Service i zarządzać nimi na platformie Azure
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, kontener, aks, kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6672c3fac1c5d546a61622e3fd6df6c5397f87a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156681"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Samouczek: Konfigurowanie klastrów usługi Azure Kubernetes (AKS) na platformie Azure przy użyciu usługi Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Usługę AKS można skonfigurować tak, aby [używała usługi Azure Active Directory (AD)](/azure/active-directory/) do uwierzytelniania użytkowników. Po skonfigurowaniu token uwierzytelniania usługi Azure AD służy do logowania się do klastra AKS. RBAC może opierać się na tożsamości użytkownika lub członkostwa w grupie katalogów.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS
> * Konfigurowanie klastra usługi AKS

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Tworzenie zarządzanego klastra usługi AKS

Przykładowy podręcznik tworzy grupę zasobów i klaster AKS w grupie zasobów.

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
    aks_version: aks_version
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
      kubernetes_version: "{{aks_version}}"
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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:

- Pierwsza sekcja `tasks` w obrębie definiuje `myResourceGroup` grupę `eastus` zasobów o nazwie w lokalizacji.
- Druga sekcja `tasks` w obrębie definiuje klaster `myAKSCluster` AKS o nazwie w grupie `myResourceGroup` zasobów.
- Dla symbolu zastępczego `your_ssh_key` wprowadź swój klucz publiczny RSA w formacie jednowierszowym — rozpoczynając od „ssh-rsa” (bez cudzysłowu).
- W `aks_version` przypadku symbolu zastępczego należy użyć polecenia [az aks get-versions.](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions)

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook azure_create_aks.yml
```

Uruchomienie podręcznika pokazuje wyniki podobne do następujących danych wyjściowych:

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

Przykładowy podręcznik w poprzedniej sekcji definiuje dwa węzły. Można dostosować liczbę węzłów, modyfikując `count` `agent_pool_profiles` wartość w bloku.

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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:

- Dla symbolu zastępczego `your_ssh_key` wprowadź swój klucz publiczny RSA w formacie jednowierszowym — rozpoczynając od „ssh-rsa” (bez cudzysłowu).

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook azure_configure_aks.yml
```

Uruchomienie podręcznika pokazuje wyniki podobne do następujących danych wyjściowych:

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

Przykładowy podręcznik usuwa klaster AKS.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook azure_delete_aks.yml
```

Uruchomienie podręcznika pokazuje wyniki podobne do następujących danych wyjściowych:

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