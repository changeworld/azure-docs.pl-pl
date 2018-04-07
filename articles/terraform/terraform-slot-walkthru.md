---
title: Terraform z miejscem wdrożenia dostawca usługi Azure
description: Terraform z samouczka miejsca wdrożenia dostawca usługi Azure
keywords: terraform, devops, Azure, miejsc wdrożenia maszyny wirtualnej
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 34b16b5fb2b5b574d166693db346ebba15eaa1f9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="using-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Przy użyciu Terraform udostępniania infrastruktury z miejsc wdrożenia usługi Azure

[Miejsca wdrożenia Azure](/azure/app-service/web-sites-staged-publishing) umożliwiają wymiany między różnymi wersjami aplikacji — na przykład produkcyjnym i pomostowym — aby zminimalizować wpływ wdrożenia przerwane. W tym artykule przedstawiono przykład użyj miejsc wdrożenia przez przedstawienie wdrożenie dwóch aplikacji za pośrednictwem usługi GitHub i na platformie Azure. Jednej aplikacji znajduje się w "gniazda produkcyjnego", podczas gdy druga aplikacji znajduje się w miejscu "tymczasowe". (Nazwy "production" i "tymczasowe" są dowolne i może być dowolną inną reprezentujący scenariusz). Po skonfigurowaniu sieci miejsc wdrożenia można następnie użyć Terraform do wymiany między dwoma miejscami w razie potrzeby.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.

