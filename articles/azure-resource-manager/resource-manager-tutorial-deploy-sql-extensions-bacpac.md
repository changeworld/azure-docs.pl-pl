---
title: Importuj pliki BACPAC SQL z szablonami Azure Resource Manager | Microsoft Docs
description: Dowiedz się, jak za pomocą rozszerzenia SQL Database importować pliki SQL BACPAC z szablonami Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 239bb77d486e8cb845ec439d84def5e34cf64348
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170232"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Samouczek: Importowanie plików BACPAC SQL za pomocą szablonów Azure Resource Manager

Dowiedz się, jak za pomocą rozszerzeń Azure SQL Database zaimportować plik BACPAC z szablonami Azure Resource Manager. Artefakty wdrożenia to wszystkie pliki, a także główny plik szablonu, który jest niezbędny do ukończenia wdrożenia. Plik BACPAC jest artefaktem. W tym samouczku utworzysz szablon służący do wdrażania SQL Server platformy Azure, SQL Database i importowania pliku BACPAC. Aby uzyskać informacje na temat wdrażania rozszerzeń maszyn wirtualnych platformy Azure za pomocą szablonów Azure Resource Manager, zobacz [# Samouczek: Wdrażanie rozszerzeń maszyn wirtualnych z szablonami Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotuj plik BACPAC
> * Otwórz szablon szybkiego startu
> * Edytuj szablon
> * Wdróż szablon
> * Weryfikowanie wdrożenia

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tego artykułu są potrzebne następujące czynności:

* [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem narzędzi Menedżer zasobów Tools. Zobacz [Instalowanie rozszerzenia ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora SQL Server. Oto przykład generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault jest przeznaczony do ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integrowanie Azure Key Vault w Menedżer zasobów Template Deployment](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-bacpac-file"></a>Przygotuj plik BACPAC

Plik BACPAC jest udostępniany w serwisie [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Aby utworzyć własne, zobacz [Eksportowanie bazy danych Azure SQL Database do pliku BACPAC](../sql-database/sql-database-export.md). W przypadku wybrania opcji opublikowania pliku we własnej lokalizacji należy zaktualizować szablon w dalszej części tego samouczka.

## <a name="open-a-quickstart-template"></a>Otwórz szablon szybkiego startu

Szablon używany w tym samouczku jest przechowywany w serwisie [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. W obszarze Visual Studio Code wybierz pozycję **plik**>**Otwórz plik**.
2. W polu **Nazwa pliku**wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Wybierz pozycję **Otwórz** , aby otworzyć plik.

    Istnieją trzy zasoby zdefiniowane w szablonie:

   * `Microsoft.Sql/servers`., Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`., Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.,  Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Warto zapoznać się z podstawową wiedzą na temat szablonu przed jego rozpoczęciem.
4. Wybierz pozycję **plik**>**Zapisz jako,** aby zapisać kopię pliku na komputerze lokalnym przy użyciu nazwy **azuredeploy. JSON**.

## <a name="edit-the-template"></a>Edytuj szablon

Dodaj dwa dodatkowe zasoby do szablonu.

* Aby zezwolić na importowanie plików BACPAC przez rozszerzenie bazy danych SQL, musisz zezwolić na dostęp do usług platformy Azure. Dodaj następujący kod JSON do definicji programu SQL Server:

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

    ![Azure Resource Manager wdrożyć rozszerzenia SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

* Dodaj zasób rozszerzenia SQL Database do definicji bazy danych przy użyciu następującego kodu JSON:

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

    ![Azure Resource Manager wdrożyć rozszerzenia SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    Aby zrozumieć definicję zasobu, zapoznaj się z informacjami o [rozszerzeniu SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Poniżej przedstawiono kilka ważnych elementów:

    * **dependsOn**: należy utworzyć zasób rozszerzenia po utworzeniu bazy danych SQL.
    * **storageKeyType**: typ klucza magazynu do użycia. Wartość może być równa `StorageAccessKey` lub `SharedAccessKey`. Ponieważ udostępniony plik BACPAC jest udostępniany na koncie usługi Azure Storage z dostępem publicznym, tutaj użyto elementu "SharedAccessKey".
    * **storageKey**: klucz magazynu do użycia. Jeśli typ klucza magazynu to SharedAccessKey, musi być poprzedzony znakiem "?."
    * **storageUri**: identyfikator URI magazynu do użycia. Jeśli nie zdecydujesz się na korzystanie z pliku BACPAC, musisz zaktualizować wartości.
    * **administratorLoginPassword**: hasło administratora SQL. Użyj wygenerowanego hasła. Zobacz [wymagania wstępne](#prerequisites).

## <a name="deploy-the-template"></a>Wdróż szablon

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zapoznaj się z sekcją [Wdrażanie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) , aby zapoznać się z procedurą wdrażania. Zamiast tego użyj poniższego skryptu wdrażania programu PowerShell:

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

Użyj wygenerowanego hasła. Zobacz [wymagania wstępne](#prerequisites).

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

W portalu wybierz bazę danych SQL z nowo wdrożonej grupy zasobów. Wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** , a następnie wprowadź poświadczenia administratora. Zobaczysz dwie tabele zaimportowane do bazy danych:

![Azure Resource Manager wdrożyć rozszerzenia SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy** .
3. Wybierz nazwę grupy zasobów.  Zobaczysz łącznie sześć zasobów w grupie zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono SQL Server, SQL Database i zaimportowano plik BACPAC. Plik BACPAC jest przechowywany na koncie usługi Azure Storage. Każdy z adresem URL może uzyskiwać dostęp do pliku. Aby dowiedzieć się, jak zabezpieczyć plik BACPAC (artefakt), zobacz

> [!div class="nextstepaction"]
> [Zabezpieczanie artefaktów](./resource-manager-tutorial-secure-artifacts.md)
