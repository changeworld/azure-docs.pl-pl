---
title: Bezpieczne artefakty w szablonach
description: Dowiedz się, jak zabezpieczać artefakty używane w szablonach usługi Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ad6ea3c68ed6f48ac48bbbdafed7f8660df23937
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239227"
---
# <a name="tutorial-secure-artifacts-in-arm-template-deployments"></a>Samouczek: Bezpieczne artefakty we wdrożeniach szablonów ARM

Dowiedz się, jak zabezpieczyć artefakty używane w szablonach usługi Azure Resource Manager (ARM) przy użyciu konta usługi Azure Storage z sygnaturami dostępu współdzielonego (SAS). Artefakty wdrożenia to dowolne pliki oprócz pliku szablonu głównego, które są potrzebne do ukończenia wdrożenia. Na przykład w [samouczku: Importowanie plików SQL BACPAC z szablonami ARM,](./template-tutorial-deploy-sql-extensions-bacpac.md)główny szablon tworzy wystąpienie usługi Azure SQL Database. Wywołuje również plik BACPAC do tworzenia tabel i wstawiania danych. Plik BACPAC jest artefaktem i jest przechowywany na koncie usługi Azure Storage. Klucz konta magazynu został użyty do uzyskania dostępu do artefaktu.

W tym samouczku użyjesz sygnatury dostępu współdzielonego w celu przyznania ograniczonego dostępu do pliku BACPAC na swoim koncie usługi Azure Storage. Aby uzyskać więcej informacji o sygnaturze dostępu współdzielonego, zobacz [Using Shared Access Signatures (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) (Używanie sygnatur dostępu współdzielonego).

Aby dowiedzieć się, jak zabezpieczyć szablon połączony, zobacz [Samouczek: Tworzenie połączonych szablonów ARM](./template-tutorial-create-linked-templates.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotuj plik BACPAC.
> * Otwórz istniejący szablon.
> * Edytuj szablon.
> * Wdrażanie szablonu.
> * Sprawdź wdrożenie.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Visual Studio Kod z rozszerzeniem Narzędzia Menedżera zasobów. Zobacz [Tworzenie szablonów ARM za pomocą programu Visual Studio](./use-vs-code-to-create-template.md).
* Przejrzyj [samouczek: Importuj pliki SQL BACPAC z szablonami ARM](./template-tutorial-deploy-sql-extensions-bacpac.md). W tym samouczku został utworzony szablon używany w ramach bieżącego samouczka. W tym artykule udostępniono link do pobrania ukończonego szablonu.
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora programu SQL Server. Oto przykład, którego można użyć do wygenerowania hasła:

    ```console
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: Integrowanie usługi Azure Key Vault we wdrażaniu szablonu ARM](./template-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-bacpac-file"></a>Przygotowywanie pliku BACPAC

W tej sekcji należy przygotować plik BACPAC tak, aby plik był dostępny bezpiecznie podczas wdrażania szablonu ARM. W tej sekcji opisano pięć procedur:

* Pobieranie pliku BACPAC.
* Tworzenie konta usługi Azure Storage.
* Utwórz kontener obiektów blob konta magazynu.
* Przekazywanie pliku BACPAC do kontenera.
* Pobieranie tokenu SAS pliku BACPAC.

1. Wybierz pozycję **Spróbuj,** aby otworzyć powłokę chmury. Następnie wklej następujący skrypt programu PowerShell do okna powłoki.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName `
        -Location $location `
        -SkuName "Standard_LRS"

    $context = $storageAccount.Context

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Zapisz adres URL pliku BACPAC i token Sygnatury dostępu Współdzielonego. Te wartości są potrzebne podczas wdrażania szablonu.

## <a name="open-an-existing-template"></a>Otwieranie istniejącego szablonu

W tej sesji można zmodyfikować szablon utworzony w [samouczku: Importuj pliki SQL BACPAC z szablonami ARM,](./template-tutorial-deploy-sql-extensions-bacpac.md) aby wywołać plik BACPAC za pomocą tokenu Sygnatury dostępu Współdzielonego. Szablon opracowany w samouczku rozszerzenia SQL jest współużytkowana w [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. W programie Visual Studio Code wybierz pozycję **Plik** > **otwórz plik**.
1. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.

    W szablonie zdefiniowano cztery zasoby:

   * `Microsoft.Sql/servers`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.
1. Wybierz **opcję Zapisz plik,** > **Save As** aby zapisać kopię pliku na komputerze lokalnym o nazwie *azuredeploy.json*.

## <a name="edit-the-template"></a>Edytowanie szablonu

1. Zastąp definicję parametru storageAccountKey następującą definicją parametru:

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Parametry artefaktów zabezpieczeń z samouczka dotyczącego usługi Resource Manager](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Zaktualizuj wartość następujących trzech elementów zasobu rozszerzenia SQL:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

Ukończony szablon wygląda następująco:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Wdrożenie szablonu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Zobacz [sekcję Wdrażanie szablonu](./template-tutorial-create-multiple-instances.md#deploy-the-template) dla procedury wdrażania. Zamiast tego użyj następującego skryptu wdrażania programu PowerShell.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Użyj wygenerowanego hasła. Zobacz [Wymagania wstępne](#prerequisites).
Wartości _artifactsLocation, _artifactsLocationSasToken i bacpacFileName można znaleźć w polu [Przygotowywanie pliku BACPAC](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

W portalu wybierz bazę danych SQL z nowo wdrożonej grupy zasobów. Wybierz pozycję **Edytor zapytań (wersja zapoznawcza)**, a następnie wprowadź poświadczenia administratora. Zobaczysz dwie tabele zaimportowane do bazy danych.

![Edytor zapytań (wersja zapoznawcza)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów. Zobaczysz w sumie sześć zasobów w grupie zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer SQL i bazę danych SQL i zaimportowano plik BACPAC przy użyciu tokenu Sygnatury dostępu Współdzielonego. Aby dowiedzieć się, jak wdrażać zasoby platformy Azure w wielu regionach i jak stosować praktyki bezpiecznego wdrażania, zobacz

> [!div class="nextstepaction"]
> [Korzystanie z praktyk bezpiecznego wdrażania](./deployment-manager-tutorial.md)
