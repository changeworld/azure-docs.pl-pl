---
title: Ciągła integracja z użyciem usługi Azure Pipelines
description: Dowiedz się, jak ciągle kompilować, testować i wdrażać szablony Azure Resource Manager.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2dfe17b99c1a9b1130695c8e5cd9c65ca7681d35
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472450"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Samouczek: Ciągła integracja szablonów Azure Resource Manager z Azure Pipelines

Dowiedz się, jak używać Azure Pipelines do ciągłego kompilowania i wdrażania projektów szablonu Azure Resource Manager.

Usługa Azure DevOps zapewnia usługi deweloperskie do obsługi zespołów do planowania pracy, współpracy nad programowaniem kodu oraz kompilowania i wdrażania aplikacji. Deweloperzy mogą korzystać z chmury, korzystając z Azure DevOps Services. Usługa Azure DevOps udostępnia zintegrowany zestaw funkcji, do których można uzyskać dostęp za pomocą przeglądarki sieci Web lub klienta IDE. Potok platformy Azure jest jedną z tych funkcji. Azure Pipelines to w pełni oferowana usługa ciągłej integracji (CI) i ciągłe dostarczanie (CD). Współpracuje z preferowanym dostawcą usługi git i można wdrożyć w większości najważniejszych usług w chmurze. Następnie można zautomatyzować kompilowanie, testowanie i wdrażanie kodu w celu Microsoft Azure, Google Cloud Platform lub Amazon Web Services.

