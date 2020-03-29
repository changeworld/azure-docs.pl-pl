---
title: Wstępnie utworzone modele do rozumienia języka
titleSuffix: Azure Cognitive Services
description: Usługa LUIS zawiera zestaw wstępnie utworzonych modeli do szybkiego dodawania typowych scenariuszy użytkownika konwersacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013571"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Dodawanie wstępnie utworzonych modeli dla typowych scenariuszy użycia 

Usługa LUIS zawiera zestaw wstępnie utworzonych modeli do szybkiego dodawania typowych scenariuszy użytkownika konwersacji. Jest to szybki i łatwy sposób, aby dodać umiejętności do konwersacyjnej aplikacji klienta bez konieczności projektowania modeli dla tych umiejętności. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

1. Na stronie **Moje aplikacje** wybierz aplikację. Spowoduje to otwarcie aplikacji do sekcji **Kompilacja** aplikacji. 

1. Wybierz **wstępnie utworzone domeny** z lewego paska narzędzi. 

1. Znajdź domenę, którą chcesz dodać do aplikacji, a następnie wybierz przycisk **Dodaj domenę.**

    > [!div class="mx-imgBorder"]
    > ![Dodawanie wstępnie utworzonej domeny kalendarza](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Dodawanie wstępnie utworzonej intencji

1. Na stronie **Moje aplikacje** wybierz aplikację. Spowoduje to otwarcie aplikacji do sekcji **Kompilacja** aplikacji. 

1. Na stronie **Intencje** wybierz pozycję **Dodaj wstępnie utworzone intencje domeny** z paska narzędzi powyżej listy intencji. 

1. Z okna podręcznego wybierz opcję **Utilities.Cancel** intent. 

    > [!div class="mx-imgBorder"]
    > ![Dodaj wstępnie utworzony zamiar](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Wybierz przycisk **Gotowe.**

## <a name="add-a-prebuilt-entity"></a>Dodawanie wstępnie utworzonej encji

1. Otwórz aplikację, klikając jej nazwę na stronie **Moje aplikacje,** a następnie kliknij pozycję **Jednostki** po lewej stronie. 

1. Na stronie **Elementy** kliknij pozycję **Dodaj wstępnie skompilowany element**.

1. W oknie dialogowym **Dodawanie wstępnie utworzonych jednostek** wybierz wstępnie utworzony element. 

    > [!div class="mx-imgBorder"]
    > ![Okno dialogowe Dodawanie wstępnie utworzonej encji](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Wybierz pozycję **Done** (Gotowe). Po dodaniu jednostki nie trzeba trenować aplikacji. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publikowanie w celu wyświetlenia wstępnie utworzonego modelu z punktu końcowego prognozowania

Najprostszym sposobem, aby wyświetlić wartość wstępnie utworzonego modelu jest zapytanie z opublikowanego punktu końcowego. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Jednostki zawierające wstępnie utworzony token encji
 
Jeśli masz jednostkę nauczoną maszynowo, która jest ograniczona przez wstępnie utworzony element, dodaj podskład nieskładka do jednostki nauczonej maszyny, a następnie dodaj ograniczenie wstępnie utworzonej jednostki.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Tworzenie modelu z pliku csv za pomocą interfejsów API REST](./luis-tutorial-node-import-utterances-csv.md)
