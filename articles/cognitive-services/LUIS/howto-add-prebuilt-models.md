---
title: Wstępnie utworzone modele dla Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS zawiera zestaw wstępnie skompilowanych modeli umożliwiających szybkie dodawanie wspólnych scenariuszy użytkowników.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013571"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Dodaj wstępnie zbudowane modele dla typowych scenariuszy użycia 

LUIS zawiera zestaw wstępnie skompilowanych modeli umożliwiających szybkie dodawanie wspólnych scenariuszy użytkowników. Jest to szybki i łatwy sposób dodawania możliwości do konwersacji aplikacji klienckiej bez konieczności projektowania modeli dla tych możliwości. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

1. Na **Moje aplikacje** wybierz aplikację. Spowoduje to otwarcie aplikacji **kompilacji** części aplikacji. 

1. Wybierz opcję **prekompilowane domeny** na pasku narzędzi po lewej stronie. 

1. Znajdź domenę, którą chcesz dodać do aplikacji, a następnie wybierz przycisk **Dodaj domenę** .

    > [!div class="mx-imgBorder"]
    > ![dodać prezbudowaną domenę kalendarza](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Dodawanie wbudowanych intencji

1. Na **Moje aplikacje** wybierz aplikację. Spowoduje to otwarcie aplikacji **kompilacji** części aplikacji. 

1. Na stronie **intencje** wybierz pozycję **Dodaj prebudowany cel domeny** z paska narzędzi powyżej listy intencje. 

1. Wybierz **Utilities.Cancel** intencji w podręcznym oknie dialogowym. 

    > [!div class="mx-imgBorder"]
    > ![dodać prekompilowane zamierzenia](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Wybierz **gotowe** przycisku.

## <a name="add-a-prebuilt-entity"></a>Dodawanie wstępnie utworzone jednostki

1. Otwórz aplikację, klikając jego nazwę **Moje aplikacje** strony, a następnie kliknij przycisk **jednostek** po lewej stronie. 

1. Na **jednostek** kliknij **Dodawanie wstępnie utworzone jednostki**.

1. W oknie dialogowym **Dodawanie wstępnie utworzonych jednostek** Wybierz wstępnie utworzoną jednostkę. 

    > [!div class="mx-imgBorder"]
    > okno dialogowe Dodawanie wstępnie skompilowanej jednostki ![](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Wybierz pozycję **Done** (Gotowe). Po dodaniu jednostki, nie musisz uczyć aplikacji. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publikuj w celu wyświetlenia prekompilowanego modelu z poziomu punktu końcowego przewidywania

Najprostszym sposobem wyświetlenia wartości prekompilowanego modelu jest zapytanie z opublikowanego punktu końcowego. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Jednostki zawierające wstępnie utworzony token jednostki
 
Jeśli masz jednostkę uczenia maszynowego, która jest ograniczona przez wstępnie utworzoną jednostkę, Dodaj podskładnik do jednostki, której to dotyczy, a następnie Dodaj ograniczenie wstępnie skompilowanej jednostki.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Kompilowanie modelu z pliku CSV przy użyciu interfejsów API REST](./luis-tutorial-node-import-utterances-csv.md)
