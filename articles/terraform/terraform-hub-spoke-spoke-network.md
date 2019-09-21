---
title: Tworzenie sieci szprych z Terraform na platformie Azure
description: Dowiedz się, jak zaimplementować dwa szprychy sieci wirtualnych połączone z koncentratorem w topologii gwiazdy
services: terraform
ms.service: azure
keywords: Terraform, Hub i szprychy, sieci, sieci hybrydowe, DevOps, maszyna wirtualna, Azure, wirtualne sieci równorzędne, szprychy, piasta-szprycha
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 9437f43a12204c9a08e1c0da11fc737e8c026c80
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173401"
---
# <a name="tutorial-create-a-spoke-virtual-network-with-terraform-in-azure"></a>Samouczek: Tworzenie sieci wirtualnej szprych z Terraform na platformie Azure

W tym samouczku zaimplementowano dwie osobne sieci szprych w celu zaprezentowania rozdzielenia obciążeń. Sieci używają wspólnych zasobów przy użyciu sieci wirtualnej centrum. Szprychy mogą być używane do izolowania obciążeń w ich własnych sieciach wirtualnych zarządzanych oddzielnie od innych szprych. Każde obciążenie może zawierać wiele warstw z wieloma podsieciami połączonymi za pośrednictwem modułów równoważenia obciążenia platformy Azure.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Używanie HCL (HashiCorp Language) do implementowania szprych sieci wirtualnych w topologii gwiazdy
> * Tworzenie maszyn wirtualnych w sieciach szprych przy użyciu programu Terraform
> * Korzystanie z programu Terraform do nawiązywania komunikacji równorzędnej sieci wirtualnych z sieciami centralnymi

## <a name="prerequisites"></a>Wymagania wstępne

1. [Tworzenie topologii sieci hybrydowej Hub i satelity z Terraform na platformie Azure](./terraform-hub-spoke-introduction.md).
1. [Utwórz lokalną sieć wirtualną za pomocą Terraform na platformie Azure](./terraform-hub-spoke-on-prem.md).
1. [Utwórz centralną sieć wirtualną z usługą Terraform na platformie Azure](./terraform-hub-spoke-hub-network.md).
1. [Utwórz centralne urządzenie sieci wirtualnej z usługą Terraform na platformie Azure](./terraform-hub-spoke-hub-nva.md).

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów

W tej sekcji są tworzone dwa skrypty. Każdy skrypt definiuje sieć wirtualną szprychy oraz maszynę wirtualną dla obciążenia. Zostanie utworzona równorzędna Sieć wirtualna od Hub do szprychy.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Otwórz usługę [Azure Cloud Shell](/azure/cloud-shell/overview). Jeśli środowisko nie zostało wybrane wcześniej, wybierz pozycję **Bash** jako swoje środowisko.

    ![Wiersz polecenia usługi Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Zmień katalog na `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Zmień katalog na nowy:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-two-spoke-networks"></a>Zadeklaruj dwie sieci szprych

1. W Cloud Shell Otwórz nowy plik o nazwie `spoke1.tf`.

    ```bash
    code spoke1.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    locals {
      spoke1-location       = "CentralUS"
      spoke1-resource-group = "spoke1-vnet-rg"
      prefix-spoke1         = "spoke1"
    }

    resource "azurerm_resource_group" "spoke1-vnet-rg" {
      name     = "${local.spoke1-resource-group}"
      location = "${local.spoke1-location}"
    }

    resource "azurerm_virtual_network" "spoke1-vnet" {
      name                = "spoke1-vnet"
      location            = "${azurerm_resource_group.spoke1-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      address_space       = ["10.1.0.0/16"]

      tags {
        environment = "${local.prefix-spoke1 }"
      }
    }

    resource "azurerm_subnet" "spoke1-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke1-vnet.name}"
      address_prefix       = "10.1.0.64/27"
    }

    resource "azurerm_subnet" "spoke1-workload" {
      name                 = "workload"
      resource_group_name  = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke1-vnet.name}"
      address_prefix       = "10.1.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke1-hub-peer" {
      name                      = "spoke1-hub-peer"
      resource_group_name       = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.spoke1-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.hub-vnet.id}"

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet" , "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke1-nic" {
      name                 = "${local.prefix-spoke1}-nic"
      location             = "${azurerm_resource_group.spoke1-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-spoke1}"
        subnet_id                     = "${azurerm_subnet.spoke1-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "spoke1-vm" {
      name                  = "${local.prefix-spoke1}-vm"
      location              = "${azurerm_resource_group.spoke1-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.spoke1-nic.id}"]
      vm_size               = "${var.vmsize}"

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke1}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-spoke1}"
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke1-peer" {
      name                      = "hub-spoke1-peer"
      resource_group_name       = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.hub-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.spoke1-vnet.id}"
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```

1. Zapisz plik i Zamknij Edytor.

1. Utwórz nowy plik o nazwie `spoke2.tf`.

      ```bash
      code spoke2.tf
      ```
    
1. Wklej następujący kod do edytora:
    
    ```hcl
    locals {
      spoke2-location       = "CentralUS"
      spoke2-resource-group = "spoke2-vnet-rg"
      prefix-spoke2         = "spoke2"
    }

    resource "azurerm_resource_group" "spoke2-vnet-rg" {
      name     = "${local.spoke2-resource-group}"
      location = "${local.spoke2-location}"
    }

    resource "azurerm_virtual_network" "spoke2-vnet" {
      name                = "${local.prefix-spoke2}-vnet"
      location            = "${azurerm_resource_group.spoke2-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      address_space       = ["10.2.0.0/16"]

      tags {
        environment = "${local.prefix-spoke2}"
      }
    }

    resource "azurerm_subnet" "spoke2-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke2-vnet.name}"
      address_prefix       = "10.2.0.64/27"
    }

    resource "azurerm_subnet" "spoke2-workload" {
      name                 = "workload"
      resource_group_name  = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke2-vnet.name}"
      address_prefix       = "10.2.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke2-hub-peer" {
      name                      = "${local.prefix-spoke2}-hub-peer"
      resource_group_name       = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.spoke2-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.hub-vnet.id}"

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke2-nic" {
      name                 = "${local.prefix-spoke2}-nic"
      location             = "${azurerm_resource_group.spoke2-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-spoke2}"
        subnet_id                     = "${azurerm_subnet.spoke2-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = "${local.prefix-spoke2}"
      }
    }

    resource "azurerm_virtual_machine" "spoke2-vm" {
      name                  = "${local.prefix-spoke2}-vm"
      location              = "${azurerm_resource_group.spoke2-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.spoke2-nic.id}"]
      vm_size               = "${var.vmsize}"

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke2}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-spoke2}"
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke2-peer" {
      name                      = "hub-spoke2-peer"
      resource_group_name       = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.hub-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.spoke2-vnet.id}"
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```
     
1. Zapisz plik i Zamknij Edytor.
  
## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Weryfikowanie sieci gwiazdy i satelity za pomocą Terraform na platformie Azure](./terraform-hub-spoke-validation.md)