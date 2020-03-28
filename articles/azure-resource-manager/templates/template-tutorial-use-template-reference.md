---
title: Dokumentacja dotycząca korzystania z szablonów
description: Skorzystaj z odwołania do szablonu usługi Azure Resource Manager, aby utworzyć szablon.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b742982121a20a2b057eba4211584b0386dde411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373428"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Samouczek: Korzystanie z odwołania do szablonu ARM

Dowiedz się, jak znaleźć informacje o schemacie szablonu i użyć tych informacji do utworzenia szablonów usługi Azure Resource Manager (ARM).

W tym samouczku użyjesz szablonu podstawowego z szablonów szybkiego startu platformy Azure. Korzystając z dokumentacji referencyjnej szablonu, można dostosować szablon.

![Odwołanie do szablonu Menedżera zasobów wdraża konto magazynu](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Informacje o szablonie
> * Znajdowanie dokumentacji szablonu
> * Edytowanie szablonu
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Tworzenie szablonów ARM za pomocą programu Visual Studio](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

[Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/) to repozytorium szablonów ARM. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Szablon definiuje zasób konta usługi Azure Storage.

1. W programie Visual Studio Code wybierz pozycję **Plik**>**otwórz plik**.
1. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
1. Wybierz **opcję Zapisz plik,**>**Save As** aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

## <a name="understand-the-schema"></a>Informacje o schemacie

1. W programie VS Code zwiń szablon do poziomu głównego. Masz najprostszą strukturę z następującymi elementami:

    ![Najprostsza struktura szablonu usługi Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: określ lokalizację pliku schematu JSON, który opisuje wersję języka szablonu.
    * **contentVersion**: określ dowolną wartość dla tego elementu, aby udokumentować znaczące zmiany w szablonie.
    * **parameters**: określ wartości zapewniane podczas wykonania wdrożenia, aby dostosować wdrożenie zasobu.
    * **variables**: określ wartości używane jako fragmenty JSON w szablonie, aby uprościć wyrażenia języka szablonu.
    * **resources**: określ typy zasobów, które są wdrażane lub aktualizowane w grupie zasobów.
    * **outputs**: określ wartości, które są zwracane po wdrożeniu.

1. Rozwiń element **resources**. Jest tam zdefiniowany zasób `Microsoft.Storage/storageAccounts`.

    ![Definicja konta magazynu szablonu usługi Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

## <a name="find-the-template-reference"></a>Znajdowanie dokumentacji szablonu

1. Przejdź do [odwołania do szablonu platformy Azure](https://docs.microsoft.com/azure/templates/).
1. W polu **Filtr według tytułu** wprowadź konta **magazynu**i wybierz pierwsze **konta magazynu** w obszarze Odwołanie **> Magazyn**.

    ![Konto magazynu odwołania do szablonu usługi Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Dostawca zasobów ma zwykle kilka wersji interfejsu API:

    ![Wersje konta magazynu referencyjnego szablonu Menedżera zasobów](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Z lewego okienka wybierz **pozycję Wszystkie zasoby** w obszarze **Magazyn.** Ta strona zawiera listę typów zasobów i wersji dostawcy zasobów magazynu. Zaleca się używanie najnowszych wersji interfejsu API dla typów zasobów zdefiniowanych w szablonie.

    ![Wersje kont magazynu referencyjnych szablonów Menedżera zasobów](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Wybierz najnowszą wersję **magazynuZakład zasobu.**  Najnowsza wersja to **2019-06-01,** gdy ten artykuł jest napisany.

1. Ta strona zawiera szczegółowe informacje o typie zasobu storageAccount.  Na przykład wyświetla listę dozwolonych wartości dla obiektu **Sku.** Jest więcej skus niż to, co jest wymienione w szablonie Szybkiego startu, który został otwarty wcześniej. Szablon przewodnika Szybki start można dostosować tak, aby zawierał wszystkie dostępne typy magazynu.

    ![Skus konta magazynu referencyjnego szablonu Menedżera zasobów](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Edytowanie szablonu

W programie Visual Studio Code dodaj dodatkowe typy kont magazynu, jak pokazano na poniższym zrzucie ekranu:

![Zasoby konta magazynu szablonów Menedżera zasobów](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Zapoznaj się z sekcją [Wdrażanie szablonu](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) przewodnika Szybki Start programu Visual Studio Code, aby uzyskać procedurę wdrażania. Podczas wdrażania szablonu określ parametr **storageAccountType** o nowo dodanej wartości, na przykład **Premium_ZRS**. Wdrożenie zakończy się niepowodzeniem, jeśli użyjesz oryginalnego szablonu szybkiego **startu,** ponieważ Premium_ZRS nie była dozwoloną wartością.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia dokumentacji szablonu w celu dostosowania istniejącego szablonu. Aby dowiedzieć się, jak utworzyć wiele wystąpień konta magazynu, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie wielu wystąpień](./template-tutorial-create-multiple-instances.md)
