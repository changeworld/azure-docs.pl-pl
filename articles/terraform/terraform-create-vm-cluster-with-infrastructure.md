---
title: Tworzenie klastra maszyn wirtualnych przy użyciu programu Terraform i HCL
description: Użycie programu Terraform i języka HCL (HashiCorp Configuration Language) do utworzenia klastra maszyn wirtualnych z systemem Linux i modułem równoważenia obciążenia na platformie Azure
services: terraform
ms.service: azure
keywords: terraform, devops, virtual machine, network, modules
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/13/2017
ms.openlocfilehash: a0358859d6f806a94c529bae2eb6fa9d1ab82963
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884826"
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Tworzenie klastra maszyn wirtualnych przy użyciu programu Terraform i HCL

W tym samouczku przedstawiono tworzenie małego klastra obliczeniowego za pomocą języka [HashiCorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL). W ramach konfiguracji jest tworzony moduł równoważenia obciążenia, dwie maszyny wirtualne z systemem Linux w [zestawie dostępności](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) i wszystkie niezbędne zasoby sieciowe.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie uwierzytelniania platformy Azure
> * Tworzenie pliku konfiguracji programu Terraform
> * Inicjowanie narzędzia Terraform
> * Tworzenie planu wykonania programu Terraform
> * Zastosowanie planu wykonania programu Terraform

## <a name="1-set-up-azure-authentication"></a>1. Konfigurowanie uwierzytelniania platformy Azure

> [!NOTE]
> Jeśli [używasz zmiennych środowiskowych programu Terraform](/azure/virtual-machines/linux/terraform-install-configure) lub uruchamiasz ten samouczek w usłudze [Azure Cloud Shell](terraform-cloud-shell.md), pomiń tę sekcję.

W tej sekcji wygenerujesz jednostkę usługi platformy Azure i dwa pliki konfiguracji programu Terraform zawierające poświadczenia z jednostki usługi.

1. [Skonfigurowanie jednostki usługi Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) umożliwia programowi Terraform aprowizowanie zasobów na platformie Azure. Podczas tworzenia jednostki zanotuj wartości identyfikatora subskrypcji, dzierżawy, identyfikatora aplikacji i hasła.

2. Otwórz wiersz polecenia.

3. Utwórz pusty katalog, w którym będą przechowywane pliki programu Terraform.

4. Utwórz nowy plik przechowujący deklaracje zmiennych. Nazwa pliku może być dowolna, a rozszerzenie to `.tf`.

5. Skopiuj następujący kod do pliku deklaracji zmiennych:

   ```tf
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
   }
   ```

6. Utwórz nowy plik, który będzie zawierać wartości zmiennych programu Terraform. Typowa nazwa pliku zmiennych programu Terraform to `terraform.tfvars`, ponieważ program Terraform automatycznie ładuje każdy plik o nazwie `terraform.tfvars` (lub pasujący do wzorca `*.auto.tfvars`) znajdujący się w bieżącym katalogu. 

7. Skopiuj następujący kod do pliku zmiennych. Pamiętaj, aby zamienić symbole zastępcze w następujący sposób: Dla symbolu zastępczego `subscription_id` użyj identyfikatora subskrypcji platformy Azure określonego podczas uruchamiania polecenia `az account set`. Dla symbolu zastępczego `tenant_id` użyto wartości `tenant` zwróconej przez polecenie `az ad sp create-for-rbac`. Dla symbolu zastępczego `client_id` użyto wartości `appId` zwróconej przez polecenie `az ad sp create-for-rbac`. Dla symbolu zastępczego `client_secret` użyto wartości `password` zwróconej przez polecenie `az ad sp create-for-rbac`.

   ```tf
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Tworzenie pliku konfiguracji programu Terraform

W tej sekcji utworzysz plik zawierający definicje zasobów dla infrastruktury.

1. Utwórz nowy plik o nazwie `main.tf`. 

2. Skopiuj następujące przykładowe definicje zasobów do nowo utworzonego pliku `main.tf`: 

   ```tf
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. Inicjowanie narzędzia Terraform 

[Polecenie terraform init](https://www.terraform.io/docs/commands/init.html) służy do inicjowania katalogu, który zawiera pliki konfiguracji programu Terraform utworzone w poprzednich sekcjach. Dobrą praktyką jest uruchomienie polecenia `terraform init` zawsze po zapisaniu nowej konfiguracji programu Terraform. 

> [!TIP]
> Polecenie `terraform init` jest idempotentne, co oznacza, że można je uruchomić wielokrotnie, uzyskując ten sam wynik. Dlatego jeśli pracujesz w środowisku zespołowym i sądzisz, że pliki konfiguracji mogły zostać zmienione, zawsze dobrym pomysłem jest wywołanie polecenia `terraform init` przed wykonaniem lub zastosowaniem planu.

Aby zainicjować program Terraform, uruchom następujące polecenie:

  ```cmd
  terraform init
  ```

  ![Inicjowanie programu Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Tworzenie planu wykonania programu Terraform

[Polecenie terraform plan](https://www.terraform.io/docs/commands/plan.html) służy do tworzenia planu wykonania. Aby wygenerować plan wykonania, program Terraform agreguje wszystkie pliki `.tf` w bieżącym katalogu. 

Jeśli pracujesz w środowisku zespołowym, w którym konfiguracja mogła zostać zmieniona między momentem utworzenia planu wykonania a momentem zastosowania planu wykonania, użyj [parametru -out polecenia terraform plan](https://www.terraform.io/docs/commands/plan.html#out-path), aby zapisać plan wykonania w pliku. Jeśli pracujesz w środowisku jako pojedyncza osoba, możesz pominąć parametr `-out`.

Jeśli nazwa pliku zmiennych programu Terraform jest inna niż `terraform.tfvars` i nie jest zgodna z wzorcem z `*.auto.tfvars`, musisz określić nazwę pliku przy użyciu [parametru -var-file polecenia terraform plan](https://www.terraform.io/docs/commands/plan.html#var-file-foo) podczas uruchamiania polecenia `terraform plan`.

Podczas przetwarzania polecenia `terraform plan` program Terraform wykonuje odświeżanie i określa akcje niezbędne do osiągnięcia żądanego stanu określonego w plikach konfiguracji.

Jeśli nie potrzebujesz zapisywać planu wykonania, uruchom następujące polecenie:

  ```cmd
  terraform plan
  ```

Jeśli musisz zapisać plan wykonania, uruchom następujące polecenie (zastępując symbol zastępczy &lt;path> wymaganą ścieżką wyjściową):

  ```cmd
  terraform plan -out=<path>
  ```

![Tworzenie planu wykonania programu Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Zastosowanie planu wykonania programu Terraform

Ostatnim krokiem tego samouczka jest użycie [polecenia terraform apply](https://www.terraform.io/docs/commands/apply.html) w celu zastosowania zbioru akcji wygenerowanego przez polecenie `terraform plan`.

Jeśli chcesz zastosować najnowszy plan wykonania, uruchom następujące polecenie:

  ```cmd
  terraform apply
  ```

Jeśli chcesz zastosować wcześniej zapisany plan wykonania, uruchom następujące polecenie (zastępując symbol zastępczy &lt;path> ścieżką zawierającą zapisany plan wykonania):

  ```cmd
  terraform apply <path>
  ```

![Stosowanie planu wykonania programu Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Kolejne kroki

- Przeglądanie listy [modułów Azure Terraform](https://registry.terraform.io/modules/Azure)
- Tworzenie [zestawu skalowania maszyn wirtualnych za pomocą narzędzia Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
