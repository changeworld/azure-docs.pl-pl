---
title: Tworzenie klastra maszyny Wirtualnej z Terraform i HCL
description: Użyj Terraform i HashiCorp konfiguracji języka (HCL) do utworzenia klastra maszyny wirtualnej systemu Linux z modułu równoważenia obciążenia na platformie Azure
keywords: terraform, devops, maszyny wirtualnej, sieci, modułów
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/13/2017
ms.author: tarcher
ms.openlocfilehash: 2435d694e6a1671a234d02f90860e5cafe98c2df
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2017
ms.locfileid: "24518804"
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Tworzenie klastra maszyny Wirtualnej z Terraform i HCL

W tym samouczku pokazano tworzenie klastra obliczeniowego małych za pomocą [HashiCorp konfiguracji języka](https://www.terraform.io/docs/configuration/syntax.html) (HCL). Konfiguracja tworzy moduł równoważenia obciążenia, dwóch maszyn wirtualnych systemu Linux w [zestawu dostępności](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)i wszystkie niezbędne zasoby sieciowe.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie uwierzytelniania platformy Azure
> * Utwórz plik konfiguracji Terraform
> * Inicjowanie Terraform
> * Tworzenie planu wykonania Terraform
> * Zastosuj Terraform plan wykonania

## <a name="1-set-up-azure-authentication"></a>1. Konfigurowanie uwierzytelniania platformy Azure

> [!NOTE]
> Jeśli użytkownik [używać zmiennych środowiskowych Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables), lub uruchomić w tym samouczku [powłoki chmury Azure](terraform-cloud-shell.md), Pomiń tę sekcję.

W tej sekcji służy do generowania nazwy głównej usługi platformy Azure i dwóch plików konfiguracyjnych Terraform zawierający poświadczenia od podmiotu zabezpieczeń.

1. [Konfigurowanie nazwy głównej usługi Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) umożliwiające Terraform do udostępniania zasobów na platformie Azure. Podczas tworzenia podmiot zabezpieczeń, zanotuj wartości Identyfikatora subskrypcji, dzierżawy, appId i hasło.

2. Otwórz wiersz polecenia.

3. Utwórz pusty katalog do przechowywania plików Terraform.

4. Utwórz nowy plik przechowujący z deklaracji zmiennych. Można określić nazwę tego pliku niczego od `.tf` rozszerzenia.

5. Skopiuj następujący kod do pliku deklaracja zmiennej:

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

6. Utwórz nowy plik, który zawiera wartości zmiennych Terraform. Często jest nazwa pliku zmiennej Terraform `terraform.tfvars` jako Terraform automatycznie ładuje dowolne pliki o nazwie `terraform.tfvars` (lub wzorzec `*.auto.tfvars`) Jeśli nie istnieje w bieżącym katalogu. 

7. Skopiuj następujący kod do pliku zmiennych. Upewnij się, że Zastąp symbole zastępcze w następujący sposób: dla `subscription_id`, identyfikator subskrypcji platformy Azure, określony podczas uruchamiania `az account set`. Aby uzyskać `tenant_id`, użyj `tenant` wartość zwracana z `az ad sp create-for-rbac`. Aby uzyskać `client_id`, użyj `appId` wartość zwracana z `az ad sp create-for-rbac`. Aby uzyskać `client_secret`, użyj `password` wartość zwracana z `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Utwórz plik konfiguracji Terraform

W tej sekcji utworzysz plik, który zawiera definicje zasobów dla infrastruktury.

1. Utwórz nowy plik o nazwie `main.tf`. 

2. Następujące przykładowe definicje zasobów do nowo utworzonej kopii `main.tf` pliku: 

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

## <a name="3-initialize-terraform"></a>3. Inicjowanie Terraform 

[Polecenia init terraform](https://www.terraform.io/docs/commands/init.html) służy do inicjowania katalogu, który zawiera pliki konfiguracji Terraform - pliki zostały utworzone z poprzednich sekcjach. Zawsze należy uruchomić `terraform init` polecenia po zapisaniu nowej konfiguracji Terraform. 

> [!TIP]
> `terraform init` Polecenie jest idempotentności, co oznacza, że może ona zostać wywołana wielokrotnie podczas produkowania takiego samego wyniku. Dlatego jeśli pracujesz w środowisku współpracy i uważasz, że pliki konfiguracji mógł ulec zmianie, jest zawsze dobrym pomysłem jest wywołać `terraform init` polecenia przed wykonaniem lub zastosowaniu planu.

Aby zainicjować Terraform, uruchom następujące polecenie:

  ```cmd
  terraform init
  ```

  ![Inicjowanie Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Tworzenie planu wykonania Terraform

[Terraform planu polecenia](https://www.terraform.io/docs/commands/plan.html) służy do tworzenia planu wykonywania. Aby wygenerować planu wykonywania, Terraform agreguje wszystkich `.tf` plików w bieżącym katalogu. 

Jeśli pracujesz w środowisku współpracy, których konfiguracja może zmieniać między czas utworzenia planu wykonania i zastosować plan wykonania, należy użyć [terraform planu polecenia-out parametru](https://www.terraform.io/docs/commands/plan.html#out-path)można zapisać planu wykonania do pliku. W przeciwnym razie, jeśli pracujesz w środowisku pojedynczego osoby, można pominąć `-out` parametru.

Jeśli nazwa pliku zmiennych Terraform nie jest `terraform.tfvars` i nie będzie zgodna z `*.auto.tfvars` wzorzec, należy określić przy użyciu nazwy pliku [parametru - var pliku polecenia planu terraform](https://www.terraform.io/docs/commands/plan.html#var-file-foo) podczas uruchamiania `terraform plan`polecenia.

Podczas przetwarzania `terraform plan` polecenia Terraform wykonuje odświeżania i określa, jakie akcje są niezbędne do osiągnięcia żądany stan określony w plikach konfiguracji.

Jeśli musisz zapisać plan wykonania, uruchom następujące polecenie:

  ```cmd
  terraform plan
  ```

Jeśli musisz zapisać plan wykonania, uruchom następujące polecenie (zastępowanie &lt;ścieżka > Ścieżka żądanego wyniku symbol zastępczy):

  ```cmd
  terraform plan -out=<path>
  ```

![Tworzenie planu wykonania Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Zastosuj Terraform plan wykonania

Ostatnim krokiem tego samouczka jest użycie [polecenie stosowania terraform](https://www.terraform.io/docs/commands/apply.html) stosować zestaw wygenerowany przez akcje `terraform plan` polecenia.

Jeśli chcesz zastosować najnowszych plan wykonania, uruchom następujące polecenie:

  ```cmd
  terraform apply
  ```

Jeśli chcesz zastosować plan wykonania wcześniej zapisany, uruchom następujące polecenie (zastępowanie &lt;ścieżka > Symbol zastępczy ścieżkę zawierającą plan wykonania zapisane):

  ```cmd
  terraform apply <path>
  ```

![Stosowanie planu wykonania Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Następne kroki

- Przejrzyj listę [modułów Azure Terraform](https://registry.terraform.io/modules/Azure)
- Utwórz [zestawu skalowania maszyn wirtualnych z Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)