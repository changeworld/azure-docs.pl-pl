---
title: Samouczek — Konfigurowanie sieci korzystającą wtyczki kubenet w usłudze Azure Kubernetes Service (AKS) za pomocą rozwiązania ansible
description: Informacje dotyczące konfigurowania sieci korzystającą wtyczki kubenet w klastrze usługi Azure Kubernetes Service (AKS) za pomocą rozwiązania ansible
keywords: rozwiązania ansible, Azure, DevOps, bash, cloudshell, element PlayBook, AKS, kontener, AKS, Kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 949a55fd8c004bc656d02816231c4ebb6dd8f92b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242169"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Samouczek: Konfigurowanie sieci korzystającą wtyczki kubenet w usłudze Azure Kubernetes Service (AKS) za pomocą rozwiązania ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Za pomocą AKS można wdrożyć klaster przy użyciu następujących modeli sieci:

- [Korzystającą wtyczki kubenet Networking](/azure/aks/configure-kubenet) — zasoby sieciowe są zwykle tworzone i konfigurowane jako wdrożony klaster AKS.
- [Sieć Azure Container Network Interface (CNI) Networking](/azure/aks/configure-azure-cni) — klaster AKS jest połączony z istniejącymi zasobami i konfiguracjami sieci wirtualnej.

Aby uzyskać więcej informacji na temat sieci do aplikacji w AKS, zobacz [pojęcia dotyczące sieci dla aplikacji w AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS
> * Konfigurowanie sieci korzystającą wtyczki kubenet Azure

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Kod element PlayBook w tej sekcji tworzy następujące zasoby platformy Azure:

- Sieć wirtualna
- Podsieć w sieci wirtualnej

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

Kod element PlayBook w tej sekcji tworzy klaster AKS w sieci wirtualnej. 

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

Poniżej przedstawiono niektóre kluczowe uwagi, które należy wziąć pod uwagę podczas pracy z przykładową element PlayBook:

- Użyj modułu `azure_rm_aks_version`, aby znaleźć obsługiwaną wersję.
- @No__t-0 to podsieć utworzona w poprzedniej sekcji.
- @No__t-0 definiuje właściwości wtyczki sieciowej korzystającą wtyczki kubenet.
- @No__t-0 służy do przypisywania wewnętrznych usług w klastrze AKS na adres IP. Ten zakres adresów IP powinien być przestrzenią adresową, która nie jest używana w innym miejscu w sieci. 
- Adres `dns_service_ip` powinien być adresem ". 10" zakresu adresów IP usługi.
- @No__t-0 powinna być dużą przestrzenią adresową, która nie jest używana w innym miejscu w środowisku sieciowym. Zakres adresów musi być wystarczająco duży, aby pomieścić liczbę węzłów, do których można skalować w górę. Nie można zmienić tego zakresu adresów po wdrożeniu klastra.
- Zakres adresów IP pod jest używany do przypisywania przestrzeni adresowej/24 do każdego węzła w klastrze. W poniższym przykładzie `pod_cidr` z 192.168.0.0/16 przypisuje pierwszy węzeł 192.168.0.0/24, drugi węzeł 192.168.1.0/24 i trzeci węzeł 192.168.2.0/24.
- W miarę skalowania lub uaktualniania klastra platforma Azure nadal przypisuje zakres adresów IP pod każdym nowym węzłem.
- Element PlayBook ładuje `ssh_key` z `~/.ssh/id_rsa.pub`. Jeśli zmodyfikujesz go, użyj formatu jednowierszowego — rozpoczynając od "SSH-RSA" (bez cudzysłowów).
- Wartości `client_id` i `client_secret` są ładowane z `~/.azure/credentials`, czyli domyślnego pliku poświadczeń. Można ustawić te wartości jako nazwę główną usługi lub załadować te wartości ze zmiennych środowiskowych:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Kojarzenie zasobów sieciowych

Podczas tworzenia klastra AKS są tworzone sieciowe grupy zabezpieczeń i trasy. Te zasoby są zarządzane przez usługę AKS i aktualizowane podczas tworzenia i uwidaczniania usług. Skojarz sieciową grupę zabezpieczeń i tabelę tras z podsiecią sieci wirtualnej w następujący sposób. 

Zapisz następujący element PlayBook jako `associate.yml`.

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

Poniżej przedstawiono niektóre kluczowe uwagi, które należy wziąć pod uwagę podczas pracy z przykładową element PlayBook:

- @No__t-0 to nazwa grupy zasobów, w której są tworzone węzły AKS.
- @No__t-0 to podsieć utworzona w poprzedniej sekcji.


## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowej element PlayBook

W tej sekcji znajduje się kompletna Przykładowa element PlayBook, która wywołuje zadania utworzone w tym artykule. 

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

W sekcji `vars` wprowadź następujące zmiany:

- Dla klucza `resource_group` Zmień wartość `aksansibletest` na nazwę grupy zasobów.
- Dla klucza `name` Zmień wartość `aksansibletest` na nazwę AKS.
- Dla klucza `Location` Zmień wartość `eastus` na lokalizację grupy zasobów.

Uruchom kompletną element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook aks-kubenet.yml
```

Uruchomienie element PlayBook pokazuje wyniki podobne do następujących:

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

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod jako `cleanup.yml`:

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

W sekcji `vars` Zastąp symbol zastępczy `{{ resource_group_name }}` nazwą grupy zasobów.

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek — Konfigurowanie sieci Azure Container Networking Interface (CNI) w AKS przy użyciu rozwiązania ansible](./ansible-aks-configure-cni-networking.md)