---
title: Ustaw Terraform używany do tworzenia skali maszyny wirtualnej platformy Azure
description: Samouczek dotyczący korzystania z Terraform do skonfigurowania i wersji skali maszyny wirtualnej platformy Azure ustawić wraz z sieci wirtualnej i zarządzane dołączone dyski
keywords: terraform, metodyki devops wirtualnego urządzenia, Azure, skalowanie zestawu, sieci, magazynu, modułów
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/04/2018
ms.topic: article
ms.openlocfilehash: b7cd9ad90198ead7c68d838547232429dbd1289f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757324"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale"></a>Terraform umożliwiają utworzenie skali maszyny wirtualnej platformy Azure

[Zestawy skalowania maszyny wirtualnej platformy Azure](/azure/virtual-machine-scale-sets) umożliwiają tworzenie i zarządzanie nimi grupy identyczne, obciążenie zrównoważonym maszyn wirtualnych, gdy liczba wystąpień maszyn wirtualnych można automatycznie Zwiększ lub Zmniejsz w odpowiedzi na żądanie lub zdefiniowany harmonogram. 

Z tego samouczka, dowiesz się jak używać [powłoki chmury Azure](/azure/cloud-shell/overview) umożliwia wykonywanie następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie wdrażania Terraform
> * Użyj zmiennych i dane wyjściowe Terraform wdrożenia 
> * Tworzenie i wdrażanie infrastruktury sieciowej
> * Tworzenie i wdrażanie zestaw skali maszyny wirtualnej i dołączenie go do sieci
> * Tworzenie i wdrażanie jumpbox nawiązywania połączenia z maszynami wirtualnymi za pośrednictwem SSH

