---
title: Instalowanie i konfigurowanie programu Terraform do użytku z platformą Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować narzędzie Terraform w celu tworzenia zasobów platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/19/2018
ms.author: echuvyrov
ms.openlocfilehash: 71cf07b227a75e53119f2f35e79ccd7926b551e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60418867"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalowanie i konfigurowanie programu Terraform w celu aprowizowania maszyn wirtualnych i innych infrastruktury na platformie Azure
 
Narzędzie Terraform pozwala łatwo definiować, w wersji zapoznawczej i wdrażania infrastruktury chmury za pomocą [szablonów prostego języka](https://www.terraform.io/docs/configuration/syntax.html). W tym artykule opisano czynności niezbędnych do program Terraform służy do inicjowania obsługi administracyjnej zasobów na platformie Azure.

Aby dowiedzieć się więcej o tym, jak używać narzędzia Terraform w systemie Azure, odwiedź stronę [Centrum programu Terraform](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Narzędzie Terraform jest instalowany domyślnie w [Cloud Shell](/azure/terraform/terraform-cloud-shell). Jeśli zdecydujesz się zainstalować narzędzia Terraform lokalnie, wykonania następnego kroku, w przeciwnym razie w dalszym ciągu [Konfigurowanie dostępu programu Terraform na platformie Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Instalowanie programu Terraform

Aby zainstalować narzędzia Terraform, [Pobierz](https://www.terraform.io/downloads.html) odpowiedni pakiet dla systemu operacyjnego w oddzielnych katalog_instalacji. Pobierany jest pojedynczy plik wykonywalny, dla którego należy także zdefiniować globalnego ścieżki. Aby uzyskać instrukcje dotyczące sposobu ustawiania ścieżki w systemach Linux i Mac, przejdź do [tej strony sieci Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Aby uzyskać instrukcje na temat sposobu ustawiania ścieżki na Windows, przejdź do [tej strony sieci Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Sprawdź konfigurację ścieżki z `terraform` polecenia. Dostępne opcje programu Terraform jest wyświetlana lista, tak jak w następujących przykładowych danych wyjściowych:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Konfigurowanie dostępu programu Terraform na platformie Azure

Aby włączyć narzędzia Terraform inicjowania obsługi administracyjnej zasobów na platformie Azure, Utwórz [jednostki usługi Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). Nazwa główna usługi przyznaje skrypty narzędzia Terraform inicjowania obsługi administracyjnej zasobów w subskrypcji platformy Azure.

Jeśli masz wiele subskrypcji platformy Azure, najpierw kwerendy Twoje konto za pomocą [Pokaż konta az](/cli/azure/account#az-account-show) w celu uzyskania listy subskrypcji, identyfikator i dzierżawy wartości Identyfikatora:

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Aby użyć wybranej subskrypcji, skonfigurować subskrypcję dla tej sesji z [zestaw konta az](/cli/azure/account#az-account-set). Ustaw `SUBSCRIPTION_ID` zmiennej środowiskowej, aby przechowywać wartości zwracanej `id` pola z subskrypcji, w której chcesz użyć:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Teraz możesz utworzyć nazwę główną usługi do użycia przy użyciu programu Terraform. Użyj [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)i ustaw *zakres* do Twojej subskrypcji w następujący sposób:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Twoje `appId`, `password`, `sp_name`, i `tenant` są zwracane. Zwróć uwagę na `appId` i `password`.

## <a name="configure-terraform-environment-variables"></a>Konfigurowanie zmiennych środowiskowych narzędzia Terraform

Aby skonfigurować narzędzie Terraform w celu użyć jednostki usługi Azure AD, ustaw następujące zmienne środowiskowe, które są następnie używane przez [modułów Azure Terraform](https://registry.terraform.io/modules/Azure). Można również ustawić środowiska pracy w chmurze platformy Azure innych niż publicznej platformy Azure.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Poniższy przykładowy skrypt powłoki służy do ustawiania tych zmiennych:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Uruchom skrypt przykładowy

Utwórz plik `test.tf` pusty katalog i wklej poniższy skrypt.

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Zapisz plik, a następnie zainicjować wdrożenia programu Terraform. Ten krok spowoduje pobranie moduły platformy Azure, musisz utworzyć grupę zasobów platformy Azure.

```bash
terraform init
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Możesz wyświetlić podgląd akcji, które można wykonać za pomocą skryptu programu Terraform z `terraform plan`. Po wykonaniu tych czynności utworzyć grupę zasobów, zastosuj planu narzędzia Terraform w następujący sposób:

```bash
terraform apply
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zainstalowane narzędzie Terraform lub używasz usługi Cloud Shell do konfigurowania poświadczeń platformy Azure i rozpocząć tworzenie zasobów w subskrypcji platformy Azure. Aby utworzyć pełniejsze wdrożenia programu Terraform na platformie Azure, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Tworzenie maszyny Wirtualnej platformy Azure przy użyciu programu Terraform](terraform-create-complete-vm.md)
