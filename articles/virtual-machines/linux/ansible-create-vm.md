---
title: Tworzenie podstawowej maszyny Wirtualnej systemu Linux na platformie Azure za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i zarządzać nimi podstawowe maszyny wirtualnej systemu Linux na platformie Azure za pomocą rozwiązania Ansible
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 35dfe8348718e0edf8683f7eeddf286831697d89
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931433"
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Tworzenie podstawowego maszyny wirtualnej na platformie Azure za pomocą rozwiązania Ansible
Rozwiązanie Ansible pozwala zautomatyzować procesy wdrażania i konfiguracji zasobów w danym środowisku. Aby zarządzać maszyn wirtualnych (VM) na platformie Azure, takie same jak w przypadku innych zasobów, można za pomocą rozwiązania Ansible. W tym artykule opisano tworzenie podstawowej maszyny Wirtualnej za pomocą rozwiązania Ansible. Możesz też dowiedzieć się, jak [tworzenie kompletnego środowiska maszyny Wirtualnej za pomocą rozwiązania Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby zarządzać zasobami platformy Azure za pomocą rozwiązania Ansible, potrzebne są następujące elementy:

- Rozwiązanie Ansible i zestawu Azure Python SDK modułów zainstalowanych w systemie hosta.
    - Instalowanie rozwiązania Ansible na [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), i [SLES 12 z dodatkiem SP2](ansible-install-configure.md#sles-12-sp2)
- Poświadczenia platformy Azure i rozwiązania Ansible skonfigurowane do korzystania z nich.
    - [Tworzenie poświadczeń platformy Azure i konfigurowanie rozwiązania Ansible](ansible-install-configure.md#create-azure-credentials)
- Interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. 
    - Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). Można również użyć [usługi Azure Cloud Shell](/azure/cloud-shell/quickstart) z przeglądarki sieci web.


## <a name="create-supporting-azure-resources"></a>Utworzyć pomocnicze zasoby platformy Azure
W tym przykładzie utworzysz element runbook, który wdraża maszynę Wirtualną w istniejącej infrastruktury. Najpierw utwórz grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group#az-group-create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Tworzenie sieci wirtualnej dla maszyny Wirtualnej za pomocą [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az-network-vnet-create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i podsieć o nazwie *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Tworzenie i uruchamianie elementu playbook rozwiązania Ansible
Utwórz element playbook rozwiązania Ansible, o nazwie *azure_create_vm.yml* i Wklej poniższą zawartość. W tym przykładzie tworzy jedną maszynę Wirtualną i konfiguruje poświadczenia protokołu SSH. Wprowadź pełną publicznego klucza danych użytkownika w *key_data* Sparuj w następujący sposób:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

Aby utworzyć maszynę Wirtualną za pomocą rozwiązania Ansible, uruchom element playbook w następujący sposób:

```bash
ansible-playbook azure_create_vm.yml
```

Dane wyjściowe wyglądają podobnie do poniższego przykładu, który pokazuje, że maszyna wirtualna została pomyślnie utworzona:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>Kolejne kroki
Ten przykład tworzy Maszynę wirtualną w istniejącej grupie zasobów i z siecią wirtualną, która jest już wdrożony. Aby uzyskać bardziej szczegółowy przykład o tym, jak za pomocą rozwiązania Ansible utworzyć pomocnicze zasoby, takie jak sieci wirtualnej i reguły sieciowej grupy zabezpieczeń, zobacz [tworzenie kompletnego środowiska maszyny Wirtualnej za pomocą rozwiązania Ansible](ansible-create-complete-vm.md).
