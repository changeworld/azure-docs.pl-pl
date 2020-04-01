---
title: Samouczek — tworzenie klastra maszyn wirtualnych platformy Azure za pomocą terraform i hcl
description: W tym samouczku używasz Terraform i HCL do tworzenia klastra maszyn wirtualnych systemu Linux z modułem równoważenia obciążenia na platformie Azure
keywords: azure devops terraform vm wirtualny klaster maszyny
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ae1b8eac15309ff27297d9472e70d32e68acaaac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945269"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Samouczek: Tworzenie klastra maszyn wirtualnych platformy Azure za pomocą terraform i hcl

W tym samouczku zobaczysz, jak utworzyć mały klaster obliczeniowy przy użyciu [funkcji HCL](https://www.terraform.io/docs/configuration/syntax.html). 

Dowiesz się, jak wykonać następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie uwierzytelniania platformy Azure.
> * Utwórz plik konfiguracyjny Terraform.
> * Użyj pliku konfiguracyjnego Terraform, aby utworzyć moduł równoważenia obciążenia.
> * Użyj pliku konfiguracji Terraform, aby wdrożyć dwie maszyny wirtualne z systemem Linux w zestawie dostępności.
> * Zainicjuj narzędzie Terraform.
> * Utwórz plan wykonania Terraform.
> * Zastosuj plan wykonania Terraform, aby utworzyć zasoby platformy Azure.

## <a name="1-set-up-azure-authentication"></a>1. Konfigurowanie uwierzytelniania platformy Azure

> [!NOTE]
> Jeśli [używasz zmiennych środowiskowych programu Terraform](terraform-install-configure.md) lub uruchamiasz ten samouczek w usłudze [Azure Cloud Shell](terraform-cloud-shell.md), pomiń tę sekcję.

W tej sekcji wygenerujesz jednostkę usługi platformy Azure i dwa pliki konfiguracji programu Terraform zawierające poświadczenia z jednostki usługi.

1. [Skonfigurowanie jednostki usługi Azure AD](terraform-install-configure.md#set-up-terraform-access-to-azure) umożliwia programowi Terraform aprowizowanie zasobów na platformie Azure. Podczas tworzenia jednostki zanotuj wartości identyfikatora subskrypcji, dzierżawy, identyfikatora aplikacji i hasła.

2. Otwórz wiersz polecenia.

3. Utwórz pusty katalog, w którym będą przechowywane pliki programu Terraform.

4. Utwórz nowy plik przechowujący deklaracje zmiennych. Nazwa pliku może być dowolna, a rozszerzenie to `.tf`.

5. Skopiuj następujący kod do pliku deklaracji zmiennych:

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      version = "~>1.40"
     
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Utwórz nowy plik, który będzie zawierać wartości zmiennych programu Terraform. Często nazywa się plik `terraform.tfvars` zmiennej Terraform, ponieważ Terraform automatycznie `terraform.tfvars` ładuje dowolny `*.auto.tfvars`plik o nazwie (lub podąża za wzorcem), jeśli znajduje się w bieżącym katalogu. 

7. Skopiuj następujący kod do pliku zmiennych. Upewnij się, że zastąpiono symbole zastępcze w następujący sposób: dla `subscription_id` użyto identyfikatora subskrypcji platformy określonego przy uruchamianiu polecenia `az account set`. Dla symbolu zastępczego `tenant_id` użyto wartości `tenant` zwróconej przez polecenie `az ad sp create-for-rbac`. Dla symbolu zastępczego `client_id` użyto wartości `appId` zwróconej przez polecenie `az ad sp create-for-rbac`. Dla symbolu zastępczego `client_secret` użyto wartości `password` zwróconej przez polecenie `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Tworzenie pliku konfiguracyjnego Terraform

W tej sekcji utworzysz plik zawierający definicje zasobów dla infrastruktury.

1. Utwórz nowy plik o nazwie `main.tf`. 

2. Skopiuj następujące przykładowe definicje zasobów do nowo utworzonego pliku `main.tf`: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
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
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. Inicjowanie terraform 

[Polecenie terraform init](https://www.terraform.io/docs/commands/init.html) służy do inicjowania katalogu, który zawiera pliki konfiguracji programu Terraform utworzone w poprzednich sekcjach. Dobrą praktyką jest zawsze uruchamiać `terraform init` polecenie po napisaniu nowej konfiguracji Terraform. 

> [!TIP]
> Polecenie `terraform init` jest idempotentne, co oznacza, że można je uruchomić wielokrotnie, uzyskując ten sam wynik. Dlatego jeśli pracujesz w środowisku zespołowym i sądzisz, że pliki konfiguracji mogły zostać zmienione, zawsze dobrym pomysłem jest wywołanie polecenia `terraform init` przed wykonaniem lub zastosowaniem planu.

Aby zainicjować program Terraform, uruchom następujące polecenie:

  ```bash
  terraform init
  ```

  ![Inicjowanie programu Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Tworzenie planu wykonania Terraform

[Polecenie terraform plan](https://www.terraform.io/docs/commands/plan.html) służy do tworzenia planu wykonania. Aby wygenerować plan wykonania, program Terraform agreguje wszystkie pliki `.tf` w bieżącym katalogu. 

[Parametr -out](https://www.terraform.io/docs/commands/plan.html#out-path) zapisuje plan wykonania w pliku wyjściowym. Ta funkcja rozwiązuje problemy współbieżności typowe w środowiskach wielowykładowych. Jednym z takich problemów rozwiązanych przez plik wyjściowy jest następujący scenariusz:

1. Dev 1 tworzy plik konfiguracyjny.
1. Dev 2 modyfikuje plik konfiguracyjny.
1. Dev 1 stosuje (uruchamia) plik konfiguracyjny.
1. Dev 1 uzyskuje nieoczekiwane wyniki nie wiedząc, że Dev 2 zmodyfikowano konfigurację.

Dev 1 określający plik wyjściowy zapobiega wpływowi dev 2 na dev 1. 

Jeśli nie musisz zapisywać planu wykonania, uruchom następujące polecenie:

  ```bash
  terraform plan
  ```

Jeśli chcesz zapisać plan wykonania, uruchom następujące polecenie. Zastąp symbole zastępcze odpowiednimi wartościami dla danego środowiska.

  ```bash
  terraform plan -out=<path>
  ```

Innym przydatnym parametrem jest [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Domyślnie Terraform próbował znaleźć plik zmiennych w następujący sposób:
- Plik o nazwie`terraform.tfvars`
- Plik o nazwie przy użyciu następującego wzorca:`*.auto.tfvars`

Jednak plik zmiennych nie musi być zgodny z jedną z dwóch poprzednich konwencji. W takim przypadku określ nazwę pliku `-var-file` zmiennych za pomocą parametru, w którym nazwa pliku zmiennej nie zawiera rozszerzenia. Poniższy przykład ilustruje ten punkt:

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform określa akcje niezbędne do osiągnięcia stanu określonego w pliku konfiguracyjnym.

![Tworzenie planu wykonania programu Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Stosowanie planu wykonania Terraform

Ostatnim krokiem tego samouczka jest użycie [polecenia terraform apply](https://www.terraform.io/docs/commands/apply.html) w celu zastosowania zbioru akcji wygenerowanego przez polecenie `terraform plan`.

Jeśli chcesz zastosować najnowszy plan wykonania, uruchom następujące polecenie:

  ```bash
  terraform apply
  ```

Jeśli chcesz zastosować wcześniej zapisany plan wykonania, uruchom następujące polecenie. Zastąp symbole zastępcze odpowiednimi wartościami dla danego środowiska:

  ```bash
  terraform apply <path>
  ```

![Stosowanie planu wykonania programu Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Tworzenie zestawu skalowania maszyny wirtualnej platformy Azure przy użyciu terraform](terraform-create-vm-scaleset-network-disks-hcl.md)