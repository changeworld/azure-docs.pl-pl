---
title: Rozszerzenia kodu Azure Terraform VS | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się, jak zainstalować i korzystać z rozszerzeniem programu Terraform w programie Visual Studio Code.
keywords: Narzędzie terraform, metodyki devops, maszyna wirtualna, azure
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190230"
---
# <a name="azure-terraform-vs-code-extension"></a>Rozszerzenie Azure Terraform programu VS Code

Rozszerzenia Microsoft Azure Terraform Visual Studio Code (VS Code) pozwala zwiększyć produktywność deweloperów podczas tworzenia, testowania i używania programu Terraform na platformie Azure. Rozszerzenie udostępnia obsługę poleceń narzędzia Terraform, wizualizacja wykresu zasobów i CloudShell integracji programu VS Code.

## <a name="what-you-do"></a>Co należy zrobić

- Zainstaluj plik wykonywalny programu HashiCorp Terraform typu open source na swojej maszynie.
- W lokalnej instalacji programu VS Code, należy zainstalować rozszerzenia kodu programu VS Terraform na platformie Azure.

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym samouczku dowiesz się:

- Jak Terraform można zautomatyzować i uprościć Inicjowanie obsługi administracyjnej usług platformy Azure.
- Jak zainstalować i używać rozszerzenia kodu programu VS Terraform firmy Microsoft dla usług platformy Azure.
- Jak używać programu VS Code do zapisu, planowanie i wykonywanie programu Terraform planów.

## <a name="what-you-need"></a>Co jest potrzebne

