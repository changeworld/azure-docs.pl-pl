---
title: Samouczek — konfigurowanie sieci kubenet w usłudze Azure Kubernetes (AKS) przy użyciu usługi Ansible
description: Dowiedz się, jak skonfigurować sieć kubenet w klastrze usługi Azure Kubernetes Service (AKS) za pomocą ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, kontener, aks, kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bfb19371ad651439c087cebd03023d48852ee2df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156878"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Samouczek: Konfigurowanie sieci kubenet w usłudze Azure Kubernetes (AKS) przy użyciu usługi Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Za pomocą usługi AKS można wdrożyć klaster przy użyciu następujących modeli sieciowych:

- [Sieć Kubenet](/azure/aks/configure-kubenet) — zasoby sieciowe są zazwyczaj tworzone i konfigurowane podczas wdrażania klastra AKS.
- [Sieci interfejsu CNI (Azure Container Networking Interface)](/azure/aks/configure-azure-cni) — klaster AKS jest połączony z istniejącymi zasobami i konfiguracjami sieci wirtualnej.

Aby uzyskać więcej informacji na temat sieci do aplikacji w aks, zobacz [Pojęcia sieci dla aplikacji w AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS
> * Konfigurowanie sieci kubenet platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Kod podręcznika w tej sekcji tworzy następujące zasoby platformy Azure:

- Sieć wirtualna
- Podsieć w ramach sieci wirtualnej

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

Kod podręcznika w tej sekcji tworzy klaster AKS w sieci wirtualnej. 

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

Oto kilka kluczowych uwag, które należy wziąć pod uwagę podczas pracy z przykładowym podręcznikiem:

- Użyj `azure_rm_aks_version` modułu, aby znaleźć obsługiwana wersja.
- Jest `vnet_subnet_id` to podsieć utworzona w poprzedniej sekcji.
- Definiuje `network_profile` właściwości wtyczki sieci kubenet.
- Służy `service_cidr` do przypisywania usług wewnętrznych w klastrze AKS do adresu IP. Ten zakres adresów IP powinien być przestrzenią adresową, która nie jest używana w innym miejscu w sieci. 
- Adres `dns_service_ip` powinien być adresem ".10" zakresu adresów IP usługi.
- Powinna `pod_cidr` to być duża przestrzeń adresowa, która nie jest używana w innym miejscu w środowisku sieciowym. Zakres adresów musi być wystarczająco duży, aby pomieścić liczbę węzłów, które mają być skalowane do. Nie można zmienić tego zakresu adresów po wdrożeniu klastra.
- Zakres adresów IP zasobnika służy do przypisywania /24 przestrzeni adresowej do każdego węzła w klastrze. W `pod_cidr` poniższym przykładzie 192.168.0.0/16 przypisuje pierwszy węzeł 192.168.0.0/24, drugi węzeł 192.168.1.0/24 i trzeci węzeł 192.168.2.0/24.
- W miarę skalowania lub uaktualniania klastra platforma Azure nadal przypisuje zakres adresów IP zasobnika do każdego nowego węzła.
- Podręcznik ładuje `ssh_key` się `~/.ssh/id_rsa.pub`z . Jeśli go zmodyfikujesz, użyj formatu jednowierszowego - zaczynając od "ssh-rsa" (bez cudzysłowów).
- I `client_id` `client_secret` wartości są `~/.azure/credentials`ładowane z , który jest domyślnym plikiem poświadczeń. Można ustawić te wartości na jednostkę usługi lub załadować te wartości ze zmiennych środowiskowych:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Kojarzenie zasobów sieciowych

Podczas tworzenia klastra AKS tworzone są sieciowe grupy zabezpieczeń i tabela tras. Te zasoby są zarządzane przez usługi AKS i aktualizowane podczas tworzenia i uwidaczniania usług. Skojarzyć grupę zabezpieczeń sieci i tabelę tras z podsiecią sieci wirtualnej w następujący sposób. 

Zapisz poniższy podręcznik `associate.yml`jako .

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

Oto kilka kluczowych uwag, które należy wziąć pod uwagę podczas pracy z przykładowym podręcznikiem:

- Jest `node_resource_group` to nazwa grupy zasobów, w której tworzone są węzły AKS.
- Jest `vnet_subnet_id` to podsieć utworzona w poprzedniej sekcji.


## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowego podręcznika

W tej sekcji wymieniono pełny przykładowy podręcznik, który wywołuje zadania tworzenia w tym artykule. 

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

W `vars` sekcji wejmij następujące zmiany:

- W `resource_group` przypadku klucza `aksansibletest` zmień wartość na nazwę grupy zasobów.
- W `name` przypadku klucza `aksansibletest` zmień wartość na nazwę usługi AKS.
- W `Location` przypadku klucza `eastus` zmień wartość na lokalizację grupy zasobów.

Uruchom kompletny podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook aks-kubenet.yml
```

Uruchomienie podręcznika pokazuje wyniki podobne do następujących danych wyjściowych:

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

Gdy nie są już potrzebne, usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod `cleanup.yml`jako:

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

W `vars` sekcji zastąp `{{ resource_group_name }}` symbol zastępczy nazwą grupy zasobów.

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek — konfigurowanie sieci CNI (Azure Container Networking Interface) w usłudze AKS przy użyciu usługi Ansible](./ansible-aks-configure-cni-networking.md)