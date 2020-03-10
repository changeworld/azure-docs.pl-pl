---
title: Szybki Start — Instalowanie i Konfigurowanie Terraform w celu udostępniania zasobów platformy Azure
description: W tym quicstart zainstalujesz i skonfigurujesz Terraform do tworzenia zasobów platformy Azure
keywords: Konfiguracja instalacji usługi Azure DevOps Terraform
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943502"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Szybki Start: Instalowanie i Konfigurowanie Terraform w celu udostępniania zasobów platformy Azure
 
Terraform zapewnia łatwy sposób definiowania, podglądu i wdrażania infrastruktury chmurowej przy użyciu [prostego języka tworzenia szablonów](https://www.terraform.io/docs/configuration/syntax.html). W tym artykule opisano kroki niezbędne do udostępnienia zasobów na platformie Azure za pomocą Terraform.

Aby dowiedzieć się więcej na temat używania Terraform z platformą Azure, odwiedź [centrum Terraform](/azure/terraform).
> [!NOTE]
> Aby uzyskać pomoc techniczną Terraform, skontaktuj się z Terraform bezpośrednio przy użyciu jednego z kanałów społeczności:
>
>    * [Sekcja Terraform](https://discuss.hashicorp.com/c/terraform-core) portalu Community zawiera pytania, przypadki użycia i przydatne wzorce.
>
>    * Pytania dotyczące dostawcy można znaleźć w sekcji [dostawcy Terraform](https://discuss.hashicorp.com/c/terraform-providers) w portalu społeczności.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Terraform jest instalowany domyślnie w [Cloud Shell](/azure/terraform/terraform-cloud-shell). Jeśli zdecydujesz się zainstalować usługę Terraform lokalnie, wykonaj następny krok, w przeciwnym razie Kontynuuj [Konfigurowanie dostępu Terraform do platformy Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Zainstaluj Terraform

Aby zainstalować Terraform, [Pobierz](https://www.terraform.io/downloads.html) odpowiedni pakiet dla systemu operacyjnego do oddzielnego katalogu instalacyjnego. Pobieranie zawiera pojedynczy plik wykonywalny, dla którego należy również zdefiniować ścieżkę globalną. Aby uzyskać instrukcje dotyczące sposobu ustawiania ścieżki w systemach Linux i Mac, przejdź do [tej strony sieci Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Aby uzyskać instrukcje dotyczące sposobu ustawiania ścieżki w systemie Windows, przejdź do [tej strony sieci Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Sprawdź konfigurację ścieżki przy użyciu polecenia `terraform`. Zostanie wyświetlona lista dostępnych opcji Terraform, jak w następujących przykładowych danych wyjściowych:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Konfigurowanie dostępu Terraform do platformy Azure

Aby włączyć Terraform do udostępniania zasobów na platformie Azure, Utwórz jednostkę [usługi Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). Nazwa główna usługi przyznaje skryptom Terraform obsługę zasobów w ramach subskrypcji platformy Azure.

Jeśli masz wiele subskrypcji platformy Azure, najpierw Zbadaj swoje konto za pomocą opcji [AZ Account List](/cli/azure/account#az-account-list) , aby uzyskać listę identyfikatorów subskrypcji i wartości identyfikatorów dzierżawców:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Aby skorzystać z wybranej subskrypcji, ustaw dla tej sesji subskrypcję [AZ Account Set](/cli/azure/account#az-account-set). Ustaw zmienną środowiskową `SUBSCRIPTION_ID` na przechowywanie wartości zwracanego pola `id` z subskrypcji, której chcesz użyć:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Teraz można utworzyć jednostkę usługi do użycia z usługą Terraform. Użyj [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)i ustaw *zakres* na subskrypcję w następujący sposób:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

`appId`, `password`, `sp_name`i `tenant` są zwracane. Zanotuj `appId` i `password`.

## <a name="configure-terraform-environment-variables"></a>Konfigurowanie zmiennych środowiskowych Terraform

Aby skonfigurować Terraform do korzystania z nazwy głównej usługi Azure AD, ustaw następujące zmienne środowiskowe, które są następnie używane przez [moduły Terraform platformy Azure](https://registry.terraform.io/modules/Azure). Możesz również ustawić środowisko, jeśli pracujesz z chmurą platformy Azure inną niż usługa Azure Public.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Aby ustawić te zmienne, można użyć następującego przykładowego skryptu powłoki:

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

## <a name="run-a-sample-script"></a>Uruchamianie przykładowego skryptu

Utwórz plik `test.tf` w pustym katalogu i wklej go w poniższym skrypcie.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Zapisz plik, a następnie zainicjuj wdrożenie Terraform. Ten krok umożliwia pobranie modułów platformy Azure wymaganych do utworzenia grupy zasobów platformy Azure.

```bash
terraform init
```

Dane wyjściowe są podobne do poniższego przykładu:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Możesz wyświetlić podgląd akcji, które mają zostać wykonane przez skrypt Terraform z `terraform plan`. Gdy wszystko jest gotowe do utworzenia grupy zasobów, należy zastosować plan Terraform w następujący sposób:

```bash
terraform apply
```

Dane wyjściowe są podobne do poniższego przykładu:

```console
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

## <a name="next-steps"></a>Następne kroki

W tym artykule zainstalowano Terraform lub użyto Cloud Shell do skonfigurowania poświadczeń platformy Azure i rozpoczęcia tworzenia zasobów w ramach subskrypcji platformy Azure. Aby utworzyć pełniejsze wdrożenie Terraform na platformie Azure, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Tworzenie maszyny wirtualnej platformy Azure przy użyciu usługi Terraform](terraform-create-complete-vm.md)