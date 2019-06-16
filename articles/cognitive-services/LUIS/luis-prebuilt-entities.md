---
title: Wstępnie utworzonych jednostek
titleSuffix: Azure Cognitive Services
description: Usługa LUIS zawiera zestaw wstępnie utworzonych jednostek rozpoznawania typowe rodzaje informacji, takich jak daty, godziny, liczby, pomiarów i waluty. Obsługa wstępnie utworzone jednostki jest zależna od kultury aplikacją usługi LUIS.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cfc4ff58cfeb65f80f9ac5ce2dd532defde5ef8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60596109"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Wstępnie utworzonych jednostek, rozpoznawał standardowe typy danych

Usługa LUIS zawiera zestaw wstępnie utworzonych jednostek rozpoznawania typowe rodzaje informacji, takich jak daty, godziny, numery, pomiarów i waluty. 

## <a name="add-a-prebuilt-entity"></a>Dodawanie wstępnie utworzone jednostki

1. Otwórz aplikację, klikając jego nazwę **Moje aplikacje** strony, a następnie kliknij przycisk **jednostek** po lewej stronie. 

1. Na **jednostek** kliknij **Dodawanie wstępnie utworzone jednostki**.

1. W **Dodaj ze wstępnie utworzonych jednostek** okna dialogowego Wybierz datetimeV2 wstępnie utworzone jednostki. 

    ![Dodaj wstępnie utworzone jednostki, okno dialogowe](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Wybierz pozycję **Done** (Gotowe).

## <a name="publish-the-app"></a>Publikowanie aplikacji

Najprostszym sposobem, aby wyświetlić wartość wstępnie utworzone jednostki jest zapytanie z opublikowanych punktu końcowego. 

1. Na górnym pasku narzędzi wybierz **Publikuj**. Publikowanie **produkcji**. 

1. Gdy pojawi się powiadomienie o powodzeniu zielony, wybierz **można znaleźć na liście punktów końcowych** linku umożliwia wyświetlenie listy punktów końcowych.

1. Wybierz punkt końcowy. Punkt końcowy zostanie otwarta nowa karta przeglądarki. Nie zamykaj kartę przeglądarki i w dalszym ciągu **testu** sekcji.

## <a name="test"></a>Testowanie
Po dodaniu jednostki, nie musisz uczyć aplikacji. 

Test nowe opcje w punkcie końcowym dodany przez wartość **q** parametru. Skorzystaj z poniższej tabeli wypowiedzi sugerowanych dla **q**:

|Wypowiedź testu|Wartość jednostki|
|--|:--|
|Zarezerwuj lot jutro|2018-10-19|
|Anuluj terminu 3 marca|LUIS zwrócił najnowszych marca 3 w przeszłości (2018-03-03) i marca 3 w przyszłości (2019-03-03), ponieważ wypowiedź nie określił roku.|
|Zaplanuj spotkanie o 10: 00|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Oznaczanie jednostek zawierający token wstępnie utworzone jednostki
 W przypadku tekstu, takie jak `HH-1234`, którą chcesz oznaczyć jako jednostka niestandardowa _i_ masz [liczbę wstępnie](luis-reference-prebuilt-number.md) dodana do modelu, nie będzie mógł oznaczyć jednostki niestandardowej w portalu usługi LUIS. Możesz oznaczyć go za pomocą interfejsu API. 

 Aby oznaczyć ten typ tokenu, gdy jej część jest oznaczona jako przy użyciu wstępnie utworzone jednostki, należy usunąć wstępnie utworzone jednostki z aplikacji usługi LUIS. Nie potrzebujesz, to w opracowywaniu aplikacji. Następnie oznaczyć tokenu przy użyciu jednostki niestandardowej. Wstępnie utworzone jednostki można następnie dodać do aplikacji usługi LUIS.

 Inny przykład należy wziąć pod uwagę wypowiedź jako listę preferencji klasy: `I want first year spanish, second year calculus, and fourth year english lit.` Jeśli aplikacja usługi LUIS ma numer Prebuild dodane, `first`, `second`, i `fourth` już będą oznaczone znakiem liczb porządkowych. Jeśli chcesz przechwytywać numeru porządkowego a klasy, można tworzyć złożone jednostki i otacza go wstępnie porządkowe i jednostką niestandardową dla nazwy klasy.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Odwołanie do wstępnie utworzone jednostki](./luis-reference-prebuilt-entities.md)
