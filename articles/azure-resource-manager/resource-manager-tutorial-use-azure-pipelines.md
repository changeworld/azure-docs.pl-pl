---
title: Ciągła Integracja z usługą Azure potoki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak stale tworzenia, testowania i wdrażania szablonów usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 85dc0476da12bea64610b6910b0682fef00f4b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057792"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Samouczek: Ciągła integracja szablonów usługi Azure Resource Manager za pomocą potoków usługi Azure

Dowiedz się, jak za pomocą potoków Azure ciągłe tworzenie i wdrażanie projektów szablonu usługi Azure Resource Manager.

DevOps platformy Azure udostępnia usługi dla deweloperów do obsługi zespołów do planowania pracy, współpraca nad tworzenia kodu i tworzenia i wdrażania aplikacji. Deweloperzy mogą pracować w chmurze dzięki usługom DevOps platformy Azure. DevOps platformy Azure udostępnia zintegrowane funkcje, które mogą uzyskać dostęp przez przeglądarkę sieci web lub klienta środowiska IDE. Potok usługi Azure jest jednym z tych funkcji. Potoki usługi Azure jest w pełni wyposażone, ciągłej integracji (CI) i ciągłe dostarczanie (CD). Przy użyciu preferowanego dostawcy Git i lepiej można wdrożyć na większość ważniejszych usług w chmurze. Następnie można zautomatyzować, kompilacji, testowania i wdrażania kodu do Microsoft Azure, Google Cloud Platform i usług Amazon Web Services.

