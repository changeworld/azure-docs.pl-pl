---
title: Wdrażanie za pomocą terraformu z usługi Azure Cloud Shell | Dokumenty firmy Microsoft
description: Wdrażanie za pomocą terraform z usługi Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: 8bacadd8941131f608411e61cc15c120c1b2bc60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458158"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Wdrażanie za pomocą terraformy firmy Bash w usłudze Azure Cloud Shell
W tym artykule oszukujesz tworzenie grupy zasobów za pomocą [dostawcy usługi Terraform AzureRM](https://www.terraform.io/docs/providers/azurerm/index.html).

[Hashicorp Terraform](https://www.terraform.io/) to narzędzie open source, które kodyfikuje interfejsy API do deklaratywnych plików konfiguracyjnych, które mogą być współużytkowane przez członków zespołu do edycji, przeglądu i wersji. Dostawca usługi Microsoft AzureRM służy do interakcji z zasobami obsługiwanymi przez usługę Azure Resource Manager za pośrednictwem interfejsów API azurerm.

## <a name="automatic-authentication"></a>Uwierzytelnianie automatyczne
Terraform jest domyślnie zainstalowany w bash w usłudze Cloud Shell. Ponadto usługa Cloud Shell automatycznie uwierzytelnia domyślną subskrypcję interfejsu wiersza polecenia platformy Azure w celu wdrożenia zasobów za pośrednictwem modułów platformy Azure terraform.

Terraform używa domyślnej subskrypcji interfejsu wiersza polecenia platformy Azure, która jest ustawiona. Aby zaktualizować subskrypcje domyślne, uruchom:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Przewodnik
### <a name="launch-bash-in-cloud-shell"></a>Uruchom Bash w Cloud Shell
1. Uruchom powłokę Cloud Shell z preferowanej lokalizacji
2. Sprawdź, czy twoja preferowana subskrypcja jest ustawiona

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Tworzenie szablonu Terraform
Utwórz nowy szablon Terraform o nazwie main.tf za pomocą preferowanego edytora tekstu.

```
vim main.tf
```

Skopiuj/wklej następujący kod do usługi Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Zapisz plik i zamknij edytor tekstu.

### <a name="terraform-init"></a>Terraform init
Zacznij od `terraform init`uruchomienia pliku .

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

[Polecenie terraform init](https://www.terraform.io/docs/commands/init.html) służy do inicjowania katalogu roboczego zawierającego pliki konfiguracyjne Terraform. Polecenie `terraform init` jest pierwszym poleceniem, które powinno zostać uruchomione po zapisaniu nowej konfiguracji Terraform lub sklonowaniu istniejącej z kontroli wersji. Jest to bezpieczne, aby uruchomić to polecenie wiele razy.

### <a name="terraform-plan"></a>Terraform plan
Wyświetl podgląd zasobów, które mają zostać `terraform plan`utworzone przez szablon Terraform za pomocą programu .

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

[Polecenie terraform plan](https://www.terraform.io/docs/commands/plan.html) służy do tworzenia planu wykonania. Terraform wykonuje odświeżanie, chyba że jawnie wyłączone, a następnie określa, jakie akcje są niezbędne do osiągnięcia żądanego stanu określonego w plikach konfiguracyjnych. Plan można zapisać przy użyciu -out, a następnie dostarczone do terraform stosuje się, aby upewnić się, że tylko wstępnie zaplanowane akcje są wykonywane.

### <a name="terraform-apply"></a>Terraform apply
Aprowizuj `terraform apply`zasoby platformy Azure za pomocą pliku .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Polecenie terraform apply](https://www.terraform.io/docs/commands/apply.html) służy do stosowania zmian wymaganych do osiągnięcia żądanego stanu konfiguracji.

### <a name="verify-deployment-with-azure-cli"></a>Weryfikowanie wdrażania za pomocą interfejsu wiersza polecenia platformy Azure
Uruchom, `az group show -n myRgName` aby sprawdzić, czy zasób zakończył się pomyślnie inicjowania obsługi administracyjnej.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Posprzątaj z terraform zniszczyć
Oczyść grupę zasobów utworzoną [za pomocą polecenia Terraform destroy,](https://www.terraform.io/docs/commands/destroy.html) aby oczyścić infrastrukturę stworzoną przez terraform.

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

Pomyślnie utworzono zasób platformy Azure za pośrednictwem terraform. Odwiedź kolejne kroki, aby kontynuować naukę o usłudze Cloud Shell.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o dostawcy platformy Terraform Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash w cloud shell szybki start](quickstart.md)
