---
title: Ciągła integracja z użyciem usługi Azure Pipelines
description: Dowiedz się, jak stale tworzyć, testować i wdrażać szablony usługi Azure Resource Manager.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b117861a528b6983876d28d5b343ea88c2bcadc0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260687"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Samouczek: Ciągła integracja szablonów usługi Azure Resource Manager z potokami platformy Azure

Dowiedz się, jak używać potoków platformy Azure do ciągłego tworzenia i wdrażania projektów szablonów usługi Azure Resource Manager.

Usługa Azure DevOps zapewnia usługi dla deweloperów, aby wspierać zespoły w planowaniu pracy, współpracy nad tworzeniem kodu oraz tworzeniu i wdrażaniu aplikacji. Deweloperzy mogą pracować w chmurze przy użyciu usług Azure DevOps. Usługa Azure DevOps zapewnia zintegrowany zestaw funkcji, do których można uzyskać dostęp za pośrednictwem przeglądarki sieci Web lub klienta IDE. Usługa Azure Pipeline jest jedną z tych funkcji. Usługa Azure Pipelines to w pełni funkcjonalna usługa ciągłej integracji (CI) i ciągłego dostarczania (CD). Współpracuje z preferowanym dostawcą git i można wdrożyć w większości głównych usług w chmurze. Następnie możesz zautomatyzować kompilację, testowanie i wdrażanie kodu na platformie Microsoft Azure, Google Cloud Platform lub Amazon Web Services.

