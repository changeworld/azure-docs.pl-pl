---
title: Szybki start — tworzenie kompletnej maszyny Wirtualnej z systemem Linux na platformie Azure za pomocą programu Terraform
description: W tym przewodniku Szybki start można użyć Terraform do tworzenia i zarządzania kompletnym środowiskiem maszyny wirtualnej systemu Linux na platformie Azure
keywords: azure devops terraform linux vm maszyna wirtualna
ms.topic: quickstart
ms.date: 03/15/2020
ms.openlocfilehash: f262734cc16d97e4d73af371410403a4cbb8815e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79415465"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Szybki start: tworzenie kompletnej infrastruktury maszyn wirtualnych systemu Linux na platformie Azure za pomocą programu Terraform

Terraform umożliwia definiowanie i tworzenie kompletnych wdrożeń infrastruktury na platformie Azure. Szablony Terraform są tworzone w formacie czytelnym dla człowieka, które tworzą i konfigurują zasoby platformy Azure w spójny, powtarzalny sposób. W tym artykule pokazano, jak utworzyć kompletne środowisko systemu Linux i obsługi zasobów z Terraform. Można również dowiedzieć się, jak [zainstalować i skonfigurować Terraform](terraform-install-configure.md).

> [!NOTE]
> Aby uzyskać wsparcie specyficzne dla terraform, skontaktuj się bezpośrednio z Terraform, korzystając z jednego z kanałów społeczności:
>
>    * [Sekcja Terraform](https://discuss.hashicorp.com/c/terraform-core) w portalu społeczności zawiera pytania, przypadki użycia i przydatne wzorce.
>
>    * Pytania związane z dostawcą można znaleźć w sekcji [Dostawcy Terraform](https://discuss.hashicorp.com/c/terraform-providers) w portalu społecznościowym.


## <a name="create-azure-connection-and-resource-group"></a>Tworzenie połączenia i grupy zasobów platformy Azure

Przejdźmy przez każdą sekcję szablonu Terraform. Możesz również zobaczyć pełną wersję [szablonu Terraform,](#complete-terraform-script) który można kopiować i wklejać.

W `provider` sekcji informuje Terraform do korzystania z dostawcy platformy Azure. Aby uzyskać `subscription_id`wartości `client_id` `client_secret`dla `tenant_id`, , i , zobacz [Instalowanie i konfigurowanie terraform](terraform-install-configure.md). 

> [!TIP]
> Jeśli tworzysz zmienne środowiskowe dla wartości lub korzystasz z [usługi Azure Cloud Shell Bash,](/azure/cloud-shell/overview) nie musisz dołączać deklaracji zmiennych w tej sekcji.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

W poniższej sekcji utworzy się grupę zasobów o nazwie `myResourceGroup` w `eastus` lokalizacji:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

W sekcjach dodatkowych odwołujesz się `${azurerm_resource_group.myterraformgroup.name}`do grupy zasobów za pomocą pliku .

## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
W poniższej sekcji utworzy się sieć wirtualną o nazwie `myVnet` w przestrzeni `10.0.0.0/16` adresowej:

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

W poniższej sekcji utworzy się podsieć o nazwie `mySubnet` w sieci wirtualnej: `myVnet`

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Tworzenie publicznego adresu IP
Aby uzyskać dostęp do zasobów w Internecie, utwórz i przypisz publiczny adres IP do maszyny Wirtualnej. W poniższej sekcji utworzy `myPublicIP`się publiczny adres IP o nazwie:

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


## <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Sieciowe grupy zabezpieczeń kontrolują przepływ ruchu sieciowego do i z maszyny Wirtualnej. W poniższej sekcji utworzy `myNetworkSecurityGroup` się sieciową grupę zabezpieczeń o nazwie i definiuje regułę zezwalaną na ruch SSH na porcie TCP 22:

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


## <a name="create-virtual-network-interface-card"></a>Tworzenie karty interfejsu sieci wirtualnej
Karta wirtualnego interfejsu sieciowego (NIC) łączy maszynę wirtualną z daną siecią wirtualną, publicznym adresem IP i sieciową grupą zabezpieczeń. W poniższej sekcji szablonu Terraform `myNIC` utworzono wirtualną kartę sieciową o nazwie połączoną z utworzonymi zasobami sieci wirtualnej:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

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

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>Tworzenie konta magazynu dla diagnostyki
Do przechowywania diagnostyki rozruchu maszyny Wirtualnej potrzebne jest konto magazynu. Te diagnostyki rozruchu może pomóc w rozwiązywaniu problemów i monitorować stan maszyny Wirtualnej. Utworzone konto magazynu służy tylko do przechowywania danych diagnostyki rozruchu. Ponieważ każde konto magazynu musi mieć unikatową nazwę, w poniższej sekcji wygeneruje się losowy tekst:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Teraz możesz utworzyć konto magazynu. W poniższej sekcji utworzono konto magazynu o nazwie na podstawie losowego tekstu wygenerowanego w poprzednim kroku:

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

Ostatnim krokiem jest utworzenie maszyny Wirtualnej i użycie wszystkich utworzonych zasobów. W poniższej sekcji utworzy się maszynę wirtualną o nazwie `myVM` i dołącza wirtualną kartę sieciową o nazwie `myNIC`. Używany `Ubuntu 16.04-LTS` jest najnowszy obraz, a `azureuser` użytkownik o nazwie jest tworzony z wyłączonym uwierzytelnianiem hasłem.

 Dane klucza SSH `ssh_keys` znajdują się w sekcji. Podaj publiczny klucz `key_data` SSH w tej dziedzinie.

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Kompletny skrypt Terraform

Aby połączyć wszystkie te sekcje i wyświetlić terraform w akcji, utwórz plik o nazwie `terraform_azure.tf` i wklej następującą zawartość:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
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

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
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
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Tworzenie i wdrażanie infrastruktury
Po utworzeniu szablonu Terraform pierwszym krokiem jest zainicjowanie terraformu. Ten krok gwarantuje, że Terraform ma wszystkie wymagania wstępne do tworzenia szablonu na platformie Azure.

```bash
terraform init
```

Następnym krokiem jest sprawdzenie terraform i sprawdzenie poprawności szablonu. Ten krok porównuje żądane zasoby do informacji o stanie zapisanych przez Terraform, a następnie wyprowadza planowane wykonanie. Zasoby platformy Azure nie są tworzone w tym momencie.

```bash
terraform plan
```

Po wykonaniu poprzedniego polecenia powinien zostać wyświetlony następujący ekran:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
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

Jeśli wszystko wygląda poprawnie i jesteś gotowy do tworzenia infrastruktury na platformie Azure, zastosuj szablon w Terraform:

```bash
terraform apply
```

Po zakończeniu terraform infrastruktury maszyny Wirtualnej jest gotowy. Uzyskaj publiczny adres IP maszyny Wirtualnej za pomocą [programu AZ VM Show:](/cli/azure/vm)

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Następnie można SSH do maszyny Wirtualnej:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dowiedz się więcej o korzystaniu z programu Terraform na platformie Azure](/azure/terraform)