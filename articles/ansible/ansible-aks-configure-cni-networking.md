---
title: Samouczek — konfigurowanie sieci CNI platformy Azure w usłudze Azure Kubernetes (AKS) przy użyciu usługi Ansible
description: Dowiedz się, jak skonfigurować sieć kubenet w klastrze usługi Azure Kubernetes Service (AKS) za pomocą ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, kontener, aks, kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156899"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Samouczek: Konfigurowanie sieci CNI platformy Azure w usłudze Azure Kubernetes (AKS) przy użyciu usługi Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Za pomocą usługi AKS można wdrożyć klaster przy użyciu następujących modeli sieciowych:

- [Sieć Kubenet](/azure/aks/configure-kubenet) — zasoby sieciowe są zazwyczaj tworzone i konfigurowane podczas wdrażania klastra AKS.
- [Sieci CNI platformy Azure](/azure/aks/configure-azure-cni) — klaster AKS jest połączony z istniejącymi zasobami i konfiguracjami sieci wirtualnej(VNET).

Aby uzyskać więcej informacji na temat sieci do aplikacji w aks, zobacz [Pojęcia sieci dla aplikacji w AKS](/azure/aks/concepts-network).

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

Przykładowy kod podręcznika w tej sekcji jest używany do:

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

Przykładowy kod podręcznika w tej sekcji jest używany do:

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

Oto kilka kluczowych uwag, które należy wziąć pod uwagę podczas pracy z przykładowym podręcznikiem:

- Użyj `azure_rm_aks_version` modułu, aby znaleźć obsługiwana wersja.
- Jest `vnet_subnet_id` to podsieć utworzona w poprzedniej sekcji.
- Podręcznik ładuje `ssh_key` się `~/.ssh/id_rsa.pub`z . Jeśli go zmodyfikujesz, użyj formatu jednowierszowego - zaczynając od "ssh-rsa" (bez cudzysłowów).
- I `client_id` `client_secret` wartości są `~/.azure/credentials`ładowane z , który jest domyślnym plikiem poświadczeń. Można ustawić te wartości na jednostkę usługi lub załadować te wartości ze zmiennych środowiskowych:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowego podręcznika

Przykładowy kod podręcznika w tej sekcji służy do testowania różnych funkcji pokazanych w tym samouczku.

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

Oto kilka kluczowych uwag, które należy wziąć pod uwagę podczas pracy z przykładowym podręcznikiem:

- Zmień `aksansibletest` wartość na nazwę grupy zasobów.
- Zmień `aksansibletest` wartość na nazwę usługi AKS.
- Zmień `eastus` wartość na lokalizację grupy zasobów.

Uruchom podręcznik za pomocą polecenia ansible-playbook:

```bash
ansible-playbook aks-azure-cni.yml
```

Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

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

Gdy nie są już potrzebne, usuń zasoby utworzone w tym artykule. 

Przykładowy kod podręcznika w tej sekcji jest używany do:

- Usuwanie grupy zasobów, o `vars` której mowa w sekcji.

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

Oto kilka kluczowych uwag, które należy wziąć pod uwagę podczas pracy z przykładowym podręcznikiem:

- Zastąp `{{ resource_group_name }}` symbol zastępczy nazwą grupy zasobów.
- Wszystkie zasoby w określonej grupie zasobów zostaną usunięte.

Uruchom podręcznik za pomocą polecenia ansible-playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Konfigurowanie usługi Azure Active Directory w usłudze AKS przy użyciu usługi Ansible](./ansible-aks-configure-rbac.md)