Ten samouczek jest przeznaczony dla usługi Azure Resource Manager szablon deweloperów, którzy są nowe usługom DevOps platformy Azure i potoków usługi Azure. Jeśli już znasz z usług GitHub i metodyki DevOps, możesz przejść do [utworzyć potok](#create-a-pipeline).

> [!NOTE]
> Wybierz nazwę projektu. Podczas wykonywania kroków samouczka, należy zamienić dowolną **AzureRmPipeline** z Twoją nazwą projektu.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowywanie repozytorium GitHub
> * Tworzenie projektu DevOps platformy Azure
> * Instrukcje tworzenia potoku usługi Azure
> * Weryfikacja wdrożenia potoku
> * Aktualizowanie szablonu i ponowne wdrażanie
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* **Konto usługi GitHub**, której użyjesz go do utworzenia repozytorium szablonów. Jeśli nie masz, możesz to zrobić [utworzyć jedno za darmo](https://github.com). Aby uzyskać więcej informacji o korzystaniu z repozytoriów GitHub, zobacz [repozytoriów GitHub kompilacji](/azure/devops/pipelines/repos/github).
* **Zainstaluj oprogramowanie Git**. Korzysta z tego samouczka instrukcji *powłoki Git Bash* lub *powłoki Git Shell*. Aby uzyskać instrukcje, zobacz [zainstalować Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Organizacja DevOps platformy Azure**. Jeśli nie masz, możesz go utworzyć za darmo. Zobacz [tworzenie organizacji lub kolekcji projektów]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **[Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem Narzędzia Menedżera zasobów**. Zobacz [Instalowanie rozszerzenia](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-a-github-repository"></a>Przygotowywanie repozytorium GitHub

GitHub jest używany do przechowywania kodem źródłowym projektu, łącznie z szablonów usługi Resource Manager. W przypadku innych obsługiwanych repozytoriów, zobacz [repozytoriów obsługiwane przez usługi Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Tworzenie repozytorium usługi GitHub

Jeśli nie masz konta usługi GitHub, zobacz [wymagania wstępne](#prerequisites).

1. Zaloguj się do [GitHub](https://github.com).
2. Wybierz obraz konta w prawym górnym rogu, a następnie wybierz **repozytoriów**.

    ![Platformy Azure potoków Azure metodyki DevOps Azure Resource Manager utworzyć repozytorium GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Wybierz **New**, zielony przycisk.
1. W **nazwę repozytorium**, wprowadź nazwę repozytorium.  Na przykład **repozytorium AzureRmPipeline**. Pamiętaj, aby zamienić dowolną **AzureRmPipeline** z Twoją nazwą projektu. Możesz wybrać opcję **publicznych** lub **prywatnej** dla pośrednictwa w tym samouczku. A następnie wybierz **tworzenia repozytorium**.
1. Zanotuj adres URL. Adres URL repozytorium jest następujący format:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

To repozytorium jest określany jako *repozytorium zdalnego*. Każdy z deweloperów o tym samym projekcie można sklonować swoje własne *lokalnego repozytorium*i scalania zmian do repozytorium zdalnego.

### <a name="clone-the-remote-repository"></a>Klonowanie zdalnego repozytorium

1. Otwórz program powłoki Git Shell lub powłoki Git Bash.  Zobacz [Wymagania wstępne](#prerequisites).
1. Sprawdź bieżącego folderu **github**.
1. Uruchom następujące polecenie:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Zastąp **[YourAccountName]** nazwa konta usługi GitHub i Zastąp **[YourGitHubRepositoryName]** nazwę repozytorium utworzony w poprzedniej procedurze.

    Poniższych zrzutach ekranu przedstawiono przykład.

    ![Platformy Azure potoków Azure metodyki DevOps Azure Resource Manager utworzyć powłoki bash w usłudze GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

**CreateAzureStorage** folder to folder, w którym jest przechowywany szablon. **Pwd** polecenie wyświetla w ścieżce folderu. Ścieżka jest, gdzie zapisać szablon, aby w poniższej procedurze.

### <a name="download-a-quickstart-template"></a>Pobierz szablon szybkiego startu

Zamiast tworzenia szablonu, możesz pobrać [szablon szybkiego startu]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Ten szablon tworzy konto usługi Azure Storage.

1. Otwórz program Visual Studio code. Zobacz [Wymagania wstępne](#prerequisites).
2. Otwórz szablon przy użyciu następującego adresu URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Zapisz plik jako **azuredeploy.json** do **CreateAzureStorage** folderu. Nazwa folderu i nazwa pliku są używane, są one w potoku.  Jeśli zmienisz te nazwy, należy zaktualizować nazwy używane w potoku.

### <a name="push-the-template-to-the-remote-repository"></a>Szablon wypychania do repozytorium zdalnego

Azuredeploy.json dodano w repozytorium lokalnym. Następnie Przekaż szablon do repozytorium zdalnego.

1. Otwórz *powłoki Git Shell* lub *powłoki Git Bash*, jeśli nie jest otwarty.
1. Zmień katalog na folder CreateAzureStorage w lokalnym repozytorium.
1. Sprawdź **azuredeploy.json** plik znajduje się w folderze.
1. Uruchom następujące polecenie:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Możesz otrzymać ostrzeżenie dotyczące LF. Możesz zignorować to ostrzeżenie. **główny** to gałąź główna.  Zwykle tworzysz gałąź dla każdej aktualizacji. Aby uprościć samouczka, możesz użyć głównej gałęzi bezpośrednio.
1. Przejdź do repozytorium GitHub z poziomu przeglądarki.  Adres URL jest  **https://github.com/ [YourAccountName] / [YourGitHubRepository]** . Zostanie wyświetlona **CreateAzureStorage** folder i **Azuredeploy.json** wewnątrz folderu.

Do tej pory zostały utworzone z repozytorium GitHub i przekazany szablon do repozytorium.

## <a name="create-a-devops-project"></a>Utwórz projekt DevOps

Organizacja DevOps jest wymagana, przed przejściem do następnej procedury.  Jeśli nie masz, zobacz [wymagania wstępne](#prerequisites).

1. Zaloguj się do [Azure DevOps](https://dev.azure.com).
1. Wybierz organizację DevOps z lewej strony.

    ![Platformy Azure potoków Azure metodyki DevOps Azure Resource Manager, Utwórz projekt DevOps platformy Azure](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Wybierz pozycję **Create project** (Utwórz projekt). Jeśli nie masz żadnych projektów, Utwórz stronę projektu zostanie automatycznie otwarty.
1. Wprowadź następujące wartości:

    * **Nazwa projektu**: Wprowadź nazwę projektu. Można użyć nazwy projektu, wybrany przez Ciebie na początku tego samouczka.
    * **Kontrola wersji**: Wybierz **Git**. Może być konieczne rozwinąć **zaawansowane** się **kontroli wersji**.

    Użyj wartości domyślnej dla innych właściwości.
1. Wybierz pozycję **Create project** (Utwórz projekt).

Tworzenie połączenia z usługą, który służy do wdrażania projektów na platformie Azure.

1. Wybierz **ustawienia projektu** u dołu menu po lewej stronie.
1. Wybierz **obsługują połączenia** w obszarze **potoki**.
1. Wybierz **połączenia nowej usługi**, a następnie wybierz pozycję **AzureResourceManager**.
1. Wprowadź następujące wartości:

    * **Nazwa połączenia**: Wprowadź nazwę połączenia. Na przykład **połąc AzureRmPipeline**. Zanotuj tę nazwę, potrzebna jest nazwa podczas tworzenia potoku.
    * **Zakres poziom**: Wybierz **subskrypcji**.
    * **Subskrypcja**: Wybierz swoją subskrypcję.
    * **Grupa zasobów**: Pozostaw to pole puste.
    * **Zezwalaj na wszystkie potoki, aby korzystać z tego połączenia**. (wybrane)
1. Kliknij przycisk **OK**.

## <a name="create-a-pipeline"></a>Tworzenie potoku

Do tej pory zostały wykonane następujące zadania.  Jeśli pominiesz przedstawione w poprzednich sekcjach, ponieważ jesteś zaznajomiony z usługi GitHub i metodyki DevOps, należy wykonać zadania przed kontynuowaniem.

- Tworzenie repozytorium usługi GitHub, a następnie Zapisz [ten szablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) do **CreateAzureStorage** folderu w repozytorium.
- Utwórz projekt DevOps i tworzenie połączenia z usługą Azure Resource Manager.

Aby utworzyć potok, kończąc je krokiem wdrażania szablonu:

1. Wybierz **potoki** menu po lewej stronie.
1. Wybierz **nowy potok**.
1. Z **Connect** zaznacz **GitHub**. Jeśli pojawi się monit, wprowadź swoje poświadczenia usługi GitHub, a następnie postępuj zgodnie z instrukcjami. Jeśli zostanie wyświetlony następujący ekran, wybierz **tylko wybranym repozytoriów**i sprawdź repozytorium znajduje się na liście przed wybraniem **zatwierdzić i zainstaluj**.

    ![Platformy Azure potoków Azure metodyki DevOps Azure Resource Manager do wybrania repozytoriów](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Z **wybierz** , a następnie wybierz repozytorium.  Nazwa domyślna to **[YourAccountName] / [YourGitHubRepositoryName]** .
1. Z **Konfiguruj** zaznacz **potoku Starter**. Pokazuje **azure pipelines.yml** plik potoku z dwóch etapów skryptu.
1. Zastąp **kroki** sekcji przy użyciu poniższego kodu YAML:

    ```yaml
    steps:
    - task: AzureResourceGroupDeployment@2
      inputs:
        azureSubscription: '[YourServiceConnectionName]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Jest ona wyglądać następująco:

    ![Usługa Azure yaml potoków metodyki DevOps platformy Azure Resource Manager w platformy Azure](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Wprowadź następujące zmiany:

    * **azureSubscription**: Zaktualizuj wartość za pomocą połączenia z usługą utworzony w poprzedniej procedurze.
    * **Akcja**: **utworzyć lub zaktualizować grupy zasobów** Akcja wykonuje akcje 2 - 1. Utwórz grupę zasobów, jeśli podano nazwę nowej grupy zasobów; 2. Wdróż szablon określony.
    * **resourceGroupName**: Określ nazwę nowej grupy zasobów. Na przykład **AzureRmPipeline-rg**.
    * **Lokalizacja**: Określ lokalizację grupy zasobów.
    * **templateLocation**: gdy **połączony artefakt** jest określony, zadanie szuka pliku szablonu, bezpośrednio z połączonych repozytorium.
    * **csmFile** jest ścieżka do pliku szablonu. Nie trzeba określać plik parametrów szablonu, ponieważ wszystkie parametry zdefiniowane w szablonie mają przypisane wartości domyślne.

    Aby uzyskać więcej informacji o zadaniu, zobacz [zadania wdrożenia grupy zasobów platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
1. Wybierz polecenie **Zapisz i uruchom**.
1. Wybierz **Zapisz i uruchom** ponownie. Kopia pliku YAML, są zapisywane w repozytorium połączonych. Widać pliku YAML, przejdź do repozytorium.
1. Upewnij się, że potok jest wykonywane pomyślnie.

    ![Usługa Azure yaml potoków metodyki DevOps platformy Azure Resource Manager w platformy Azure](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów. Nazwa jest określona w pliku YAML potoku.  Zostanie wyświetlona jedno konto magazynu utworzone.  Nazwa konta magazynu, który rozpoczyna się od **przechowywania**.
1. Wybierz nazwę konta magazynu, aby go otworzyć.
1. Wybierz **właściwości**. Zwróć uwagę **jednostki SKU** jest **Standard_LRS**.

    ![Usługa Azure potoków metodyki DevOps platformy Azure Resource Manager w Azure portalu weryfikacji.](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Aktualizacja i ponowne wdrażanie

Podczas aktualizacji szablonu, a następnie Wypchnij zmiany do repozytorium zdalnego, potok automatycznie aktualizuje zasobów, konto magazynu w tym przypadku.

1. Otwórz **azuredeploy.json** ze swojego lokalnego repozytorium w programie Visual Studio Code.
1. Aktualizacja **defaultValue** z **storageAccountType** do **Standard_GRS**. Zobacz poniższy zrzut ekranu:

    ![Platformy Azure potoków Azure metodyki DevOps Azure Resource Manager zaktualizować yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Zapisz zmiany.
1. Wypchnij zmiany do repozytorium zdalnego, uruchamiając następujące polecenia z usługi Git/powłoki Bash.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Pierwsze polecenie synchronizuje lokalnego repozytorium z repozytorium zdalnym. Należy pamiętać, że plik YAML potoku została dodana do repozytorium zdalnego.

    Z głównej gałęzi repozytorium zdalnego zaktualizowane potok jest uruchamiany ponownie.

Aby sprawdzić zmiany, możesz sprawdzić jednostki SKU konta magazynu.  Zobacz [Weryfikacja wdrożenia](#verify-the-deployment).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

Można również usunąć repozytorium GitHub i projekt DevOps platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzysz potok DevOps platformy Azure do wdrożenia szablonu usługi Azure Resource Manager. Aby dowiedzieć się, jak wdrażać zasoby platformy Azure w wielu regionach i jak stosować praktyki bezpiecznego wdrażania, zobacz

> [!div class="nextstepaction"]
> [Używanie usługi Azure Deployment Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
