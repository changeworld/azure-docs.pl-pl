---
title: Tworzenie szablonu — kod programu Visual Studio
description: Program Visual Studio Code i rozszerzenie Narzędzia usługi Azure Resource Manager służą do pracy z szablonami usługi Resource Manager.
author: mumian
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: c9447d356cff792d9a70e33cc2a5e35898d8982b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80131895"
---
# <a name="quickstart-create-arm-templates-by-using-visual-studio-code"></a>Szybki start: tworzenie szablonów ARM przy użyciu programu Visual Studio Code

Dowiedz się, jak tworzyć i edytować szablony usługi Azure Resource Manager (ARM) za pomocą kodu programu Visual Studio i rozszerzenia Narzędzia usługi Azure Resource Manager. Szablony ARM można tworzyć w programie Visual Studio Code bez rozszerzenia, ale rozszerzenie udostępnia opcje autouzupełniania, które upraszczają tworzenie szablonów. Aby zapoznać się z pojęciami związanymi z wdrażaniem rozwiązań platformy Azure i zarządzanie nimi, zobacz [omówienie wdrażania szablonów.](overview.md)

W tym przewodniku Szybki start można wdrożyć konto magazynu:

![Diagram kodu programu Visual Studio dla programu Visual Studio dla menedżera zasobów](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

- [Program Visual Studio Code](https://code.visualstudio.com/)
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

Istnieje wiele metod wdrażania szablonów. Powłoka usługi Azure Cloud jest używana w tym przewodniku Szybki start. Powłoka w chmurze obsługuje zarówno interfejsu wiersza polecenia platformy Azure, jak i programu Azure PowerShell. Użyj selektora kart, aby wybrać między wierszem polecenia i programem PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Zaloguj się do [powłoki usługi Azure Cloud](https://shell.azure.com)

2. Wybierz preferowane środowisko, wybierając program **PowerShell** lub **Bash**(CLI) w lewym górnym rogu.  Po przełączeniu wymagane jest ponowne uruchomienie powłoki.

    # <a name="cli"></a>[Cli](#tab/CLI)

    ![Interfejs wiersza polecenia usługi Cloud Shell w witrynie Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershell"></a>[Powershell](#tab/PowerShell)

    ![Powłoka usługi Azure portal w chmurze PowerShell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Wybierz pozycję **Przekaż/pobierz pliki**, a następnie wybierz pozycję **Przekaż**.

    # <a name="cli"></a>[Cli](#tab/CLI)

    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershell"></a>[Powershell](#tab/PowerShell)

    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Wybierz plik, który został zapisany w poprzedniej sekcji. Nazwa domyślna to **azuredeploy.json**. Plik szablonu musi być dostępny z poziomu powłoki.

    Opcjonalnie możesz skorzystać z poleceń **ls** i **cat**, aby sprawdzić, czy plik został przekazany pomyślnie.

    # <a name="cli"></a>[Cli](#tab/CLI)

    ![Wyświetlanie listy plików w usłudze Cloud Shell w witrynie Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershell"></a>[Powershell](#tab/PowerShell)

    ![Wyświetlanie listy plików w usłudze Cloud Shell w witrynie Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. W usłudze Cloud Shell uruchom poniższe polecenia. Wybierz kartę, aby wyświetlić kod programu PowerShell lub kod interfejsu wiersza polecenia.

    # <a name="cli"></a>[Cli](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[Powershell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

    Zaktualizuj nazwę pliku szablonu, jeśli plik zapisano pod nazwą inną niż **azuredeploy.json**.

    Poniższy zrzut ekranu przedstawia przykładowe wdrożenie:

    # <a name="cli"></a>[Cli](#tab/CLI)

    ![Wdrażanie szablonu w usłudze Cloud Shell w witrynie Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershell"></a>[Powershell](#tab/PowerShell)

    ![Wdrażanie szablonu w usłudze Cloud Shell w witrynie Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    Nazwa konta magazynu i adres URL magazynu w sekcji danych wyjściowych zostały wyróżnione na zrzucie ekranu. W następnym kroku będzie potrzebna nazwa konta magazynu.

5. Uruchom następujące polecenie interfejsu wiersza polecenia lub programu PowerShell, aby wyświetlić nowo utworzone konto magazynu:

    # <a name="cli"></a>[Cli](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```

    # <a name="powershell"></a>[Powershell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```

    ---

Aby dowiedzieć się więcej o korzystaniu z kont magazynu platformy Azure, zobacz przewodnik [Szybki start — przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Ten przewodnik Szybki start koncentruje się głównie na użyciu programu Visual Studio Code do edycji jednego z istniejących szablonów szybkiego startu platformy Azure. Przedstawiono w nim również sposób wdrażania szablonu przy użyciu interfejsu wiersza polecenia lub programu PowerShell z poziomu usługi Azure Cloud Shell. Szablony spośród szablonów szybkiego startu platformy Azure mogą nie zaspokajać wszystkich Twoich potrzeb. Aby dowiedzieć się więcej o tworzeniu szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)