Ten samouczek jest przeznaczony dla deweloperów szablonów usługi Azure Resource Manager, którzy są nowymi usługami Azure DevOps i potokami platformy Azure. Jeśli znasz już gitHub i DevOps, możesz przejść do [tworzenia potoku](#create-a-pipeline).

> [!NOTE]
> Wybierz nazwę projektu. Po przejściu przez samouczek, zastąpić dowolną z **AzureRmPipeline** z nazwą projektu.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowywanie repozytorium GitHub
> * Tworzenie projektu DevOps platformy Azure
> * Tworzenie potoku platformy Azure
> * Weryfikowanie wdrożenia potoku
> * Aktualizowanie szablonu i ponowne wdrożenie
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* **Konto GitHub**, gdzie używasz go do utworzenia repozytorium szablonów. Jeśli go nie masz, możesz [go utworzyć za darmo.](https://github.com) Aby uzyskać więcej informacji na temat korzystania z repozytoriów GitHub, zobacz [Tworzenie repozytoriów GitHub](/azure/devops/pipelines/repos/github).
* **Zainstaluj Git**. Ta instrukcja samouczka używa *Git Bash* lub *Git Shell*. Aby uzyskać instrukcje, zobacz [Instalowanie gita]( https://www.atlassian.com/git/tutorials/install-git).
* **Organizacja Azure DevOps**. Jeśli go nie masz, możesz go utworzyć za darmo. Zobacz [Tworzenie kolekcji organizacji lub projektu]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Tworzenie szablonów usługi Azure Resource Manager za pomocą kodu programu Visual Studio.](use-vs-code-to-create-template.md)

## <a name="prepare-a-github-repository"></a>Przygotowywanie repozytorium GitHub

GitHub służy do przechowywania kodu źródłowego projektu, w tym szablonów Menedżera zasobów. Aby uzyskać inne obsługiwane repozytoria, zobacz [repozytoria obsługiwane przez usługę Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Tworzenie repozytorium GitHub

Jeśli nie masz konta GitHub, zobacz [Wymagania wstępne](#prerequisites).

1. Zaloguj się do [GitHub](https://github.com).
2. Wybierz obraz konta w prawym górnym rogu, a następnie wybierz **pozycję Twoje repozytoria**.

    ![Potoki azure resource manager azure devops azure tworzą repozytorium GitHub](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Wybierz **pozycję Nowy**, zielony przycisk.
1. W **nazwie repozytorium**wprowadź nazwę repozytorium.  Na przykład **AzureRmPipeline-repo**. Pamiętaj, aby zastąpić dowolną usługę **AzureRmPipeline** nazwą projektu. Możesz wybrać **publiczne** lub **prywatne,** aby przejść przez ten samouczek. A następnie wybierz **pozycję Utwórz repozytorium**.
1. Zapisz adres URL. Adres URL repozytorium jest następującym formatem:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

To repozytorium jest określane jako *zdalne repozytorium*. Każdy z deweloperów tego samego projektu może sklonować własne *repozytorium lokalne*i scalić zmiany w zdalnym repozytorium.

### <a name="clone-the-remote-repository"></a>Klonowanie zdalnego repozytorium

1. Otwórz Git Shell lub Git Bash.  Zobacz [Wymagania wstępne](#prerequisites).
1. Sprawdź, czy twój bieżący folder to **github**.
1. Uruchom następujące polecenie:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Zastąp **[YourAccountName]** swoją nazwą konta GitHub i zastąp **[YourGitHubRepositoryName]** nazwą repozytorium utworzoną w poprzedniej procedurze.

    Poniższy zrzut ekranu przedstawia przykład.

    ![Potoki azure resource manager azure devops azure tworzą github bash](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

Folder **CreateAzureStorage** jest folderem, w którym szablon jest przechowywany. Polecenie **pwd** pokazuje ścieżkę folderu. Ścieżka jest miejscem, w którym można zapisać szablon w poniższej procedurze.

### <a name="download-a-quickstart-template"></a>Pobieranie szablonu przewodnika Szybki start

Zamiast tworzyć szablon, można pobrać [szablon przewodnika Szybki start]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Ten szablon tworzy konto usługi Azure Storage.

1. Otwórz kod programu Visual Studio. Zobacz [Wymagania wstępne](#prerequisites).
2. Otwórz szablon z następującym adresem URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Zapisz plik jako **azuredeploy.json** w folderze **CreateAzureStorage.** Zarówno nazwa folderu, jak i nazwa pliku są używane, ponieważ są one w potoku.  Jeśli zmienisz te nazwy, należy zaktualizować nazwy używane w potoku.

### <a name="push-the-template-to-the-remote-repository"></a>Wypychanie szablonu do zdalnego repozytorium

Azuredeploy.json został dodany do lokalnego repozytorium. Następnie należy przekazać szablon do zdalnego repozytorium.

1. Otwórz *Git Shell* lub *Git Bash*, jeśli nie jest otwarty.
1. Zmień katalog na folder CreateAzureStorage w lokalnym repozytorium.
1. Sprawdź, czy plik **azuredeploy.json** znajduje się w folderze.
1. Uruchom następujące polecenie:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Możesz otrzymać ostrzeżenie o LF. Ostrzeżenie można zignorować. **master** jest gałęzią główną.  Zazwyczaj tworzysz gałąź dla każdej aktualizacji. Aby uprościć samouczek, należy użyć gałęzi głównej bezpośrednio.
1. Przejdź do repozytorium GitHub z przeglądarki.  Adres URL to ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. W folderze zostanie wyświetlony folder **CreateAzureStorage** i **Azuredeploy.json.**

Do tej pory utworzono repozytorium GitHub i przesłano szablon do repozytorium.

## <a name="create-a-devops-project"></a>Tworzenie projektu DevOps

Organizacja DevOps jest potrzebna, zanim będzie można przejść do następnej procedury.  Jeśli go nie masz, zobacz [Wymagania wstępne](#prerequisites).

1. Zaloguj się do [usługi Azure DevOps](https://dev.azure.com).
1. Wybierz organizację DevOps od lewej.

    ![Potoki azure resource manager azure devops azure create Azure DevOps project Azure Resource Manager Azure DevOps create Azure DevOps project Azure Resource Manager Azure DevOps](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Wybierz pozycję **Create project** (Utwórz projekt). Jeśli nie masz żadnych projektów, strona tworzenia projektu jest otwierana automatycznie.
1. Wprowadź następujące wartości:

    * **Nazwa projektu**: wprowadź nazwę projektu. Można użyć nazwy projektu, który został wybrany na samym początku samouczka.
    * **Kontrola wersji**: Wybierz **Git**. Może być konieczne rozwinięcie **zaawansowanego,** aby wyświetlić **kontrolę wersji**.

    Użyj wartości domyślnej dla innych właściwości.
1. Wybierz pozycję **Create project** (Utwórz projekt).

Utwórz połączenie usługi, które jest używane do wdrażania projektów na platformie Azure.

1. Wybierz **pozycję Ustawienia projektu** u dołu lewego menu.
1. Wybierz **pozycję Połączenia usługi w** obszarze **Potoki**.
1. Wybierz **pozycję Nowe połączenie usługi**, a następnie wybierz pozycję **AzureResourceManager**.
1. Wprowadź następujące wartości:

    * **Nazwa połączenia**: wprowadź nazwę połączenia. Na przykład **AzureRmPipeline-conn**. Zapisz tę nazwę, potrzebujesz nazwy podczas tworzenia potoku.
    * **Poziom zakresu**: wybierz **pozycję Subskrypcja**.
    * **Subskrypcja:** wybierz subskrypcję.
    * **Grupa zasobów**: Pozostaw ją pustą.
    * **Zezwól wszystkim potokom na korzystanie z tego połączenia**. (wybrano)
1. Kliknij przycisk **OK**.

## <a name="create-a-pipeline"></a>Tworzenie potoku

Do tej pory wykonaliśmy następujące zadania.  Jeśli pominąć poprzednie sekcje, ponieważ są zaznajomieni z GitHub i DevOps, należy wykonać zadania przed kontynuowaniem.

- Utwórz repozytorium GitHub i zapisz [ten szablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) w folderze **CreateAzureStorage** w repozytorium.
- Utwórz projekt DevOps i utwórz połączenie usługi Azure Resource Manager.

Aby utworzyć potok z krokiem do wdrożenia szablonu:

1. Z menu po lewej stronie **wybierz polecenie Potoki.**
1. Wybierz **nowy potok**.
1. Na karcie **Connect** (Połączenie) wybierz pozycję **GitHub**. Jeśli zostaniesz poproszony, wprowadź poświadczenia usługi GitHub, a następnie postępuj zgodnie z instrukcjami. Jeśli widzisz następujący ekran, wybierz **pozycję Wybierz tylko repozytoria**i sprawdź, czy repozytorium znajduje się na liście przed wybraniem opcji **Zatwierdź & Zainstaluj**.

    ![Usługi Azure Resource Manager Azure DevOps Azure Pipelines tylko wybrać repozytoria](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na karcie **Wybierz** wybierz repozytorium.  Domyślna nazwa to **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Na karcie **Konfigurowanie** wybierz pozycję **Potok startowy**. Pokazuje plik potoku **azure-pipelines.yml** z dwoma krokami skryptu.
1. Zastąp sekcję **kroków** następującym YAML:

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

    Wygląda jak:

    ![Usługi Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Wprowadź następujące zmiany:

    * **deploymentScope**: Wybierz zakres wdrożenia z `Management Group` `Subscription` opcji: , i `Resource Group`. Użyj **grupy zasobów** w tym samouczku. Aby dowiedzieć się więcej o zakresach, zobacz [Zakresy wdrażania](deploy-rest.md#deployment-scope).
    * **ConnectedServiceName**: Określ nazwę połączenia usługi utworzoną wcześniej.
    * **Nazwa subskrypcji:** Określ docelowy identyfikator subskrypcji.
    * **akcja**: Akcja **Utwórz lub aktualizuj grupę zasobów** wykonuje 2 akcje - 1. utworzyć grupę zasobów, jeśli podana jest nowa nazwa grupy zasobów; 2. wdrożyć określony szablon.
    * **resourceGroupName**: określ nową nazwę grupy zasobów. Na przykład **AzureRmPipeline-rg**.
    * **lokalizacja**: określić lokalizację grupy zasobów.
    * **templateLocation**: po określeniu **artefaktu połączonego** zadanie wyszukuje plik szablonu bezpośrednio z połączonego repozytorium.
    * **csmFile** jest ścieżką do pliku szablonu. Nie trzeba określać pliku parametrów szablonu, ponieważ wszystkie parametry zdefiniowane w szablonie mają wartości domyślne.

    Aby uzyskać więcej informacji na temat tego zadania, zobacz [Zadanie wdrażania grupy zasobów platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)i zadanie wdrażania [szablonu usługi Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Wybierz polecenie **Zapisz i uruchom**.
1. Wybierz **pozycję Zapisz i uruchom ponownie.** Kopia pliku YAML jest zapisywana w podłączonym repozytorium. Możesz zobaczyć plik YAML, przejdź do repozytorium.
1. Sprawdź, czy potok został pomyślnie wykonany.

    ![Usługi Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów. Nazwa jest to, co zostało określone w pliku YAML potoku.  Zobaczysz jedno konto magazynu utworzone.  Nazwa konta magazynu zaczyna się od **magazynu**.
1. Wybierz nazwę konta magazynu, aby ją otworzyć.
1. Wybierz **pozycję Właściwości**. Zwróć uwagę, **że replikacja** to **magazyn lokalnie nadmiarowy (LRS).**

    ![Weryfikacja portalu Azure Resource Manager Azure DevOps Azure Pipelines](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Aktualizacja i ponowne wdrożenie

Po zaktualizowaniu szablonu i wypchnięciu zmian do repozytorium zdalnego potok automatycznie aktualizuje zasoby, konto magazynu w tym przypadku.

1. Otwórz **azuredeploy.json** z lokalnego repozytorium w programie Visual Studio Code.
1. Zaktualizuj **domyślnąValue** **storageAccountType** **do Standard_GRS**. Zobacz poniższy zrzut ekranu:

    ![Usługa Azure Resource Manager Azure DevOps Azure Pipelines aktualizuje yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Zapisz zmiany.
1. Wypchnij zmiany do zdalnego repozytorium, uruchamiając następujące polecenia z Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Pierwsze polecenie synchronizuje repozytorium lokalne ze zdalnym repozytorium. Pamiętaj, że plik YAML potoku został dodany do zdalnego repozytorium.

    Po zaktualizowaniu gałęzi głównej repozytorium zdalnego potok jest uruchamiany ponownie.

Aby zweryfikować zmiany, można sprawdzić właściwość Replikacja konta magazynu.  Zobacz [Zweryfikowanie wdrożenia](#verify-the-deployment).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

Można również usunąć repozytorium GitHub i projektu Azure DevOps.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz potok DevOps platformy Azure w celu wdrożenia szablonu usługi Azure Resource Manager. Aby dowiedzieć się, jak wdrażać zasoby platformy Azure w wielu regionach i jak stosować praktyki bezpiecznego wdrażania, zobacz

> [!div class="nextstepaction"]
> [Korzystanie z praktyk bezpiecznego wdrażania](./deployment-manager-tutorial.md)
