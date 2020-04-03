---
title: Wdrażanie usługi za pomocą szablonu usługi Azure Resource Manager
titleSuffix: Azure Cognitive Search
description: Można szybko wdrożyć wystąpienie usługi Azure Cognitive Search przy użyciu szablonu Menedżera zasobów platformy Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607624"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Szybki start: wdrażanie wyszukiwania kognitywnego przy użyciu szablonu Menedżera zasobów

W tym artykule otrzymasz od procesu wdrażania zasobu usługi Azure Cognitive Search przy użyciu szablonu Usługi Azure Cognitive Search.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-a-cognitive-search-service"></a>Tworzenie usługi wyszukiwania poznawczego

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów platformy Azure](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

Zasób platformy Azure zdefiniowany w tym szablonie:

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): tworzenie usługi Azure Cognitive Search

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy zasób usługi Azure Cognitive Search.

[![Wdrażanie na platformie Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

Portal wyświetla formularz, który umożliwia łatwe podanie wartości parametrów. Niektóre parametry są wstępnie wypełnione wartościami domyślnymi z szablonu. Musisz podać subskrypcję, grupę zasobów, lokalizację i nazwę usługi. Jeśli chcesz używać usług Cognitive Services w potoku [wzbogacania AI,](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) na przykład do analizowania plików obrazów binarnych dla tekstu, wybierz lokalizację, która oferuje zarówno wyszukiwanie funkcji Poznawczych, jak i usługi Cognitive Services. Obie usługi muszą znajdować się w tym samym regionie dla obciążeń wzbogacania AI. Po wypełnieniu formularza należy zaakceptować warunki, a następnie wybrać przycisk zakupu, aby zakończyć wdrożenie.

> [!div class="mx-imgBorder"]
> ![Wyświetlanie szablonu w portalu Azure](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Po zakończeniu wdrażania można uzyskać dostęp do nowej grupy zasobów i nowej usługi wyszukiwania w portalu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne szybkie starty wyszukiwania poznawczego i samouczki opierają się na tym przewodniku Szybki start. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start i samouczkami, możesz pozostawić ten zasób na swoim miejscu. Gdy nie jest już potrzebna, można usunąć grupę zasobów, która usuwa usługę wyszukiwania funkcji Cognitive Search i powiązane zasoby.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono usługę wyszukiwania funkcji Cognitive Search przy użyciu szablonu usługi Azure Resource Manager i zweryfikowano wdrożenie. Aby dowiedzieć się więcej o wyszukiwarce funkcji Cognitive Search i usłudze Azure Resource Manager, przejdź do poniższych artykułów.

 - Przeczytaj [omówienie usługi Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Tworzenie indeksu](https://docs.microsoft.com/azure/search/search-get-started-portal) dla usługi wyszukiwania
 - [Tworzenie aplikacji wyszukiwania](https://docs.microsoft.com/azure/search/search-create-app-portal) za pomocą kreatora portalu
 - [Tworzenie zestawu umiejętności w](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) celu wyodrębniania informacji z danych


