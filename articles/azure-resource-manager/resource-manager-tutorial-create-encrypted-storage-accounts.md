---
title: Tworzenie szablonu usługi Azure Resource Manager na potrzeby wdrażania szyfrowanego konta magazynu | Microsoft Docs
description: Użyj programu Visual Studio Code, aby utworzyć szablon na potrzeby wdrażania szyfrowanego konta magazynu.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7c78636a210ae90c5bfe1d0bfd35e4e05633f5cd
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188203"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Samouczek: tworzenie szablonu usługi Azure Resource Manager na potrzeby wdrażania szyfrowanego konta magazynu

Dowiedz się, jak znaleźć informacje niezbędne do ukończenia szablonu usługi Azure Resource Manager.

W tym samouczku użyjesz szablonu podstawowego z szablonów szybkiego startu platformy Azure, aby utworzyć konto usługi Azure Storage.  Korzystając z dokumentacji referencyjnej szablonu, możesz dostosować szablon podstawowy, aby utworzyć szyfrowane konto magazynu.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Informacje o formacie szablonu
> * Używanie parametrów w szablonie
> * Korzystanie ze zmiennych w szablonie
> * Edytowanie szablonu
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* [Program Visual Studio Code](https://code.visualstudio.com/)
* Rozszerzenie Narzędzia usługi Resource Manager. Aby zainstalować to rozszerzenie, zobacz [Instalacja rozszerzenia Narzędzia usługi Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Szablon definiuje zasób konta usługi Azure Storage.

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz pozycję **File (Plik)**>**Save As (Zapisz jako)**, aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

## <a name="understand-the-format"></a>Omówienie formatu

W programie VS Code zwiń szablon do poziomu głównego. Masz najprostszą strukturę z następującymi elementami:

![Najprostsza struktura szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: określ lokalizację pliku schematu JSON, który opisuje wersję języka szablonu.
* **contentVersion**: określ dowolną wartość dla tego elementu, aby udokumentować znaczące zmiany w szablonie.
* **parameters**: określ wartości zapewniane podczas wykonania wdrożenia, aby dostosować wdrożenie zasobu.
* **variables**: określ wartości używane jako fragmenty JSON w szablonie, aby uprościć wyrażenia języka szablonu.
* **resources**: określ typy zasobów, które są wdrażane lub aktualizowane w grupie zasobów.
* **outputs**: określ wartości, które są zwracane po wdrożeniu.

## <a name="use-parameters-in-template"></a>Używanie parametrów w szablonie

Parametry umożliwiają dostosowanie wdrożenia poprzez podanie wartości dopasowanych do danego środowiska. Parametry zdefiniowane w szablonie są używane podczas ustawiania wartości dla konta magazynu.

![Parametry szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

W tym szablonie zdefiniowano dwa parametry. Zwróć uwagę na funkcję szablonu używaną w parametrze location.defaultValue:

```json
"defaultValue": "[resourceGroup().location]",
```

Funkcja resourceGroup() zwraca obiekt reprezentujący bieżącą grupę zasobów. Aby uzyskać listę funkcji szablonu, zobacz [Funkcje szablonu usługi Azure Resource Manager](./resource-group-template-functions.md).

Aby użyć parametrów zdefiniowanych w szablonie:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>Korzystanie ze zmiennych w szablonie

Zmienne umożliwiają tworzenie wartości, których można używać w całym szablonie. Zmienne pomagają w redukowaniu złożoności szablonu.

![Zmienne szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

Ten szablon definiuje jedną zmienną, *storageAccountName*. W definicji użyto dwóch funkcji szablonu:

- **concat()**: łączy ciągi. Aby uzyskać więcej informacji, zobacz [concat](./resource-group-template-functions-string.md#concat).
- **uniqueString()**: tworzy deterministyczny ciąg skrótu w oparciu o wartości podane jako parametry. Każde konto magazynu platformy Azure musi mieć unikatową nazwę dla całej platformy Azure. Ta funkcja udostępnia unikatowy ciąg. Aby uzyskać informacje o innych funkcjach ciągów, zobacz [Funkcje ciągów](./resource-group-template-functions-string.md).

Aby użyć zmiennej zdefiniowanej w szablonie:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>Edytowanie szablonu

Aby znaleźć konfigurację konta magazynu związaną z szyfrowaniem, możesz zajrzeć do dokumentacji szablonu dla konta usługi Azure Storage.

1. Przejdź do pozycji [Szablony platformy Azure](https://docs.microsoft.com/azure/templates/).
2. W spisie treści po lewej stronie wybierz pozycję **Dokumentacja**->**Magazyn**->**Konta magazynu**. Strona zawiera informacje umożliwiające zdefiniowanie informacji o koncie magazynu.
3. Przejrzyj informacje związane z szyfrowaniem.  
4. Wewnątrz elementu properties w definicji zasobu konta magazynu dodaj następujący kod json:

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    Ta część włącza funkcję szyfrowania usługi magazynu obiektów blob.

Ostateczny element resources wygląda następująco:

![Zasoby szyfrowanego konta magazynu w szablonie usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Istnieje wiele metod wdrażania szablonów.  W tym samouczku zostanie użyta usługa Cloud Shell z poziomu witryny Azure Portal. Usługa Cloud Shell obsługuje zarówno wiersz polecenia platformy Azure, jak i program Azure PowerShell. W instrukcjach podanych w tym miejscu używany jest interfejs wiersza polecenia.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Cloud Shell** w prawym górnym rogu, jak pokazano na poniższym obrazie:

    ![Usługa Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. Wybierz strzałkę w dół, a następnie wybierz pozycję **Bash**, jeżeli wybrana powłoka jest inna niż Bash. W tym samouczku użyty zostanie interfejs wiersza polecenia platformy Azure.

    ![Interfejs wiersza polecenia usługi Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. Wybierz pozycję **Uruchom ponownie**, aby ponownie uruchomić powłokę.
5. Wybierz pozycję **Przekaż/pobierz pliki**, a następnie wybierz pozycję **Przekaż**.

    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
6. Wybierz plik, który został zapisany wcześniej w ramach tego samouczka. Nazwa domyślna to **azuredeploy.json**.
7. W usłudze Cloud shell uruchom polecenie **ls**, aby zweryfikować, czy plik został pomyślnie przekazany. Można również użyć polecenia **cat**, aby zweryfikować zawartość szablonu.

    ![Wyświetlanie listy plików w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
8. W usłudze Cloud Shell uruchom następujące polecenia:

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file azuredeploy.json
    ```
    Poniżej przedstawiono zrzut ekranu przedstawiający przykładowe wdrożenie:

    ![Wdrażanie szablonu w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    Na zrzucie ekranu zostały użyte następujące wartości:

    * **&lt;ResourceGroupName>**: myresourcegroup0719. Istnieją dwa wystąpienia tego parametru.  Pamiętaj, aby użyć tej samej wartości.
    * **&lt;AzureLocation>**: eastus2
    * **&lt;DeployName>**: mydeployment0719
    * **&lt;TemplateFile>**: azuredeploy.json

    W danych wyjściowych na zrzucie ekranu nazwa konta magazynu to *fhqbfslikdqdsstandardsa*. 

9. Uruchom następujące polecenie programu PowerShell, aby wyświetlić nowo utworzone konto magazynu:

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

    Wyświetlone dane wyjściowe powinny być podobne do poniższego zrzutu ekranu, co wskazuje na włączenie szyfrowania dla magazynu obiektów blob.

    ![Szyfrowane konto magazynu usługi Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia dokumentacji szablonu w celu dostosowania istniejącego szablonu. Szablon używany w tym samouczku zawiera tylko jeden zasób platformy Azure.  W następnym samouczku zostanie utworzony szablon z wieloma zasobami.  Niektóre zasoby zawierają zasoby zależne.

> [!div class="nextstepaction"]
> [Tworzenie wielu zasobów](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
