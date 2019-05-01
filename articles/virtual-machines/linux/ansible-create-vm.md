---
title: Przewodnik Szybki Start — Konfigurowanie maszyn wirtualnych systemu Linux na platformie Azure, za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start Dowiedz się, jak utworzyć maszynę wirtualną systemu Linux na platformie Azure za pomocą rozwiązania Ansible
keywords: ansible, azure, devops, maszyna wirtualna
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 4bf7d43b682a2a42d9909f9cd33aa9542a1a9330
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711627"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Szybki start: Konfigurowanie maszyn wirtualnych systemu Linux na platformie Azure, za pomocą rozwiązania Ansible

Rozwiązanie Ansible umożliwia automatyczne tworzenie, konfigurowanie i wdrażanie zasobów platformy Azure przy użyciu języka deklaratywnego, za pośrednictwem *podręczników* rozwiązania Ansible. W tym artykule przedstawiono przykładowe playbook rozwiązania Ansible, konfigurowania maszyn wirtualnych systemu Linux. Na końcu tego artykułu znajdziesz [kompletny podręcznik rozwiązania Ansible](#complete-sample-ansible-playbook).

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Rozwiązanie Ansible wymaga grupy zasobów, w której wdrażane są Twoje zasoby. W poniższej sekcji przykładowego podręcznika rozwiązania Ansible tworzona jest grupa zasobów o nazwie `myResourceGroup` w lokalizacji `eastus`:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utworzenie maszyny wirtualnej platformy Azure wymaga utworzenia [sieci wirtualnej](/azure/virtual-network/virtual-networks-overview) lub użycia istniejącej sieci wirtualnej. Należy także określić sposób uzyskiwania dostępu do maszyn wirtualnych w sieci wirtualnej. W poniższej sekcji przykładowego podręcznika rozwiązania Ansible tworzona jest sieć wirtualna o nazwie `myVnet` w przestrzeni adresowej `10.0.0.0/16`:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Wszystkie zasoby platformy Azure wdrożone do sieci wirtualnej są wdrażane w [podsieci](/azure/virtual-network/virtual-network-manage-subnet) w ramach sieci wirtualnej. 

W poniższej sekcji przykładowego podręcznika rozwiązania Ansible tworzona jest podsieć o nazwie `mySubnet` w sieci wirtualnej `myVnet`:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP





[Publiczne adresy IP](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) umożliwiają zasobom internetowym komunikowanie się w ramach ruchu przychodzącego z zasobami platformy Azure. Publiczne adresy IP umożliwiają również zasoby platformy Azure komunikację wychodzącą z publicznymi usługami platformy Azure. W obu przypadkach adres IP jest przypisywany do zasobu, do którego uzyskiwany jest dostęp. Ten adres jest dedykowany zasobowi, dopóki nie można cofnąć przypisania. Jeśli publiczny adres IP nie jest przypisany do zasobu, zasób może nadal komunikować się ruch wychodzący do Internetu. Połączenie jest nawiązywane przez platformę Azure, dynamiczne przydzielanie dostępnego adresu IP. Dynamicznie przypisany adres nie jest dedykowany zasobowi.

W poniższej sekcji przykładowego podręcznika rozwiązania Ansible tworzony jest publiczny adres IP o nazwie `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

[Sieciowe grupy zabezpieczeń](/azure/virtual-network/security-overview) filtrowanie ruchu sieciowego między zasobami platformy Azure w sieci wirtualnej. Zasady zabezpieczeń są zdefiniowane określające przychodzący i wychodzący ruch do i z zasobów platformy Azure. Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń i zasobów platformy Azure, zobacz [Integracja sieci wirtualnej dla usług platformy Azure](/azure/virtual-network/virtual-network-for-azure-services)

Następujący element playbook tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup`. Sieciowa grupa zabezpieczeń zawiera regułę zezwalającą na ruch SSH na porcie TCP 22.

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

## <a name="create-a-virtual-network-interface-card"></a>Tworzenie wirtualnej karty sieciowej

Wirtualna karta sieciowa łączy maszynę wirtualną z daną siecią wirtualną, publicznym adresem IP oraz sieciową grupą zabezpieczeń. 

Poniższej sekcji, w sekcji element playbook rozwiązania Ansible przykładowy tworzy wirtualną kartę sieciową o nazwie `myNIC` połączone zasoby sieci wirtualne utworzone:

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

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Ostatnim krokiem jest utworzenie maszyny wirtualnej, która wykorzystuje wszystkie zasoby utworzone w poprzednich sekcjach tego artykułu. 

W tej sekcji przykładowego podręcznika rozwiązania Ansible jest tworzona maszyna wirtualna o nazwie `myVM`, do której jest dołączana wirtualna karta sieciowa o nazwie `myNIC`. Zastąp symbol zastępczy &lt;your-key-data> własnymi kompletnymi danymi klucza publicznego.

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
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Kompletny przykładowy podręcznik rozwiązania Ansible

Ta sekcja zawiera cały przykładowy podręcznik rozwiązania Ansible utworzony w ramach tego artykułu. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
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
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
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
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Uruchamianie przykładowego podręcznika rozwiązania Ansible

W tej sekcji omówiono uruchamianie przykładowego podręcznika rozwiązania Ansible zaprezentowanego w tym artykule.

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](/azure/cloud-shell/overview).

1. Utwórz plik, który ma zawierać podręcznik, i nazwij go `azure_create_complete_vm.yml`, a następnie otwórz go w edytorze VI w następujący sposób:

   ```azurecli-interactive
   vi azure_create_complete_vm.yml
   ```

1. Przejdź do trybu wstawiania, naciskając klawisz **I**.

1. Wklej [kompletny przykładowy podręcznik rozwiązania Ansible](#complete-sample-ansible-playbook) do edytora.

1. Opuść tryb wstawiania, naciskając klawisz **Esc**.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Uruchom przykładowy podręcznik rozwiązania Ansible.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. Dane wyjściowe wyglądają mniej więcej tak — możesz zobaczyć, że maszyna wirtualna została pomyślnie utworzona:

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network inteface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. Aby uzyskać dostęp do maszyny wirtualnej z systemem Linux, należy użyć polecenia SSH. Zastąp symbol zastępczy &lt;ip-address> adresem IP z poprzedniego kroku.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Szybki start: Zarządzanie maszyny wirtualnej z systemem Linux na platformie Azure za pomocą rozwiązania Ansible](./ansible-manage-linux-vm.md)