---
title: Tworzenie pełnej maszyny Wirtualnej systemu Linux na platformie Azure za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i zarządzać nimi kompletnego środowiska maszyny wirtualnej systemu Linux na platformie Azure za pomocą rozwiązania Ansible
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
ms.openlocfilehash: 63228f8bf8729f1bf3796a77516490ae7088d5ed
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930848"
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Tworzenie kompletnego środowiska maszyny wirtualnej systemu Linux na platformie Azure za pomocą rozwiązania Ansible
Rozwiązanie Ansible pozwala zautomatyzować procesy wdrażania i konfiguracji zasobów w danym środowisku. Aby zarządzać maszyn wirtualnych (VM) na platformie Azure, takie same jak w przypadku innych zasobów, można za pomocą rozwiązania Ansible. W tym artykule pokazano, jak tworzenie kompletnego środowiska systemu Linux i zasoby za pomocą rozwiązania Ansible pomocnicze. Możesz też dowiedzieć się, jak [Tworzenie podstawowej maszyny Wirtualnej za pomocą rozwiązania Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby zarządzać zasobami platformy Azure za pomocą rozwiązania Ansible, potrzebne są następujące elementy:

- Rozwiązanie Ansible i zestawu Azure Python SDK modułów zainstalowanych w systemie hosta.
    - Instalowanie rozwiązania Ansible na [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), i [SLES 12 z dodatkiem SP2](ansible-install-configure.md#sles-12-sp2)
- Poświadczenia platformy Azure i rozwiązania Ansible skonfigurowane do korzystania z nich.
    - [Tworzenie poświadczeń platformy Azure i konfigurowanie rozwiązania Ansible](ansible-install-configure.md#create-azure-credentials)
- Interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. 
    - Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). Można również użyć [Cloud Shell](/azure/cloud-shell/quickstart) z poziomu przeglądarki.


## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
Spróbujmy Przyjrzyj się każdej sekcji element playbook rozwiązania Ansible i Utwórz poszczególnych zasobów platformy Azure. Aby uzyskać pełną elementu playbook, zobacz [tej części tego artykułu](#complete-ansible-playbook).

Następujących sekcji w element playbook rozwiązania Ansible tworzy sieć wirtualną o nazwie *myVnet* w *10.0.0.0/16* przestrzeń adresowa:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Aby dodać podsieć, poniższej sekcji tworzy podsieć o nazwie *mySubnet* w *myVnet* sieci wirtualnej:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Tworzenie publicznego adresu IP
Dostęp do zasobów w Internecie, Utwórz i przypisz publiczny adres IP do maszyny Wirtualnej. Następujących sekcji w element playbook rozwiązania Ansible tworzy publiczny adres IP o nazwie *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Utwórz sieciową grupę zabezpieczeń
Sieciowe grupy zabezpieczeń Sterowanie przepływem ruchu sieciowego do i z maszyny Wirtualnej. Następujących sekcji w element playbook rozwiązania Ansible tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup* i definiuje regułę zezwalającą na ruch SSH na porcie TCP 22:

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


## <a name="create-virtual-network-interface-card"></a>Utwórz wirtualną kartę sieciową
Karty interfejsu sieci wirtualnej (NIC) nawiązuje połączenie z maszyną Wirtualną do danej sieci wirtualnej, publiczny adres IP i sieciową grupą zabezpieczeń. Następujących sekcji w element playbook rozwiązania Ansible tworzy wirtualną kartę Sieciową o nazwie *myNIC* połączone zasoby sieci wirtualne zostały utworzone:

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
Ostatnim krokiem jest tworzenie maszyny Wirtualnej i używanie wszystkie zasoby utworzone. Następujących sekcji w element playbook rozwiązania Ansible tworzy maszynę Wirtualną o nazwie *myVM* i dołącza wirtualną kartę Sieciową o nazwie *myNIC*. Wprowadź pełną publicznego klucza danych użytkownika w *key_data* Sparuj w następujący sposób:

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

## <a name="complete-ansible-playbook"></a>Wykonaj playbook rozwiązania Ansible
Aby zebrać wszystkie te sekcje, należy utworzyć element playbook rozwiązania Ansible, o nazwie *azure_create_complete_vm.yml* i Wklej poniższą zawartość. Wprowadź pełną publicznego klucza danych użytkownika w *key_data* pary:

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

Rozwiązanie Ansible musi mieć grupę zasobów, aby wdrożyć swoje zasoby na. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Aby utworzyć kompletne środowisko maszyny Wirtualnej za pomocą rozwiązania Ansible, uruchom element playbook w następujący sposób:

```bash
ansible-playbook azure_create_complete_vm.yml
```

Dane wyjściowe wyglądają podobnie do poniższego przykładu, który pokazuje, że maszyna wirtualna została pomyślnie utworzona:

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
Ten przykład tworzy kompletne środowisko maszyny Wirtualnej, łącznie z wymaganych zasobów sieci wirtualnej. Na przykład bardziej bezpośrednie utworzyć Maszynę wirtualną do istniejących zasobów sieci z użyciem opcji domyślnych, zobacz [Utwórz Maszynę wirtualną](ansible-create-vm.md).
