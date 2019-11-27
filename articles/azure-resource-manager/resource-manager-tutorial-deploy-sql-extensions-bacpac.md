---
title: Importuj pliki BACPAC SQL z szablonami
description: Informacje o sposobie importowania plików BACPAC bazy danych SQL przy użyciu szablonów usługi Azure Resource Manager z wykorzystaniem rozszerzenia usługi SQL Database.
author: mumian
ms.date: 11/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 741521551335712400e5f61822d7dda31199d3df
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422176"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Samouczek: importowanie plików BACPAC bazy danych SQL za pomocą szablonów usługi Azure Resource Manager

Informacje o sposobie importowania pliku BACPAC przy użyciu szablonów usługi Azure Resource Manager z wykorzystaniem rozszerzeń usługi Azure SQL Database. Artefakty wdrożenia to dowolne pliki, oprócz plików szablonów głównych, które są potrzebne do ukończenia wdrożenia. Artefaktem jest na przykład plik BACPAC. W tym samouczku utworzysz szablon umożliwiający wdrożenie serwera SQL Azure Server i usługi SQL Database oraz zaimportowanie pliku BACPAC. Aby uzyskać informacje dotyczące wdrażania rozszerzeń maszyny wirtualnej platformy Azure przy użyciu szablonów usługi Azure Resource Manager, zobacz [# Samouczek: wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowywanie pliku BACPAC
> * Otwieranie szablonu szybkiego startu
> * Edytowanie szablonu
> * Wdrożenie szablonu
> * Weryfikowanie wdrożenia

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Visual Studio Code z rozszerzeniem Menedżer zasobów Tools. [Aby utworzyć szablony Azure Resource Manager, zobacz temat używanie Visual Studio Code](./resource-manager-tools-vs-code.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora programu SQL Server. Poniżej przedstawiono przykład służący do generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-bacpac-file"></a>Przygotowywanie pliku BACPAC

Plik BACPAC jest udostępniany w serwisie [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Aby utworzyć własny plik, zobacz [Eksportowanie bazy danych Azure SQL Database do pliku BACPAC](../sql-database/sql-database-export.md). W przypadku wybrania publikowania pliku do własnej lokalizacji musisz zaktualizować szablon w dalszej części tego samouczka.

Plik BACPAC musi być przechowywany na koncie usługi Azure Storage, aby można go było zaimportować przy użyciu szablonu Menedżer zasobów.

1. Otwórz [chmurę usługi Cloud Shell](https://shell.azure.com).
1. Wybierz pozycję **pliki do przekazania/pobrania**, a następnie wybierz pozycję **Przekaż**.
1. Określ następujący adres URL, a następnie wybierz pozycję **Otwórz**.

    ```url
    https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac
    ```

1. Skopiuj i wklej następujący skrypt programu PowerShell do okna powłoki.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFile = "$HOME/SQLDatabaseExtension.bacpac"
    $blobName = "SQLDatabaseExtension.bacpac"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    Set-AzStorageBlobContent -File $bacpacFile `
                             -Container $containerName `
                             -Blob $blobName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$blobName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Zapisz klucz konta magazynu i adres URL pliku BACPAC. Te wartości są wymagane podczas wdrażania szablonu.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablon używany w tym samouczku jest przechowywany w serwisie [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. W programie Visual Studio Code wybierz pozycję **File (Plik)** >**Open File (Otwórz plik)** .
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. Wybierz pozycję **Open (Otwórz)** , aby otworzyć plik.

    W tym szablonie zdefiniowano trzy zasoby:

   * `Microsoft.Sql/servers`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.
4. Wybierz pozycję **Plik**>**Zapisz jako**, aby zapisać kopię pliku o nazwie **azuredeploy.json** na komputerze lokalnym.

## <a name="edit-the-template"></a>Edytowanie szablonu

1. Dodaj dwa więcej parametrów na końcu sekcji **Parameters** , aby ustawić klucz konta magazynu i adres URL BACPAC:

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

    Dodaj przecinek po kluczu **adminPassword**. Aby sformatować plik JSON z VS Code, naciśnij klawisze **[Shift] + [Alt] + F**.

    Aby uzyskać te dwie wartości, zobacz [Przygotowywanie pliku BACPAC](#prepare-a-bacpac-file) .

1. Dodaj do szablonu dwa dodatkowe zasoby.

    * Aby zezwolić na importowanie plików BACPAC przez rozszerzenie bazy danych SQL, musisz zezwolić na ruch z usług platformy Azure. Dodaj następującą definicję reguły zapory w definicji programu SQL Server:

        ```json
        {
          "type": "firewallrules",
          "apiVersion": "2015-05-01-preview",
          "name": "AllowAllAzureIps",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[variables('databaseServerName')]"
          ],
          "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
          }
        }
        ```

        Szablon powinien wyglądać następująco:

        ![Plik BACPAC umożliwiający wdrażanie rozszerzeń SQL usługi Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Dodaj zasób rozszerzenia usługi SQL Database do definicji bazy danych za pomocą następującego kodu JSON:

        ```json
        "resources": [
            {
              "type": "extensions",
              "apiVersion": "2014-04-01",
              "name": "Import",
              "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
              ],
              "properties": {
                "storageKeyType": "StorageAccessKey",
                "storageKey": "[parameters('storageAccountKey')]",
                "storageUri": "[parameters('bacpacUrl')]",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import"
              }
            }
        ]
        ```

        Szablon powinien wyglądać następująco:

        ![Plik BACPAC umożliwiający wdrażanie rozszerzeń SQL usługi Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Aby poznać definicję zasobu, zapoznaj się z [dokumentacją rozszerzenia usługi SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Poniżej przedstawiono niektóre ważne elementy:

        * **dependsOn**: zasób rozszerzenia musi zostać utworzony po utworzeniu bazy danych SQL.
        * **storageKeyType**: Określ typ klucza magazynu, który ma być używany. Wartością może być `StorageAccessKey` lub `SharedAccessKey`. Użyj `StorageAccessKey` w tym samouczku.
        * **storageKey**: Określ klucz dla konta magazynu, w którym przechowywany jest plik BACPAC. Jeśli typ klucza magazynu to SharedAccessKey, musi być poprzedzony znakiem "?"
        * **storageUri**: Określ adres URL pliku BACPAC przechowywanego na koncie magazynu.
        * **administratorLoginPassword**: hasło administratora bazy danych SQL. Użyj wygenerowanego hasła. Zobacz [Wymagania wstępne](#prerequisites).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zapoznaj się sekcją [Wdrażanie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), gdzie znajduje się procedura wdrażania. Alternatywnie możesz też użyć następującego skryptu wdrażania programu PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Rozważ użycie tej samej nazwy projektu, która została użyta podczas przygotowywania pliku BACPAC, tak aby wszystkie zasoby były przechowywane w ramach tej samej grupy zasobów.  Łatwiej jest zarządzać zasobami, takimi jak czyszczenie zasobów. Jeśli używasz tej samej nazwy projektu, możesz usunąć polecenie **New-AzResourceGroup** ze skryptu lub odpowiedzieć na t lub n, gdy zostanie wyświetlony monit o zaktualizowanie istniejącej grupy zasobów.

Użyj wygenerowanego hasła. Zobacz [Wymagania wstępne](#prerequisites).

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby uzyskać dostęp do programu SQL Server z komputera klienckiego, należy dodać dodatkową regułę zapory. Aby uzyskać więcej informacji, zobacz [Tworzenie reguł zapory IP i zarządzanie nimi](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

W portalu wybierz bazę danych SQL z nowo wdrożonej grupy zasobów. Wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** , a następnie wprowadź poświadczenia administratora. Zobaczysz dwie tabele zaimportowane do bazy danych:

![Plik BACPAC umożliwiający wdrażanie rozszerzeń SQL usługi Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono program SQL Server i usługę SQL Database oraz zaimportowano plik BACPAC. Plik BACPAC jest przechowywany na koncie usługi Azure Storage. Każda osoba dysponująca adresem URL może uzyskać dostęp do tego pliku. Aby dowiedzieć się, jak zabezpieczyć plik BACPAC (artefakt), zobacz

> [!div class="nextstepaction"]
> [Zabezpieczanie artefaktów](./resource-manager-tutorial-secure-artifacts.md)
