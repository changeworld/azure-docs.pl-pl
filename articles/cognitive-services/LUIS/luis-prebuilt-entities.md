---
title: Wstępnie utworzonych jednostek Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Usługa LUIS zawiera zestaw wstępnie utworzonych jednostek rozpoznawania typowe rodzaje informacji, takich jak daty, godziny, liczby, pomiarów i waluty. Obsługa wstępnie utworzone jednostki jest zależna od kultury aplikacją usługi LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 0fe9dbed302fd2d61305167a3bda25b1b403b761
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139978"
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


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Odwołanie do wstępnie utworzone jednostki](./luis-reference-prebuilt-entities.md)
