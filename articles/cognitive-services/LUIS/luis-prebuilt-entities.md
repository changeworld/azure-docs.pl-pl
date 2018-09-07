---
title: Wstępnie utworzonych jednostek Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Usługa LUIS zawiera zestaw wstępnie utworzonych jednostek rozpoznawania typowe rodzaje informacji, takich jak daty, godziny, liczby, pomiarów i waluty. Obsługa wstępnie utworzone jednostki jest zależna od kultury aplikacją usługi LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: f62c078a023d9ee7ca535cb5e02623df7a568e8a
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052877"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Wstępnie utworzonych jednostek, rozpoznawał standardowe typy danych

Usługa LUIS zawiera zestaw wstępnie utworzonych jednostek rozpoznawania typowe rodzaje informacji, takich jak daty, godziny, numery, pomiarów i waluty. Obsługa wstępnie utworzone jednostki jest zależna od kultury aplikacją usługi LUIS. Aby uzyskać pełną listę wstępnie utworzone jednostki, które obsługuje usługi LUIS, w tym pomoc od kultury, zobacz [odwołania do wstępnie utworzone jednostki](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **BUILTIN.DateTime** jest przestarzała. Zastępuje się wyrazami [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), która umożliwia rozpoznawanie daty i zakresy czasu, a także Rozpoznawanie niejednoznacznych daty i godziny.

## <a name="add-a-prebuilt-entity"></a>Dodawanie wstępnie utworzone jednostki

1. Otwórz aplikację, klikając jego nazwę **Moje aplikacje** strony, a następnie kliknij przycisk **jednostek** po lewej stronie. 
2. Na **jednostek** kliknij **Zarządzanie ze wstępnie utworzonych jednostek**.

3. W **Dodaj ze wstępnie utworzonych jednostek** okno dialogowe, kliknij przycisk wstępnie utworzone jednostki, które chcesz dodać (na przykład "datetimeV2"). Następnie kliknij przycisk **Save** (Zapisz).

    ![Dodaj wstępnie utworzone jednostki, okno dialogowe](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Użyj wstępnie utworzone jednostki liczb
Gdy wstępnie utworzone jednostki znajduje się w aplikacji, jej prognozy są uwzględnione w opublikowanej aplikacji. Działanie wstępnie utworzonych jednostek jest wstępnie przeszkolonych i **nie** można modyfikować. Wykonaj następujące kroki, aby zobaczyć, jak działa wstępnie utworzone jednostki:

1. Dodaj **numer** jednostki do aplikacji, następnie [Train](luis-interactive-test.md) i [publikowania](luis-how-to-publish-app.md) aplikacji.
2. Kliknij adres URL punktu końcowego w **publikowania aplikacji** strony, aby otworzyć punkt końcowy usługi LUIS w przeglądarce sieci web. 
3. Dołącz wypowiedź do adresu URL, który zawiera wyrażenie liczbowe. Na przykład można wpisać w `buy two plane ticktets`i zobaczyć, który identyfikuje LUIS `two` jako `builtin.number` jednostki i identyfikuje `2` jako jego wartość `resolution` pola. `resolution` Pola pomaga rozwiązać liczb i dat canonical formularza, który jest łatwiejsze dla aplikacji klienckich do użycia. 

    ![Wypowiedź w przeglądarce, zawierającą numer jednostki](./media/luis-use-prebuilt-entity/browser-query.png)

Usługa LUIS inteligentnie może rozpoznawać numery, które nie są w postaci niestandardowych. Wypróbuj różne wyrażeń liczbowych w swoje wypowiedzi i sprawdzić LUIS zwraca.

Poniższy kod przedstawia odpowiedź JSON, Luis, zawierającej rozpoznawanie wartości 24, wypowiedź "dwadzieścia".

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>Użyj jednostki datetimeV2 wbudowanych
**DatetimeV2** wstępnie utworzone jednostki rozpoznaje daty, godziny, zakresów dat i czasów trwania. Wykonaj następujące kroki, aby zobaczyć, jak `datetimeV2` wstępnie utworzone jednostki działania:

1. Dodaj **datetimeV2** jednostki do aplikacji, następnie [Train](luis-interactive-test.md) i [publikowania](luis-how-to-publish-app.md) aplikacji.
2. Kliknij adres URL punktu końcowego w **publikowania aplikacji** strony, aby otworzyć punkt końcowy usługi LUIS w przeglądarce sieci web. 
3. Adres URL, który zawiera zakres dat, Dołącz wypowiedź. Na przykład można wpisać w `book a flight tomorrow`i zobaczyć, który identyfikuje LUIS `tomorrow` jako `builtin.datetimeV2.date` jednostki i identyfikuje jutrzejszą datę jako wartość w `resolution` pola. 

Poniższy przykład pokazuje, co może wyglądać odpowiedź JSON z usługi LUIS gdyby bieżącą datę 31 października 2017 r.

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Odwołanie do wstępnie utworzone jednostki](./luis-reference-prebuilt-entities.md)
