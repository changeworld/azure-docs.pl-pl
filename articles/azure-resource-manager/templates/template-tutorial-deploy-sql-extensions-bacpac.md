---
title: Importowanie plików SQL BACPAC z szablonami
description: Dowiedz się, jak używać rozszerzeń bazy danych SQL do importowania plików SQL BACPAC za pomocą szablonów usługi Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8e65ebbfa0971bf2156165b55ca18eee3cc74bc9
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239275"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Samouczek: Importowanie plików SQL BACPAC za pomocą szablonów ARM

Dowiedz się, jak za pomocą rozszerzeń bazy danych SQL platformy Azure zaimportować plik BACPAC za pomocą szablonów usługi Azure Resource Manager (ARM). Artefakty wdrażania to wszystkie pliki, oprócz głównych plików szablonów, które są potrzebne do ukończenia wdrożenia. Plik BACPAC jest tu artefaktem. 

W tym samouczku utworzysz szablon do wdrożenia serwera SQL platformy Azure i bazy danych SQL i zaimportowania pliku BACPAC. Aby uzyskać informacje dotyczące wdrażania rozszerzeń maszyn wirtualnych platformy Azure przy użyciu szablonów ARM, zobacz [Samouczek: Wdrażanie rozszerzeń maszyn wirtualnych za pomocą szablonów ARM](./template-tutorial-deploy-vm-extensions.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotuj plik BACPAC.
> * Otwórz szablon przewodnika Szybki start.
> * Edytuj szablon.
> * Wdrażanie szablonu.
> * Sprawdź wdrożenie.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Tworzenie szablonów ARM za pomocą programu Visual Studio](./use-vs-code-to-create-template.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora programu Azure SQL Server. Oto przykład, którego można użyć do wygenerowania hasła:

    ```console
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: Integrowanie usługi Azure Key Vault we wdrażaniu szablonu ARM](./template-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-bacpac-file"></a>Przygotowywanie pliku BACPAC

Plik BACPAC jest udostępniany w [gitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Aby utworzyć własny plik, zobacz [Eksportowanie bazy danych Azure SQL Database do pliku BACPAC](../../sql-database/sql-database-export.md). W przypadku wybrania publikowania pliku do własnej lokalizacji musisz zaktualizować szablon w dalszej części tego samouczka.

Plik BACPAC musi być przechowywany na koncie usługi Azure Storage, zanim będzie można go zaimportować przy użyciu szablonu ARM. Następujący skrypt programu PowerShell przygotowuje plik BACPAC z następującymi krokami:

* Pobieranie pliku BACPAC.
* Tworzenie konta usługi Azure Storage.
* Utwórz kontener obiektów blob konta magazynu.
* Przekazywanie pliku BACPAC do kontenera.
* Wyświetl klucz konta magazynu i adres URL obiektu blob.

1. Wybierz pozycję **Spróbuj,** aby otworzyć powłokę chmury. Następnie wklej następujący skrypt programu PowerShell do okna powłoki.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Zapisz klucz konta magazynu i adres URL pliku BACPAC. Te wartości są potrzebne podczas wdrażania szablonu.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablon użyty w tym samouczku jest przechowywany w [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. W programie Visual Studio Code wybierz pozycję **Plik** > **otwórz plik**.
1. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.

    W szablonie zdefiniowano dwa zasoby:

   * `Microsoft.Sql/servers`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

        Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.
1. Wybierz **opcję Zapisz plik,** > **Save As** aby zapisać kopię pliku na komputerze lokalnym o nazwie *azuredeploy.json*.

## <a name="edit-the-template"></a>Edytowanie szablonu

1. Dodaj jeszcze dwa parametry na końcu sekcji **parametry,** aby ustawić klucz konta magazynu i adres URL BACPAC.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    Dodaj przecinek po **adminPassword**. Aby sformatować plik JSON z programu Visual Studio Code, wybierz klawisze Shift+Alt+F.

    Aby uzyskać te dwie wartości, zobacz [Przygotowywanie pliku BACPAC](#prepare-a-bacpac-file).

1. Dodaj do szablonu dwa dodatkowe zasoby.

    * Aby zezwolić rozszerzeniu bazy danych SQL na importowanie plików BACPAC, należy zezwolić na ruch z usług platformy Azure. Dodaj następującą definicję reguły zapory w definicji serwera SQL:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        Szablon wygląda następująco:

        ![Szablon z definicją reguł zapory](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Dodaj zasób rozszerzenia usługi SQL Database do definicji bazy danych za pomocą następującego kodu JSON:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        Szablon wygląda następująco:

        ![Szablon z rozszerzeniem bazy danych SQL](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Aby poznać definicję zasobu, zapoznaj się z [dokumentacją rozszerzenia usługi SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Poniżej przedstawiono niektóre ważne elementy:

        * **dependsOn**: zasób rozszerzenia musi zostać utworzony po utworzeniu bazy danych SQL.
        * **storageKeyType**: Określ typ klucza magazynu do użycia. Wartością może być `StorageAccessKey` lub `SharedAccessKey`. Użyj `StorageAccessKey` w tym samouczku.
        * **storageKey**: Określ klucz dla konta magazynu, na którym jest przechowywany plik BACPAC. Jeśli typ klucza `SharedAccessKey`magazynu jest , musi być poprzedzony "?".
        * **storageUri**: Określ adres URL pliku BACPAC przechowywanego na koncie magazynu.
        * **administratorLoginPassword**: hasło administratora bazy danych SQL. Użyj wygenerowanego hasła. Zobacz [Wymagania wstępne](#prerequisites).

Ukończony szablon wygląda następująco:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Wdrożenie szablonu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Zapoznaj się sekcją [Wdrażanie szablonu](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), gdzie znajduje się procedura wdrażania. Alternatywnie możesz też użyć następującego skryptu wdrażania programu PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Należy rozważyć użycie tej samej nazwy projektu, która została użyta podczas przygotowywania pliku BACPAC, tak aby wszystkie zasoby były przechowywane w tej samej grupie zasobów. W ten sposób łatwiej jest zarządzać zadaniami zasobów, takimi jak czyszczenie zasobów. Jeśli używasz tej samej nazwy projektu, `New-AzResourceGroup` możesz usunąć polecenie ze skryptu lub odpowiedzieć tak (y) lub nie (n), gdy pojawi się pytanie, czy chcesz zaktualizować istniejącą grupę zasobów.

Użyj wygenerowanego hasła. Zobacz [Wymagania wstępne](#prerequisites).

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby uzyskać dostęp do serwera SQL z komputera klienckiego, należy dodać dodatkową regułę zapory. Aby uzyskać więcej informacji, zobacz [Tworzenie reguł zapory IP i zarządzanie nimi](../../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

W witrynie Azure portal wybierz bazę danych SQL z nowo wdrożonej grupy zasobów. Wybierz pozycję **Edytor zapytań (wersja zapoznawcza)**, a następnie wprowadź poświadczenia administratora. Zobaczysz dwie tabele zaimportowane do bazy danych.

![Edytor zapytań (wersja zapoznawcza)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
1. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
1. Wybierz nazwę grupy zasobów. Zobaczysz w sumie sześć zasobów w grupie zasobów.
1. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer SQL i bazę danych SQL i zaimportowano plik BACPAC. Plik BACPAC jest przechowywany na koncie usługi Azure Storage. Każda osoba dysponująca adresem URL może uzyskać dostęp do tego pliku. Aby dowiedzieć się, jak zabezpieczyć plik BACPAC (artefakt), zobacz:

> [!div class="nextstepaction"]
> [Zabezpieczanie artefaktów](./template-tutorial-secure-artifacts.md)
