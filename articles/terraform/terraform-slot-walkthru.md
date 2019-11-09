---
title: Samouczek — Inicjowanie obsługi infrastruktury przy użyciu miejsc wdrożenia platformy Azure za pomocą Terraform
description: Samouczek dotyczący korzystania z programu Terraform w połączeniu z miejscami wdrożenia dostawcy platformy Azure
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 0bfd10325f1a62e74f0d3573f052d114069491a3
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838058"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>Samouczek: Inicjowanie obsługi infrastruktury przy użyciu miejsc wdrożenia platformy Azure za pomocą Terraform

[Miejsc wdrożenia platformy Azure](/azure/app-service/deploy-staging-slots) możesz użyć do przełączania się między różnymi wersjami aplikacji. Ta możliwość pozwala zminimalizować wpływ uszkodzonych wdrożeń. 

W tym artykule przedstawiono w formie przewodnika przykładowe zastosowanie miejsc wdrożenia do wdrożenia dwóch aplikacji za pomocą usługi GitHub i na platformie Azure. Jedna aplikacja jest hostowana w miejscu produkcyjnym. Druga aplikacja jest hostowana w miejscu przejściowym. (Nazwy "produkcyjne" i "przemieszczanie" są dowolne. Mogą one być odpowiednie dla danego scenariusza.) Po skonfigurowaniu miejsc wdrożenia Użyj Terraform do wymiany między dwoma gniazdami zgodnie z potrzebami.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Konto usługi GitHub**: konto usługi [GitHub](https://www.github.com) jest potrzebne do rozwidlenia testowego repozytorium GitHub i używania go.

## <a name="create-and-apply-the-terraform-plan"></a>Tworzenie i stosowanie planu programu Terraform

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Otwórz usługę [Azure Cloud Shell](/azure/cloud-shell/overview). Jeśli środowisko nie zostało wybrane wcześniej, wybierz pozycję **Bash** jako swoje środowisko.

    ![Wiersz polecenia usługi Cloud Shell](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Zmień katalog na `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Utwórz katalog o nazwie `deploy`.

    ```bash
    mkdir deploy
    ```

1. Utwórz katalog o nazwie `swap`.

    ```bash
    mkdir swap
    ```

1. Użyj polecenia `ls` programu bash, aby zweryfikować, czy pomyślnie utworzono oba katalogi.

    ![Usługa Cloud Shell po utworzeniu katalogów](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Zmień katalog na `deploy`.

    ```bash
    cd deploy
    ```

1. W usłudze Cloud Shell utwórz plik o nazwie `deploy.tf`.

    ```bash
    code deploy.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

1. Teraz, gdy utworzono plik, zweryfikuj jego zawartość.

    ```bash
    cat deploy.tf
    ```

1. Zainicjuj narzędzie Terraform.

    ```bash
    terraform init
    ```

1. Utwórz plan programu Terraform.

    ```bash
    terraform plan
    ```

1. Aprowizuj zasoby zdefiniowane w pliku konfiguracji `deploy.tf`. (Potwierdź tę akcję, wpisując `yes` po wyświetleniu monitu).

    ```bash
    terraform apply
    ```

1. Zamknij okno usługi Cloud Shell.

1. W menu głównym witryny Azure Portal wybierz pozycję **Grupy zasobów**.

    ![Wybór pozycji „Grupy zasobów” w portalu](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Na karcie **Grupy zasobów** wybierz pozycję **slotDemoResourceGroup**.

    ![Grupa zasobów utworzona przez program Terraform](./media/terraform-slot-walkthru/resource-group.png)

Teraz widzisz wszystkie zasoby utworzone przez program Terraform.

![Zasoby utworzone przez program Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Rozwidlenie projektu testowego

Zanim będzie można przetestować utworzone zasoby i zamienianie miejsc wdrożenia, musisz rozwidlić projekt testowy z usługi GitHub.

1. Przejdź do [repozytorium awesome-terraform w usłudze GitHub](https://github.com/Azure/awesome-terraform).

1. Wykonaj rozwidlenie repozytorium **awesome-terraform**.

    ![Tworzenie rozwidlenia repozytorium GitHub awesome-terraform](./media/terraform-slot-walkthru/fork-repo.png)

1. Stosuj się do wszystkich monitów, aby przeprowadzić rozwidlenie do Twojego środowiska.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Wdrażanie z usługi GitHub do miejsc wdrożenia

Po rozwidleniu repozytorium projektu testowego skonfiguruj miejsca wdrożenia, wykonując następujące kroki:

1. W menu głównym witryny Azure Portal wybierz pozycję **Grupy zasobów**.

1. Wybierz pozycję **slotDemoResourceGroup**.

1. Wybierz pozycję **slotAppService**.

1. Wybierz pozycję **Opcje wdrożenia**.

    ![Opcje wdrożenia dla zasobu usługi App Service](./media/terraform-slot-walkthru/deployment-options.png)

1. Na karcie **Opcja wdrożenia** wybierz pozycję **Wybierz źródło**, a następnie **GitHub**.

    ![Wybieranie źródła wdrożenia](./media/terraform-slot-walkthru/select-source.png)

1. Gdy platforma Azure nawiąże połączenie i wyświetli wszystkie opcje, wybierz pozycję **Autoryzacja**.

1. Na karcie **Autoryzacja** wybierz pozycję **Autoryzuj** i podaj poświadczenia, których platforma Azure potrzebuje do uzyskania dostępu do Twojego konta usługi GitHub. 

1. Po zweryfikowaniu przez platformę Azure poświadczeń usługi GitHub zostanie wyświetlony komunikat z informacją o zakończeniu procesu autoryzacji. Wybierz pozycję **OK**, aby zamknąć kartę **Autoryzacja**.

1. Wybierz pozycję **Wybierz organizację** i określ organizację.

1. Wybierz pozycję **Wybierz projekt**.

1. Na karcie **Wybierz projekt** określ projekt **awesome-terraform**.

    ![Wybieranie projektu awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Wybierz pozycję **Wybierz gałąź**.

1. Na karcie **Wybierz gałąź** wybierz pozycję **master**.

    ![Wybieranie gałęzi master](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Na karcie **Opcja wdrożenia** wybierz pozycję **OK**.

W tym momencie wdrożono miejsce produkcyjne. Aby wdrożyć miejsce przejściowe, wykonaj poprzednie kroki z następującymi modyfikacjami:

- W kroku 3 wybierz zasób **slotAppServiceSlotOne**.

- W kroku 13 wybierz gałąź roboczą, a nie gałąź master.

    ![Wybieranie gałęzi roboczej](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testowanie wdrożeń aplikacji

W poprzednich sekcjach skonfigurowano dwa miejsca — **slotAppService** i **slotAppServiceSlotOne**— na potrzeby wdrażania z różnych gałęzi w usłudze GitHub. Sprawdzimy podgląd aplikacji internetowych, aby zweryfikować, że zostały wdrożone pomyślnie.

1. W menu głównym witryny Azure Portal wybierz pozycję **Grupy zasobów**.

1. Wybierz pozycję **slotDemoResourceGroup**.

1. Wybierz pozycję **slotAppService** lub **slotAppServiceSlotOne**.

1. Na stronie przeglądu wybierz pozycję **Adres URL**.

    ![Wybieranie adresu URL na karcie przeglądu w celu renderowania aplikacji](./media/terraform-slot-walkthru/resource-url.png)

1. W zależności od wybranej aplikacji są wyświetlane następujące wyniki:
    - **slotAppService** Web App — niebieska Strona z tytułem strony **aplikacji demonstracyjnej o gnieździe 1**. 
    - **slotAppServiceSlotOne** Web App — Zielona Strona z tytułem strony **aplikacji demonstracyjnej o gnieździe 2**.

    ![Wyświetlanie podglądu aplikacji w celu przetestowania, czy zostały wdrożone poprawnie](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Zamiana dwóch miejsc wdrożenia

Aby przetestować wymianę dwóch miejsc wdrożenia, wykonaj następujące czynności:
 
1. Przejdź do karty przeglądarki z uruchomioną aplikacją **slotAppService** (aplikacja z niebieską stroną). 

1. Wróć do witryny Azure Portal na oddzielnej karcie.

1. Otwórz usługę Cloud Shell.

1. Zmień katalog na **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. W usłudze Cloud Shell utwórz plik o nazwie `swap.tf`.

    ```bash
    code swap.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

1. Zainicjuj narzędzie Terraform.

    ```bash
    terraform init
    ```

1. Utwórz plan programu Terraform.

    ```bash
    terraform plan
    ```

1. Aprowizuj zasoby zdefiniowane w pliku konfiguracji `swap.tf`. (Potwierdź tę akcję, wpisując `yes` po wyświetleniu monitu).

    ```bash
    terraform apply
    ```

1. Po zamianie gniazd na Terraform Wróć do przeglądarki. Odśwież stronę. 

Aplikacja internetowa w miejscu przejściowym **slotAppServiceSlotOne** została umieszczona w miejscu produkcyjnym i jest teraz renderowana w kolorze zielonym. 

![Miejsca wdrożenia zostały zamienione](./media/terraform-slot-walkthru/slots-swapped.png)

Aby powrócić do oryginalnej, produkcyjnej wersji aplikacji, zastosuj ponownie plan programu Terraform utworzony na podstawie pliku konfiguracji `swap.tf`.

```bash
terraform apply
```

Po zamianie aplikacji zobaczysz oryginalną konfigurację.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o korzystaniu z Terraform na platformie Azure](/azure/terraform)