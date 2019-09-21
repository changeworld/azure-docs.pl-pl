---
title: Tworzenie zestawu skalowania maszyn wirtualnych platformy Azure przy użyciu narzędzia Terraform
description: Samouczek dotyczący konfigurowania i wersjonowania zestawu skalowania maszyn wirtualnych platformy Azure wraz z siecią wirtualną i zarządzanymi dołączonymi dyskami przy użyciu narzędzia Terraform
services: terraform
ms.service: azure
keywords: terraform, devops, virtual machine, Azure, scale set, network, storage, modules
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: a6bc0879d07cadc6c5b0b1a21b11b3075ec69719
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169880"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych platformy Azure przy użyciu narzędzia Terraform

[Zestawy skalowania maszyn wirtualnych platformy Azure](/azure/virtual-machine-scale-sets) pozwalają utworzyć grupę identycznych maszyn wirtualnych o zrównoważonym obciążeniu, w której liczba wystąpień maszyn wirtualnych może się automatycznie zwiększać lub zmniejszać w odpowiedzi na zapotrzebowanie lub zdefiniowany harmonogram, oraz zarządzać nią.

W tym samouczku wyjaśniono, jak za pomocą usługi [Azure Cloud Shell](/azure/cloud-shell/overview) wykonać następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie wdrożenia narzędzia Terraform
> * Używanie zmiennych i danych wyjściowych dla wdrożenia programu Terraform
> * Tworzenie i wdrażanie infrastruktury sieci
> * Tworzenie i wdrażanie zestawu skalowania maszyn wirtualnych i dołączanie go do sieci
> * Tworzenie i wdrażanie rampy w celu nawiązywania połączeń z maszynami wirtualnymi za pośrednictwem protokołu SSH

