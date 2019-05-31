---
title: Tworzenie pełnej maszyny Wirtualnej systemu Linux na platformie Azure za pomocą narzędzia Terraform | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i zarządzać nimi kompletnego środowiska maszyny wirtualnej systemu Linux na platformie Azure za pomocą narzędzia Terraform
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: 406af03ab81b279be4f0d72dcca4ec8ec0e4ee55
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304650"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Tworzenie całej infrastruktury maszyny wirtualnej systemu Linux na platformie Azure przy użyciu programu Terraform

Narzędzie Terraform umożliwia definiowanie i tworzenie wdrożeń w całej infrastruktury na platformie Azure. Możesz tworzyć szablony programu Terraform w formacie czytelnym dla człowieka, które utworzyć i skonfigurować zasoby platformy Azure w sposób spójny, do odtworzenia. W tym artykule pokazano, jak tworzenie kompletnego środowiska systemu Linux i zasoby przy użyciu programu Terraform pomocnicze. Możesz też dowiedzieć się, jak [Instalowanie i konfigurowanie programu Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Tworzenie połączenia platformy Azure i grupy zasobów

Przejdźmy przez każdej sekcji szablonu programu Terraform. Można również wyświetlić pełną wersję [szablonu programu Terraform](#complete-terraform-script) , można skopiować i wkleić.

`provider` Sekcji informuje narzędzie Terraform w celu używania dostawcy platformy Azure. Aby uzyskać wartości dla *subscription_id*, *client_id*, *client_secret*, i *tenant_id*, zobacz [zainstalować i Skonfiguruj program Terraform](terraform-install-configure.md). 

> [!TIP]
> Jeśli tworzenia zmiennych środowiskowych dla wartości, lub korzystasz z [środowisko usługi Azure Cloud Shell Bash](/azure/cloud-shell/overview) , nie musisz dołączyć deklaracje zmiennych w tej sekcji.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

Poniższa sekcja tworzy grupę zasobów o nazwie `myResourceGroup` w `eastus` lokalizacji:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

W dodatkowych sekcjach, możesz odwoływać się do grupę zasobów za pomocą *${azurerm_resource_group.myterraformgroup.name}* .

## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
Poniższa sekcja umożliwia utworzenie sieci wirtualnej o nazwie *myVnet* w *10.0.0.0/16* przestrzeń adresowa:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags = {
        environment = "Terraform Demo"
    }
}
```

Poniższa sekcja tworzy podsieć o nazwie *mySubnet* w *myVnet* sieci wirtualnej:

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Tworzenie publicznego adresu IP
Dostęp do zasobów w Internecie, Utwórz i przypisz publiczny adres IP do maszyny Wirtualnej. Poniższa sekcja tworzy publiczny adres IP o nazwie *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Utwórz sieciową grupę zabezpieczeń
Sieciowe grupy zabezpieczeń Sterowanie przepływem ruchu sieciowego do i z maszyny Wirtualnej. Poniższa sekcja tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup* i definiuje regułę zezwalającą na ruch SSH na porcie TCP 22:

```tf
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Utwórz wirtualną kartę sieciową
Karty interfejsu sieci wirtualnej (NIC) nawiązuje połączenie z maszyną Wirtualną do danej sieci wirtualnej, publiczny adres IP i sieciową grupą zabezpieczeń. Poniższej sekcji w szablonie Terraform tworzy wirtualną kartę Sieciową o nazwie *myNIC* połączone zasoby sieci wirtualne zostały utworzone:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Utwórz konto magazynu diagnostyki
Do przechowywania diagnostyki rozruchu dla maszyny Wirtualnej, należy na koncie magazynu. Te Diagnostyka rozruchu może ułatwić rozwiązywanie problemów i monitorować stan maszyny Wirtualnej. Utworzone konto magazynu jest tylko do przechowywania danych diagnostyki rozruchu. Ponieważ każde konto magazynu musi mieć unikatową nazwę, poniższa sekcja generuje losowy tekst:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Teraz można utworzyć konta magazynu. Poniższa sekcja tworzy konto magazynu o nazwie na podstawie losowego tekstu wygenerowanego w poprzednim kroku:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Ostatnim krokiem jest tworzenie maszyny Wirtualnej i używanie wszystkie zasoby utworzone. Poniższa sekcja umożliwia utworzenie maszyny Wirtualnej o nazwie *myVM* i dołącza wirtualną kartę Sieciową o nazwie *myNIC*. Najnowsze *Ubuntu 16.04-LTS* jest używany obraz, a użytkownik o nazwie *azureuser* jest tworzony przy użyciu uwierzytelniania hasła wyłączone.

 Dane klucza SSH znajduje się w *ssh_keys* sekcji. Podaj prawidłowy publiczny klucz SSH w *key_data* pola.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Kompletny skrypt programu Terraform

Zbierz razem wszystkie te sekcje, i zobaczyć Terraform w akcji, Utwórz plik o nazwie *terraform_azure.tf* i wklej następującą zawartością:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = "${azurerm_resource_group.myterraformgroup.name}"
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Tworzenie i wdrażanie infrastruktury
Za pomocą szablonu programu Terraform utworzyć pierwszym krokiem jest do zainicjowania programu Terraform. Ten krok zapewnia, że narzędzia Terraform ma wszystkie wstępnie wymagane składniki do tworzenia szablonu na platformie Azure.

```bash
terraform init
```

Następnym krokiem jest zapewnienie Terraform Przegląd i weryfikacja szablonu. W tym kroku porównuje żądanych zasobów do informacji o stanie zapisane przez narzędzie Terraform, a następnie generuje planowanego wykonania. Zasoby są *nie* tworzenia na platformie Azure.

```bash
terraform plan
```

Po wykonaniu poprzedniego polecenia, powinien zostać wyświetlony podobny do następującego ekranu:

```bash
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Jeśli wszystko wygląda poprawnie i możesz przystąpić do tworzenia infrastruktury na platformie Azure, Zastosuj szablon w narzędziu Terraform:

```bash
terraform apply
```

Po zakończeniu działania narzędzia Terraform infrastruktury maszyny Wirtualnej jest gotowy. Uzyskaj publiczny adres IP maszyny wirtualnej za pomocą [az vm show](/cli/azure/vm):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Następnie możesz SSH z maszyną wirtualną:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Kolejne kroki
Utworzono podstawową infrastrukturą na platformie Azure przy użyciu programu Terraform. W przypadku bardziej złożonych scenariuszy, w tym przykłady, które można skalować maszyn wirtualnych i równoważenia obciążenia Użyj zestawów, zobacz liczne [przykłady programu Terraform na platformie Azure](https://github.com/hashicorp/terraform/tree/master/examples). Aby uzyskać aktualną listę obsługiwanych dostawców platformy Azure, zobacz [dokumentacja programu Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).
