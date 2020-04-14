---
title: Tworzenie szablonu — kod programu Visual Studio
description: Program Visual Studio Code i rozszerzenie Narzędzia usługi Azure Resource Manager służą do pracy z szablonami usługi Resource Manager.
author: mumian
ms.date: 04/13/2020
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 96e57146fb6bb17cbb8bb5975371e07b66f3ec8b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255094"
---
# <a name="quickstart-create-arm-templates-by-using-visual-studio-code"></a>Szybki start: tworzenie szablonów ARM przy użyciu programu Visual Studio Code

Dowiedz się, jak tworzyć i edytować szablony usługi Azure Resource Manager (ARM) za pomocą kodu programu Visual Studio i rozszerzenia Narzędzia usługi Azure Resource Manager. Szablony ARM można tworzyć w programie Visual Studio Code bez rozszerzenia, ale rozszerzenie udostępnia opcje autouzupełniania, które upraszczają tworzenie szablonów. Aby zapoznać się z pojęciami związanymi z wdrażaniem rozwiązań platformy Azure i zarządzanie nimi, zobacz [omówienie wdrażania szablonów.](overview.md)

W tym przewodniku Szybki start można wdrożyć konto magazynu:

![Diagram kodu programu Visual Studio przewodnika Visual Studio dla programu Szybki start dla menedżera zasobów](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

- [Visual Studio Code](https://code.visualstudio.com/).
- Rozszerzenie Narzędzia usługi Resource Manager. Aby przeprowadzić instalację, wykonaj następujące kroki:

    1. Otwórz program Visual Studio Code.
    2. Naciśnij klawisze **CTRL + SHIFT + X**, aby otworzyć okienko rozszerzenia.
    3. Wyszukaj pozycję **Azure Resource Manager Tools**, a następnie wybierz pozycję **Install (Zainstaluj)**.
    4. Wybierz pozycję **Reload (Załaduj ponownie)**, aby zakończyć instalację rozszerzenia.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Zamiast tworzyć szablon od podstaw, otwórz szablon z obszaru [Azure Quickstart Templates (Szablony szybkiego startu platformy Azure)](https://azure.microsoft.com/resources/templates/). Szablony szybki start platformy Azure to repozytorium szablonów ARM.

Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Szablon definiuje zasób konta usługi Azure Storage.

1. W programie Visual Studio Code wybierz pozycję **Plik**>**otwórz plik**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz **opcję Zapisz plik,**>**Save As** aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

## <a name="edit-the-template"></a>Edytowanie szablonu

Aby dowiedzieć się, jak edytować szablon za pomocą programu Visual Studio Code, dodaj jeszcze jeden element do sekcji `outputs` zawierający identyfikator URI magazynu.

1. Dodaj jeszcze jedno wyjście do wyeksportowanego szablonu:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Po wykonaniu wszystkich czynności sekcja danych wyjściowych będzie wyglądać następująco:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Jeśli skopiowano i wklejono kod w programie Visual Studio Code, spróbuj ponownie wpisać element **value**, aby użyć funkcji IntelliSense rozszerzenia Narzędzia usługi Resource Manager.

    ![Funkcja IntelliSense programu Visual Studio Code szablonu usługi Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Wybierz pozycję**Zapisz** **plik,**>aby zapisać plik.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Istnieje wiele metod wdrażania szablonów. Usługa Azure Cloud Shell jest używana w tym przewodniku Szybki start. Powłoka w chmurze obsługuje zarówno interfejsu wiersza polecenia platformy Azure, jak i usługi Azure PowerShell. Użyj selektora kart, aby wybrać między wierszem polecenia i programem PowerShell.

1. Zaloguj się do [powłoki chmury azure](https://shell.azure.com)

2. Wybierz preferowane środowisko, wybierając program **PowerShell** lub **Bash**(CLI) w lewym górnym rogu.  Po przełączeniu wymagane jest ponowne uruchomienie powłoki.

    # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ![Narzędzie cli powłoki chmury usługi Azure portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure portal Cloud Shell PowerShell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Wybierz pozycję **Przekaż/pobierz pliki**, a następnie wybierz pozycję **Przekaż**.

    # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ![Plik przekazywania usługi Azure portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Plik przekazywania usługi Azure portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Wybierz plik, który został zapisany w poprzedniej sekcji. Nazwa domyślna to **azuredeploy.json**. Plik szablonu musi być dostępny z poziomu powłoki.

    Opcjonalnie możesz skorzystać z poleceń **ls** i **cat**, aby sprawdzić, czy plik został przekazany pomyślnie.

    # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ![Plik listy powłoki w chmurze portalu Azure](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Plik listy powłoki w chmurze portalu Azure](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. W usłudze Cloud Shell uruchom następujące polecenia. Wybierz kartę, aby wyświetlić kod programu PowerShell lub kod interfejsu wiersza polecenia. Podaj nazwę projektu, która jest używana do generowania nazwy grupy zasobów.  Nazwa grupy zasobów to nazwa projektu z **dołączenym rg.**

    # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" &&
    echo "Press [ENTER] to continue ..."
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

    Zaktualizuj nazwę pliku szablonu, jeśli plik zapisano pod nazwą inną niż **azuredeploy.json**.

    Poniższy zrzut ekranu przedstawia przykładowe wdrożenie:

    # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ![Szablon wdrażania powłoki chmury usługi Azure portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Szablon wdrażania powłoki chmury usługi Azure portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    Nazwa konta magazynu i adres URL magazynu w sekcji danych wyjściowych zostały wyróżnione na zrzucie ekranu. W następnym kroku będzie potrzebna nazwa konta magazynu.

5. Uruchom następujące polecenie interfejsu wiersza polecenia lub programu PowerShell, aby wyświetlić nowo utworzone konto magazynu:

    # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName &&
    echo "Press [ENTER] to continue ..."
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

Aby dowiedzieć się więcej o korzystaniu z kont magazynu platformy Azure, zobacz przewodnik [Szybki start — przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów. Nazwa grupy zasobów to nazwa projektu z **dołączenym rg.** W grupie zasobów zostanie wyświetlony zasób konta magazynu.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Ten przewodnik Szybki start koncentruje się głównie na użyciu programu Visual Studio Code do edycji jednego z istniejących szablonów szybkiego startu platformy Azure. Dowiedzialiście się również, jak wdrożyć szablon przy użyciu interfejsu wiersza polecenia lub programu PowerShell z usługi Azure Cloud Shell. Szablony spośród szablonów szybkiego startu platformy Azure mogą nie zaspokajać wszystkich Twoich potrzeb. Aby dowiedzieć się więcej o tworzeniu szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)