> [!NOTE]
> Najnowszą wersję Terraform pliki konfiguracji używane w tym artykule są [Terraform świetny repozytorium w usłudze Github](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Zainstaluj Terraform**: postępuj zgodnie z instrukcjami w artykule [Terraform i Konfiguracja dostępu do platformy Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Utwórz parę kluczy SSH**: Jeśli nie masz już parę kluczy, postępuj zgodnie z instrukcjami w artykule SSH [sposobu tworzenia i używania SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Utwórz strukturę katalogów

1. Przejdź do [portalu Azure](http://portal.azure.com).

1. Otwórz [powłoki chmury Azure](/azure/cloud-shell/overview). Jeśli wcześniej nie wybrano środowisko, wybierz **Bash** jako środowiska.

    ![Chmura wierszu polecenia powłoki](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Przejdź do `clouddrive` katalogu.

    ```bash
    cd clouddrive
    ```

1. Utwórz katalog o nazwie `vmss`.

    ```bash
    mkdir vmss
    ```

1. Przejdź do nowego katalogu:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Utwórz plik definicje zmiennych
W tej sekcji można zdefiniować zmienne, które zasoby utworzone przez Terraform dostosować.

W powłoce chmury Azure wykonaj następujące czynności:

1. Utwórz plik o nazwie `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Wprowadź tryb wstawiania, wybierając I klucza.

1. Wklej następujący kod do edytora:

  ```JSON
  variable "location" {
    description = "The location where resources will be created"
  }

  variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
  }

  variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
  }
  ```

1. Zamknij tryb wstawiania, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Utwórz plik definicji danych wyjściowych
W tej sekcji należy utworzyć plik, który opisuje dane wyjściowe po wdrożeniu.

W powłoce chmury Azure wykonaj następujące czynności:

1. Utwórz plik o nazwie `output.tf`.

    ```bash
    vi output.tf
    ```

1. Wprowadź tryb wstawiania, wybierając I klucza.

1. Wklej następujący kod do edytora do udostępnienia w pełni kwalifikowaną nazwę (FQDN) dla maszyn wirtualnych. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Zamknij tryb wstawiania, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Zdefiniuj w szablonie infrastruktury sieciowej
W tej sekcji utworzysz następującymi elementami infrastruktury sieci w nowej grupy zasobów platformy Azure: 

  - Jedną sieć wirtualną (VNET) z przestrzeni adresowej 10.0.0.0/16 
  - Jedna podsieć z przestrzenią adresową 10.0.2.0/24
  - Dwa publiczne adresy IP. Używaną przez maszynę wirtualną równoważenia obciążenia zestaw skalowania, używany do nawiązania połączenia SSH jumpbox innych.

W powłoce chmury Azure wykonaj następujące czynności:

1. Utwórz plik o nazwie `vmss.tf` do opisywania skali maszyny wirtualnej ustawić infrastruktury.

    ```bash
    vi vmss.tf
    ```

1. Wprowadź tryb wstawiania, wybierając I klucza.

1. Wklej następujący kod na końcu pliku, aby w pełni kwalifikowaną nazwę (FQDN) dla maszyn wirtualnych. 

  ```JSON
  resource "azurerm_resource_group" "vmss" {
    name     = "${var.resource_group_name}"
    location = "${var.location}"
    tags     = "${var.tags}"
  }

  resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
  }

  resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    tags                = "${var.tags}"
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
    domain_name_label            = "${random_string.fqdn.result}"
    tags                         = "${var.tags}"
  }
  ```

1. Zamknij tryb wstawiania, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>Zainicjuj obsługę infrastruktury sieciowej
Przy użyciu powłoki chmury Azure z katalogu, w którym utworzono pliki konfiguracji (.tf) wykonaj następujące czynności:

1. Inicjowanie Terraform.

  ```bash
  terraform init 
  ```

1. Uruchom następujące polecenie, aby wdrażania infrastruktury zdefiniowanych na platformie Azure.

  ```bash
  terraform apply
  ```

  Terraform monituje o wartości "Lokalizacja" jako **lokalizacji** zmienna jest zdefiniowana w `variables.tf`, ale nigdy nie jest ustawiona. Można wprowadzić żadnej prawidłowej lokalizacji — takie jak "Zachodnie nam" a następnie wybierając Enter. (Użyj nawiasów wokół wartości ze spacjami).

1. Terraform wyświetla dane wyjściowe, zgodnie z definicją w `output.tf` pliku. Jak pokazano na poniższym zrzucie ekranu, nazwę FQDN ma postać &lt;id >.&lt; Lokalizacja >. cloudapp.azure.com. Wartość identyfikatora jest obliczoną wartością i lokalizacja jest podane podczas uruchamiania Terraform wartość.

  ![Pełni kwalifikowanej nazwy domeny dla publicznego adresu IP zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. W menu portalu Azure wybierz **grup zasobów** z poziomu menu głównego.

1. Na **grup zasobów** wybierz opcję **myResourceGroup** do wyświetlania zasobów, które zostały utworzone przez Terraform.
  ![Zasobów sieciowych zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Dodaj zestaw skali maszyny wirtualnej

W tej sekcji możesz Dowiedz się, jak dodać do szablonu w następujących zasobach:

- Moduł równoważenia obciążenia Azure i reguły obsługi aplikacji i dołączenie go jako publiczny adres IP skonfigurowany w tym artykule
- Wewnętrznej bazy danych Azure puli adresów i przypisz je do usługi równoważenia obciążenia 
- Port sondy kondycji używany przez aplikację i skonfigurować usługę równoważenia obciążenia 
- Ustaw działo się za usługą równoważenia obciążenia w sieci Wirtualnej wdrożone wcześniej w tym artykule skali maszyny wirtualnej
- [Nginx](http://nginx.org/) w węzłach maszyna wirtualna używa skalowania [init chmury](http://cloudinit.readthedocs.io/en/latest/).

W powłoce chmury wykonaj następujące czynności:

1. Otwórz `vmss.tf` pliku konfiguracji.

  ```bash
  vi vmss.tf
  ```

1. Przejdź do końca pliku, a następnie wprowadź tryb dołączania, wybierając klucza.

1. Wklej następujący kod na końcu pliku:

  ```JSON
  resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.vmss.id}"
    }

    tags = "${var.tags}"
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
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = "osdisk"
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
      admin_username       = "${var.admin_user}"
      admin_password       = "${var.admin_password}"
      custom_data          = "${file("web.conf")}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
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

    tags = "${var.tags}"
}
  ```

1. Zamknij tryb wstawiania, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Utwórz plik o nazwie `web.conf` jako konfiguracji chmury init dla maszyn wirtualnych, które są częścią zestawu skalowania. 

    ```bash
    vi web.conf
    ```

1. Wprowadź tryb wstawiania, wybierając I klucza.

1. Wklej następujący kod do edytora:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Zamknij tryb wstawiania, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Otwórz `variables.tf` pliku konfiguracji.

  ```bash
  vi variables.tf
  ```

1. Przejdź do końca pliku, a następnie wprowadź tryb dołączania, wybierając klucza.

1. Dostosowywanie wdrożenia wklejając następujący kod na końcu pliku:

  ```JSON
  variable "application_port" {
      description = "The port that you want to expose to the external load balancer"
      default     = 80
  }

  variable "admin_user" {
      description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
      default     = "azureuser"
  }

  variable "admin_password" {
      description = "Default password for admin account"
  }
  ``` 

1. Zamknij tryb wstawiania, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Tworzenie planu Terraform do wizualizacji wdrożenia zestawu skali maszyny wirtualnej. (Należy określić hasło wybrane, a także lokalizacji zasobów).

  ```bash
  terraform plan
  ```

  Dane wyjściowe polecenia powinny być podobne do następującego zrzutu ekranu:

  ![Dane wyjściowe z tworzenia zestawu skali maszyny wirtualnej](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Wdrożenie nowych zasobów na platformie Azure.

  ```bash
  terraform apply 
  ```

  Dane wyjściowe polecenia powinny być podobne do następującego zrzutu ekranu:

  ![Grupa zasobów zestawu skalowania maszyn wirtualnych Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Otwórz przeglądarkę i połącz się z nazwą FQDN, który został zwrócony przez polecenie. 

  ![Wyniki funkcję przeglądania, aby nazwa FQDN](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Dodaj jumpbox SSH
SSH *jumpbox* pojedynczy serwer, który można "przeskoku" za pośrednictwem Aby uzyskać dostęp do innych serwerów w sieci. W tym kroku należy skonfigurować następujące zasoby:

- Interfejs sieciowy (lub jumpbox) podłączone do tej samej podsieci co zestaw skali maszyny wirtualnej.

- Maszyny wirtualnej jest połączona z interfejsem sieciowym. Ta jumpbox jest dostępny zdalnie. Po nawiązaniu połączenia można SSH do dowolnego w zestawie skalowania maszyn wirtualnych.

1. Otwórz `vmss.tf` pliku konfiguracji.

  ```bash
  vi vmss.tf
  ```

1. Przejdź do końca pliku, a następnie wprowadź tryb dołączania, wybierając klucza.

1. Wklej następujący kod na końcu pliku:

  ```JSON
  resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    public_ip_address_allocation = "static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = "${var.tags}"
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

    tags = "${var.tags}"
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
      admin_username = "${var.admin_user}"
      admin_password = "${var.admin_password}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = "${var.tags}"
  }
  ```

1. Otwórz `output.tf` pliku konfiguracji.

  ```bash
  vi output.tf
  ```

1. Przejdź do końca pliku, a następnie wprowadź tryb dołączania, wybierając klucza.

1. Wklej następujący kod na końcu pliku, aby wyświetlić nazwę hosta jumpbox po zakończeniu wdrażania:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Zamknij tryb wstawiania, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Wdróż jumpbox.

  ```bash
  terraform apply 
  ```

Po zakończeniu wdrożenia jest podobny zawartości grupy zasobów pokazano na poniższym zrzucie ekranu:

![Grupa zasobów zestawu skalowania maszyn wirtualnych Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Możliwość logowania się przy użyciu hasła jest wyłączone na jumpbox i zestawu skalowania maszyny wirtualnej została wdrożona. Zaloguj się przy użyciu protokołu SSH, aby uzyskać dostęp maszyny wirtualne.

## <a name="environment-cleanup"></a>Czyszczenie środowiska 

Aby usunąć zasoby Terraform, które zostały utworzone w tym samouczku, wprowadź następujące polecenie w powłoce chmury:

```bash
terraform destroy
```

Niszczenie może potrwać kilka minut.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób Terraform umożliwiają utworzenie zestawu skalowania maszyny wirtualnej platformy Azure. Poniżej przedstawiono dodatkowe zasoby ułatwiające Dowiedz się więcej o Terraform na platformie Azure: 

 [Koncentrator Terraform w domenie Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentacji dostawcy Terraform platformy Azure](http://aka.ms/terraform)  
 [Źródłowy dostawca Terraform Azure](http://aka.ms/tfgit)  
 [Moduły Terraform Azure](http://aka.ms/tfmodules)