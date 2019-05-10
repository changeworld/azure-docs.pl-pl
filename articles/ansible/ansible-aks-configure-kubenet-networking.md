---
title: Samouczek — Konfigurowanie sieci w usłudze Azure Kubernetes Service (AKS) za pomocą rozwiązania Ansible wtyczki kubenet | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą rozwiązania Ansible w celu skonfigurowania wtyczki kubenet sieci w klastrze usługi Azure Kubernetes Service (AKS)
keywords: ansible, azure, metodyki devops, bash, cloudshell, element playbook, aks, kontener, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: cd32347f9de87ea6272be922d0359f1cc7f6f758
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231298"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Samouczek: Konfigurowanie wtyczki kubenet sieci w usłudze Azure Kubernetes Service (AKS) za pomocą rozwiązania Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Za pomocą usługi AKS, można wdrożyć klaster przy użyciu następujących modeli sieci:

- [Sieć z wtyczki Kubenet](/azure/aks/configure-kubenet) -zasobów sieciowych zwykle są tworzone i konfigurowane jako klaster AKS jest wdrażany.
- [Sieć usługi Azure Container Networking interfejsu (CNI)](/azure/aks/configure-azure-cni) — klaster AKS jest podłączony do istniejących zasobów sieci wirtualnej i konfiguracji.

Aby uzyskać więcej informacji na temat sieci do aplikacji w usłudze AKS, zobacz [sieci pojęcia związane z aplikacjami w usłudze AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS
> * Skonfiguruj sieć platformy Azure wtyczki kubenet

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Kod elementu playbook w tej sekcji powoduje utworzenie następujących zasobów platformy Azure:

- Sieć wirtualna
- Podsieci w sieci wirtualnej

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

Kod elementu playbook w tej sekcji tworzy klaster usługi AKS w taki sposób, w ramach sieci wirtualnej. 

Zapisz następujący podręcznik jako `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Poniżej przedstawiono ważne uwagi należy wziąć pod uwagę podczas pracy z elementu playbook próbki:

- Użyj `azure_rm_aks_version` modułu, aby znaleźć obsługiwanej wersji.
- `vnet_subnet_id` Podsieć utworzona w poprzedniej sekcji.
- `network_profile` Definiuje właściwości dla wtyczki kubenet wtyczki sieci.
- `service_cidr` Jest używany do przypisywania wewnętrznych usług w klastrze AKS z adresem IP. Ten zakres adresów IP powinna być przestrzeń adresów, który nie jest używany w innym miejscu w sieci. 
- `dns_service_ip` Adres powinien być ". 10" adres zakres adresów IP usługi.
- `pod_cidr` Powinny być dużych adresowa, która nie jest używana w innym miejscu w danym środowisku sieciowym. Zakres adresów musi być wystarczająco duży, aby uwzględnić liczbę węzłów, które oczekują skalować do. Nie można zmienić tego zakresu adresów, po wdrożeniu klastra.
- Zakres adresów IP zasobnika jest używany do przypisywania prefiksie/24 przestrzeń adresowa do każdego węzła w klastrze. W poniższym przykładzie `pod_cidr` z 192.168.0.0/16 przypisze pierwszy 192.168.0.0/24 węzła, drugi 192.168.1.0/24 węzła i trzeci 192.168.2.0/24 węzła.
- Skaluje klaster lub uaktualnień Azure w dalszym ciągu Przypisz zakres adresów IP zasobnika do każdego nowego węzła.
- Element playbook ładuje `ssh_key` z `~/.ssh/id_rsa.pub`. Jeśli zmodyfikujesz go tak, użyj formacie jednowierszowym — począwszy od "ssh-rsa" (bez cudzysłowu).
- `client_id` i `client_secret` wartości są ładowane z `~/.azure/credentials`, która jest domyślny plik poświadczeń. Możesz ustawić te wartości z usługą główną lub załadować te wartości ze zmiennych środowiskowych:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Skojarz zasobów sieciowych

Podczas tworzenia klastra usługi AKS tabeli sieci zabezpieczeń grupy i wyznaczać trasy są tworzone. Te zasoby są zarządzane przez usługę AKS i aktualizowane, gdy tworzenie i udostępnianie usług. Kojarzenie tabeli grupy i wyznaczać trasy zabezpieczeń sieci z podsieci sieci wirtualnej w następujący sposób. 

Zapisz poniższy element playbook jako `associate.yml`.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Poniżej przedstawiono ważne uwagi należy wziąć pod uwagę podczas pracy z elementu playbook próbki:

- `node_resource_group` To nazwa grupy zasobów, w którym są tworzone węzłów AKS.
- `vnet_subnet_id` Podsieć utworzona w poprzedniej sekcji.


## <a name="run-the-sample-playbook"></a>Uruchamianie elementu playbook próbki

W tej sekcji przedstawiono pełny przykład elementu playbook, który wywołuje zadania tworzenia w tym artykule. 

Zapisz następujący podręcznik jako `aks-kubenet.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

W `vars` sekcji, należy wprowadzić następujące zmiany:

- Aby uzyskać `resource_group` zmian klucza, `aksansibletest` wartość, aby nazwa grupy zasobów.
- Aby uzyskać `name` zmian klucza, `aksansibletest` wartość nazwy usługi AKS.
- Aby uzyskać `Location` zmian klucza, `eastus` wartość Twojej lokalizacji grupy zasobów.

Uruchamianie przy użyciu elementu playbook pełną `ansible-playbook` polecenia:

```bash
ansible-playbook aks-kubenet.yml
```

Uruchamianie elementu playbook zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz poniższy kod jako `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

W `vars` sekcji i Zastąp `{{ resource_group_name }}` nazwą grupy zasobów.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek — Konfigurowanie sieci wtyczki Azure Container Networking interfejsu (CNI) w usłudze AKS za pomocą rozwiązania Ansible](./ansible-aks-configure-cni-networking.md)