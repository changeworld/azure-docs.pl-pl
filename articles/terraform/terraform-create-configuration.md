---
title: Szybki Start — Tworzenie konfiguracji Terraform dla platformy Azure
description: Rozpocznij pracę z programem Terraform na platformie Azure od wdrożenia usług Azure Cosmos DB i Azure Container Instances.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470025"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Szybki Start: Tworzenie konfiguracji Terraform dla platformy Azure

W tym przewodniku szybki start utworzysz zasoby platformy Azure przy użyciu Terraform. Kroki opisane w tym artykule przeprowadzą Cię przez proces tworzenia następujących zasobów:

> [!div class="checklist"]
> * Wystąpienie Azure Cosmos DB
> * Wystąpienie kontenera platformy Azure
> * Aplikacja, która działa w ramach tych dwóch zasobów

## <a name="create-first-configuration"></a>Tworzenie pierwszej konfiguracji

W tej sekcji utworzysz konfigurację dla wystąpienia Azure Cosmos DB.

1. Zaloguj się do [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz Azure Cloud Shell.

1. Uruchom Edytor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Konfiguracja w tym kroku umożliwia modelowanie kilku zasobów platformy Azure. Te zasoby obejmują grupę zasobów platformy Azure i wystąpienie Azure Cosmos DB. Do utworzenia unikatowej nazwy wystąpienia Cosmos DB jest używana Losowa liczba całkowita. Konfigurowanych jest również kilka ustawień usługi Cosmos DB. Aby uzyskać więcej informacji, zobacz [Informacje o Cosmos DB Terraform](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Skopiuj i wklej następującą konfigurację Terraform do edytora:

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

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

## <a name="run-the-configuration"></a>Uruchom konfigurację

W tej sekcji użyjesz kilku poleceń Terraform, aby uruchomić konfigurację.

1. Polecenie [terraform init](https://www.terraform.io/docs/commands/init.html) inicjuje katalog roboczy. Uruchom następujące polecenie w Cloud Shell:

    ```bash
    terraform init
    ```

1. Za pomocą polecenia [planu Terraform](https://www.terraform.io/docs/commands/plan.html) można sprawdzić poprawność składni konfiguracji. Parametr `-out` kieruje wyniki do pliku. Plik wyjściowy może być później używany do zastosowania konfiguracji. Uruchom następujące polecenie w Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. Polecenie [Terraform Apply](https://www.terraform.io/docs/commands/apply.html) służy do zastosowania konfiguracji. Określono plik wyjściowy z poprzedniego kroku. To polecenie powoduje utworzenie zasobów platformy Azure. Uruchom następujące polecenie w Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Aby sprawdzić wyniki w Azure Portal, przejdź do nowej grupy zasobów. Nowe wystąpienie Azure Cosmos DB znajduje się w nowej grupie zasobów.

## <a name="update-configuration"></a>Aktualizowanie konfiguracji

W tej sekcji pokazano, jak zaktualizować konfigurację w celu uwzględnienia wystąpienia kontenera platformy Azure. Kontener uruchamia aplikację, która odczytuje i zapisuje dane w usłudze Cosmos DB.

1. Uruchom Edytor Cloud Shell:

    ```bash
    code main.tf
    ```

1. W konfiguracji w tym kroku są ustawiane dwie zmienne środowiskowe: `COSMOS_DB_ENDPOINT` i `COSMOS_DB_MASTERKEY`. Te zmienne przechowują lokalizację i klucz na potrzeby uzyskiwania dostępu do bazy danych. Wartości tych zmiennych są uzyskiwane z wystąpienia bazy danych utworzonego w poprzednim kroku. Ten proces jest nazywany interpolacją. Aby dowiedzieć się więcej na temat interpolacji programu Terraform, zobacz [Interpolation Syntax](https://www.terraform.io/docs/configuration/interpolation.html) (Składnia interpolacji). Konfiguracja obejmuje również blok danych wyjściowych, który zwraca w pełni kwalifikowaną nazwę domeny (FQDN) wystąpienia kontenera. Skopiuj i wklej następujący kod do edytora:

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

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

1. Tak jak w poprzedniej sekcji, uruchom następujące polecenie, aby wykonać wizualne zmiany:

    ```bash
    terraform plan --out plan.out
    ```

1. Uruchom `terraform apply` polecenie, aby zastosować konfigurację.

    ```bash
    terraform apply plan.out
    ```

1. Zanotuj nazwę FQDN wystąpienia kontenera.

## <a name="test-application"></a>Testowanie aplikacji

Aby przetestować aplikację, przejdź do nazwy FQDN wystąpienia kontenera. Powinny być widoczne wyniki podobne do następujących:

![Aplikacja do głosowania na platformie Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule.

Uruchom polecenie [Terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) , aby usunąć zasoby platformy Azure utworzone w ramach tego samouczka:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zainstaluj i skonfiguruj Terraform, aby udostępnić zasoby platformy Azure](terraform-install-configure.md).