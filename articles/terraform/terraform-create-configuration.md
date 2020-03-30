---
title: Szybki start — tworzenie konfiguracji terraform dla platformy Azure
description: Rozpocznij pracę z programem Terraform na platformie Azure od wdrożenia usług Azure Cosmos DB i Azure Container Instances.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77470025"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Szybki start: tworzenie konfiguracji terraform dla platformy Azure

W tym przewodniku Szybki start można utworzyć zasoby platformy Azure przy użyciu terraform. Kroki opisane w tym artykule umożliwiają tworzenie następujących zasobów:

> [!div class="checklist"]
> * Wystąpienie usługi Azure Cosmos DB
> * Wystąpienie kontenera platformy Azure
> * Aplikacja, która działa w tych dwóch zasobach

## <a name="create-first-configuration"></a>Tworzenie pierwszej konfiguracji

W tej sekcji należy utworzyć konfigurację dla wystąpienia usługi Azure Cosmos DB.

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz powłokę chmury azure.

1. Uruchom edytor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Konfiguracja w tym kroku modeluje kilka zasobów platformy Azure. Zasoby te obejmują grupę zasobów platformy Azure i wystąpienie usługi Azure Cosmos DB. Losowa liczba całkowita jest używana do tworzenia unikatowej nazwy wystąpienia usługi Cosmos DB. Konfigurowanych jest również kilka ustawień usługi Cosmos DB. Aby uzyskać więcej informacji, zobacz [odwołanie do planu terraform usługi Cosmos DB](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Skopiuj i wklej następującą konfigurację Terraform do edytora:

    ```hcl
    resource "azurerm_resource_group" "vote-resource-group" {
      name     = "vote-resource-group"
      location = "westus"
    }

    resource "random_integer" "ri" {
      min = 10000
      max = 99999
    }

    resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
      name                = "tfex-cosmos-db-${random_integer.ri.result}"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      offer_type          = "Standard"
      kind                = "GlobalDocumentDB"

      consistency_policy {
        consistency_level       = "BoundedStaleness"
        max_interval_in_seconds = 10
        max_staleness_prefix    = 200
      }

      geo_location {
        location          = "westus"
        failover_priority = 0
      }
    }
    ```

1. Zapisz plik**&lt;(Ctrl>S**) i zamknij edytor**&lt;(Ctrl>Q**).

## <a name="run-the-configuration"></a>Uruchamianie konfiguracji

W tej sekcji można użyć kilku poleceń Terraform, aby uruchomić konfigurację.

1. Polecenie [terraform init](https://www.terraform.io/docs/commands/init.html) inicjuje katalog roboczy. Uruchom następujące polecenie w usłudze Cloud Shell:

    ```bash
    terraform init
    ```

1. Polecenie [planu terraform](https://www.terraform.io/docs/commands/plan.html) może służyć do sprawdzania poprawności składni konfiguracji. Parametr `-out` kieruje wyniki do pliku. Plik wyjściowy może być później użyty do zastosowania konfiguracji. Uruchom następujące polecenie w usłudze Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. Polecenie [terraform apply](https://www.terraform.io/docs/commands/apply.html) służy do stosowania konfiguracji. Określono plik wyjściowy z poprzedniego kroku. To polecenie powoduje, że zasoby platformy Azure mają zostać utworzone. Uruchom następujące polecenie w usłudze Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Aby zweryfikować wyniki w witrynie Azure portal, przejdź do nowej grupy zasobów. Nowe wystąpienie usługi Azure Cosmos DB znajduje się w nowej grupie zasobów.

## <a name="update-configuration"></a>Aktualizowanie konfiguracji

W tej sekcji pokazano, jak zaktualizować konfigurację, aby uwzględnić wystąpienie kontenera platformy Azure. Kontener uruchamia aplikację, która odczytuje i zapisuje dane w usłudze Cosmos DB.

1. Uruchom edytor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Konfiguracja w tym kroku ustawia `COSMOS_DB_ENDPOINT` dwie `COSMOS_DB_MASTERKEY`zmienne środowiskowe: i . Te zmienne przechowują lokalizację i klucz na potrzeby uzyskiwania dostępu do bazy danych. Wartości dla tych zmiennych są uzyskiwane z wystąpienia bazy danych utworzonego w poprzednim kroku. Ten proces jest nazywany interpolacją. Aby dowiedzieć się więcej na temat interpolacji programu Terraform, zobacz [Interpolation Syntax](https://www.terraform.io/docs/configuration/interpolation.html) (Składnia interpolacji). Konfiguracja obejmuje również blok danych wyjściowych, który zwraca w pełni kwalifikowaną nazwę domeny (FQDN) wystąpienia kontenera. Skopiuj i wklej następujący kod do edytora:

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      ip_address_type     = "public"
      dns_name_label      = "vote-aci"
      os_type             = "linux"

      container {
        name   = "vote-aci"
        image  = "microsoft/azure-vote-front:cosmosdb"
        cpu    = "0.5"
        memory = "1.5"
        ports {
          port     = 80
          protocol = "TCP"
        }

        secure_environment_variables = {
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. Zapisz plik**&lt;(Ctrl>S**) i zamknij edytor**&lt;(Ctrl>Q**).

1. Podobnie jak w poprzedniej sekcji, uruchom następujące polecenie, aby wizualnie zmiany, które mają zostać wprowadzone:

    ```bash
    terraform plan --out plan.out
    ```

1. Uruchom `terraform apply` polecenie, aby zastosować konfigurację.

    ```bash
    terraform apply plan.out
    ```

1. Zanotuj numer FQDN wystąpienia kontenera.

## <a name="test-application"></a>Testowanie aplikacji

Aby przetestować aplikację, przejdź do nazwy FQDN wystąpienia kontenera. Powinny być widoczne wyniki podobne do następujących danych wyjściowych:

![Aplikacja do głosowania na platformie Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń zasoby utworzone w tym artykule.

Uruchom polecenie [terraform destroy,](https://www.terraform.io/docs/commands/destroy.html) aby usunąć zasoby platformy Azure utworzone w tym samouczku:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zainstaluj i skonfiguruj program Terraform w celu aprowizowania zasobów platformy Azure](terraform-install-configure.md).