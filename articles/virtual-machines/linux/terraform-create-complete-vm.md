---
title: Tworzenie kompletnej maszyny wirtualnej z systemem Linux na platformie Azure za pomocą Terraform | Microsoft Docs
description: Dowiedz się, jak za pomocą programu Terraform utworzyć i zarządzać kompletnym środowiskiem maszyn wirtualnych z systemem Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: bb4628477719d0aa2f176c466047531b42d7cfc3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924888"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Tworzenie kompletnej infrastruktury maszyn wirtualnych z systemem Linux na platformie Azure przy użyciu usługi Terraform

Terraform umożliwia definiowanie i Tworzenie pełnych wdrożeń infrastruktury na platformie Azure. Szablony Terraform można tworzyć w formacie czytelnym dla człowieka, który umożliwia tworzenie i Konfigurowanie zasobów platformy Azure w spójny, powtarzalny sposób. W tym artykule opisano sposób tworzenia kompletnego środowiska systemu Linux i obsługi zasobów przy użyciu programu Terraform. Możesz również dowiedzieć się, jak [zainstalować i skonfigurować Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Utwórz połączenie platformy Azure i grupę zasobów

Przejdźmy do każdej sekcji szablonu Terraform. Możesz również wyświetlić pełną wersję [szablonu Terraform](#complete-terraform-script) , który można skopiować i wkleić.

Sekcja `provider` informuje Terraform o konieczności użycia dostawcy platformy Azure. Aby uzyskać wartości dla *subscription_id*, *client_id*, *Client_secret*i *tenant_id*, zobacz [Install and configure Terraform](terraform-install-configure.md). 

> [!TIP]
> Jeśli tworzysz zmienne środowiskowe dla wartości lub używają [Azure Cloud Shell środowiska bash](/azure/cloud-shell/overview) , nie musisz uwzględniać deklaracji zmiennych w tej sekcji.

```hcl
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

W poniższej sekcji została utworzona grupa zasobów o nazwie `myResourceGroup` w lokalizacji `eastus`:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

W dodatkowych sekcjach należy odwołać się do grupy zasobów z *$ {azurerm_resource_group. myterraformgroup. Name}* .

## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
W poniższej sekcji została utworzona sieć wirtualna o nazwie *myVnet* w przestrzeni adresowej *10.0.0.0/16* :

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

W poniższej sekcji przedstawiono Tworzenie podsieci o nazwie Moja *podsieć* w sieci wirtualnej *myVnet* :

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Utwórz publiczny adres IP
Aby uzyskać dostęp do zasobów za pośrednictwem Internetu, Utwórz i przypisz publiczny adres IP do maszyny wirtualnej. W poniższej sekcji został utworzony publiczny adres IP o nazwie *myPublicIP*:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Utwórz sieciową grupę zabezpieczeń
Sieciowe grupy zabezpieczeń kontrolują przepływ ruchu sieciowego do i z maszyny wirtualnej. W poniższej sekcji przedstawiono tworzenie sieciowej grupy zabezpieczeń o nazwie *myNetworkSecurityGroup* i definiuje regułę zezwalającą na ruch SSH na porcie TCP 22:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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


## <a name="create-virtual-network-interface-card"></a>Utwórz kartę sieci wirtualnej
Karta interfejsu sieci wirtualnej łączy MASZYNę wirtualną z daną siecią wirtualną, publicznym adresem IP i sieciową grupą zabezpieczeń. Poniższa sekcja w szablonie Terraform tworzy wirtualną kartę sieciową o nazwie *myNIC* połączoną z utworzonymi przez siebie zasobami sieci wirtualnej:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    network_security_group_id   = azurerm_network_security_group.myterraformnsg.id

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


## <a name="create-storage-account-for-diagnostics"></a>Utwórz konto magazynu na potrzeby diagnostyki
Aby można było przechowywać diagnostykę rozruchu dla maszyny wirtualnej, musisz mieć konto magazynu. Te diagnostyki rozruchu mogą pomóc w rozwiązywaniu problemów i monitorowaniu stanu maszyny wirtualnej. Utworzone konto magazynu służy tylko do przechowywania danych diagnostycznych rozruchowych. Ponieważ każde konto magazynu musi mieć unikatową nazwę, Poniższa sekcja generuje losowy tekst:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Teraz można utworzyć konto magazynu. W poniższej sekcji utworzono konto magazynu o nazwie na podstawie losowego tekstu wygenerowanego w poprzednim kroku:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Ostatnim krokiem jest utworzenie maszyny wirtualnej i użycie wszystkich utworzonych zasobów. Poniższa sekcja tworzy MASZYNę wirtualną o nazwie *myVM* i dołącza wirtualną kartę sieciową o nazwie *myNIC*. Jest używany najnowszy obraz *Ubuntu 16,04-LTS* , a użytkownik o nazwie *azureuser* jest tworzony z wyłączonym uwierzytelnianiem przy użyciu hasła.

 Dane klucza SSH znajdują się w sekcji *ssh_keys* . Podaj prawidłowy publiczny klucz SSH w polu *key_data* .

```hcl
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
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
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Ukończ skrypt Terraform

Aby przenieść wszystkie te sekcje i zobaczyć Terraform w działaniu, Utwórz plik o nazwie *terraform_azure. TF* i wklej następującą zawartość:

```hcl
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
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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
    resource_group_name       = azurerm_resource_group.myterraformgroup.name
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
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
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
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
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Kompilowanie i wdrażanie infrastruktury
Po utworzeniu szablonu Terraform pierwszym krokiem jest zainicjowanie Terraform. Ten krok zapewnia, że Terraform ma wszystkie wymagania wstępne dotyczące kompilowania szablonu na platformie Azure.

```bash
terraform init
```

Następnym krokiem jest sprawdzenie Terraform i zweryfikowanie szablonu. Ten krok umożliwia porównanie żądanych zasobów z informacjami o stanie zapisywanymi przez Terraform, a następnie wyjściem z zaplanowanego wykonania. Zasoby *nie* są tworzone na platformie Azure.

```bash
terraform plan
```

Po wykonaniu poprzedniego polecenia powinien wyglądać podobnie do następującego ekranu:

```console
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

Jeśli wszystko wygląda poprawnie i wszystko jest gotowe do skompilowania infrastruktury na platformie Azure, Zastosuj szablon w Terraform:

```bash
terraform apply
```

Po zakończeniu Terraform infrastruktura maszyn wirtualnych będzie gotowa. Uzyskaj publiczny adres IP maszyny wirtualnej za pomocą [AZ VM show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Następnie można obsłużyć protokół SSH na maszynie wirtualnej:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Następne kroki
Utworzono infrastrukturę podstawową na platformie Azure za pomocą Terraform. W przypadku bardziej złożonych scenariuszy, w tym przykładów, które używają modułów równoważenia obciążenia i zestawów skalowania maszyn wirtualnych, zobacz wiele [przykładów Terraform dla platformy Azure](https://github.com/hashicorp/terraform/tree/master/examples). Aby uzyskać aktualną listę obsługiwanych dostawców platformy Azure, zapoznaj się z [dokumentacją Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).
