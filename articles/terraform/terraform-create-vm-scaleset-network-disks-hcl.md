---
title: Ustaw Użyj Terraform utworzyć skalę maszyny wirtualnej platformy Azure
description: Samouczek dotyczący korzystania z narzędzia Terraform w celu skonfigurowania i wersji skalowania maszyn wirtualnych platformy Azure, ustaw wraz z sieci wirtualnej i zarządzane dołączone dyski
keywords: terraform i infrastruktury devops i wirtualnych maszyn, Azure, skalowanie zestawu, sieć, Magazyn, modułów
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/04/2018
ms.topic: article
ms.openlocfilehash: 5922bad24c50a9d315aae42ce11a33801b9dbcaf
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971837"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Ustaw Użyj Terraform utworzyć skalę maszyny wirtualnej platformy Azure

[Zestawy skalowania maszyn wirtualnych platformy Azure](/azure/virtual-machine-scale-sets) pozwalają na tworzenie i zarządzanie nimi grupę identycznych, obciążenia równoważenia maszyn wirtualnych, gdzie liczbę wystąpień maszyn wirtualnych może automatycznie zwiększyć lub zmniejszyć w odpowiedzi na zapotrzebowanie lub zdefiniowany harmonogram. 

W tym samouczku dowiesz się, jak używać [usługi Azure Cloud Shell](/azure/cloud-shell/overview) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie wdrożenia programu Terraform
> * Użycie zmiennych i danych wyjściowych dla wdrożenia programu Terraform 
> * Tworzenie i wdrażanie infrastruktury sieci
> * Tworzenie i wdrażanie zestawu skalowania maszyn wirtualnych i dołączyć go do sieci
> * Tworzenie i wdrażanie serwera przesiadkowego, aby nawiązać połączenie z maszynami wirtualnymi za pośrednictwem protokołu SSH