> [!NOTE]
> Najnowsza wersja plików konfiguracji narzędzia Terraform używanych w tym artykule znajduje się w [repozytorium Awesome Terraform w witrynie GitHub](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Zainstaluj narzędzie Terraform**: postępuj zgodnie ze wskazówkami w artykule [Terraform and configure access to Azure (Terraform i konfigurowanie dostępu do platformy Azure)](/azure/virtual-machines/linux/terraform-install-configure).

- **Utwórz parę kluczy SSH**: jeśli jeszcze nie masz pary kluczy SSH, postępuj zgodnie z instrukcjami w artykule [How to create and use an SSH public and private key pair for Linux VMs in Azure (Jak utworzyć parę publicznych i prywatnych kluczy SSH dla maszyn wirtualnych z systemem Linux i używać ich)](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Otwórz usługę [Azure Cloud Shell](/azure/cloud-shell/overview). Jeśli środowisko nie zostało wybrane wcześniej, wybierz pozycję **Bash** jako swoje środowisko.

    ![Wiersz polecenia usługi Cloud Shell](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Zmień katalog na `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Utwórz katalog o nazwie `vmss`.

    ```bash
    mkdir vmss
    ```

1. Zmień katalog na nowy:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Tworzenie plików definicji zmiennych
W tej sekcji zdefiniujesz zmienne, za pomocą których dostosujesz zasoby utworzone w narzędziu Terraform.

W usłudze Azure Cloud Shell wykonaj następujące czynności:

1. Utwórz plik o nazwie `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Przejdź do trybu wstawiania, naciskając klawisz I.

1. Wklej następujący kod do edytora:

   ```hcl
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

1. Opuść tryb wstawiania, naciskając klawisz Esc.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Tworzenie pliku definicji danych wyjściowych
W tej sekcji utworzysz plik opisujący dane wyjściowe po wdrożeniu.

W usłudze Azure Cloud Shell wykonaj następujące czynności:

1. Utwórz plik o nazwie `output.tf`.

    ```bash
    vi output.tf
    ```

1. Przejdź do trybu wstawiania, naciskając klawisz I.

1. Wklej do edytora poniższy kod, aby uwidocznić w pełni kwalifikowaną nazwę domeny (FQDN) dla maszyn wirtualnych.
   :

   ```hcl
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
   ```

1. Opuść tryb wstawiania, naciskając klawisz Esc.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definiowanie infrastruktury sieci w szablonie
W tej sekcji utworzysz w nowej grupie zasobów platformy Azure następującą infrastrukturę sieci:

  - Jedna sieć wirtualna (VNET) z przestrzenią adresową 10.0.0.0/16
  - Jedna podsieć z przestrzenią adresową 10.0.2.0/24
  - Dwa publiczne adresy IP: jeden używany przez moduł równoważenia obciążenia zestawu skalowania maszyn wirtualnych, a drugi służący do nawiązywania połączenia z rampą SSH

W usłudze Azure Cloud Shell wykonaj następujące czynności:

1. Utwórz plik o nazwie `vmss.tf` w celu opisania infrastruktury zestawu skalowania maszyn wirtualnych.

    ```bash
    vi vmss.tf
    ```

1. Przejdź do trybu wstawiania, naciskając klawisz I.

1. Na końcu pliku wklej poniższy kod, aby uwidocznić w pełni kwalifikowaną nazwę domeny (FQDN) dla maszyn wirtualnych.

   ```hcl
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
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}"
    tags                         = "${var.tags}"
   }
   ```

1. Opuść tryb wstawiania, naciskając klawisz Esc.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

   ```bash
   :wq
   ```

## <a name="provision-the-network-infrastructure"></a>Aprowizowanie infrastruktury sieci
Używając usługi Azure Cloud Shell z katalogu, w którym zostały utworzone pliki konfiguracji (.tf), wykonaj następujące kroki:

1. Zainicjuj narzędzie Terraform.

   ```bash
   terraform init
   ```

1. Uruchom poniższe polecenie, aby wdrożyć zdefiniowaną infrastrukturę na platformie Azure.

   ```bash
   terraform apply
   ```

   Narzędzie Terraform wyświetli monit o podanie wartości zmiennej „location”, ponieważ zmienna **location** jest zdefiniowana w pliku `variables.tf`, ale nigdy nie została ustawiona. Możesz wprowadzić dowolną prawidłową lokalizację, np. „Zachodnie stany USA”, a następnie nacisnąć klawisz Enter. (Wokół każdej wartości zawierającej spacje użyj nawiasów).

1. Narzędzie Terraform wyświetli dane wyjściowe zgodnie z definicją w pliku `output.tf`. Jak pokazano na poniższym zrzucie ekranu, nazwa FQDN przyjmuje postać &lt;id>.&lt;location>.cloudapp.azure.com. Wartość id jest wartością obliczoną, a location to wartość podana podczas uruchamiania narzędzia Terraform.

   ![W pełni kwalifikowana nazwa domeny zestawu skalowania maszyn wirtualnych dla publicznego adresu IP](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Z menu głównego w witrynie Azure Portal wybierz pozycję **Grupy zasobów**.

1. Na karcie **Grupy zasobów** wybierz pozycję **myResourceGroup**, aby wyświetlić zasoby, które zostały utworzone przez narzędzie Terraform.
   ![Zasoby sieciowe zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Dodawanie zestawu skalowania maszyn wirtualnych

W tej sekcji nauczysz się, jak dodać do szablonu następujące zasoby:

- Moduł równoważenia obciążenia platformy Azure oraz reguły obsługi aplikacji i dołączania jej do publicznego adresu IP skonfigurowanego wcześniej w tym artykule
- Pula adresów zaplecza platformy Azure i przypisywanie jej do modułu równoważenia obciążenia
- Port sondy kondycji używany przez aplikację i skonfigurowany w module równoważenia obciążenia
- Zestaw skalowania maszyn wirtualnych stojący za modułem równoważenia obciążenia, który jest uruchamiany w sieci wirtualnej wdrożonej we wcześniejszej części tego artykułu
- Serwer [nginx](https://nginx.org/) na węzłach skalowania maszyny wirtualnej korzystający z pakietu [cloud-init](https://cloudinit.readthedocs.io/en/latest/).

W usłudze Cloud Shell wykonaj następujące kroki:

1. Otwórz plik konfiguracji `vmss.tf`.

   ```bash
   vi vmss.tf
   ```

1. Przejdź na koniec pliku, a następnie przejdź do trybu dołączania, naciskając klawisz A.

1. Na końcu pliku wklej następujący kod:

   ```hcl
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
        primary = true
      }
    }

    tags = "${var.tags}"
   }
   ```

1. Opuść tryb wstawiania, naciskając klawisz Esc.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Utwórz plik o nazwie `web.conf`, który będzie pełnił rolę konfiguracji pakietu cloud-init dla maszyn wirtualnych będących częścią zestawu skalowania.

    ```bash
    vi web.conf
    ```

1. Przejdź do trybu wstawiania, naciskając klawisz I.

1. Wklej następujący kod do edytora:

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Opuść tryb wstawiania, naciskając klawisz Esc.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

     ```bash
     :wq
     ```

1. Otwórz plik konfiguracji `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Przejdź na koniec pliku, a następnie przejdź do trybu dołączania, naciskając klawisz A.

