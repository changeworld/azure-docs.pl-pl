---
title: Korzystanie z dokumentacji szablonu usługi Azure Resource Manager | Microsoft Docs
description: Skorzystaj z dokumentacji szablonu usługi Azure Resource Manager, aby utworzyć szablon na potrzeby wdrażania szyfrowanego konta magazynu.
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
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 81c02c5e39e23b49291561821b0732227feb5c05
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103652"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>Samouczek: Korzystanie z dokumentacji szablonu usługi Azure Resource Manager

Dowiedz się, jak znaleźć informacje o schemacie szablonu i używać tych informacji do tworzenia szablonów usługi Azure Resource Manager.

W tym samouczku użyjesz szablonu podstawowego z szablonów szybkiego startu platformy Azure. Korzystając z dokumentacji referencyjnej szablonu, możesz dostosować szablon podstawowy, aby utworzyć zaszyfrowane konto usługi Storage.

![Odwołanie do szablonu usługi Resource Manager wdrażanie zaszyfrowanego konta magazynu](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Informacje o szablonie
> * Znajdowanie dokumentacji szablonu
> * Edytowanie szablonu
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem [Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

[Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Szablon definiuje zasób konta usługi Azure Storage.

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz pozycję **File (Plik)**>**Save As (Zapisz jako)**, aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

## <a name="understand-the-schema"></a>Informacje o schemacie

1. W programie VS Code zwiń szablon do poziomu głównego. Masz najprostszą strukturę z następującymi elementami:

    ![Najprostsza struktura szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: określ lokalizację pliku schematu JSON, który opisuje wersję języka szablonu.
    * **contentVersion**: określ dowolną wartość dla tego elementu, aby udokumentować znaczące zmiany w szablonie.
    * **parameters**: określ wartości zapewniane podczas wykonania wdrożenia, aby dostosować wdrożenie zasobu.
    * **variables**: określ wartości używane jako fragmenty JSON w szablonie, aby uprościć wyrażenia języka szablonu.
    * **resources**: określ typy zasobów, które są wdrażane lub aktualizowane w grupie zasobów.
    * **outputs**: określ wartości, które są zwracane po wdrożeniu.

2. Rozwiń element **resources**. Jest tam zdefiniowany zasób `Microsoft.Storage/storageAccounts`. Ten szablon tworzy nieszyfrowane konto usługi Storage.

    ![Definicja konta magazynu szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>Znajdowanie dokumentacji szablonu

1. Przejdź do [odwołanie do szablonu usługi Azure](https://docs.microsoft.com/azure/templates/).
2. W **Filtruj według tytułu** wprowadź **kont magazynu**.
3. Wybierz **odwołania/szablonu odwołania/Storage/&lt;wersji > / konta magazynu** jak pokazano na poniższym zrzucie ekranu:

    ![Konto magazynu odwołania do szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    Jeśli nie wiesz, którą wersję wybrać, użyj najnowszej wersji.

4. Znajdź informacje o definicjach związanych z szyfrowaniem.  

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    Na tej samej stronie internetowej następujący opis potwierdza, że obiekt `encryption` jest używany do tworzenia szyfrowanego konta magazynu.

    ![Szyfrowanie konta magazynu w odwołaniu do szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    Istnieją dwa sposoby zarządzania kluczem szyfrowania. Można użyć kluczy szyfrowania zarządzanych przez firmę Microsoft za pomocą szyfrowania usługi Storage lub użyć własnych kluczy szyfrowania. W celu uproszczenia w tym samouczku używasz opcji `Microsoft.Storage`, więc nie musisz tworzyć usługi Azure Key Vault.

    ![Obiekt szyfrowania konta magazynu w odwołaniu do szablonu usługi Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    Obiekt szyfrowania powinien wyglądać następująco:

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>Edytowanie szablonu

Z poziomu programu Visual Studio Code zmodyfikuj szablon, aby element resources wyglądał następująco:

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

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia dokumentacji szablonu w celu dostosowania istniejącego szablonu. Aby dowiedzieć się, jak utworzyć wiele wystąpień konta magazynu, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie wielu wystąpień](./resource-manager-tutorial-create-multiple-instances.md)