> [!NOTE]
> Najnowszą wersję programu Terraform, pliki konfiguracji używane w tym artykule znajdują się w [Awesome Terraform repozytorium w serwisie Github](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Zainstaluj narzędzia Terraform**: postępuj zgodnie z instrukcjami w artykule [Terraform i skonfigurować dostęp do platformy Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Tworzenie pary kluczy SSH**: Jeśli nie masz jeszcze SSH pary kluczy, postępuj zgodnie z instrukcjami w artykule [sposobu tworzenia i używania publicznych i prywatnych pary kluczy SSH dla maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów

1. Przejdź do [witryny Azure portal](http://portal.azure.com).

1. Otwórz [usługi Azure Cloud Shell](/azure/cloud-shell/overview). Jeśli środowisko nie została wybrana wcześniej, wybierz opcję **Bash** jako środowiska.

    ![Cloud Shell w wierszu polecenia](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Zmień katalog na `clouddrive` katalogu.

    ```bash
    cd clouddrive
    ```

1. Utwórz katalog o nazwie `vmss`.

    ```bash
    mkdir vmss
    ```

1. Zmień katalogi do nowego katalogu:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Utwórz plik definicji zmiennych
W tej sekcji można zdefiniować zmienne, które Dostosuj zasoby utworzone w narzędziu Terraform.

W ramach usługi Azure Cloud Shell wykonaj następujące czynności:

1. Utwórz plik o nazwie `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Wprowadź trybu wstawiania, wybierając I klucz.

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

1. Tryb wstawiania w zakończenia, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Utwórz plik definicji danych wyjściowych
W tej sekcji utworzysz plik który opisano dane wyjściowe po wdrożeniu.

W ramach usługi Azure Cloud Shell wykonaj następujące czynności:

1. Utwórz plik o nazwie `output.tf`.

    ```bash
    vi output.tf
    ```

1. Wprowadź trybu wstawiania, wybierając I klucz.

1. Wklej następujący kod do edytora do udostępnienia w pełni kwalifikowana nazwa domeny (FQDN) dla maszyn wirtualnych. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Tryb wstawiania w zakończenia, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Zdefiniuj infrastrukturę sieciową w szablonie
W tej sekcji utworzysz następującymi elementami infrastruktury sieci w nowej grupie zasobów platformy Azure: 

  - Jednej sieci wirtualnej (VNET) przy użyciu przestrzeni adresów 10.0.0.0/16 
  - Jednej podsieci z przestrzeni adresowej 10.0.2.0/24
  - Dwa publiczne adresy IP. Jeden używanym przez maszynę wirtualną równoważenia obciążenia zestawu skalowania, inne użyte do nawiązania połączenia z serwerem przesiadkowym SSH.

W ramach usługi Azure Cloud Shell wykonaj następujące czynności:

1. Utwórz plik o nazwie `vmss.tf` do opisania skalowania maszyn wirtualnych zestawu infrastruktury.

    ```bash
    vi vmss.tf
    ```

1. Wprowadź trybu wstawiania, wybierając I klucz.

1. Wklej następujący kod na końcu pliku do udostępnienia w pełni kwalifikowana nazwa domeny (FQDN) dla maszyn wirtualnych. 

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

1. Tryb wstawiania w zakończenia, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>Udostępniać infrastrukturę sieci
Przy użyciu usługi Azure Cloud Shell z katalogu, w którym zostały utworzone pliki konfiguracji (.tf) wykonaj następujące czynności:

1. Inicjowanie programu Terraform.

  ```bash
  terraform init 
  ```

1. Uruchom następujące polecenie, aby wdrożyć zdefiniowanych infrastruktury na platformie Azure.

  ```bash
  terraform apply
  ```

  Terraform wyświetli monit o podanie wartości "Lokalizacja" jako **lokalizacji** zmienna jest zdefiniowana w `variables.tf`, ale nigdy nie jest ustawiona. Można wprowadzić żadnej prawidłowej lokalizacji — np. "Zachodnie stany USA" a następnie wybierając przycisk Enter. (Użyj nawiasów wokół dowolnej wartości ze spacjami).

1. Narzędzie Terraform drukuje dane wyjściowe, zgodnie z definicją w `output.tf` pliku. Jak pokazano na poniższym zrzucie ekranu, nazwy FQDN ma postać &lt;id >.&lt; Lokalizacja >. cloudapp.azure.com. Wartość identyfikatora jest obliczoną wartością i lokalizacja to wartości, podane podczas uruchamiania narzędzia Terraform.

  ![W pełni kwalifikowanej nazwy domeny dla publicznego adresu IP zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. W menu portalu Azure wybierz **grup zasobów** z menu głównego.

1. Na **grup zasobów** zaznacz **myResourceGroup** do wyświetlania zasobów, które zostały utworzone przez narzędzie Terraform.
  ![Zasoby sieciowe zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Dodaj zestaw skalowania maszyn wirtualnych

W tej sekcji dowiesz się, jak dodać następujące zasoby do szablonu:

- Usługa Azure load balancer i reguł do obsługi aplikacji i dołączyć je do publicznego adresu IP skonfigurowane wcześniej w tym artykule
- Pula adresów zaplecza platformy Azure, a przypisz go do modułu równoważenia obciążenia 
- Port sondy kondycji używane przez aplikację i skonfigurować w usłudze równoważenia obciążenia 
- Profiluje za modułem równoważenia obciążenia, który jest uruchamiany w sieci Wirtualnej wdrożony we wcześniejszej części tego artykułu zestawu skalowania maszyn wirtualnych
- [Serwer Nginx](http://nginx.org/) na węzłach maszyna wirtualna używa skalowania [pakietu cloud-init](http://cloudinit.readthedocs.io/en/latest/).

W usłudze Cloud Shell wykonaj następujące czynności:

1. Otwórz `vmss.tf` pliku konfiguracji.

  ```bash
  vi vmss.tf
  ```

1. Przejdź na koniec pliku, a następnie wprowadź trybie append, wybierając klawisz A.

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

1. Tryb wstawiania w zakończenia, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Utwórz plik o nazwie `web.conf` ma pełnić rolę konfiguracji pakietu cloud-init dla maszyn wirtualnych, które są częścią zestawu skalowania. 

    ```bash
    vi web.conf
    ```

1. Wprowadź trybu wstawiania, wybierając I klucz.

1. Wklej następujący kod do edytora:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Tryb wstawiania w zakończenia, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Otwórz `variables.tf` pliku konfiguracji.

  ```bash
  vi variables.tf
  ```

1. Przejdź na koniec pliku, a następnie wprowadź trybie append, wybierając klawisz A.

1. Wklejając następujący kod na końcu pliku, dostosować wdrożenia:

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

1. Tryb wstawiania w zakończenia, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Tworzenie planu narzędzia Terraform w celu wizualizacji wdrażania zestawu skalowania maszyn wirtualnych. (Należy określić hasło wybrane, a także lokalizacji zasobów).

  ```bash
  terraform plan
  ```

  Dane wyjściowe polecenia powinny być podobne do Poniższy zrzut ekranu:

  ![Dane wyjściowe z tworzenia zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Wdrażanie nowych zasobów na platformie Azure.

  ```bash
  terraform apply 
  ```

  Dane wyjściowe polecenia powinny być podobne do Poniższy zrzut ekranu:

  ![Grupa zasobów zestawu skalowania maszyn wirtualnych programu Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Otwórz przeglądarkę i połącz się nazwy FQDN, który został zwrócony przez polecenie. 

  ![Wyniki przeglądania do pełni kwalifikowaną nazwę domeny](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Dodaj serwer przesiadkowy SSH
SSH *rampy* pojedynczy serwer, który "przejdziesz" za pośrednictwem w celu uzyskania dostępu do innych serwerów w sieci. W tym kroku należy skonfigurować następujące zasoby:

- Interfejs sieciowy (lub serwera przesiadkowego) podłączone do tej samej podsieci co zestaw skalowania maszyn wirtualnych.

- Maszyna wirtualna jest połączona z tym interfejsem sieciowym. Ta rampy jest dostępny zdalnie. Po nawiązaniu połączenia możesz SSH do dowolnej maszyny wirtualne w zestawie skalowania.

1. Otwórz `vmss.tf` pliku konfiguracji.

  ```bash
  vi vmss.tf
  ```

1. Przejdź na koniec pliku, a następnie wprowadź trybie append, wybierając klawisz A.

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

1. Przejdź na koniec pliku, a następnie wprowadź trybie append, wybierając klawisz A.

1. Wklej następujący kod na końcu pliku, aby wyświetlić nazwę hosta serwera przesiadkowego, gdy wdrożenie jest ukończone:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Tryb wstawiania w zakończenia, wybierając klawisz Esc.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Wdrażanie serwera przesiadkowego.

  ```bash
  terraform apply 
  ```

Po zakończeniu wdrożenia powinien wyglądać zawartość grupy zasobów, który pokazano na poniższym zrzucie ekranu:

![Grupa zasobów zestawu skalowania maszyn wirtualnych programu Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Możliwość zalogowania się przy użyciu hasła jest wyłączona na serwer przesiadkowy i zestawu skalowania maszyn wirtualnych została wdrożona. Zaloguj się przy użyciu protokołu SSH na dostęp do maszyny wirtualne.

## <a name="environment-cleanup"></a>Czyszczenie środowiska 

Aby usunąć zasoby programu Terraform, które zostały utworzone w ramach tego samouczka, wprowadź następujące polecenie w usłudze Cloud Shell:

```bash
terraform destroy
```

Zniszczenie może potrwać kilka minut.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób tworzenia zestawu skalowania maszyn wirtualnych platformy Azure za pomocą narzędzia Terraform. Poniżej przedstawiono niektóre dodatkowe zasoby, aby dowiedzieć się więcej o narzędziu Terraform na platformie Azure: 

 [Centrum programu Terraform w domenie Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentacja usługi Terraform Azure dostawcy](http://aka.ms/terraform)  
 [Źródłowy dostawca Terraform Azure](http://aka.ms/tfgit)  
 [Moduły Terraform Azure](http://aka.ms/tfmodules)