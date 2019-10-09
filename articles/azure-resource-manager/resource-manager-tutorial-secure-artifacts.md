---
title: Bezpieczne artefakty w wdrożeniach szablonów Azure Resource Manager | Microsoft Docs
description: Dowiedz się, jak zabezpieczyć artefakty używane w szablonach Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bcf64d98f53d85937ee7960ec3938280814267d8
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170191"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Samouczek: bezpieczne artefakty we wdrożeniach szablonów Azure Resource Manager

Dowiedz się, jak zabezpieczyć artefakty używane w szablonach Azure Resource Manager przy użyciu konta usługi Azure Storage z sygnaturami dostępu współdzielonego (SAS). Artefakty wdrożenia to dowolne pliki, oprócz głównego pliku szablonu, które są potrzebne do ukończenia wdrożenia. Na przykład w [samouczku: Importowanie plików SQL BACPAC za pomocą szablonów Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), główny szablon tworzy Azure SQL Database; wywołuje również plik BACPAC, aby tworzyć tabele i wstawiać dane. Plik BACPAC jest artefaktem. Artefakt jest przechowywany na koncie usługi Azure Storage z dostępem publicznym. W tym samouczku użyjesz SYGNATURy dostępu współdzielonego, aby przyznać ograniczony dostęp do pliku BACPAC na własnym koncie usługi Azure Storage. Aby uzyskać więcej informacji na temat SYGNATURy [dostępu współdzielonego, zobacz Using Shared Access Signatures (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Aby dowiedzieć się, jak zabezpieczyć połączony szablon, zobacz [Samouczek: Tworzenie połączonych Azure Resource Manager szablonów](./resource-manager-tutorial-create-linked-templates.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotuj plik BACPAC
> * Otwórz istniejący szablon
> * Edytuj szablon
> * Wdróż szablon
> * Weryfikowanie wdrożenia

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tego artykułu są potrzebne następujące czynności:

* [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem narzędzi Menedżer zasobów Tools. Zobacz [Instalowanie rozszerzenia ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Samouczek przeglądowy [: Importowanie plików BACPAC SQL za pomocą szablonów Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). Szablon używany w tym samouczku jest utworzony w tym samouczku. W tym artykule znajduje się link do pobierania ukończonego szablonu.
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora SQL Server. Oto przykład generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault jest przeznaczony do ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integrowanie Azure Key Vault w Menedżer zasobów Template Deployment](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-bacpac-file"></a>Przygotuj plik BACPAC

W tej sekcji przygotowano plik BACPAC, aby plik był bezpiecznie dostępny podczas wdrażania szablonu Menedżer zasobów. W tej sekcji znajduje się pięć procedur:

* Pobierz plik BACPAC.
* Utwórz konto usługi Azure Storage.
* Utwórz kontener obiektów BLOB konta magazynu.
* Przekaż plik BACPAC do kontenera.
* Pobierz token sygnatury dostępu współdzielonego pliku BACPAC.

Aby zautomatyzować te kroki przy użyciu skryptu programu PowerShell, zapoznaj się z skryptem, aby [przekazać połączony szablon](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Pobierz plik BACPAC

Pobierz [plik BACPAC](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac)i Zapisz plik na komputerze lokalnym o tej samej nazwie, **SQLDatabaseExtension. BACPAC**.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

1. Wybierz Poniższy obraz, aby otworzyć szablon Menedżer zasobów w Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Wprowadź następujące właściwości:

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową** i nadaj jej nazwę. Grupa zasobów to kontener zasobów platformy Azure do celów zarządzania. W tym samouczku można użyć tej samej grupy zasobów dla konta magazynu i Azure SQL Database. Zanotuj tę nazwę grupy zasobów, która jest niezbędna podczas tworzenia Azure SQL Database w dalszej części samouczków.
    * **Lokalizacja**: Wybierz region. Na przykład **środkowe stany USA**.
    * **Typ konta magazynu**: Użyj wartości domyślnej, która jest **Standard_LRS**.
    * **Lokalizacja**: Użyj wartości domyślnej, która jest **[resourceName (). Location]** . Oznacza to, że należy użyć lokalizacji grupy zasobów dla konta magazynu.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: (wybrane)
3. Wybierz pozycję **Kup**.
4. Wybierz ikonę powiadomienia (ikonę dzwonka) w prawym górnym rogu portalu, aby wyświetlić stan wdrożenia.

    ![Okienko powiadomień portalu Menedżer zasobów](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Po pomyślnym wdrożeniu konta magazynu wybierz pozycję **Przejdź do grupy zasobów** w okienku powiadomień, aby otworzyć grupę zasobów.

### <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów BLOB

Przed przekazaniem jakichkolwiek plików wymagany jest kontener obiektów BLOB.

1. Wybierz konto magazynu, aby je otworzyć. Zobaczysz tylko jedno konto magazynu znajdujące się w grupie zasobów. Nazwa konta magazynu różni się od podanego na poniższym zrzucie ekranu.

    ![Konto magazynu samouczka Menedżer zasobów](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Wybierz kafelek **obiekty blob** .

    ![Obiekty blob samouczka Menedżer zasobów](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Wybierz pozycję **+ kontener** z góry, aby utworzyć nowy kontener.
4. Wprowadź następujące wartości:

    * **Nazwa**: wprowadź **sqlbacpac**.
    * **Poziom dostępu publicznego**: Użyj wartości domyślnej, **prywatnej (bez dostępu anonimowego)** .
5. Wybierz **przycisk OK**.
6. Wybierz pozycję **sqlbacpac** , aby otworzyć nowo utworzony kontener.

### <a name="upload-the-bacpac-file-to-the-container"></a>Przekaż plik BACPAC do kontenera

1. Wybierz pozycję **Przekaż**.
2. Wprowadź następujące wartości:

    * **Pliki**: postępując zgodnie z instrukcjami, aby wybrać pobrany wcześniej plik BACPAC. Nazwa domyślna to **SQLDatabaseExtension. BACPAC**.
    * **Typ uwierzytelniania**: wybierz pozycję **SAS**.  Wartość domyślna to *sygnatura dostępu współdzielonego* .
3. Wybierz pozycję **Przekaż**.  Po pomyślnym przekazaniu pliku nazwa pliku powinna zostać wyświetlona w kontenerze.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Generate token SAS

1. Kliknij prawym przyciskiem myszy **SQLDatabaseExtension. BACPAC** z kontenera, a następnie wybierz polecenie **Generuj sygnaturę dostępu współdzielonego**.
2. Wprowadź następujące wartości:

    * **Uprawnienie**: Użyj ustawienia domyślnego, **Odczytaj**.
    * **Data/godzina rozpoczęcia i wygaśnięcia**: wartość domyślna to osiem godzin do użycia z tokenem SAS. Jeśli potrzebujesz więcej czasu na ukończenie tego samouczka, zaktualizuj **ważność**.
    * **Dozwolone adresy IP**: pozostaw to pole puste.
    * **Dozwolone protokoły**: Użyj wartości domyślnej: **https**.
    * **Klucz podpisywania**: Użyj wartości domyślnej: **klucz 1**.
3. Wybierz pozycję **Generuj token SAS i adres URL obiektu BLOB**.
4. Utwórz kopię **adresu URL sygnatury dostępu współdzielonego obiektu BLOB**. W środku adresu URL jest nazwa pliku **SQLDatabaseExtension. BACPAC**.  Nazwa pliku dzieli adres URL na trzy części:

   - **Lokalizacja artefaktu**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Upewnij się, że lokalizacja jest zakończona znakiem "/".
   - **Nazwa pliku BACPAC**: SQLDatabaseExtension. BACPAC.
   - **Token sygnatury dostępu współdzielonego lokalizacji artefaktu**: Upewnij się, że token poprzedza znak "?".

     Te trzy wartości będą potrzebne w [ramach wdrażania szablonu](#deploy-the-template).

## <a name="open-an-existing-template"></a>Otwórz istniejący szablon

W tej sesji należy zmodyfikować szablon utworzony w [samouczku: Importowanie plików SQL BACPAC za pomocą szablonów Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) , aby wywołać plik BACPAC z tokenem SAS.  Szablon opracowany w samouczku rozszerzenia SQL jest udostępniany w serwisie [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. W obszarze Visual Studio Code wybierz pozycję **plik**>**Otwórz plik**.
2. W polu **Nazwa pliku**wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Wybierz pozycję **Otwórz** , aby otworzyć plik.

    W szablonie zdefiniowano pięć zasobów:

   * `Microsoft.Sql/servers`., Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`., Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`., Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.,  Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.,  Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Warto zapoznać się z podstawową wiedzą na temat szablonu przed jego rozpoczęciem.
4. Wybierz pozycję **plik**>**Zapisz jako,** aby zapisać kopię pliku na komputerze lokalnym przy użyciu nazwy **azuredeploy. JSON**.

## <a name="edit-the-template"></a>Edytuj szablon

Dodaj następujące dodatkowe parametry:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Samouczek Menedżer zasobów bezpiecznych parametrów artefaktów](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Zaktualizuj wartość następujących dwóch elementów:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Wdróż szablon

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zapoznaj się z sekcją [Wdrażanie szablonu](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) , aby zapoznać się z procedurą wdrażania. Zamiast tego użyj poniższego skryptu wdrażania programu PowerShell:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Użyj wygenerowanego hasła. Zobacz [wymagania wstępne](#prerequisites).
Aby uzyskać wartości _artifactsLocation, _artifactsLocationSasToken i bacpacFileName, zobacz [generowanie tokenu SAS](#generate-a-sas-token).

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

W tym samouczku wdrożono SQL Server, SQL Database i zaimportowano plik BACPAC przy użyciu tokenu sygnatury dostępu współdzielonego. Aby dowiedzieć się, jak utworzyć potok platformy Azure, aby ciągle opracowywać i wdrażać szablony Menedżer zasobów, zobacz

> [!div class="nextstepaction"]
> [Ciągła integracja z potokiem platformy Azure](./resource-manager-tutorial-use-azure-pipelines.md)
