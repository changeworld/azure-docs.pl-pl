---
title: Używanie programu Visual Studio Code do tworzenia szablonu usługi Azure Resource Manager | Microsoft Docs
description: Program Visual Studio Code i rozszerzenie Narzędzia usługi Azure Resource Manager służą do pracy z szablonami usługi Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: c73084b03736a422e5e3b617ec058ade1de0fbb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464784"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Szybki start: tworzenie szablonów usługi Azure Resource Manager przy użyciu programu Visual Studio Code

Dowiedz się, jak używać kodu programu Visual Studio i rozszerzenia narzędzi usługi Azure Resource Manager do tworzenia i edytowania szablonów usługi Azure Resource Manager. Szablony usługi Resource Manager można tworzyć w programie Visual Studio Code bez korzystania z rozszerzenia, ale rozszerzenie udostępnia opcje autouzupełniania, które upraszczają proces tworzenia szablonu. Aby zrozumieć pojęcia związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Usługa Azure Resource Manager — omówienie](resource-group-overview.md).

W ramach tego samouczka możesz wdrożyć konto magazynu:

![diagram kodu programu visual studio szybkiego startu w szablonu Menedżera zasobów](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

- [Program Visual Studio Code](https://code.visualstudio.com/)
- Rozszerzenie Narzędzia usługi Resource Manager. Aby przeprowadzić instalację, wykonaj następujące kroki:

    1. Otwórz program Visual Studio Code.
    2. Naciśnij klawisze **CTRL + SHIFT + X**, aby otworzyć okienko rozszerzenia.
    3. Wyszukaj pozycję **Azure Resource Manager Tools**, a następnie wybierz pozycję **Install (Zainstaluj)**.
    4. Wybierz pozycję **Reload (Załaduj ponownie)**, aby zakończyć instalację rozszerzenia.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Zamiast tworzyć szablon od podstaw, otwórz szablon z obszaru [Azure Quickstart Templates (Szablony szybkiego startu platformy Azure)](https://azure.microsoft.com/resources/templates/). Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager.

Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Szablon definiuje zasób konta usługi Azure Storage.

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz pozycję **File (Plik)**>**Save As (Zapisz jako)**, aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

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

    ![Funkcja IntelliSense programu Visual Studio Code szablonu usługi Resource Manager](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Wybierz pozycję **File (Plik)**>**Save (Zapisz)**, aby zapisać plik.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Istnieje wiele metod wdrażania szablonów. Usługa Azure Cloud shell jest używany w tym przewodniku Szybki Start. Cloud shell obsługuje zarówno wiersza polecenia platformy Azure i programu Azure PowerShell. Selektor karty dokonać wyboru między programu PowerShell i interfejsu wiersza polecenia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Logowanie do [usługi Azure Cloud Shell](https://shell.azure.com)

2. Wybierz preferowaną środowisko, wybierając opcję **PowerShell** lub **Bash**(CLI) w lewym górnym rogu.  Po przełączeniu wymagane jest ponowne uruchomienie powłoki.

    # <a name="clitabcli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ![Interfejs wiersza polecenia usługi Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/PowerShell)

    ![Azure portal usługa Cloud shell programu PowerShell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Wybierz pozycję **Przekaż/pobierz pliki**, a następnie wybierz pozycję **Przekaż**.

    # <a name="clitabcli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
   
    # <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/PowerShell)
    
    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)
    
    ---

    Wybierz plik, który został zapisany w poprzedniej sekcji. Nazwa domyślna to **azuredeploy.json**. Plik szablonu musi być dostępny z poziomu powłoki.

    Opcjonalnie możesz skorzystać z poleceń **ls** i **cat**, aby sprawdzić, czy plik został przekazany pomyślnie. 

    # <a name="clitabcli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ![Wyświetlanie listy plików w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
   
    # <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/PowerShell)
    
    ![Wyświetlanie listy plików w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)
    
    ---
4. W usłudze Cloud Shell uruchom poniższe polecenia. Wybierz kartę, aby wyświetlić kod programu PowerShell lub kod interfejsu wiersza polecenia.

    # <a name="clitabcli"></a>[Interfejs wiersza polecenia](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location "$location" &&
    az group deployment create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```
   
    # <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/PowerShell)
    
    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    
    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```
    
    ---

    Zaktualizuj nazwę pliku szablonu, jeśli plik zapisano pod nazwą inną niż **azuredeploy.json**. 

    Poniższy zrzut ekranu przedstawia przykładowe wdrożenie:

    # <a name="clitabcli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ![Wdrażanie szablonu w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)
   
    # <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/PowerShell)
    
    ![Wdrażanie szablonu w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)
    
    ---

    Nazwa konta magazynu i adres URL magazynu w sekcji danych wyjściowych zostały wyróżnione na zrzucie ekranu. W następnym kroku będzie potrzebna nazwa konta magazynu.

5. Uruchom następujące polecenie interfejsu wiersza polecenia lub programu PowerShell, aby wyświetlić nowo utworzone konto magazynu:

    # <a name="clitabcli"></a>[Interfejs wiersza polecenia](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```
   
    # <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/PowerShell)
    
    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```
    
    ---

Aby dowiedzieć się więcej o korzystaniu z kont usługi Azure Storage, zobacz [Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Kolejne kroki

Ten przewodnik Szybki start koncentruje się głównie na użyciu programu Visual Studio Code do edycji jednego z istniejących szablonów szybkiego startu platformy Azure. Przedstawiono w nim również sposób wdrażania szablonu przy użyciu interfejsu wiersza polecenia lub programu PowerShell z poziomu usługi Azure Cloud Shell. Szablony spośród szablonów szybkiego startu platformy Azure mogą nie zaspokajać wszystkich Twoich potrzeb. W kolejnym samouczku dowiesz się, jak znaleźć informacje w dokumentacji szablonów, aby utworzyć zaszyfrowane konto usługi Azure Storage.

> [!div class="nextstepaction"]
> [Tworzenie zaszyfrowanego konta usługi Storage](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
