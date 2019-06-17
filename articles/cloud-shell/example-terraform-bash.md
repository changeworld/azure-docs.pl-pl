---
title: Wdrażanie przy użyciu programu Terraform z powłoki Bash w usłudze Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Wdrażanie przy użyciu programu Terraform z powłoki Bash w usłudze Azure Cloud Shell
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
ms.openlocfilehash: a08a4e7df6cf0493ab1aa6aced1abf888a61072a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119116"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Wdrażanie przy użyciu programu Terraform z powłoki Bash w usłudze Azure Cloud Shell
W tym artykule opisano proces tworzenia grupy zasobów za pomocą [dostawcy Terraform AzureRM](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Narzędzie Hashicorp Terraform](https://www.terraform.io/) to narzędzie typu open source, które kodyfikuje interfejsów API w plikach konfiguracji deklaratywne, które mogą być współużytkowane przez członków zespołu można edytować, przejrzeniu i kontrolą wersji. Dostawca Microsoft AzureRM służy do interakcji z zasobami obsługiwane przez usługę Azure Resource Manager za pośrednictwem interfejsów API usługi AzureRM. 

## <a name="automatic-authentication"></a>Automatyczne uwierzytelnianie
Narzędzie Terraform jest instalowany w programie Bash w usłudze Cloud Shell domyślnie. Ponadto usługa Cloud Shell uwierzytelnia automatycznie domyślną subskrypcję wiersza polecenia platformy Azure do wdrażania zasobów za pośrednictwem modułów Terraform Azure.

Narzędzie Terraform używa domyślnej subskrypcji wiersza polecenia platformy Azure, który jest ustawiony. Aby zaktualizować domyślnie subskrypcje, uruchom polecenie:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Przewodnik
### <a name="launch-bash-in-cloud-shell"></a>Uruchom powłokę Bash w usłudze Cloud Shell
1. Uruchom usługę Cloud Shell z preferowanych lokalizacji
2. Upewnij się, że preferowaną subskrypcja jest skonfigurowana

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Tworzenie szablonu programu Terraform
Utwórz nowy szablon programu Terraform o nazwie main.tf z preferowanym edytorze tekstu.

```
vim main.tf
```

Kopiuj/wklej następujący kod do usługi Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Zapisz plik i zamknij Edytor tekstu.

### <a name="terraform-init"></a>Inicjowanie programu Terraform
Rozpocznij, uruchamiając `terraform init`.

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

[Polecenia init terraform](https://www.terraform.io/docs/commands/init.html) służy do inicjowania katalogu roboczego zawierającego pliki konfiguracyjne programu Terraform. `terraform init` Polecenie jest pierwsze polecenie, które powinno być uruchamiane po zapisywania nowej konfiguracji programu Terraform lub klonowania istniejącej z kontroli wersji. Jest bezpieczne uruchomić to polecenie wiele razy.

### <a name="terraform-plan"></a>Terraform plan
W wersji zapoznawczej zasoby, które ma zostać utworzony przez szablon programu Terraform z `terraform plan`.

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

[Polecenie terraform plan](https://www.terraform.io/docs/commands/plan.html) służy do tworzenia planu wykonania. Narzędzie Terraform wykonuje odświeżenia, chyba że jawnie wyłączone i określa, jakie akcje są niezbędne do osiągnięcia żądanego stanu, które są określone w plikach konfiguracji. Plan można zapisać przy użyciu-out, następnie udostępniana terraform dotyczą zapewnienia wykonywania zaplanowanych akcji.

### <a name="terraform-apply"></a>Terraform apply
Aprowizowanie zasobów platformy Azure przy użyciu `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Terraform zastosowanie polecenia](https://www.terraform.io/docs/commands/apply.html) służy do stosowania zmian wymagany do osiągnięcia żądanego stanu konfiguracji.

### <a name="verify-deployment-with-azure-cli"></a>Sprawdź wdrażanie za pomocą wiersza polecenia platformy Azure
Uruchom `az group show -n myRgName` można sprawdzić zasobu zakończyła się pomyślnie, inicjowanie obsługi administracyjnej.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Czyszczenie przy użyciu programu terraform zniszczyć
Usuwaj grupy zasobów utworzonej przy użyciu [Terraform zniszczyć polecenia](https://www.terraform.io/docs/commands/destroy.html) wyczyścić utworzony przez narzędzie Terraform infrastruktury.

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

Pomyślnie utworzono zasób platformy Azure za pomocą programu Terraform. Odwiedź stronę następnych kroków, aby uzyskać więcej informacji o usłudze Cloud Shell.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o dostawcy usługi Terraform Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Powłoka bash w usłudze Cloud Shell przewodnika Szybki Start](quickstart.md)