1. Dostosuj wdrożenie, wklejając na końcu pliku następujący kod:

    ```hcl
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

1. Opuść tryb wstawiania, naciskając klawisz Esc.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

     ```bash
     :wq
     ```

1. Utwórz plan narzędzia Terraform, aby zwizualizować wdrożenie zestawu skalowania maszyn wirtualnych. (Musisz określić wybrane przez siebie hasło, a także lokalizację swoich zasobów).

    ```bash
    terraform plan
    ```

    Dane wyjściowe polecenia powinny wyglądać podobnie jak na poniższym zrzucie ekranu:

    ![Dane wyjściowe z utworzenia zestawu skalowania maszyn wirtualnych](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Wdróż nowe zasoby na platformie Azure.

    ```bash
    terraform apply
    ```

    Dane wyjściowe polecenia powinny wyglądać podobnie jak na poniższym zrzucie ekranu:

    ![Grupa zasobów zestawu skalowania maszyn wirtualnych narzędzia Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Otwórz przeglądarkę i połącz się z nazwą FQDN, która została zwrócona przez polecenie.

    ![Wyniki przejścia do w pełni kwalifikowanej nazwy domeny](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Dodawanie rampy SSH
*Rampa* SSH to pojedynczy serwer, przez który „przeskakujesz”, aby uzyskać dostęp do innych serwerów w sieci. W tym kroku skonfigurujesz następujące zasoby:

- Interfejs sieciowy (rampa) podłączony do tej samej podsieci co zestaw skalowania maszyn wirtualnych.

- Maszyna wirtualna połączona z tym interfejsem sieciowym. „Rampa” jest dostępna zdalnie. Po nawiązaniu połączenia możesz za pomocą protokołu SSH połączyć się z dowolną maszyną wirtualną w zestawie skalowania.

1. Otwórz plik konfiguracji `vmss.tf`.

   ```bash
   vi vmss.tf
   ```

1. Przejdź na koniec pliku, a następnie przejdź do trybu dołączania, naciskając klawisz A.

1. Na końcu pliku wklej następujący kod:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    allocation_method = "Static"
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

1. Otwórz plik konfiguracji `output.tf`.

   ```bash
   vi output.tf
   ```

1. Przejdź na koniec pliku, a następnie przejdź do trybu dołączania, naciskając klawisz A.

1. Na końcu pliku wklej następujący kod, aby po ukończeniu wdrożenia wyświetlić nazwę hosta rampy:

   ```hcl
   output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
   }
   ```

1. Opuść tryb wstawiania, naciskając klawisz Esc.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Wdróż rampę.

   ```bash
   terraform apply
   ```

Po zakończeniu wdrożenia zawartość grupy zasobów powinna przypominać tę, którą pokazano na poniższym zrzucie ekranu:

![Grupa zasobów zestawu skalowania maszyn wirtualnych narzędzia Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Możliwość logowania się przy użyciu hasła na rampie i we wdrożonym zestawie skalowania maszyn wirtualnych jest wyłączona. Aby uzyskać dostęp do maszyn wirtualnych, zaloguj się za pomocą protokołu SSH.

## <a name="environment-cleanup"></a>Czyszczenie środowiska

Aby usunąć zasoby narzędzia Terraform, które zostały utworzone w ramach tego samouczka, wprowadź w usłudze Cloud Shell następujące polecenie:

```bash
terraform destroy
```

Proces niszczenia może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób tworzenia zestawu skalowania maszyn wirtualnych platformy Azure za pomocą narzędzia Terraform. Poniżej przedstawiono kilka dodatkowych zasobów zawierających więcej informacji na temat narzędzia Terraform na platformie Azure:

[Centrum narzędzia Terraform w witrynie Microsoft.com](https://docs.microsoft.com/azure/terraform/)
[Terraform — dokumentacja dostawcy platformy Azure](https://aka.ms/terraform)
[Terraform — źródło dostawcy platformy Azure](https://aka.ms/tfgit)
[Terraform — moduły platformy Azure](https://aka.ms/tfmodules)
