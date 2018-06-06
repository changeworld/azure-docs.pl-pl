---
title: Tworzenie podstawowej maszyny Wirtualnej systemu Linux na platformie Azure za pomocą Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Ansible tworzenie i zarządzanie nimi podstawowej maszyny wirtualnej systemu Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: e36bdbf84b275fb8a6a4e42496b3080bebf1b193
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716639"
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Tworzenie podstawowej maszyny wirtualnej na platformie Azure z Ansible
Ansible umożliwia automatyzację wdrożenia i konfiguracji zasobów w danym środowisku. Ansible służy do zarządzania maszyn wirtualnych (VM) na platformie Azure, takie same jak w przypadku innych zasobów. W tym artykule przedstawiono sposób tworzenia podstawowej maszyny Wirtualnej z Ansible. Możesz też dowiedzieć się, jak [utworzyć pełne środowisko maszyny Wirtualnej z Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>Wymagania wstępne
Do zarządzania zasobami Azure z Ansible, potrzebne są następujące elementy:

- Ansible i moduły Azure Python SDK zainstalowanych w systemie hosta.
    - Zainstaluj na Ansible [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), i [SLES 12 z dodatkiem SP2](ansible-install-configure.md#sles-12-sp2)
- Poświadczenia platformy Azure i Ansible skonfigurowane do korzystania z nich.
    - [Utwórz poświadczenia platformy Azure i skonfigurować Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. 
    - Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). Można również użyć [powłoki chmury Azure](/azure/cloud-shell/quickstart) z przeglądarki sieci web.


## <a name="create-supporting-azure-resources"></a>Tworzenie obsługi zasobów platformy Azure
W tym przykładzie utworzysz element runbook, który wdraża maszynę Wirtualną do istniejącej infrastruktury. Najpierw należy utworzyć grupy zasobów z [Tworzenie grupy az](/cli/azure/group#az-group-create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Tworzenie sieci wirtualnej dla maszyny Wirtualnej z [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az-network-vnet-create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i podsieć o nazwie *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Tworzenie i uruchamianie podręcznikowym Ansible
Utwórz podręcznikowym Ansible o nazwie *azure_create_vm.yml* i Wklej poniższą zawartość. W tym przykładzie tworzy jednej maszyny Wirtualnej i konfiguruje poświadczenia SSH. Wprowadź pełną publicznego klucza danych użytkownika w *key_data* Sparuj w następujący sposób:

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

Aby utworzyć maszynę Wirtualną z Ansible, uruchom podręcznikowym w następujący sposób:

```bash
ansible-playbook azure_create_vm.yml
```

Dane wyjściowe wygląda podobnie do poniższego przykładu, pokazujący, że pomyślnie utworzono maszynę Wirtualną:

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
To przykładowe polecenie tworzy Maszynę wirtualną w istniejącej grupie zasobów i z siecią wirtualną już wdrożone. Aby bardziej szczegółowy przykład dotyczące używania Ansible utworzyć obsługi zasobów, takich jak sieć wirtualną i reguł sieciowej grupy zabezpieczeń, zobacz [utworzyć pełne środowisko maszyny Wirtualnej z Ansible](ansible-create-complete-vm.md).
