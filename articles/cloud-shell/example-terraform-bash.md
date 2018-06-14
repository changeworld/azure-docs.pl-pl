---
title: Wdrażanie z Terraform z Bash w chmurze Azure powłoki | Dokumentacja firmy Microsoft
description: Wdrażanie z Terraform z Bash w chmurze Azure powłoki
services: Azure
documentationcenter: ''
author: tomarcher
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: 6df6a3a5242e0a5fc5c03136e1cd20967a93487a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386524"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Wdrażanie z Terraform z Bash w chmurze Azure powłoki
W tym artykule przedstawiono tworzenie grupy zasobów z [Terraform AzureRM dostawcy](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Hashicorp Terraform](https://www.terraform.io/) to narzędzie typu open source, które zostały określone zasady interfejsów API w plikach konfiguracji deklaratywne, które mogą być udostępnione wśród członków zespołu można edytowane, przejrzeć i numerów wersji. Dostawca Microsoft AzureRM służy do interakcji z zasobami obsługiwane przez Menedżera zasobów Azure za pośrednictwem interfejsów API AzureRM. 

## <a name="automatic-authentication"></a>Automatyczne uwierzytelnianie
Terraform jest instalowany w Bash w chmurze powłoki domyślnie. Ponadto powłoki chmury uwierzytelnia automatycznie domyślne subskrypcji Azure CLI 2.0 do wdrażania zasobów w modułach Terraform Azure.

Terraform korzysta z subskrypcji Azure CLI 2.0 domyślna, która jest ustawiona. Aby zaktualizować domyślne subskrypcje, uruchom polecenie:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Przewodnik
### <a name="launch-bash-in-cloud-shell"></a>Uruchamianie Bash w powłoce chmury
1. Uruchom powłokę chmury z preferowanych lokalizacji
2. Sprawdź, czy ustawiono preferowanych subskrypcji

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Tworzenie szablonu Terraform
Utwórz nowy szablon Terraform o nazwie main.tf z edytora tekstu preferowany.

```
vim main.tf
```

Skopiuj/Wklej następujący kod do powłoki chmury.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Zapisz plik i zamknij Edytor tekstu.

### <a name="terraform-init"></a>Terraform init
Rozpocznij uruchamiając `terraform init`.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

[Polecenia init terraform](https://www.terraform.io/docs/commands/init.html) służy do inicjowania zawierającego pliki konfiguracyjne Terraform katalogu roboczego. `terraform init` Polecenie jest pierwsze polecenie, które powinny być uruchamiane po zapisywania nowej konfiguracji Terraform lub klonowania istniejący z kontroli wersji. Jest bezpieczne uruchomić to polecenie wiele razy.

### <a name="terraform-plan"></a>Terraform plan
Podgląd zasoby, które ma zostać utworzony przez szablon Terraform z `terraform plan`.

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

[Terraform planu polecenia](https://www.terraform.io/docs/commands/plan.html) służy do tworzenia planu wykonywania. Terraform wykonuje odświeżania, chyba że jawnie wyłączone, a następnie określa, jakie akcje są niezbędne do osiągnięcia żądanego stanu określona w plikach konfiguracji. Można zapisać planu przy użyciu-out i następnie dostarczona do terraform dotyczą upewnij się, wykonywane są tylko wstępnie planowanych działań.

### <a name="terraform-apply"></a>Zastosuj Terraform
Udostępnianie zasobów platformy Azure z `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Polecenie stosowania terraform](https://www.terraform.io/docs/commands/apply.html) służy do zmiany wymagane do konfiguracji żądanego stanu.

### <a name="verify-deployment-with-azure-cli-20"></a>Sprawdź wdrożenie z 2.0 interfejsu wiersza polecenia platformy Azure
Uruchom `az group show -n myRgName` można sprawdzić zasobu zakończyła się pomyślnie, inicjowania obsługi administracyjnej.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Wyczyść z terraform zniszczyć
Czyszczenie grupy zasobów utworzonej z [Terraform zniszczyć polecenia](https://www.terraform.io/docs/commands/destroy.html) wyczyścić utworzony Terraform infrastruktury.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Pomyślnie utworzono zasobów platformy Azure za pośrednictwem Terraform. Odwiedź stronę kolejne kroki, aby kontynuować zapoznawanie powłoki chmury.

## <a name="next-steps"></a>Kolejne kroki
[Więcej informacji na temat dostawcy usługi Terraform Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash w chmurze powłoki Szybki Start](quickstart.md)