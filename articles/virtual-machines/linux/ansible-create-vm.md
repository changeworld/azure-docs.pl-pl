---
title: Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure za pomocą rozwiązania Ansible
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux na platformie Azure przy użyciu rozwiązania Ansible
ms.service: virtual-machines-linux
keywords: ansible, azure, devops, maszyna wirtualna
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/22/2018
ms.openlocfilehash: 38cc6cd8f375fe7c60a706541bc74313e8ea2c4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60188144"
---
# <a name="use-ansible-to-create-a-linux-virtual-machine-in-azure"></a>Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure za pomocą rozwiązania Ansible
Rozwiązanie Ansible umożliwia automatyczne tworzenie, konfigurowanie i wdrażanie zasobów platformy Azure przy użyciu języka deklaratywnego, za pośrednictwem *podręczników* rozwiązania Ansible. W poszczególnych sekcjach tego artykułu opisano kolejne sekcje podręcznika rozwiązania Ansible, umożliwiające tworzenie i konfigurowanie różnych aspektów maszyny wirtualnej z systemem Linux. Na końcu tego artykułu znajdziesz [kompletny podręcznik rozwiązania Ansible](#complete-sample-ansible-playbook).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)]

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
[Publiczne adresy IP](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) umożliwiają zasobom internetowym komunikowanie się w ramach ruchu przychodzącego z zasobami platformy Azure. Publiczne adresy IP umożliwiają również zasobom platformy Azure komunikowanie się w ramach ruchu wychodzącego z Internetem i publicznymi usługami platformy Azure za pomocą adresu IP przypisanego do zasobu. Ten adres jest dedykowany zasobowi, dopóki nie cofniesz jego przypisania. Jeśli publiczny adres IP nie jest przypisany do zasobu, zasób nadal może łączyć się z Internetem w ramach komunikacji wychodzącej, ale platforma Azure dynamicznie przypisuje dostępny adres IP, który nie jest dedykowany dla zasobu. 

W poniższej sekcji przykładowego podręcznika rozwiązania Ansible tworzony jest publiczny adres IP o nazwie `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
[Sieciowa grupa zabezpieczeń](/azure/virtual-network/security-overview) umożliwia filtrowanie ruchu sieciowego przychodzącego do zasobów platformy Azure i wychodzącego z nich w sieci wirtualnej platformy Azure. Sieciowa grupa zabezpieczeń zawiera reguły zabezpieczeń, które zezwalają na lub blokują przychodzący ruch sieciowy lub wychodzący ruch sieciowy dla kilku typów zasobów platformy Azure. 

W poniższej sekcji przykładowego podręcznika rozwiązania Ansible tworzona jest sieciowa grupa zabezpieczeń o nazwie `myNetworkSecurityGroup` i jest definiowana reguła zezwalająca na ruch SSH do portu TCP 22:

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

W poniższej sekcji przykładowego podręcznika rozwiązania Ansible tworzona jest wirtualna karta sieciowa o nazwie `myNIC` połączona z zasobami utworzonej sieci wirtualnej:

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

## <a name="create-a-virtual-machine"></a>Utwórz maszynę wirtualną
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
> [Zarządzanie maszyną wirtualną z systemem Linux na platformie Azure za pomocą rozwiązania Ansible](./ansible-manage-linux-vm.md)