Ten samouczek jest przeznaczony dla deweloperów szablonów Azure Resource Manager, którzy są nowymi Azure DevOps Services i Azure Pipelines. Jeśli znasz już usługi GitHub i DevOps, możesz pominąć, aby [utworzyć potok](#create-a-pipeline).

> [!NOTE]
> Wybierz nazwę projektu. Podczas przechodzenia przez samouczek Zastąp dowolną **AzureRmPipeline** nazwą projektu.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowywanie repozytorium GitHub
> * Tworzenie projektu DevOps platformy Azure
> * Tworzenie potoku platformy Azure
> * Weryfikowanie wdrożenia potoku
> * Zaktualizuj szablon i Wdróż ponownie
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* **Konto usługi GitHub**, za pomocą którego można utworzyć repozytorium dla szablonów. Jeśli nie masz takiego konta, możesz [je utworzyć bezpłatnie](https://github.com). Aby uzyskać więcej informacji o korzystaniu z repozytoriów usługi GitHub, zobacz [Tworzenie repozytoriów GitHub](/azure/devops/pipelines/repos/github).
* **Zainstaluj oprogramowanie Git**. W tej instrukcji samouczka jest stosowana funkcja *git bash* lub *powłoka git*. Aby uzyskać instrukcje, zobacz [Instalowanie usługi git]( https://www.atlassian.com/git/tutorials/install-git).
* **Organizacja usługi Azure DevOps**. Jeśli go nie masz, możesz utworzyć go bezpłatnie. Zobacz [Tworzenie organizacji lub kolekcji projektów]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio Code z rozszerzeniem Menedżer zasobów Tools. [Aby utworzyć szablony Azure Resource Manager, zobacz temat używanie Visual Studio Code](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Przygotowywanie repozytorium GitHub

GitHub służy do przechowywania kodu źródłowego projektu, w tym Menedżer zasobów szablonów. W przypadku innych obsługiwanych repozytoriów zobacz [repozytoria obsługiwane przez usługę Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Tworzenie repozytorium GitHub

Jeśli nie masz konta usługi GitHub, zapoznaj się z tematem [wymagania wstępne](#prerequisites).

1. Zaloguj się do usługi [GitHub](https://github.com).
2. Wybierz swój obraz konta w prawym górnym rogu, a następnie wybierz **swoje repozytoria**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines Tworzenie repozytorium GitHub](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Wybierz pozycję **Nowy**, zielony przycisk.
1. W polu **Nazwa repozytorium**wprowadź nazwę repozytorium.  Na przykład **AzureRmPipeline-repozytorium**. Pamiętaj, aby zastąpić dowolną **AzureRmPipeline** nazwą projektu. Możesz wybrać opcję **publiczny** lub **prywatny** do przechodzenia przez ten samouczek. A następnie wybierz pozycję **Utwórz repozytorium**.
1. Zapisz adres URL. Adres URL repozytorium ma następujący format:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

To repozytorium jest określane jako *zdalne repozytorium*. Każdy deweloper tego samego projektu może sklonować własne *repozytorium lokalne*i scalić zmiany w repozytorium zdalnym.

### <a name="clone-the-remote-repository"></a>Klonowanie repozytorium zdalnego

1. Otwórz powłokę Git lub narzędzie git bash.  Zobacz [Wymagania wstępne](#prerequisites).
1. Sprawdź, czy bieżący folder jest w serwisie **GitHub**.
1. Uruchom następujące polecenie:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Zastąp ciąg **[YourAccountName]** nazwą konta usługi GitHub i Zastąp ciąg **[YourGitHubRepositoryName]** nazwą swojego repozytorium utworzoną w poprzedniej procedurze.

    Poniższy zrzut ekranu przedstawia przykład.

    ![Azure Resource Manager Azure DevOps Azure Pipelines tworzenia witryny GitHub bash](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

Folder **CreateAzureStorage** jest folderem, w którym przechowywany jest szablon. **PWD** polecenie wyświetla ścieżkę folderu. Ścieżka służy do zapisywania szablonu w poniższej procedurze.

### <a name="download-a-quickstart-template"></a>Pobierz szablon szybkiego startu

Zamiast tworzenia szablonu można pobrać [szablon szybkiego startu]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Ten szablon służy do tworzenia konta usługi Azure Storage.

1. Otwórz program Visual Studio Code. Zobacz [Wymagania wstępne](#prerequisites).
2. Otwórz szablon z następującym adresem URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Zapisz plik jako **azuredeploy. JSON** w folderze **CreateAzureStorage** . Nazwa folderu i nazwa pliku są używane, ponieważ znajdują się w potoku.  W przypadku zmiany tych nazw należy zaktualizować nazwy używane w potoku.

### <a name="push-the-template-to-the-remote-repository"></a>Wypychanie szablonu do repozytorium zdalnego

Plik azuredeploy. JSON został dodany do repozytorium lokalnego. Następnie Przekaż szablon do zdalnego repozytorium.

1. Otwórz *powłokę git* lub *git bash*, jeśli nie jest ona otwarta.
1. Zmień katalog na folder CreateAzureStorage w lokalnym repozytorium.
1. Sprawdź, czy plik **azuredeploy. JSON** znajduje się w folderze.
1. Uruchom następujące polecenie:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Może pojawić się ostrzeżenie dotyczące LF. Możesz zignorować to ostrzeżenie. **główny** jest gałęzią główną.  Tworzona jest zwykle gałąź dla każdej aktualizacji. Aby uprościć samouczek, należy bezpośrednio użyć gałęzi głównej.
1. Przejdź do repozytorium GitHub z przeglądarki.  Adres URL jest **https://github.com/ [YourAccountName]/[YourGitHubRepository]** . W folderze jest widoczny folder **CreateAzureStorage** i plik **Azuredeploy. JSON** .

Do tej pory utworzono repozytorium GitHub i przekazano szablon do repozytorium.

## <a name="create-a-devops-project"></a>Tworzenie projektu DevOps

Aby można było wykonać następną procedurę, wymagana jest organizacja DevOps.  Jeśli go nie masz, zobacz [wymagania wstępne](#prerequisites).

1. Zaloguj się do [usługi Azure DevOps](https://dev.azure.com).
1. Wybierz organizację DevOps z lewej strony.

    ![Azure Resource Manager Azure DevOps Azure Pipelines tworzenia projektu DevOps platformy Azure](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Wybierz pozycję **Create project** (Utwórz projekt). Jeśli nie masz żadnych projektów, Strona Tworzenie projektu zostanie otwarta automatycznie.
1. Wprowadź następujące wartości:

    * **Nazwa projektu**: Wprowadź nazwę projektu. Możesz użyć nazwy projektu, która została pobrana na początku samouczka.
    * **Kontrola wersji**: wybierz pozycję **git**. Może być konieczne rozszerzenie **Zaawansowane** , aby zobaczyć **kontrolę wersji**.

    Użyj wartości domyślnej dla innych właściwości.
1. Wybierz pozycję **Create project** (Utwórz projekt).

Utwórz połączenie usługi używane do wdrażania projektów na platformie Azure.

1. Wybierz pozycję **Ustawienia projektu** w dolnej części menu po lewej stronie.
1. Wybierz pozycję **połączenia usługi** w obszarze **potoki**.
1. Wybierz pozycję **nowe połączenie usługi**, a następnie wybierz pozycję **AzureResourceManager**.
1. Wprowadź następujące wartości:

    * **Nazwa połączenia**: Wprowadź nazwę połączenia. Na przykład **AzureRmPipeline-poł**. Zapisz tę nazwę, podczas tworzenia potoku musisz mieć nazwę.
    * **Poziom zakresu**: Wybierz **subskrypcję**.
    * **Subskrypcja**: wybierz swoją subskrypcję.
    * **Grupa zasobów**: pozostaw to pole puste.
    * **Zezwalaj wszystkim potokom na korzystanie z tego połączenia**. (wybrane)
1. Kliknij przycisk **OK**.

## <a name="create-a-pipeline"></a>Tworzenie potoku

Do tej pory zostały wykonane następujące zadania.  Jeśli pominiesz poprzednie sekcje, ponieważ znasz już usługi GitHub i DevOps, musisz wykonać zadania przed kontynuowaniem.

- Utwórz repozytorium GitHub i Zapisz [ten szablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) w folderze **CreateAzureStorage** w repozytorium.
- Utwórz projekt DevOps i Utwórz połączenie z usługą Azure Resource Manager.

Aby utworzyć potok z krokiem do wdrożenia szablonu:

1. Wybierz pozycję **potoki** w menu po lewej stronie.
1. Wybierz pozycję **Nowy potok**.
1. Na karcie **Connect** (Połączenie) wybierz pozycję **GitHub**. Jeśli zostanie wyświetlony monit, wprowadź swoje poświadczenia usługi GitHub, a następnie postępuj zgodnie z instrukcjami. Jeśli widzisz Poniższy ekran, wybierz opcję **tylko wybierz repozytoria**i sprawdź, czy repozytorium znajduje się na liście przed wybraniem opcji **Zatwierdź & Zainstaluj**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines wybierz tylko repozytoria](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na karcie **Wybierz** wybierz repozytorium.  Nazwa domyślna to **[YourAccountName]/[YourGitHubRepositoryName]** .
1. Na karcie **Konfiguracja** wybierz pozycję **potok początkowy**. Pokazuje plik potoku **Azure-Pipelines. yml** z dwoma krokami skryptu.
1. Zastąp sekcję **kroki** następującym YAML:

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Powinien wyglądać następująco:

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Wprowadź następujące zmiany:

    * **deloymentScope**: Wybierz zakres wdrożenia z opcji: `Management Group`, `Subscription` i `Resource Group`. Użyj **grupy zasobów** w tym samouczku. Aby dowiedzieć się więcej o zakresach, zobacz sekcję [Deployment Scopes](deploy-rest.md#deployment-scope).
    * **ConnectedServiceName**: Określ nazwę połączenia usługi utworzoną wcześniej.
    * **Subscriptionname**: Określ Identyfikator subskrypcji docelowej.
    * **Akcja**: Akcja **Utwórz lub Zaktualizuj grupę zasobów** wykonuje 2 akcje — 1. Utwórz grupę zasobów, jeśli podano nową nazwę grupy zasobów; dwóch. Wdróż określony szablon.
    * **resourceGroupName**: Określ nową nazwę grupy zasobów. Na przykład **AzureRmPipeline-RG**.
    * **Lokalizacja**: Określ lokalizację grupy zasobów.
    * **templateLocation**: po określeniu **połączonego artefaktu** zadanie szuka pliku szablonu bezpośrednio z połączonego repozytorium.
    * **csmFile** jest ścieżką do pliku szablonu. Nie musisz określać pliku parametrów szablonu, ponieważ wszystkie parametry zdefiniowane w szablonie mają wartości domyślne.

    Aby uzyskać więcej informacji o zadaniu, zobacz zadanie [wdrażania grupy zasobów platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)i [zadanie wdrażania Azure Resource Manager szablonu](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Wybierz polecenie **Zapisz i uruchom**.
1. Wybierz pozycję **Zapisz i uruchom** ponownie. Kopia pliku YAML jest zapisywana w połączonym repozytorium. Plik YAML można zobaczyć, przechodzenie do repozytorium.
1. Sprawdź, czy potok został pomyślnie wykonany.

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Otwórz grupę zasobów. Nazwa jest określona w pliku YAML potoku.  Zobaczysz jedno utworzone konto magazynu.  Nazwa konta magazynu rozpoczyna się od **zapisania**.
1. Wybierz nazwę konta magazynu, aby go otworzyć.
1. Wybierz pozycję **Właściwości**. Zauważ, że **replikacja** jest **magazynem lokalnie nadmiarowym (LRS)** .

    ![Azure Resource Manager weryfikacji portalu usługi Azure DevOps Azure Pipelines](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Aktualizowanie i ponowne wdrażanie

Gdy aktualizujesz szablon i wypychasz zmiany do repozytorium zdalnego, potok automatycznie zaktualizuje zasoby, konto magazynu w tym przypadku.

1. Otwórz plik **azuredeploy. JSON** z lokalnego repozytorium w Visual Studio Code.
1. Zaktualizuj wartość **DefaultValue** elementu **storageAccountType** do **Standard_GRS**. Zobacz poniższy zrzut ekranu:

    ![Azure Resource Manager usługi Azure DevOps Azure Pipelines Update YAML](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Zapisz zmiany.
1. Wypchnij zmiany do repozytorium zdalnego, uruchamiając następujące polecenia z narzędzia Git bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Pierwsze polecenie synchronizuje repozytorium lokalne ze zdalnym repozytorium. Zapamiętaj, że plik potoku YAML został dodany do zdalnego repozytorium.

    Po zaktualizowaniu głównej gałęzi repozytorium zdalnego potok jest uruchamiany ponownie.

Aby sprawdzić zmiany, można sprawdzić Właściwość replikacji konta magazynu.  Zobacz [weryfikacja wdrożenia](#verify-the-deployment).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

Możesz również chcieć usunąć repozytorium GitHub i projekt usługi Azure DevOps.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz potok usługi Azure DevOps w celu wdrożenia szablonu Azure Resource Manager. Aby dowiedzieć się, jak wdrażać zasoby platformy Azure w wielu regionach i jak stosować praktyki bezpiecznego wdrażania, zobacz

> [!div class="nextstepaction"]
> [Korzystanie z praktyk bezpiecznego wdrażania](./deployment-manager-tutorial.md)
