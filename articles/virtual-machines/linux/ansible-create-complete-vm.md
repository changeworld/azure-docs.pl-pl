---
title: Utwórz pełną maszyny Wirtualnej systemu Linux na platformie Azure za pomocą Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Ansible tworzenie i zarządzanie nimi pełną środowiska maszyny wirtualnej systemu Linux na platformie Azure
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
ms.openlocfilehash: d3514b57b5dc3541dd0a3c0f584fd689749ada7c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716462"
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Utwórz pełną środowiska maszyny wirtualnej systemu Linux na platformie Azure z Ansible
Ansible umożliwia automatyzację wdrożenia i konfiguracji zasobów w danym środowisku. Ansible służy do zarządzania maszyn wirtualnych (VM) na platformie Azure, takie same jak w przypadku innych zasobów. W tym artykule przedstawiono sposób tworzenia kompletne środowisko systemu Linux i pomocnicze zasoby z Ansible. Możesz też dowiedzieć się, jak [Tworzenie podstawowej maszyny Wirtualnej z Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Wymagania wstępne
Do zarządzania zasobami Azure z Ansible, potrzebne są następujące elementy:

- Ansible i moduły Azure Python SDK zainstalowanych w systemie hosta.
    - Zainstaluj na Ansible [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), i [SLES 12 z dodatkiem SP2](ansible-install-configure.md#sles-12-sp2)
- Poświadczenia platformy Azure i Ansible skonfigurowane do korzystania z nich.
    - [Utwórz poświadczenia platformy Azure i skonfigurować Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. 
    - Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). Można również użyć [powłoki chmury](/azure/cloud-shell/quickstart) z przeglądarki.


## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
Załóżmy Spójrz na każdej części podręcznika dotyczącego Ansible i tworzenie pojedynczych zasobów platformy Azure. Dla pełnej podręcznikowym, zobacz [tej sekcji tego artykułu](#complete-ansible-playbook).

Następujące części podręcznika dotyczącego Ansible tworzy sieć wirtualną o nazwie *myVnet* w *10.0.0.0/16* przestrzeni adresów:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Aby dodać podsieci, poniższa sekcja tworzy podsieć o nazwie *mySubnet* w *myVnet* sieci wirtualnej:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Utwórz publiczny adres IP
Aby uzyskać dostęp do zasobów w Internecie, należy utworzyć i przypisać publicznego adresu IP do maszyny Wirtualnej. Następujące części podręcznika dotyczącego Ansible tworzy publiczny adres IP o nazwie *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Utwórz grupę zabezpieczeń sieci
Grupy zabezpieczeń sieci sterowanie przepływem ruchu sieciowego do i z maszyną Wirtualną. Następujące części podręcznika dotyczącego Ansible tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup* i definiuje regułę, aby zezwolić na ruch portu TCP 22 protokołu SSH:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>Tworzenie wirtualnej karty sieciowej
Karty interfejsu sieci wirtualnej (NIC) nawiązuje połączenie z maszyną Wirtualną do danej sieci wirtualnej, publiczny adres IP i grupy zabezpieczeń sieci. Następujące części podręcznika dotyczącego Ansible tworzy wirtualną kartę Sieciową o nazwie *myNIC* podłączone do zasobów sieciowych wirtualnej zostały utworzone:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej
Ostatnim krokiem jest tworzenie maszyny Wirtualnej i korzystać z zasobów utworzone. Następujące części podręcznika dotyczącego Ansible tworzy Maszynę wirtualną o nazwie *myVM* i dołącza wirtualną kartę Sieciową o nazwie *myNIC*. Wprowadź pełną publicznego klucza danych użytkownika w *key_data* Sparuj w następujący sposób:

```yaml
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
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Zakończenie podręcznikowym Ansible
Zebranie tych sekcji, Utwórz podręcznikowym Ansible o nazwie *azure_create_complete_vm.yml* i Wklej poniższą zawartość. Wprowadź pełną publicznego klucza danych użytkownika w *key_data* pary:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
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
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

Ansible musi mieć grupę zasobów do wszystkich zasobów do wdrożenia. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Aby utworzyć pełne środowisko maszyny Wirtualnej z Ansible, uruchom podręcznikowym w następujący sposób:

```bash
ansible-playbook azure_create_complete_vm.yml
```

Dane wyjściowe wygląda podobnie do poniższego przykładu, pokazujący, że pomyślnie utworzono maszynę Wirtualną:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Kolejne kroki
W tym przykładzie tworzy pełną środowiska maszyny Wirtualnej, w tym wymagane zasoby sieci wirtualnej. Na przykład bardziej bezpośrednie utworzyć Maszynę wirtualną do istniejących zasobów sieciowych z domyślnych opcji, zobacz [tworzenie maszyny Wirtualnej](ansible-create-vm.md).
