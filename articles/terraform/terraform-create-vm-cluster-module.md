---
title: Użyj Terraform modułów, aby utworzyć klaster maszyny Wirtualnej na platformie Azure
description: Dowiedz się, jak utworzyć klaster maszyny wirtualnej systemu Windows na platformie Azure przy użyciu modułów Terraform
keywords: terraform, devops, maszyny wirtualnej, sieci, modułów
author: rloutlaw
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 10/19/2017
ms.custom: devops
ms.author: routlaw
ms.openlocfilehash: e33aef252413eeb243b03543f171d5f1e2385b48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29952221"
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Tworzenie klastra maszyny Wirtualnej z Terraform za pomocą rejestru modułu

W tym artykule przedstawiono tworzenie małych klastra maszyny Wirtualnej z Terraform [modułu rozwiązań usługi obliczenia Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Konfigurowanie uwierzytelniania w systemie Azure
> * Utwórz szablon Terraform
> * Wizualizuj zmiany z planem
> * Zastosuj konfigurację, aby utworzyć klaster maszyny Wirtualnej

Aby uzyskać więcej informacji o Terraform, zobacz [dokumentacji Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Konfigurowanie uwierzytelniania w systemie Azure

> [!TIP]
> Jeśli użytkownik [używać zmiennych środowiskowych Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) lub uruchomione w tym samouczku [powłoki chmury Azure](/azure/cloud-shell/overview), Pomiń ten krok.

 Przegląd [Terraform zainstalować i skonfigurować dostęp do usługi Azure](/azure/virtual-machines/linux/terraform-install-configure) można utworzyć nazwy głównej usługi platformy Azure. Użyj tej nazwy głównej usługi w celu wypełnienia nowego pliku `azureProviderAndCreds.tf` w pustego katalogu z następującym kodem:

```tf
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

Utwórz nowy szablon Terraform o nazwie `main.tf` z następującym kodem:

```tf
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

Uruchom `terraform init` w katalogu konfiguracji. Przy użyciu wersji Terraform co najmniej 0.10.6 zawiera następujące dane wyjściowe:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Wizualizuj zmiany z planem

Uruchom `terraform plan` do podglądu infrastruktury maszyny wirtualnej utworzonej przez szablon.

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Tworzenie maszyn wirtualnych z Zastosuj

Uruchom `terraform apply` do obsługi administracyjnej maszyn wirtualnych na platformie Azure.

![Zastosuj Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj listę [modułów Azure Terraform](https://registry.terraform.io/modules/Azure)
- Utwórz [zestawu skalowania maszyn wirtualnych z Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
