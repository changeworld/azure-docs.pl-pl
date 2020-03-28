---
title: Samouczek — tworzenie zestawu skalowania maszyny wirtualnej platformy Azure na podstawie niestandardowego obrazu programu Packer przy użyciu terraform
description: Za pomocą narzędzia Terraform można konfigurować i wersjonować zestaw skalowania maszyn wirtualnych platformy Azure z niestandardowego obrazu wygenerowanego przez narzędzie Packer (wraz z siecią wirtualną i zarządzanymi dołączonymi dyskami).
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 92a8221d625f8b6b73343f74b85fdfcf5e578b23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472212"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Samouczek: Tworzenie zestawu skalowania maszyny wirtualnej platformy Azure na podstawie niestandardowego obrazu programu Packer przy użyciu terraform

W tym samouczku można użyć [Terraform](https://www.terraform.io/) do tworzenia i wdrażania [zestawu skalowania maszyny wirtualnej platformy Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) utworzonego przy użyciu niestandardowego obrazu utworzonego przy użyciu programu [Packer](https://www.packer.io/intro/index.html) z dyskami zarządzanymi, które używają języka [konfiguracji HashiCorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL). 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj wdrożenie Terraform.
> * Użyj zmiennych i wyjść dla wdrożenia Terraform.
> * Tworzenie i wdrażanie infrastruktury sieciowej.
> * Utwórz niestandardowy obraz maszyny wirtualnej przy użyciu programu Packer.
> * Tworzenie i wdrażanie zestawu skalowania maszyny wirtualnej przy użyciu obrazu niestandardowego.
> * Tworzenie i wdrażanie jumpbox.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

- **Terraform**: [Zainstaluj terraform i skonfiguruj dostęp do platformy Azure](terraform-install-configure.md).
- **Para kluczy SSH**: [Utwórz parę kluczy SSH](/azure/virtual-machines/linux/mac-create-ssh-keys).
- **Pakujący**: [Zainstaluj Packer](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>Tworzenie struktury plików

W pustym katalogu utwórz trzy nowe pliki o następujących nazwach:

- `variables.tf`: Ten plik zawiera wartości zmiennych używanych w szablonie.
- `output.tf`: Ten plik opisuje ustawienia wyświetlane po wdrożeniu.
- `vmss.tf`: Ten plik zawiera kod wdrażanej infrastruktury.

##  <a name="create-the-variables"></a>Tworzenie zmiennych 

W tym kroku zdefiniujesz zmienne, za pomocą których dostosujesz zasoby utworzone w narzędziu Terraform.

Edytuj `variables.tf` plik, skopiuj następujący kod, a następnie zapisz zmiany.

```hcl
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
> Domyślna wartość zmiennej resource_group_name jest nieustawię. Zdefiniuj własną wartość.

Zapisz plik.

Podczas wdrażania szablonu Terraform, chcesz uzyskać w pełni kwalifikowaną nazwę domeny, która jest używana do uzyskania dostępu do aplikacji. Użyj typu zasobu `output` narzędzia Terraform i pobierz właściwość zasobu `fqdn`. 

Przeprowadź edycję pliku `output.tf` i skopiuj poniższy kod, aby uwidocznić w pełni kwalifikowaną nazwę domeny dla maszyn wirtualnych. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definiowanie infrastruktury sieci w szablonie 

W tym kroku utworzysz w nowej grupie zasobów platformy Azure następującą infrastrukturę sieci: 
  - Jedna sieć wirtualna o przestrzeni adresowej 10.0.0.0/16.
  - Jedna podsieć o przestrzeni adresowej 10.0.2.0/24.
  - Dwa publiczne adresy IP: Jeden jest używany przez moduł równoważenia obciążenia zestawu wagowych zestawu maszyn wirtualnych. Drugi jest używany do łączenia się z jumpbox SSH.

Potrzebujesz również grupy zasobów, w której są tworzone wszystkie zasoby. 

Przeprowadź edycję pliku `vmss.tf` i skopiuj następujący kod: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = azurerm_resource_group.vmss.name
  virtual_network_name = azurerm_virtual_network.vmss.name
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Otaguj zasoby, które są wdrażane na platformie Azure, aby ułatwić ich identyfikację w przyszłości.

## <a name="create-the-network-infrastructure"></a>Tworzenie infrastruktury sieci

Zainicjuj środowisko narzędzia Terraform, uruchamiając następujące polecenie w katalogu, w którym zostały utworzone pliki `.tf`:

```bash
terraform init 
```
 
Wtyczki dostawcy są pobierane z rejestru `.terraform` Terraform do folderu w katalogu, w którym uruchomiono polecenie.

Uruchom poniższe polecenie, aby wdrożyć infrastrukturę na platformie Azure.

```bash
terraform apply
```

Sprawdź, czy w pełni kwalifikowana nazwa domeny publicznego adresu IP odpowiada Twojej konfiguracji.

![Zestaw skalowania maszyny wirtualnej Terraform w pełni kwalifikowana nazwa domeny dla publicznego adresu IP](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Grupa zasobów zawiera następujące zasoby:

![Zasoby sieciowe narzędzia Terraform zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Tworzenie obrazu platformy Azure przy użyciu programu Packer
Utwórz niestandardowy obraz systemu Linux, wykonując kroki opisane w samouczku [Jak używać programu Packer do tworzenia obrazów maszyn wirtualnych systemu Linux na platformie Azure.](/azure/virtual-machines/linux/build-image-with-packer)
 
Postępuj zgodnie z samouczka, aby utworzyć deprovisioned obraz Ubuntu z Nginx zainstalowany.

![Po utworzeniu obrazu Packer masz obraz](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Na potrzeby tego samouczka, w obrazie Packer, polecenie jest uruchamiane w celu zainstalowania Nginx. Możesz również uruchomić własny skrypt w procesie tworzenia.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Edytowanie infrastruktury w celu dodania zestawu skalowania maszyn wirtualnych

W tym kroku we wdrożonej wcześniej sieci utworzysz następujące zasoby:
- Moduł równoważenia obciążenia platformy Azure do obsługi aplikacji. Dołącz go do publicznego adresu IP, który został wdrożony wcześniej.
- Jeden moduł równoważenia obciążenia platformy Azure i reguły do obsługi aplikacji. Dołącz go do publicznego adresu IP, który został skonfigurowany wcześniej.
- Pula adresów zaplecza platformy Azure. Przypisz go do modułu równoważenia obciążenia.
- Port sondy kondycji używany przez aplikację i skonfigurowany na modułu równoważenia obciążenia.
- Zestaw skalowania maszyny wirtualnej, który znajduje się za modułem równoważenia obciążenia i jest uruchamiany w sieci wirtualnej, która została wdrożona wcześniej.
- [Nginx](https://nginx.org/) w węzłach skali maszyny wirtualnej zainstalowanych na obrazie niestandardowym.


Na końcu pliku `vmss.tf` dodaj poniższy kod.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "ssh-running-probe"
  port                = var.application_port
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = azurerm_resource_group.vmss.name
  loadbalancer_id                = azurerm_lb.vmss.id
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = var.application_port
  backend_port                   = var.application_port
  backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = azurerm_lb_probe.vmss.id
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id=data.azurerm_image.image.id
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
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = azurerm_subnet.vmss.id
      load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
      primary = true
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Dostosuj wdrożenie, dodając do pliku `variables.tf` następujący kod:

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Wdrażanie zestawu skalowania maszyn wirtualnych na platformie Azure

Aby zwizualizować wdrożenie zestawu skalowania maszyn wirtualnych, uruchom następujące polecenie:

```bash
terraform plan
```

Dane wyjściowe polecenia wyglądają jak na poniższej ilustracji:

![Plan dodawania zestawu skalowania maszyn wirtualnych narzędzia Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Wdróż dodatkowe zasoby na platformie Azure: 

```bash
terraform apply 
```

Zawartość grupy zasobów wygląda jak na poniższej ilustracji:

![Grupa zasobów zestawu skalowania maszyn wirtualnych narzędzia Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Otwórz przeglądarkę i połącz się z w pełni kwalifikowaną nazwą domeny, która została zwrócona przez polecenie. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Dodawanie rampy do istniejącej sieci 

Ten opcjonalny krok umożliwia dostęp SSH do wystąpień zestawu skalowania maszyn wirtualnych za pomocą rampy.

Do istniejącego wdrożenia dodaj następujące zasoby:
- Interfejs sieciowy połączony z tą samą podsiecią co zestaw skalowania maszyny wirtualnej
- Maszyna wirtualna z tym interfejsem sieciowym

Na końcu pliku `vmss.tf` dodaj następujący kod:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = azurerm_subnet.vmss.id
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = azurerm_public_ip.jumpbox.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = var.location
  resource_group_name   = azurerm_resource_group.vmss.name
  network_interface_ids = [azurerm_network_interface.jumpbox.id]
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
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Edytuj, `outputs.tf` aby dodać następujący kod, który wyświetla nazwa hosta jumpbox po zakończeniu wdrażania:

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>Wdrażanie rampy

Wdróż rampę.

```bash
terraform apply 
```

Po zakończeniu wdrażania zawartość grupy zasobów wygląda następująco:

![Grupa zasobów zestawu skalowania maszyn wirtualnych narzędzia Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Logowanie za pomocą hasła jest wyłączone w jumpbox i zestaw skalowania maszyny wirtualnej, który został wdrożony. Zaloguj się za pomocą SSH, aby uzyskać dostęp do maszyn wirtualnych.

## <a name="clean-up-the-environment"></a>Czyszczenie środowiska

Następujące polecenia powodują usunięcie zasobów utworzonych w ramach tego samouczka:

```bash
terraform destroy
```

Wprowadź *tak,* gdy zostaniesz poproszony o potwierdzenie usunięcia zasobów. Proces niszczenia może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o korzystaniu z programu Terraform na platformie Azure](/azure/terraform)
