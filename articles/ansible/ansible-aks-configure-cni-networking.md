---
title: Samouczek — Konfigurowanie sieci Azure CNI w usłudze Azure Kubernetes Service (AKS) za pomocą rozwiązania ansible
description: Informacje dotyczące konfigurowania sieci korzystającą wtyczki kubenet w klastrze usługi Azure Kubernetes Service (AKS) za pomocą rozwiązania ansible
keywords: rozwiązania ansible, Azure, DevOps, bash, cloudshell, element PlayBook, AKS, kontener, AKS, Kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156899"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Samouczek: Konfigurowanie sieci Azure CNI w usłudze Azure Kubernetes Service (AKS) za pomocą rozwiązania ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Za pomocą AKS można wdrożyć klaster przy użyciu następujących modeli sieci:

- [Korzystającą wtyczki kubenet Networking](/azure/aks/configure-kubenet) — zasoby sieciowe są zwykle tworzone i konfigurowane jako wdrożony klaster AKS.
- [Azure CNI Networking](/azure/aks/configure-azure-cni) — klaster AKS jest połączony z istniejącymi zasobami sieci wirtualnej (VNET) i konfiguracją.

Aby uzyskać więcej informacji na temat sieci do aplikacji w AKS, zobacz [pojęcia dotyczące sieci dla aplikacji w AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS
> * Konfigurowanie sieci wtyczki Azure CNI

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Przykładowy kod element PlayBook w tej sekcji jest używany do:

- Tworzenie sieci wirtualnej
- Tworzenie podsieci w sieci wirtualnej

Zapisz następujący podręcznik jako `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Tworzenie klastra AKS w sieci wirtualnej

Przykładowy kod element PlayBook w tej sekcji jest używany do:

- Utwórz klaster AKS w sieci wirtualnej.

Zapisz następujący podręcznik jako `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Poniżej przedstawiono niektóre kluczowe uwagi, które należy wziąć pod uwagę podczas pracy z przykładową element PlayBook:

- Użyj modułu `azure_rm_aks_version`, aby znaleźć obsługiwaną wersję.
- `vnet_subnet_id` to podsieć utworzona w poprzedniej sekcji.
- Element PlayBook ładuje `ssh_key` z `~/.ssh/id_rsa.pub`. Jeśli zmodyfikujesz go, użyj formatu jednowierszowego — rozpoczynając od "SSH-RSA" (bez cudzysłowów).
- Wartości `client_id` i `client_secret` są ładowane z `~/.azure/credentials`, czyli domyślnego pliku poświadczeń. Można ustawić te wartości jako nazwę główną usługi lub załadować te wartości ze zmiennych środowiskowych:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowej element PlayBook

Przykładowy kod element PlayBook w tej sekcji jest używany do testowania różnych funkcji przedstawionych w tym samouczku.

Zapisz następujący podręcznik jako `aks-azure-cni.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Poniżej przedstawiono niektóre kluczowe uwagi, które należy wziąć pod uwagę podczas pracy z przykładową element PlayBook:

- Zmień wartość `aksansibletest` na nazwę grupy zasobów.
- Zmień wartość `aksansibletest` na nazwę AKS.
- Zmień wartość `eastus` na lokalizację grupy zasobów.

Uruchom element PlayBook za pomocą polecenia rozwiązania ansible-element PlayBook:

```bash
ansible-playbook aks-azure-cni.yml
```

Po uruchomieniu element PlayBook są wyświetlane dane wyjściowe podobne do następujących:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Przykładowy kod element PlayBook w tej sekcji jest używany do:

- Usuń grupę zasobów, do której odwołuje się sekcja `vars`.

Zapisz następujący podręcznik jako `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Poniżej przedstawiono niektóre kluczowe uwagi, które należy wziąć pod uwagę podczas pracy z przykładową element PlayBook:

- Zastąp symbol zastępczy `{{ resource_group_name }}` nazwą grupy zasobów.
- Wszystkie zasoby w określonej grupie zasobów zostaną usunięte.

Uruchom element PlayBook za pomocą polecenia rozwiązania ansible-element PlayBook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Konfigurowanie Azure Active Directory w AKS za pomocą rozwiązania ansible](./ansible-aks-configure-rbac.md)