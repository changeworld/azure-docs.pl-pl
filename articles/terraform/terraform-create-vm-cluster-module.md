---
title: Tworzenie klastra maszyn wirtualnych na platformie Azure za pomocą modułów Terraform
description: Dowiedz się, jak za pomocą modułów Terraform utworzyć klaster maszyn wirtualnych systemu Windows na platformie Azure
services: terraform
ms.service: azure
keywords: terraform, devops, virtual machine, network, modules
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 6279b5c9022b448aea9b33a94fc1b2b35b6d23de
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169854"
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Tworzenie klastra maszyn wirtualnych za pomocą narzędzia Terraform korzystającego z rejestru modułów

W tym artykule opisano proces tworzenia małego klastra maszyn wirtualnych za pomocą modułu Terraform [Azure Compute](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Konfigurowanie uwierzytelniania na platformie Azure
> * Tworzenie szablonu narzędzia Terraform
> * Wizualizowanie zmian za pomocą planu
> * Stosowanie konfiguracji w celu utworzenia klastra maszyn wirtualnych

Aby uzyskać więcej informacji na temat narzędzia Terraform, zobacz [dokumentację narzędzia Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Konfigurowanie uwierzytelniania na platformie Azure

> [!TIP]
> Jeśli [używasz zmiennych środowiskowych narzędzia Terraform](/azure/virtual-machines/linux/terraform-install-configure) lub uruchamiasz ten samouczek w usłudze [Azure Cloud Shell](/azure/cloud-shell/overview), pomiń ten krok.

 Zapoznaj się z artykułem [Install Terraform and configure access to Azure](/azure/virtual-machines/linux/terraform-install-configure) (Instalowanie narzędzia Terraform i konfigurowanie dostępu do platformy Azure), aby utworzyć jednostkę usługi platformy Azure. Użyj tej jednostki usługi, aby wypełnić nowy plik `azureProviderAndCreds.tf` w pustym katalogu następującym kodem:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Tworzenie szablonu

Utwórz nowy szablon narzędzia Terraform o nazwie `main.tf`, używając następującego kodu:

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Uruchom polecenie `terraform init` w Twoim katalogu konfiguracji. Użycie narzędzia Terraform w wersji co najmniej 0.10.6 spowoduje wyświetlenie następujących danych wyjściowych:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Wizualizowanie zmian za pomocą planu

Uruchom polecenie `terraform plan`, aby wyświetlić podgląd infrastruktury maszyn wirtualnych utworzonej przez szablon.

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Tworzenie maszyn wirtualnych za pomocą polecenia apply

Uruchom polecenie `terraform apply`, aby aprowizować maszyny wirtualne na platformie Azure.

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Następne kroki

- Przeglądanie listy [modułów Azure Terraform](https://registry.terraform.io/modules/Azure)
- Tworzenie [zestawu skalowania maszyn wirtualnych za pomocą narzędzia Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
