---
title: Tworzenie sieci szprychy za pomocą programu Terraform na platformie Azure
description: Dowiedz się, jak zaimplementować satelity dwie sieci wirtualne połączone z koncentratorem w topologii piasty i szprych
services: terraform
ms.service: azure
keywords: terraform, gwiazdy, sieci, hybrydowych sieci, metodyki devops, maszyna wirtualna, azure, komunikacja równorzędna sieci wirtualnych, szprychy, piasty i szprych
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 9cce809401a26eb2b45b11303afcd4818a1f950b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884538"
---
# <a name="tutorial-create-a-spoke-virtual-network-with-terraform-in-azure"></a>Samouczek: Tworzenie sieci wirtualnej szprychy za pomocą programu Terraform na platformie Azure

W tym samouczku implementuje są dwie sieci oddzielny szprychy do zademonstrowania rozdzielenie obciążenia. Sieci udostępnianie wspólnych zasobów przy użyciu sieci wirtualnej koncentratora. Szprychy mogą być używane do izolowania obciążeń w ich własnych sieciach wirtualnych zarządzanych oddzielnie od innych szprych. Każde obciążenie może zawierać wiele warstw z wieloma podsieciami połączonymi za pośrednictwem modułów równoważenia obciążenia platformy Azure.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Użycie HCL (HashiCorp Language) w celu wdrożenia sieci wirtualne będące Szprychami w topologii piasty i szprych
> * Program Terraform służy do tworzenia maszyn wirtualnych w sieciach gwiazdy
> * Program Terraform służy do ustanawiania komunikacja równorzędna sieci wirtualnych przy użyciu sieci Centrum

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz koncentrator i topologię sieci hybrydowej za pomocą programu Terraform na platformie Azure typu gwiazda](./terraform-hub-spoke-introduction.md).
1. [Tworzenie sieci wirtualnej w środowisku lokalnym za pomocą programu Terraform na platformie Azure](./terraform-hub-spoke-on-prem.md).
1. [Tworzenie sieci wirtualnej koncentratora za pomocą programu Terraform na platformie Azure](./terraform-hub-spoke-hub-network.md).
1. [Utwórz urządzenie w sieci wirtualnej koncentratora za pomocą programu Terraform na platformie Azure](./terraform-hub-spoke-hub-nva.md).

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów

Dwa skrypty szprychy są tworzone w tej sekcji. Każdy skrypt definiuje szprychy sieci wirtualnej i maszyny wirtualnej dla obciążenia. Zostanie utworzona wirtualnej sieci równorzędnej z Centrum szprychą.

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

## <a name="declare-the-two-spoke-networks"></a>Zadeklaruj sieci dwóch gwiazdy

1. W usłudze Cloud Shell, otwórz nowy plik o nazwie `spoke1.tf`.

    ```bash
    code spoke1.tf
    ```

1. Wklej następujący kod do edytora:

    ```JSON
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

1. Zapisz plik i zamknij Edytor.

1. Utwórz nowy plik o nazwie `spoke2.tf`.

      ```bash
      code spoke2.tf
      ```
    
1. Wklej następujący kod do edytora:
    
    ```JSON
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
     
1. Zapisz plik i zamknij Edytor.
  
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Sprawdź poprawność gwiazdy sieci przy użyciu programu Terraform na platformie Azure](./terraform-hub-spoke-validation.md)