---
title: Utwórz pełną maszyny Wirtualnej systemu Linux na platformie Azure za pomocą Terraform | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Terraform tworzenie i zarządzanie nimi pełną środowiska maszyny wirtualnej systemu Linux na platformie Azure
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
ms.openlocfilehash: 82499df1261cf3288f37ab68c5fc582cf027f3b3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Tworzenie całej infrastruktury maszyny wirtualnej systemu Linux na platformie Azure z Terraform

Terraform umożliwia definiowanie i tworzenie wdrożeń w całej infrastruktury na platformie Azure. Należy utworzyć szablony Terraform w formacie czytelnym dla człowieka, które utworzyć i skonfigurować zasoby platformy Azure w sposób spójny, odtworzenia. W tym artykule przedstawiono sposób tworzenia kompletne środowisko systemu Linux i pomocnicze zasoby z Terraform. Możesz też dowiedzieć się, jak [zainstalować i skonfigurować Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Utwórz w połączeniu z platformą Azure i grupy zasobów

Przejdźmy do każdej sekcji szablonu Terraform. Możesz również sprawdzić pełną wersję programu [szablonu Terraform](#complete-terraform-script) , który możesz skopiować i wkleić.

`provider` Sekcji informuje Terraform do używania dostawcy usługi Azure. Aby uzyskać wartości dla *IDENTYFIKATOR_SUBSKRYPCJI*, *client_id*, *client_secret*, i *tenant_id*, zobacz [zainstalować i Skonfiguruj Terraform](terraform-install-configure.md). 

> [!TIP]
> Jeśli tworzenie zmiennych środowiskowych dla wartości lub korzystają z [środowiska Azure Cloud powłoki Bash](/azure/cloud-shell/overview) , nie trzeba uwzględnić deklaracji zmiennych w tej sekcji.

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

    tags {
        environment = "Terraform Demo"
    }
}
```

W dodatkowych sekcjach odwoływać się grupa zasobów o *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
Poniższa sekcja tworzy sieć wirtualną o nazwie *myVnet* w *10.0.0.0/16* przestrzeni adresów:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```
: Poniższa sekcja tworzy podsieć o nazwie *mySubnet* w *myVnet* sieci wirtualnej

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Utwórz publiczny adres IP
Aby uzyskać dostęp do zasobów w Internecie, należy utworzyć i przypisać publicznego adresu IP do maszyny Wirtualnej. Poniższa sekcja tworzy publiczny adres IP o nazwie *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Utwórz grupę zabezpieczeń sieci
Grupy zabezpieczeń sieci sterowanie przepływem ruchu sieciowego do i z maszyną Wirtualną. Poniższa sekcja tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup* i definiuje regułę, aby zezwolić na ruch portu TCP 22 protokołu SSH:

```tf
resource "azurerm_network_security_group" "temyterraformpublicipnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    ;
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

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Tworzenie wirtualnej karty sieciowej
Karty interfejsu sieci wirtualnej (NIC) nawiązuje połączenie z maszyną Wirtualną do danej sieci wirtualnej, publiczny adres IP i grupy zabezpieczeń sieci. Poniższa sekcja w szablonie Terraform tworzy wirtualną kartę Sieciową o nazwie *myNIC* podłączone do zasobów sieciowych wirtualnej zostały utworzone:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Utwórz konto magazynu diagnostyki
Aby przechowywać diagnostyki rozruchu dla maszyny Wirtualnej, potrzebujesz konta magazynu. Te diagnostyki rozruchu ułatwiają rozwiązywanie problemów i monitorować stan maszyny Wirtualnej. Utworzone konto magazynu jest tylko do przechowywania danych diagnostyki rozruchu. Jak każdego konta magazynu musi mieć unikatową nazwę, poniższej sekcji generuje losowe tekst:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Można teraz utworzyć konta magazynu. Poniższa sekcja tworzy konto magazynu o nazwie na podstawie losowej tekstu generowane w poprzednim kroku:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Ostatnim krokiem jest tworzenie maszyny Wirtualnej i korzystać z zasobów utworzone. Poniższa sekcja tworzy Maszynę wirtualną o nazwie *myVM* i dołącza wirtualną kartę Sieciową o nazwie *myNIC*. R *Ubuntu 16.04-LTS* obraz jest używany, a użytkownik o nazwie *azureuser* jest tworzony za wyłączone uwierzytelnianie hasła.

 Podano danych klucza SSH w *ssh_keys* sekcji. Podaj prawidłowy klucz publiczny SSH w *key_data* pola.

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

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Zakończenie Terraform skryptu

Zebranie tych sekcji i zobaczyć Terraform w akcji, Utwórz plik o nazwie *terraform_azure.tf* i wklej następującą zawartość:

```tf
variable "resourcename" {
  default = "myResourceGroup"
}

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

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
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
    public_ip_address_allocation = "dynamic"

    tags {
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

    tags {
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
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
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

    tags {
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

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Tworzenie i wdrażanie infrastruktury
Z szablonem Terraform utworzone pierwszym krokiem jest zainicjować Terraform. Ten krok zapewnia, że Terraform ma wszystkie wymagania wstępne dotyczące tworzenia szablonu na platformie Azure.

```bash
terraform init
```

Następnym krokiem jest mają Terraform sprawdzić szablonu. Ten krok porównuje wymagane zasoby do informacji o stanie zapisane przez Terraform i danych wyjściowych planowane wykonanie. Zasoby są *nie* utworzona na platformie Azure.

```bash
terraform plan
```

Po wykonaniu poprzednich polecenia powinien zostać wyświetlony ekran podobny do następującego ekranu:

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

Jeśli wszystko wygląda poprawnie i możesz przystąpić do tworzenia infrastruktury na platformie Azure, Zastosuj szablon Terraform:

```bash
terraform apply
```

Po zakończeniu Terraform infrastruktury maszyna wirtualna jest gotowa. Publiczny adres IP maszyny wirtualnej z [az maszyny wirtualnej pokazu](/cli/azure/vm#az_vm_show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Następnie możesz SSH do maszyny Wirtualnej:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Kolejne kroki
Utworzono podstawowej infrastruktury na platformie Azure przy użyciu Terraform. Dla bardziej złożonych scenariuszy, w tym przykłady, których skalowania maszyn wirtualnych i usług równoważenia obciążenia Użyj zestawów, zobacz wiele [Terraform przykłady Azure](https://github.com/hashicorp/terraform/tree/master/examples). Aktualną listę obsługiwanych dostawców Azure, zobacz [dokumentacji Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).
