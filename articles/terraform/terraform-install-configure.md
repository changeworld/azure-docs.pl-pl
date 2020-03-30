---
title: Szybki start — instalowanie i konfigurowanie programu Terraform w celu aprowizowania zasobów platformy Azure
description: W tym quicstart, zainstalować i skonfigurować Terraform do tworzenia zasobów platformy Azure
keywords: azure devops terraform zainstalować skonfigurować
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78943502"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Szybki start: instalowanie i konfigurowanie programu Terraform w celu aprowizowania zasobów platformy Azure
 
Terraform zapewnia łatwy sposób definiowania, podglądu i wdrażania infrastruktury chmury przy użyciu [prostego języka tworzenia szablonów.](https://www.terraform.io/docs/configuration/syntax.html) W tym artykule opisano kroki niezbędne do korzystania z terraform do inicjowania obsługi administracyjnej zasobów na platformie Azure.

Aby dowiedzieć się więcej o korzystaniu z platformy Terraform z platformą Azure, odwiedź [Centrum Terraform.](/azure/terraform)
> [!NOTE]
> Aby uzyskać wsparcie specyficzne dla terraform, skontaktuj się bezpośrednio z Terraform, korzystając z jednego z kanałów społeczności:
>
>    * [Sekcja Terraform](https://discuss.hashicorp.com/c/terraform-core) w portalu społeczności zawiera pytania, przypadki użycia i przydatne wzorce.
>
>    * Pytania związane z dostawcą można znaleźć w sekcji [Dostawcy Terraform](https://discuss.hashicorp.com/c/terraform-providers) w portalu społecznościowym.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Terraform jest instalowany domyślnie w [Cloud Shell](/azure/terraform/terraform-cloud-shell). Jeśli zdecydujesz się zainstalować Terraform lokalnie, wykonaj następny krok, w przeciwnym razie kontynuuj [konfigurowanie dostępu Terraform do platformy Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Instalowanie programu Terraform

Aby zainstalować program Terraform, [pobierz](https://www.terraform.io/downloads.html) odpowiedni pakiet dla swojego systemu operacyjnego do oddzielnego katalogu instalacji. Plik do pobrania zawiera pojedynczy plik wykonywalny, dla którego należy również zdefiniować ścieżkę globalną. Aby uzyskać instrukcje dotyczące ustawiania ścieżki na Linuksie i Macu, przejdź do [tej strony internetowej](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Aby uzyskać instrukcje dotyczące ustawiania ścieżki w systemie Windows, przejdź do [tej strony sieci Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Sprawdź konfigurację ścieżki `terraform` za pomocą polecenia. Wyświetlana jest lista dostępnych opcji Terraform, jak w poniższym przykładzie:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Konfigurowanie dostępu terraform do platformy Azure

Aby włączyć program Terraform do aprowizowania zasobów na platformie Azure, utwórz [jednostkę usługi Azure AD.](/cli/azure/create-an-azure-service-principal-azure-cli) Jednostka usługi udziela skryptów Terraform do aprowizowania zasobów w ramach subskrypcji platformy Azure.

Jeśli masz wiele subskrypcji platformy Azure, najpierw zapytaj swoje konto za pomocą [listy kont AZ,](/cli/azure/account#az-account-list) aby uzyskać listę identyfikatorów subskrypcji i wartości identyfikatorów dzierżawy:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Aby użyć wybranej subskrypcji, ustaw subskrypcję dla tej sesji z [zestawem kont az](/cli/azure/account#az-account-set). Ustaw `SUBSCRIPTION_ID` zmienną środowiskową tak, `id` aby przytrzymywała wartość zwróconego pola z subskrypcji, której chcesz użyć:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Teraz można utworzyć jednostkę usługi do użytku z Terraform. Użyj [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)i ustaw *zakres* subskrypcji w następujący sposób:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Twoje `appId` `password`, `sp_name`, `tenant` i są zwracane. Zanotuj `appId` i `password`.

## <a name="configure-terraform-environment-variables"></a>Konfigurowanie zmiennych środowiskowych Terraform

Aby skonfigurować program Terraform do używania jednostki usługi Azure AD, należy ustawić następujące zmienne środowiskowe, które są następnie używane przez [moduły usługi Azure Terraform.](https://registry.terraform.io/modules/Azure) Można również ustawić środowisko, jeśli pracuje z chmury platformy Azure innych niż azure publicznych.

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

Utwórz `test.tf` plik w pustym katalogu i wklej w poniższym skrypcie.

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

Zapisz plik, a następnie zainiszczanie wdrożenia Terraform. Ten krok pobiera moduły platformy Azure wymagane do utworzenia grupy zasobów platformy Azure.

```bash
terraform init
```

Dane wyjściowe są podobne do poniższego przykładu:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Można wyświetlić podgląd akcji, które mają `terraform plan`zostać wykonane przez skrypt Terraform za pomocą programu . Gdy będzie gotowy do utworzenia grupy zasobów, zastosuj plan Terraform w następujący sposób:

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

W tym artykule zainstalowano terraform lub użyto powłoki chmury do skonfigurowania poświadczeń platformy Azure i rozpoczęcia tworzenia zasobów w ramach subskrypcji platformy Azure. Aby utworzyć pełniejsze wdrożenie terraform na platformie Azure, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Tworzenie maszyny wirtualnej platformy Azure za pomocą terraform](terraform-create-complete-vm.md)