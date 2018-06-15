---
title: Ustaw Terraform używany do tworzenia skali maszyny wirtualnej platformy Azure z pakujący niestandardowego obrazu
description: Użyj Terraform, aby skonfigurować i wersji zestawu skalowania maszyny wirtualnej platformy Azure z niestandardowego obrazu generowane przez pakujący (wraz z sieci wirtualnej i zarządzanych dołączonych dysków).
keywords: terraform, devops, skalowanie maszyny ustawiony, wirtualnych, sieci, magazynu, modułów, niestandardowych obrazów, pakujący
author: VaijanathB
ms.author: tarcher
ms.date: 10/29/2017
ms.topic: article
ms.openlocfilehash: 284eae93de36986e41ba80f98f86495d8f34f57b
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2017
ms.locfileid: "23945856"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Ustaw Terraform używany do tworzenia skali maszyny wirtualnej platformy Azure z pakujący niestandardowego obrazu

W tym samouczku użyjesz [Terraform](https://www.terraform.io/) możesz utworzyć i wdrożyć [zestaw skali maszyny wirtualnej platformy Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) utworzone za pomocą niestandardowego obrazu utworzonego za pomocą [pakujący](https://www.packer.io/intro/index.html) z dyskami zarządzanych przy użyciu [HashiCorp konfiguracji języka](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie wdrożenia Terraform
> * Użyj zmiennych i dane wyjściowe Terraform wdrożenia 
> * Tworzenie i wdrażanie infrastruktury sieciowej
> * Utworzyć obraz niestandardowy maszyny wirtualnej przy użyciu pakujący
> * Tworzenie i wdrażanie skali maszyny wirtualnej ustawić przy użyciu niestandardowego obrazu
> * Tworzenie i wdrażanie jumpbox 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem
> * [Instalowanie Terraform i Konfiguracja dostępu do platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [Utwórz parę kluczy SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) Jeśli nie masz już konto
> * [Zainstaluj pakujący](https://www.packer.io/docs/install/index.html) Jeśli nie masz jeszcze pakujący zainstalowany na komputerze lokalnym


## <a name="create-the-file-structure"></a>Tworzenie struktury plików

Utwórz trzy nowe pliki w pustych katalogów z następujących nazw:

- ```variables.tf```Ten plik zawiera wartości zmiennych w szablonie.
- ```output.tf```Ten plik opisano ustawienia, które są wyświetlane po wdrożeniu.
- ```vmss.tf```Ten plik zawiera kod infrastruktury, która jest wdrażana.

##  <a name="create-the-variables"></a>Utwórz zmienne 

W tym kroku należy zdefiniować zmienne dostosować zasoby utworzone przez Terraform.

Edytuj `variables.tf` pliku, skopiuj następujący kod, a następnie zapisać zmiany.

```tf 
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> Nie ustawiono wartość domyślną zmiennej resource_group_name, definiować własne wartości.

Zapisz plik.

Podczas wdrażania szablonu Terraform chcesz pobrać w pełni kwalifikowaną nazwę, używany do uzyskania dostępu do aplikacji. Użyj ```output``` typ zasobu Terraform i get ```fqdn``` właściwości zasobu. 

Edytuj `output.tf` plik i skopiuj następujący kod do udostępnienia w pełni kwalifikowaną nazwę domeny dla maszyn wirtualnych. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Zdefiniuj w szablonie infrastruktury sieciowej 

W tym kroku możesz utworzyć następującymi elementami infrastruktury sieci w nowej grupy zasobów platformy Azure: 
  - Jedną sieć Wirtualną z przestrzeni adresowej 10.0.0.0/16 
  - Jedna podsieć z przestrzenią adresową 10.0.2.0/24
  - Dwa publiczne adresy IP. Używany przez równoważenia obciążenia zestaw skali maszyny wirtualnej; inne używane do łączenia się SSH jumpbox

Należy również grupę zasobów, w którym wszystkie zasoby są tworzone. 

Edytuj i skopiuj następujący kod w ```vmss.tf``` pliku: 

```tf 

resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Zaleca się znakowanie zasoby wdrażane na platformie Azure, aby ułatwić ich identyfikację w przyszłości.

## <a name="create-the-network-infrastructure"></a>Tworzenie infrastruktury sieciowej

Inicjowanie środowiska Terraform przez uruchomienie następującego polecenia w katalogu, w którym utworzono `.tf` plików:

```bash
terraform init 
```
 
Dostawca wtyczki pobrać z rejestru Terraform do ```.terraform``` folderu w katalogu, w którym zostało uruchomione polecenie.

Uruchom następujące polecenie, aby wdrożyć infrastrukturę platformy Azure.

```bash
terraform apply
```

Upewnij się, że w pełni kwalifikowaną nazwę publicznego adresu IP odpowiada konfiguracji.

![Terraform pełną nazwę domeny dla publicznego adresu IP zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Grupa zasobów zawiera następujące zasoby:

![Zasoby sieciowe Terraform zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Tworzenie obrazu platformy Azure przy użyciu pakujący
Tworzenie niestandardowego obrazu systemu Linux, wykonując kroki opisane w samouczku [tworzenie obrazów maszyny wirtualnej systemu Linux na platformie Azure przy użyciu pakujący](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer).
 
Postępuj zgodnie z samouczkiem, aby utworzyć obraz anulowaną aprowizacją Ubuntu z NGINX zainstalowane.

![Po utworzeniu obrazu pakujący masz obrazu](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Do celów tego samouczka w obrazie pakujący polecenie jest uruchamiane na nginx instaluje. Można również uruchomić własnego skryptu podczas tworzenia.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Edytuj infrastruktury, aby dodać zestaw skali maszyny wirtualnej

W tym kroku utworzenie następujących zasobów w sieci, która była wcześniej wdrożona:
- Moduł równoważenia obciążenia Azure do obsługi aplikacji i dołączenie go do publicznego adresu IP, który został wdrożony w kroku 4
- Jeden Azure obciążenia równoważenia i reguły w celu obsługi aplikacji i dołączenie go do publicznego adresu IP skonfigurowane wcześniej.
- Wewnętrznej bazy danych Azure puli adresów i przypisz je do usługi równoważenia obciążenia 
- Port sondy kondycji używany przez aplikację i skonfigurować usługę równoważenia obciążenia 
- Ustaw działo się za usługą równoważenia obciążenia, systemem sieci wirtualnej wdrożonej wcześniej skali maszyny wirtualnej
- [Nginx](http://nginx.org/) w węzłach skalowania maszyny wirtualnej z obrazu niestandardowego


Dodaj następujący kod na końcu `vmss.tf` pliku.

```tf


resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id                = "${azurerm_lb.vmss.id}"
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = "${var.application_port}"
  backend_port                   = "${var.application_port}"
  backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id="${data.azurerm_image.image.id}"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Dostosowywanie wdrożenia, dodając następujący kod do `variables.tf`:

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Wdrażanie skalowania maszyny wirtualnej ustawić na platformie Azure

Uruchom następujące polecenie, aby wizualizować wdrożenia zestawu skali maszyny wirtualnej:

```bash
terraform plan
```

Dane wyjściowe polecenia wygląda na poniższej ilustracji:

![Dodaj Terraform planu zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Wdrażanie dodatkowych zasobów na platformie Azure: 

```bash
terraform apply 
```

Zawartość grupy zasobów wygląda na poniższej ilustracji:

![Grupa zasobów zestawu skalowania maszyn wirtualnych Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Otwórz przeglądarkę i połącz się w pełni kwalifikowaną nazwę domeny, który został zwrócony przez polecenie. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Dodaj jumpbox do istniejącej sieci 

Ten opcjonalny krok umożliwia SSH dostęp do wystąpień skonfigurowane przy użyciu jumpbox skali maszyny wirtualnej.

Dodaj następujące zasoby do istniejącego wdrożenia:
- Interfejs sieciowy podłączone do tej samej podsieci, niż zestaw skali maszyny wirtualnej
- Maszynę wirtualną z interfejsem sieciowym

Dodaj następujący kod na końcu `vmss.tf` pliku:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Edytuj `outputs.tf` do Dodaj następujący kod, który wyświetla nazwę hosta jumpbox po zakończeniu wdrożenia:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Wdrażanie jumpbox

Wdróż jumpbox.

```bash
terraform apply 
```

Po zakończeniu wdrażania zawartości grupy zasobów wygląda poniższej ilustracji:

![Grupa zasobów zestawu skalowania maszyn wirtualnych Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Zaloguj się za pomocą hasła jest wyłączone na jumpbox i zestawu skalowania maszyny wirtualnej została wdrożona. Zaloguj się przy użyciu protokołu SSH, aby dostęp do maszyn wirtualnych.

## <a name="clean-up-the-environment"></a>Wyczyść środowisko

Zasoby, utworzonej w tym samouczku zostaną usunięte następujące polecenia:

```bash
terraform destroy
```

Typ `yes` po otrzymaniu monitu, aby potwierdzić usuwanie zasobów. Niszczenie może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono zestaw skali maszyny wirtualnej i jumpbox na platformie Azure przy użyciu Terraform. W tym samouczku omówiono:

> [!div class="checklist"]
> * Inicjowanie wdrażania Terraform
> * Użyj zmiennych i dane wyjściowe Terraform wdrożenia 
> * Tworzenie i wdrażanie infrastruktury sieciowej
> * Utworzyć obraz niestandardowy maszyny wirtualnej przy użyciu pakujący
> * Tworzenie i wdrażanie skali maszyny wirtualnej ustawić przy użyciu niestandardowego obrazu
> * Tworzenie i wdrażanie jumpbox 