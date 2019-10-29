---
title: Samouczek — Konfigurowanie rozszerzenia Visual Studio Code Azure Terraform
description: Dowiedz się, jak zainstalować rozszerzenie Azure Terraform w programie Visual Studio Code i korzystać z niego.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 93ec85a2e37350ef56ff8c2e1a0ea16bb3be355e
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969293"
---
# <a name="tutorial-configure-the-azure-terraform-visual-studio-code-extension"></a>Samouczek: Konfigurowanie rozszerzenia Visual Studio Code Azure Terraform

Rozszerzenie Visual Studio Code Azure Terraform umożliwia korzystanie z Terraform z edytora. Dzięki temu rozszerzeniu można tworzyć, testować i uruchamiać konfiguracje Terraform. Rozszerzenie obsługuje również wizualizację grafu zasobów.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Automatyzowanie aprowizacji usług platformy Azure za pomocą Terraform
> * Zainstaluj rozszerzenie Terraform Visual Studio Code dla usług platformy Azure i użyj go.
> * Użyj Visual Studio Code, aby pisać, planować i wykonywać plany Terraform.

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Terraform**: [zainstaluj i skonfiguruj narzędzie Terraform](/azure/virtual-machines/linux/terraform-install-configure).

- **Visual Studio Code**: zainstaluj program [Visual Studio Code](https://code.visualstudio.com/download) w wersji odpowiedniej dla Twojego środowiska.

## <a name="prepare-your-dev-environment"></a>Przygotowywanie środowiska deweloperskiego

### <a name="install-git"></a>Instalowanie oprogramowania Git

Aby wykonać ćwiczenia opisane w tym artykule, musisz [zainstalować oprogramowanie Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Instalowanie narzędzia HashiCorp Terraform

Na stronie internetowej [Install Terraform](https://www.terraform.io/intro/getting-started/install.html) (Instalacja narzędzia Terraform) firmy HashiCorp wykonaj instrukcje dotyczące następujących zagadnień:

- Pobieranie narzędzia Terraform
- Instalowanie narzędzia Terraform
- Sprawdzanie, czy narzędzie Terraform zostało poprawnie zainstalowane

>[!Tip]
>Nie zapomnij wykonać instrukcji dotyczących ustawiania zmiennej systemowej PATH.

### <a name="install-nodejs"></a>Instalowanie środowiska Node.js

Aby używać narzędzia Terraform w usłudze Cloud Shell, musisz [zainstalować środowisko Node.js](https://nodejs.org/) w wersji 6.0 lub nowszej.

>[!NOTE]
>Aby sprawdzić, czy środowisko Node.js jest zainstalowane, otwórz okno terminalu i wprowadź polecenie `node -v`.

### <a name="install-graphviz"></a>Instalowanie oprogramowania GraphViz

Aby używać funkcji wizualizacji narzędzia Terraform, musisz [zainstalować oprogramowanie GraphViz](https://graphviz.org/).

>[!NOTE]
>Aby sprawdzić, czy oprogramowanie GraphViz jest zainstalowane, otwórz okno terminalu i wprowadź polecenie `dot -V`.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Instalowanie rozszerzenia Azure Terraform dla programu Visual Studio Code

1. Uruchom program Visual Studio Code.

1. Wybierz pozycję **Rozszerzenia**.

    ![Przycisk Rozszerzenia](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Użyj pola tekstowego **Wyszukaj rozszerzenia w portalu Marketplace**, aby wyszukać rozszerzenie Azure Terraform:

    ![Wyszukiwanie rozszerzeń programu Visual Studio Code Search w portalu Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

1. Wybierz pozycję **Zainstaluj**.

    >[!NOTE]
    >Po wybraniu pozycji **Zainstaluj** dla rozszerzenia Azure Terraform program Visual Studio Code automatycznie zainstaluje rozszerzenie Azure Account. Azure Account jest plikiem zależności dla rozszerzenia Azure Terraform, który służy do wykonywania uwierzytelnień subskrypcji platformy Azure i rozszerzeń kodu dotyczących platformy Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Sprawdzanie, czy rozszerzenie programu Terraform jest zainstalowane w programie Visual Studio Code

1. Wybierz pozycję **Rozszerzenia**.

1. W polu tekstowym wyszukiwania wprowadź ciąg `@installed`.

    ![Zainstalowane rozszerzenia](media/terraform-vscode-extension/tf-installed-extensions.png)

Rozszerzenie Azure Terraform zostanie wyświetlone na liście zainstalowanych rozszerzeń.

![Zainstalowane rozszerzenia narzędzia Terraform](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Teraz możesz uruchamiać wszystkie obsługiwane polecenia narzędzia Terraform w środowisku usługi Cloud Shell z poziomu programu Visual Studio Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Ćwiczenie 1. Szczegółowe omówienie poleceń narzędzia Terraform

W tym ćwiczeniu utworzysz i wykonasz podstawowy plik konfiguracji narzędzia Terraform, który aprowizuje nową grupę zasobów platformy Azure.

### <a name="prepare-a-test-plan-file"></a>Przygotowywanie pliku planu testu

1. Na pasku menu w programie Visual Studio Code wybierz pozycję **Plik > Nowy plik**.

1. W przeglądarce przejdź do [strony azurerm_resource_group narzędzia Terraform](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) i skopiuj kod w bloku kodu **Przykładowe użycie**:

    ![Przykład użycia](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Wklej skopiowany kod do nowego pliku utworzonego w programie Visual Studio Code.

    ![Wklejanie kodu Przykładowe użycie](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Wartość **name** grupy zasobów można zmienić, ale musi ona być unikatowa dla Twojej subskrypcji platformy Azure.

1. Na pasku menu wybierz pozycję **Plik > Zapisz jako**.

1. W oknie dialogowym **Zapisywanie jako** przejdź do wybranej lokalizacji, a następnie wybierz pozycję **Nowy folder**. (Zmień nazwę nowego folderu na coś bardziej opisowego niż *Nowy folder*).

    >[!NOTE]
    >W tym przykładzie folder nosi nazwę TERRAFORM-TEST-PLAN.

1. Upewnij się, że nowy folder jest wyróżniony (zaznaczony), a następnie wybierz pozycję **Otwórz**.

1. W oknie dialogowym **Zapisywanie jako** zmień domyślną nazwę pliku na *main.tf*.

    ![Zapisywanie pliku pod nazwą main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

1. Wybierz pozycję **Zapisz**.
1. Na pasku menu wybierz pozycję **Plik > Otwórz folder**. Przejdź do nowo utworzonego folderu, a następnie wybierz go.

### <a name="run-terraform-init-command"></a>Uruchamianie polecenia *init* narzędzia Terraform

1. Uruchom program Visual Studio Code.

1. Na pasku menu programu Visual Studio Code wybierz pozycję **Plik > Otwórz folder**, a następnie znajdź i wybierz plik *main.tf*.

    ![Plik main.tf](media/terraform-vscode-extension/tf-main-tf.png)

1. Na pasku menu wybierz pozycję **Widok > Paleta poleceń > Azure Terraform: Init**.

1. Po wyświetleniu potwierdzenia wybierz pozycję **OK**.

    ![Czy chcesz otworzyć usługę Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. Przy pierwszym uruchomieniu Cloud Shell z nowego folderu zostanie wyświetlony monit o utworzenie aplikacji sieci Web. Wybierz pozycję **Otwórz**.

    ![Pierwsze uruchomienie usługi Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. Zostanie otwarta strona Witamy w usłudze Azure Cloud Shell. Wybierz powłokę Bash lub program PowerShell.

    ![Witamy w usłudze Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >W tym przykładzie jest wybrana powłoka Bash (Linux).

1. Jeśli nie jest jeszcze skonfigurowane konto magazynu platformy Azure, zostanie wyświetlony poniższy ekran. Wybierz pozycję **Utwórz magazyn**.

    ![Nie masz zainstalowanego magazynu](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Usługa Azure Cloud Shell zostanie uruchomiona w wybranej wcześniej powłoce i wyświetli informacje dotyczące dysku w chmurze, który właśnie został utworzony dla Ciebie.

    ![Dysk w chmurze został utworzony](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Teraz możesz zamknąć usługę Cloud Shell.

1. Na pasku menu wybierz pozycję **Widok** > **Paleta poleceń** > **Azure Terraform: init**.

    ![Narzędzie Terraform zostało pomyślnie zainicjowane](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Wizualizacja planu

Wcześniej w ramach tego samouczka zostało zainstalowane oprogramowanie GraphViz. Korzystając z oprogramowania GraphViz, narzędzie Terraform może wygenerować wizualną reprezentację konfiguracji lub planu wykonania. Rozszerzenie Azure Terraform dla programu Visual Studio Code implementuje tę funkcję za pomocą polecenia *visualize*.

- Na pasku menu wybierz pozycję **Widok > Paleta poleceń > Azure Terraform: Visualize**.

    ![Wizualizacja planu](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Uruchamianie polecenia *plan* narzędzia Terraform

Polecenie *plan* narzędzia Terraform służy do sprawdzania, czy plan wykonania zestawu zmian będzie działał w zamierzony sposób.

>[!NOTE]
>Polecenie *plan* narzędzia Terraform nie wprowadza żadnych zmian w rzeczywistych zasobach platformy Azure. Aby naprawdę wprowadzić zmiany zawarte w planie, używamy polecenia *apply* narzędzia Terraform.

- Na pasku menu wybierz pozycję **Widok** > **Paleta poleceń** > **Azure Terraform: plan**.

    ![Terraform plan](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Uruchamianie polecenia *apply* narzędzia Terraform

Gdy wyniki wykonania polecenia *plan* narzędzia Terraform są zadowalające, możesz uruchomić polecenie *apply*.

1. Na pasku menu wybierz pozycję **Widok** > **Paleta poleceń** > **Azure Terraform: apply**.

    ![Terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Wprowadź polecenie `yes`.

    ![Terraform apply yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Sprawdzanie, czy został wykonany plan narzędzia Terraform

Aby sprawdzić, czy nowa grupa zasobów platformy Azure została pomyślnie utworzona:

1. Otwórz witrynę Azure Portal.

1. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.

    ![Sprawdzanie nowego zasobu](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Nowa grupa zasobów powinna być wyświetlana w kolumnie **NAME**.

>[!NOTE]
>Na razie możesz zostawić otwarte okno witryny Azure Portal. Będziemy go używać w następnym kroku.

### <a name="run-terraform-destroy-command"></a>Uruchamianie polecenia *destroy* narzędzia Terraform

1. Na pasku menu wybierz pozycję **Widok** > **Paleta poleceń** > **Azure Terraform: destroy**.

    ![Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Wprowadź polecenie *yes*.

    ![Terraform destroy yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Sprawdzanie, czy grupa zasobów została zniszczona

Aby upewnić się, że narzędzie Terraform pomyślnie zniszczyło nową grupę zasobów:

1. Wybierz pozycję **Odśwież** na stronie **Grupy zasobów** w witrynie Azure Portal.

1. Grupa zasobów już nie będzie wyświetlana.

    ![Sprawdzanie, czy grupa zasobów została zniszczona](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Ćwiczenie 2. Moduł *compute* narzędzia Terraform

W tym ćwiczeniu dowiesz się, jak załadować moduł *compute* narzędzia Terraform do środowiska programu Visual Studio Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Klonowanie modułu terraform-azurerm-compute

1. Za pomocą [tego linku](https://github.com/Azure/terraform-azurerm-compute) uzyskaj dostęp do modułu Terraform Azure Rm Compute w witrynie GitHub.

1. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).

    ![Clone or download (Sklonuj lub pobierz)](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >W tym przykładzie nasz folder otrzymał nazwę *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>Otwieranie folderu w programie Visual Studio Code

1. Uruchom program Visual Studio Code.

1. Na pasku menu wybierz pozycję **Plik > Otwórz folder**, a następnie przejdź do folderu utworzonego w poprzednim kroku i zaznacz go.

    ![Folder terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Inicjowanie narzędzia Terraform

Zanim zaczniesz używać poleceń narzędzia Terraform z poziomu programu Visual Studio Code, pobierz wtyczki dla dwóch dostawców platformy Azure: random i azurerm.

1. W okienku terminalu środowiska IDE programu Visual Studio Code wprowadź polecenie `terraform init`.

    ![Polecenie terraform init](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Wprowadź polecenie `az login`, naciśnij klawisz **<Enter** i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

### <a name="module-test-lint"></a>Test modułu: *lint*

1. Na pasku menu wybierz pozycję **Widok > Paleta poleceń > Azure Terraform: Execute Test**.

1. Z listy opcji typu testu wybierz pozycję **lint**.

    ![Wybieranie typu testu](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Po wyświetleniu potwierdzenia wybierz pozycję **OK** i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

    ![Czy chcesz otworzyć usługę CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Podczas wykonywania testu **lint** lub **end to end** platforma Azure używa usługi kontenera do aprowizowania maszyny testowej w celu wykonania rzeczywistego testu. Z tego powodu na zwrócenie wyników testu zazwyczaj trzeba poczekać kilka minut.

Po kilku chwilach w okienku terminalu zostanie wyświetlona lista podobna do tego przykładu:

![Wyniki testu lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="test-the-module"></a>Testowanie modułu

1. Na pasku menu wybierz pozycję **Widok > Paleta poleceń > Azure Terraform: Execute Test**.

1. Z listy opcji typu testu wybierz pozycję **end to end**.

    ![Wybieranie typu testu](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Po wyświetleniu potwierdzenia wybierz pozycję **OK** i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

    ![Czy chcesz otworzyć usługę CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Podczas wykonywania testu **lint** lub **end to end** platforma Azure używa usługi kontenera do aprowizowania maszyny testowej w celu wykonania rzeczywistego testu. Z tego powodu na zwrócenie wyników testu zazwyczaj trzeba poczekać kilka minut.

Po kilku chwilach w okienku terminalu zostanie wyświetlona lista podobna do tego przykładu:

![Wyniki testu](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Lista modułów narzędzia Terraform dostępnych dla platformy Azure (i innych obsługiwanych dostawców)](https://registry.terraform.io/)