- Komputer z systemem Windows 10, Linux lub macOS 10.10 +.
- [Program Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US)
- Aktywna subskrypcja platformy Azure. [Aktywowanie bezpłatnej 30-dniowej wersji próbnej Microsoft Azure konto](https://azure.microsoft.com/free/).
- Instalacja [Terraform](https://www.terraform.io/) narzędzia open source na komputerze lokalnym.
  
## <a name="prepare-your-dev-environment"></a>Przygotowywanie środowiska deweloperskiego

### <a name="install-git"></a>Zainstaluj oprogramowanie Git

Aby ukończyć ćwiczeń opisanych w artykule, musisz [Zainstaluj oprogramowanie Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Zainstaluj narzędzia HashiCorp Terraform

Postępuj zgodnie z instrukcjami na HashiCorp [zainstalować narzędzia Terraform](https://www.terraform.io/intro/getting-started/install.html) strony sieci Web, która obejmuje:

- Pobieranie programu Terraform
- Instalowanie programu Terraform
- Weryfikowanie programu Terraform został poprawnie zainstalowany

>[!Tip]
>Pamiętaj wykonać instrukcje dotyczące ustawiania zmiennej PATH systemu.

### <a name="install-nodejs"></a>Instalowanie środowiska Node.js

Aby użyć narzędzia Terraform w usłudze Cloud Shell, musisz [zainstalować środowisko Node.js](https://nodejs.org/) 6.0 lub nowszym.

>[!NOTE]
>Aby sprawdzić, czy zainstalowano program Node.js, Otwórz okno terminala i wpisz: `node -v`

### <a name="install-graphviz"></a>Zainstaluj GraphViz

Aby za pomocą narzędzia Terraform wizualizować funkcji, musisz [zainstalować GraphViz](http://graphviz.org/).

>[!NOTE]
>Aby sprawdzić, czy zainstalowano GraphViz, Otwórz okno terminala i wpisz: `dot -V`

### <a name="install-the-azure-terraform-vs-code-extension"></a>Zainstaluj rozszerzenie Azure Terraform programu VS Code:

1. Uruchom program VS Code.
2. Kliknij pozycję **rozszerzenia** ikony.

    ![Przycisk rozszerzenia](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. Użyj **rozszerzenia usługi wyszukiwania w witrynie Marketplace** pole tekstowe, aby wyszukać rozszerzenia Azure Terraform:

    ![Wyszukiwanie rozszerzeń programu VS Code w witrynie Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

4. Kliknij pozycję **Zainstaluj**.

    >[!NOTE]
    >Po kliknięciu **zainstalować** rozszerzenia Azure Terraform programu VS Code automatycznie zainstaluje rozszerzenie Azure Account. Konto platformy Azure jest plikiem zależności dla rozszerzenia Azure Terraform, używanym do wykonywania uwierzytelnień subskrypcji platformy Azure i rozszerzenia kodu dotyczących platformy Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Sprawdź, czy zainstalowano rozszerzenia programu Terraform w programie Visual Studio Code

1. Kliknij ikonę rozszerzenia.
2. Typ `@installed` w polu tekstowym wyszukiwania.

    ![Zainstalowane rozszerzenia](media/terraform-vscode-extension/tf-installed-extensions.png)

Rozszerzenie Azure Terraform pojawi się na liście zainstalowanych rozszerzeń.

![Zainstalowane rozszerzenia programu Terraform](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Teraz możesz uruchomić wszystkie obsługiwane polecenia narzędzia Terraform w danym środowisku usługi Cloud Shell z programu VS Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Ćwiczenie 1: Terraform podstawowe polecenia przewodnik

W tym ćwiczeniu utworzysz i wykonać podstawowe plik konfiguracji programu Terraform, która aprowizuje nową grupę zasobów platformy Azure.

### <a name="prepare-a-test-plan-file"></a>Przygotowanie pliku planu testu

1. W programie VS Code wybierz **Plik > Nowy plik** z paska Menu.
2. Przejdź do [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) i skopiować kod w **przykład użycia** blok kodu:
3. Wklej skopiowany kod do nowego pliku utworzonego w programie VS Code.

    ![Wklej kod przykład użycia](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Możesz zmienić **nazwa** wartości grupy zasobów, ale musi być unikatowa dla Twojej subskrypcji platformy Azure.

4. Na pasku Menu wybierz **Plik > Zapisz jako**.
5. W **Zapisz jako** okno dialogowe, przejdź do lokalizacji, a następnie kliknij przycisk **nowy folder**. (Zmień nazwę nowego folderu na coś bardziej opisowe niż *nowy folder*.)

    >[!NOTE]
    >W tym przykładzie folder nosi nazwę TERRAFORM-TEST-PLAN.

6. Upewnij się, jest wyróżniona nowego folderu (wybrane) a następnie kliknij przycisk **Otwórz**.
7. W **Zapisz jako** okno dialogowe, zmienić domyślną nazwę pliku do *main.tf*.

    ![Zapisz jako main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

8. Kliknij pozycję **Zapisz**.
- Na pasku Menu wybierz **Plik > Otwórz Folder**. Przejdź do, a następnie wybierz nowy folder, który został utworzony.

### <a name="run-terraform-init-command"></a>Uruchom narzędzie Terraform *init* polecenia

1. Uruchom program Visual Studio Code.
2. Na pasku Menu programu VS Code wybierz **Plik > Otwórz Folder...**  a następnie znajdź i wybierz swoje *main.tf* pliku.

    ![plik main.TF](media/terraform-vscode-extension/tf-main-tf.png)

3. Na pasku Menu wybierz **Widok > Paleta poleceń... > Azure Terraform: Init**.
4. Po kilku chwilach po wyświetleniu monitu *ma Otwórz usługę Cloud Shell?* Kliknij przycisk **OK**.

    ![Czy chcesz otworzyć usługę Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. Przy pierwszym uruchomieniu usługi Cloud Shell z nowego folderu, użytkownik jest proszony o skonfigurowanie aplikacji sieci web. Kliknij przycisk **Otwórz**.

    ![Pierwsze uruchomienie usługi Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. Zapraszamy do strony usługi Azure Cloud Shell zostanie otwarta. Wybierz powłoki Bash lub programu PowerShell.

    ![Witamy w usłudze Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >W tym przykładzie wybrano powłoki Bash (Linux).

7. Jeśli konto magazynu platformy Azure nie mają już skonfigurowany, pojawi się następujący ekran. Kliknij przycisk **Utwórz magazyn**.

    ![Nie masz zainstalowanego magazynu](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Usługa Azure Cloud Shell spowoduje uruchomienie w powłoce wcześniej wybrane i wyświetla informacje o dysku w chmurze, że właśnie utworzony dla Ciebie.

    ![Dysku w chmurze została utworzona.](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. Teraz zakończysz usłudze Cloud Shell
10. Na pasku Menu wybierz **widoku** > **paletę poleceń** > **Azure Terraform: init**.

    ![Pomyślnie zainicjowano programu Terraform](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Wizualizuj planu

Wcześniej w tym samouczku zainstalowano GraphViz. Terraform służy GraphViz do generowania wizualnej reprezentacji konfiguracji lub wykonanie planu. Rozszerzenie Azure Terraform programu VS Code implementuje tę funkcję za pośrednictwem *wizualizować* polecenia.

- Z **pasek Menu**, wybierz opcję**Widok > paletę poleceń > Azure Terraform: wizualizowanie**.

    ![Wizualizuj planu](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Uruchom narzędzie Terraform *plan* polecenia

Narzędzie Terraform *plan* polecenie służy do Sprawdź, czy plan wykonywania zestaw zmian będzie wykonywał zamierzonym.

>[!NOTE]
>Narzędzie Terraform *plan* nie wprowadzać żadnych zmian z zasobami rzeczywiste platformy Azure. Aby naprawdę coś zmiany zawarte w planie, używamy programu Terraform *zastosować* polecenia.

- Na pasku Menu wybierz **widoku** > **paletę poleceń** > **Azure Terraform: plan**.

    ![Plan programu Terraform](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Uruchom narzędzie Terraform *zastosować* polecenia

Po jest zadowolony z wyników narzędzia Terraform *plan*, możesz uruchomić *zastosować* polecenia.

1. Na pasku Menu wybierz **widoku** > **paletę poleceń** > **Azure Terraform: Zastosuj**.

    ![Zastosowanie programu Terraform](media/terraform-vscode-extension/tf-terraform-apply.png)

2. Typ *tak*.

    ![Narzędzie Terraform zastosować tak](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Sprawdź, czy wykonano planu narzędzia Terraform

Aby zobaczyć, jeśli pomyślnie utworzono nowej grupy zasobów platformy Azure:

1. Otwórz witrynę Azure Portal.
2. Wybierz **grup zasobów** w okienku nawigacji po lewej stronie.

    ![Sprawdź nowy zasób](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Nowej grupy zasobów powinny zostać wyświetlone w **nazwa** kolumny.

>[!NOTE]
>Można pozostawić okna w witrynie Azure Portal otwórz teraz; Firma Microsoft będzie używać go w następnym kroku.

### <a name="run-terraform-destroy-command"></a>Uruchom narzędzie Terraform *zniszczyć* polecenia

1. Na pasku Menu wybierz **widoku** > **paletę poleceń** > **Azure Terraform: zniszczyć**.

    ![Zniszcz programu Terraform](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. Typ *tak*.

    ![Narzędzie Terraform zniszczyć tak](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Sprawdź, czy dana grupa zasobów została zniszczona.

Aby upewnić się, że Terraform pomyślnie zniszczone nowej grupy zasobów:

1. Kliknij przycisk **Odśwież** w witrynie Azure portal *grup zasobów* strony.
2. Grupa zasobów już nie zostaną wyświetlone.

    ![Upewnij się, że grupa zasobów została zniszczona.](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Ćwiczenie 2: Terraform *obliczenia* modułu

W tym ćwiczeniu dowiesz się, jak załadować Terraform *obliczenia* modułu do środowiska programu VS Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Klonowanie modułu programu terraform azurerm wystąpień obliczeniowych

1. Użyj [ten link](https://github.com/Azure/terraform-azurerm-compute) do dostępu do modułu obliczeniowego programu Terraform Azure Rm w witrynie GitHub.
2. Kliknij przycisk **klonowania lub pobierania**.

    ![Klonuj lub Pobierz](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >W tym przykładzie nosiła nazwę folderu naszych *terraform-azurerm-compute*.

### <a name="open-the-folder-in-vs-code"></a>Otwórz folder w programie VS Code

1. Uruchom program Visual Studio Code.
2. Z **pasek Menu**, wybierz opcję **Plik > Otwórz Folder** i przejdź do i wybierz folder utworzony w poprzednim kroku.

    ![folder programu terraform azurerm wystąpień obliczeniowych](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Inicjowanie programu Terraform

Przed rozpoczęciem używania poleceń programu Terraform z programu VS Code, możesz pobrać dodatków plug-in dla dwóch dostawców platformy Azure: losowych i azurerm.

1. W okienku terminalu środowiska IDE programu VS Code należy wpisać: `terraform init`

    ![polecenie init programu terraform](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. Typ `az login` i postępuj zgodnie z wyświetlanymi instrukcjami.

### <a name="module-test-lint"></a>Test modułu: *lint*

1. Z **pasek Menu**, wybierz opcję **Widok > paletę poleceń > Azure Terraform: wykonanie testu**.
2. Wybierz z listy opcji typu testu **lint**.

    ![Wybierz typ testu](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. Po wyświetleniu monitu *czy chcesz otworzyć CloudShell?* kliknij **OK** i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami.

    ![Czy chcesz otworzyć CloudShell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Podczas wykonywania jednej **lint** lub **typu end to end** testu, platforma Azure używa usługi kontenera do aprowizowania maszyny testowej do wykonania rzeczywistego testu. Z tego powodu wyników testu zazwyczaj może potrwać kilka minut, które mają zostać zwrócone.

Po kilku chwilach zostaną wyświetlone na liście w okienku terminalu podobne do poniższego przykładu:

![Wyniki testu lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Test modułu: *end-to-end*

1. Z **pasek Menu**, wybierz opcję **Widok > paletę poleceń > Azure Terraform: wykonanie testu**.
2. Wybierz z listy opcji typu testu **typu end to end**.

    ![Wybierz typ testu](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. Jeśli pojawi się pytanie *czy chcesz otworzyć CloudShell?* kliknij **OK** i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami.

    ![Czy chcesz otworzyć CloudShell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Podczas wykonywania jednej **lint** lub **typu end to end** testu, platforma Azure używa usługi kontenera do aprowizowania maszyny testowej do wykonania rzeczywistego testu. Z tego powodu wyników testu zazwyczaj może potrwać kilka minut, które mają zostać zwrócone.

Po kilku chwilach zostaną wyświetlone na liście w okienku terminalu podobne do poniższego przykładu:

![Wyniki testu end-to-end](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Kolejne kroki

Jak już wspomniano, niektóre sposoby Terraform może uprościć aprowizacja usług platformy Azure z programu Visual Studio Code. Teraz można przejrzeć niektóre z tych zasobów:
- [Rejestr modułów Terraform](https://registry.terraform.io/) zawiera listę wszystkich dostępnych modułów Terraform dla platformy Azure i innych obsługiwanych dostawców.

Dla każdego z tych modułów podano następujące informacje:

- Opis modułu ogólne możliwości i jego właściwości
- Przykład użycia
- Konfiguracje, które pokazują, jak tworzyć, uruchamiać, testów i przetestować każdego modułu na lokalnym komputerze deweloperskim
- Plik Dockerfile umożliwia kompilowanie i uruchamianie środowiska deweloperskiego modułu lokalnie.
