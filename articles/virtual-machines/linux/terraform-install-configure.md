---
title: Instalowanie i konfigurowanie Terraform do obsługi administracyjnej maszyn wirtualnych i innych infrastruktury na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować Terraform tworzenie zasobów Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: dada9c70eef2adb2704e276a5401509581e37538
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399172"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalowanie i konfigurowanie Terraform do obsługi administracyjnej maszyn wirtualnych i innych infrastruktury na platformie Azure
 
Terraform zapewnia prosty sposób definiowania Podgląd i wdrażania infrastruktury chmurowej przy użyciu [tworzenia szablonów prostego języka](https://www.terraform.io/docs/configuration/syntax.html). W tym artykule opisano czynności niezbędnych do użycia Terraform do udostępniania zasobów platformy Azure. 

> [!TIP]
Aby dowiedzieć się więcej o sposobie używania Terraform z platformy Azure, odwiedź stronę [Centrum Terraform](/azure/terraform). Terraform jest instalowany domyślnie w [powłoki chmury](/azure/terraform/terraform-cloud-shell). Za pomocą powłoki w chmurze, można pominąć instalacji/części tego dokumentu.

## <a name="install-terraform"></a>Zainstaluj Terraform

Aby zainstalować Terraform, [Pobierz](https://www.terraform.io/downloads.html) katalog instalacji odpowiedni dla systemu operacyjnego do oddzielnego pakietu. Pobieranie zawiera pojedynczy plik wykonywalny, dla którego należy także zdefiniować globalnego ścieżki. Aby uzyskać instrukcje dotyczące ustawiania ścieżka w systemie Linux i komputerów Mac, przejdź do [tej strony sieci Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Aby uzyskać instrukcje dotyczące ustawiania ścieżka w systemie Windows, przejdź do [tej strony sieci Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Sprawdź konfigurację ścieżki z `terraform` polecenia. Należy wyświetlić listę dostępnych opcji Terraform jako dane wyjściowe:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Konfigurowanie Terraform dostępu do platformy Azure

Skonfiguruj [nazwy głównej usługi Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli) umożliwiające Terraform do udostępniania zasobów na platformie Azure. Nazwy głównej usługi przyznaje skrypty Terraform przy użyciu poświadczeń do udostępniania zasobów w Twojej subskrypcji platformy Azure.

Istnieje kilka sposobów, aby utworzyć aplikację usługi Azure AD i usługi Azure AD podmiotu zabezpieczeń. Łatwiejsze i szybsze sposób dzisiaj jest użycie interfejsu wiersza polecenia Azure 2.0, którego [można pobrać i zainstalować w systemie Windows, Linux lub Mac](/cli/azure/install-azure-cli).

Zaloguj się do administrowania subskrypcji platformy Azure, wydając polecenie:

   `az login`

Jeśli masz wiele subskrypcji Azure, ich szczegóły są zwracane przez `az login` polecenia. Ustaw `SUBSCRIPTION_ID` zmiennej środowiskowej, aby pomieścić wartość zwracana `id` pole z subskrypcji, którego chcesz użyć. 

Ustaw subskrypcji, która ma być używana dla tej sesji.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Zapytania względem konta można uzyskać Identyfikatora i dzierżawcy wartości Identyfikatora subskrypcji.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Następnie należy utworzyć osobne funkcje poświadczeń dla Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Identyfikator aplikacji, hasło i sp_name oraz dzierżawy są zwracane. Zanotuj identyfikator appId i hasło.

Aby przetestować swoje poświadczenia, Otwórz powłokę nowy i uruchom następujące polecenie, przy użyciu wartości zwracane dla sp_name, hasło i dzierżawy:

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Skonfiguruj Terraform zmienne środowiskowe

Skonfiguruj Terraform używane Identyfikatora dzierżawy, identyfikator subskrypcji, identyfikator klienta i klienta w nazwy głównej usługi podczas tworzenia zasobów platformy Azure. Można również ustawić środowiska pracy z chmury Azure innych niż Azure publicznego. Ustaw następujące zmienne środowiskowe, które są używane automatycznie przez [Azure Terraform modułów](https://registry.terraform.io/modules/Azure).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

Ten przykładowy skrypt powłoki umożliwia ustawienie tych zmiennych:

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

Utwórz plik `test.tf` w pustych katalogów i wklej poniższy skrypt. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Zapisz plik, a następnie uruchom `terraform init`. To polecenie pobiera Azure modułami wymaganymi do utworzenia grupy zasobów platformy Azure. Zostanie wyświetlony następujący komunikat:

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Wyświetl podgląd skryptu `terraform plan`, a następnie utwórz `testResouceGroup` grupy zasobów z `terraform apply`:

```
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

Zainstalowano Terraform i skonfigurować poświadczenia platformy Azure, dzięki czemu możesz przystąpić do wdrażania infrastruktury do Twojej subskrypcji platformy Azure. Następnie został przetestowany instalację, tworząc pusty grupy zasobów platformy Azure.

> [!div class="nextstepaction"]
> [Tworzenie maszyny Wirtualnej platformy Azure z Terraform](terraform-create-complete-vm.md)

