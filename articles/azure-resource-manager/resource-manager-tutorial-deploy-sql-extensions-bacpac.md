---
title: Import SQL BACPAC files with templates
description: Informacje o sposobie importowania plików BACPAC bazy danych SQL przy użyciu szablonów usługi Azure Resource Manager z wykorzystaniem rozszerzenia usługi SQL Database.
author: mumian
ms.date: 04/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6291daecb578ba20f854dd2e6023890b158b8ebe
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326447"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Samouczek: importowanie plików BACPAC bazy danych SQL za pomocą szablonów usługi Azure Resource Manager

Informacje o sposobie importowania pliku BACPAC przy użyciu szablonów usługi Azure Resource Manager z wykorzystaniem rozszerzeń usługi Azure SQL Database. Artefakty wdrożenia to dowolne pliki oprócz pliku szablonu głównego, które są potrzebne do ukończenia wdrożenia. Plik BACPAC jest tu artefaktem. W tym samouczku utworzysz szablon umożliwiający wdrożenie serwera SQL Azure Server i usługi SQL Database oraz zaimportowanie pliku BACPAC. Aby uzyskać informacje dotyczące wdrażania rozszerzeń maszyny wirtualnej platformy Azure przy użyciu szablonów usługi Azure Resource Manager, zobacz [# Samouczek: wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

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

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora programu SQL Server. Poniżej przedstawiono przykład służący do generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-bacpac-file"></a>Przygotowywanie pliku BACPAC

A BACPAC file is shared in [Github](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Aby utworzyć własny plik, zobacz [Eksportowanie bazy danych Azure SQL Database do pliku BACPAC](../sql-database/sql-database-export.md). W przypadku wybrania publikowania pliku do własnej lokalizacji musisz zaktualizować szablon w dalszej części tego samouczka.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

The template used in this tutorial is stored in [Github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

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

Dodaj do szablonu dwa dodatkowe zasoby.

* Aby umożliwić rozszerzeniu usługi SQL Database importowanie plików BACPAC, musisz zezwolić na dostęp do usług platformy Azure. Dodaj następujący kod JSON do definicji serwera SQL:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2015-05-01-preview",
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
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    Szablon powinien wyglądać następująco:

    ![Plik BACPAC umożliwiający wdrażanie rozszerzeń SQL usługi Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    Aby poznać definicję zasobu, zapoznaj się z [dokumentacją rozszerzenia usługi SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Poniżej przedstawiono niektóre ważne elementy:

    * **dependsOn**: zasób rozszerzenia musi zostać utworzony po utworzeniu bazy danych SQL.
    * **storageKeyType**: typ klucza magazynu do użycia. Wartością może być `StorageAccessKey` lub `SharedAccessKey`. Ponieważ podany plik BACPAC jest udostępniany na koncie usługi Azure Storage z dostępem publicznym, używana jest w tym przypadku wartość „SharedAccessKey”.
    * **storageKey**: klucz magazynu do użycia. Jeśli typ klucza magazynu ma wartość SharedAccessKey, należy go poprzedzić znakiem „?”.
    * **storageUri**: identyfikator URI magazynu do użycia. Jeśli nie chcesz używać dostarczonego pliku BACPAC, musisz zaktualizować te wartości.
    * **administratorLoginPassword**: hasło administratora bazy danych SQL. Użyj wygenerowanego hasła. Zobacz [Wymagania wstępne](#prerequisites).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zapoznaj się sekcją [Wdrażanie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), gdzie znajduje się procedura wdrażania. Alternatywnie możesz też użyć następującego skryptu wdrażania programu PowerShell:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json"
```

Użyj wygenerowanego hasła. Zobacz [Wymagania wstępne](#prerequisites).

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

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
