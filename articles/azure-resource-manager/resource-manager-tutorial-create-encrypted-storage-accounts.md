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
ms.date: 10/18/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a3fc3e0cc30b379c84ac0ba12f733d2db4e41587
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945794"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Samouczek: tworzenie szablonu usługi Azure Resource Manager na potrzeby wdrażania szyfrowanego konta magazynu

Dowiedz się, jak znaleźć informacje niezbędne do ukończenia szablonu usługi Azure Resource Manager.

W tym samouczku użyjesz szablonu podstawowego z szablonów szybkiego startu platformy Azure, aby utworzyć konto usługi Azure Storage.  Korzystając z dokumentacji referencyjnej szablonu, możesz dostosować szablon podstawowy, aby utworzyć szyfrowane konto magazynu.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Informacje o szablonie
> * Edytowanie szablonu
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem [Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Szablon definiuje zasób konta usługi Azure Storage.

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz pozycję **File (Plik)**>**Save As (Zapisz jako)**, aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

## <a name="understand-the-schema"></a>Informacje o schemacie

W programie VS Code zwiń szablon do poziomu głównego. Masz najprostszą strukturę z następującymi elementami:

![Najprostsza struktura szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: określ lokalizację pliku schematu JSON, który opisuje wersję języka szablonu.
* **contentVersion**: określ dowolną wartość dla tego elementu, aby udokumentować znaczące zmiany w szablonie.
* **parameters**: określ wartości zapewniane podczas wykonania wdrożenia, aby dostosować wdrożenie zasobu.
* **variables**: określ wartości używane jako fragmenty JSON w szablonie, aby uprościć wyrażenia języka szablonu.
* **resources**: określ typy zasobów, które są wdrażane lub aktualizowane w grupie zasobów.
* **outputs**: określ wartości, które są zwracane po wdrożeniu.

## <a name="use-parameters"></a>Używanie parametrów

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

## <a name="use-variables"></a>Używanie zmiennych

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

Celem tego samouczka jest zdefiniowanie szablonu w celu utworzenia zaszyfrowanego konta magazynu.  Na podstawie przykładowego szablonu można utworzyć tylko podstawowe, niezaszyfrowane konto magazynu. Aby znaleźć konfigurację związaną z szyfrowaniem, możesz zajrzeć do dokumentacji szablonu dla konta usługi Azure Storage.

1. Przejdź do pozycji [Szablony platformy Azure](https://docs.microsoft.com/azure/templates/).
2. W polu **Filtruj według tytułu** wprowadź ciąg **konta magazynu**.
3. Wybierz pozycję **Odwołanie/Szablon odwołania/Magazyn/Konta magazynu** w sposób pokazany na poniższym zrzucie ekranu:

    ![Konto magazynu odwołania do szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

4. Znajdź informacje o definicjach związanych z szyfrowaniem.  

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
5. Z poziomu programu Visual Studio Code zmodyfikuj szablon, aby ostateczny element resources wyglądał następująco:
    
    ![Zasoby szyfrowanego konta magazynu w szablonie usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Zapoznaj się z sekcją [Wdrażanie szablonu](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) przewodnika Szybki Start programu Visual Studio Code, aby uzyskać procedurę wdrażania.

Poniższy zrzut ekranu przedstawia polecenie interfejsu wiersza polecenia umożliwiające wyświetlanie nowo utworzonego konta magazynu, co oznacza, że dla magazynu obiektów blob zostało włączone szyfrowanie.

![Szyfrowane konto magazynu usługi Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia dokumentacji szablonu w celu dostosowania istniejącego szablonu. Aby dowiedzieć się, jak utworzyć wiele wystąpień konta magazynu, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie wielu wystąpień](./resource-manager-tutorial-create-multiple-instances.md)