- **Konto GitHub** — [GitHub](http://www.github.com) konta jest wymagana do rozwidlania i korzystanie z testu repozytorium GitHub.

## <a name="create-and-apply-the-terraform-plan"></a>Tworzenie i stosowanie planu Terraform

1. Przejdź do [portalu Azure](http://portal.azure.com)

1. Otwórz [powłoki chmury Azure](/azure/cloud-shell/overview)i — jeśli nie utworzono go wcześniej — wybierz **Bash** jako środowiska.

    ![Chmura wierszu polecenia powłoki](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Przejdź do `clouddrive` katalogu.

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

1. Sprawdź, czy obie katalogi zostały pomyślnie utworzone przy użyciu `ls` bash polecenia.

    ![Chmura powłoki po tworzenie katalogów](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Przejdź do `deploy` katalogu.

    ```bash
    cd deploy
    ```

1. Przy użyciu [Edytor vi](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html), Utwórz plik o nazwie `deploy.tf`, który będzie zawierać [Terraform konfiguracji](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Tryb wstawiania naciskając literę `i` klucza.

1. Wklej następujący kod do edytora:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Naciśnij klawisz  **&lt;Esc >** klawisz, aby zakończyć działanie w trybie wstawiania.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie, a następnie naciskając klawisz  **&lt;Enter >**:

    ```bash
    :wq
    ```

1. Po utworzeniu pliku można sprawdzić jego zawartość.

    ```bash
    cat deploy.tf
    ```

1. Initialize Terraform.

    ```bash
    terraform init
    ```

1. Tworzenie planu Terraform.

    ```bash
    terraform plan
    ```

1. Zapewnij zasoby zdefiniowane w `deploy.tf` pliku konfiguracji. (Potwierdzenie, wprowadzając `yes` w wierszu.)

    ```bash
    terraform apply
    ```

1. Zamknij okno powłoki chmury.

1. W portalu Azure menu głównego, wybierz **grup zasobów**.

    ![Grupy zasobów w portalu Azure](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Na **grup zasobów** wybierz opcję **slotDemoResourceGroup**.

    ![Grupy zasobów utworzonej przez Terraform](./media/terraform-slot-walkthru/resource-group.png)

Po zakończeniu zostanie wyświetlony wszystkie zasoby utworzone przez Terraform.

![Zasoby utworzone przez Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Rozwidlenie projektu testowego

Zanim można przetestować tworzenie i wymiany i miejsc wdrożenia, należy rozwidlania projektu testowego z usługi GitHub.

1. Przejdź do [świetny terraform repozytorium w usłudze GitHub](https://github.com/Azure/awesome-terraform).

1. Rozwidlenia **repozytorium świetny terraform**.

    ![Rozwidlenia świetny terraform repozytorium GitHub](./media/terraform-slot-walkthru/fork-repo.png)

1. Wykonaj wszystkie monity do rozwidlania dla danego środowiska.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Wdrażanie z serwisu GitHub do Twojego miejsca wdrożenia

Raz rozwidlenia repozytorium projektu testowego, skonfiguruj miejsca wdrożenia za pośrednictwem następujące czynności:

1. W portalu Azure menu głównego, wybierz **grup zasobów**.

1. Wybierz **slotDemoResourceGroup**.

1. Wybierz **slotAppService**.

1. Wybierz **opcje wdrażania**.

    ![Opcje wdrażania dla zasobu usługi aplikacji](./media/terraform-slot-walkthru/deployment-options.png)

1. Na **opcji wdrażania** wybierz opcję **wybierz źródło**, a następnie wybierz **GitHub**.

    ![Wybierz źródło wdrożenia](./media/terraform-slot-walkthru/select-source.png)

1. Po Azure nawiązuje połączenie i wyświetla wszystkie opcje, wybierz opcję **autoryzacji**.

1. Na **autoryzacji** wybierz opcję **autoryzacji**i podaj poświadczenia wymagane do platformy Azure w celu uzyskania dostępu do konta usługi GitHub. 

1. Po Azure sprawdza poświadczenia GitHub, zostanie wyświetlony komunikat wskazujący, że proces autoryzacji został zakończony. Wybierz **OK** zamknąć **autoryzacji** kartę.

1. Wybierz **wybierz organizacji** i wybierz organizację.

1. Wybierz **projektu wybierz**.

1. Na **wybierz projekt** wybierz opcję **świetny terraform** projektu.

    ![Wybierz projekt świetny terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Wybierz **Wybierz gałąź**.

1. Na **Wybierz gałąź** wybierz opcję **wzorca**.

    ![Wybierz głównej gałęzi](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Na **opcji wdrażania** wybierz opcję **OK**.

W tym momencie wdrożono miejsca produkcji. Aby wdrożyć miejsca przemieszczania, należy wykonać wszystkie poprzednie kroki w tej sekcji z następującymi zmianami:

- W kroku 3 **slotAppServiceSlotOne** zasobów.

- W kroku 13 Wybierz gałąź "działa" zamiast gałęzi głównej.

    ![Wybierz gałąź pracy](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testowanie wdrożenia aplikacji

W poprzednich sekcjach, skonfigurować dwa gniazda - **slotAppService** i **slotAppServiceSlotOne** — do wdrożenia z różnych oddziałów w serwisie GitHub. Umożliwia podgląd aplikacje sieci web, aby sprawdzić, czy zostały pomyślnie wdrożone.

Wykonaj poniższe czynności dwa razy którym w kroku 3 wybrać **slotAppService** po raz pierwszy, a następnie wybierz **slotAppServiceSlotOne** po raz drugi:

1. W portalu Azure menu głównego, wybierz **grup zasobów**.

1. Wybierz **slotDemoResourceGroup**.

1. Wybierz opcję **slotAppService** lub **slotAppServiceSlotOne**.

1. Na stronie Przegląd wybierz **adres URL**.

    ![Wybierz adres URL na karcie Przegląd renderowanie aplikacji](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Może upłynąć kilka minut dla platformy Azure do tworzenia i wdrażania lokacji z usługi GitHub.
>
>

Dla **slotAppService** aplikacji sieci web, zobacz stronę niebieski tytuł strony **1 aplikacji demonstracyjnej miejsca**. Dla **slotAppServiceSlotOne** aplikacji sieci web, zobacz stronę zielony tytuł strony **2 aplikacji demonstracyjnej miejsca**.

![Aby sprawdzić, czy zostały one poprawnie wdrożone aplikacje w wersji Preview](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Zamienić miejsc dwa wdrożenia

Aby przetestować wymiany miejsc dwa wdrożenia, wykonaj następujące czynności:
 
1. Przejdź do karty przeglądarki systemem **slotAppService** (aplikacji ze stroną, niebieski). 

1. Wróć do portalu Azure na osobnej karcie.

1. Otwórz powłokę chmury.

1. Przejdź do **clouddrive/wymiany** katalogu.

    ```bash
    cd clouddrive/swap
    ```

1. Za pomocą edytora vi, Utwórz plik o nazwie `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Tryb wstawiania naciskając literę `i` klucza.

1. Wklej następujący kod do edytora:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Naciśnij klawisz  **&lt;Esc >** klawisz, aby zakończyć działanie w trybie wstawiania.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie, a następnie naciskając klawisz  **&lt;Enter >**:

    ```bash
    :wq
    ```

1. Initialize Terraform.

    ```bash
    terraform init
    ```

1. Tworzenie planu Terraform.

    ```bash
    terraform plan
    ```

1. Zapewnij zasoby zdefiniowane w `swap.tf` pliku konfiguracji. (Potwierdzenie, wprowadzając `yes` w wierszu.)

    ```bash
    terraform apply
    ```

1. Zamiana miejscami, wróć do przeglądarki, który jest renderowanie po zakończeniu Terraform **slotAppService** sieci web app i Odśwież stronę. 

Aplikacja sieci web w sieci **slotAppServiceSlotOne** miejsca została zapisana z miejscem produkcyjnym i teraz renderuje zielony. 

![Zapisana miejsca wdrożenia](./media/terraform-slot-walkthru/slots-swapped.png)

Aby powrócić do oryginalnej wersji produkcyjnej aplikacji, zastosuj ponownie utworzone na podstawie planu Terraform `swap.tf` pliku konfiguracji.

```bash
terraform apply
```

Po miejscami, zapoznaj się oryginalnej konfiguracji.