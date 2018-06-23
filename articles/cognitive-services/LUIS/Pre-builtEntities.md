---
title: Wbudowane jednostek LUIS | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę wbudowane jednostek, które znajdują się w języku opis inteligentnego usług (LUIS).
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: cahann
ms.reviewer: v-geberr
ms.openlocfilehash: 4858de8b8517016ca385e9105ca8948b66aa683b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36322051"
---
# <a name="prebuilt-entities"></a>Wbudowane jednostek

LUIS obejmuje zestaw jednostek wbudowane rozpoznawania typowych informacji, takich jak daty, godziny, cyfry, pomiarów i waluty. Obsługa wbudowane jednostki jest zależna od kultury LUIS aplikacji. Aby uzyskać pełną listę wbudowane jednostek, które obsługuje LUIS, w tym obsługi przez kultury, zobacz [odwołania do jednostki wbudowane](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **BUILTIN.DateTime** jest przestarzały. Został zastąpiony przez [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), zapewniające rozpoznawania Data i godzina zakresów, a także Rozpoznawanie niejednoznacznych dat i godzin.

## <a name="add-a-prebuilt-entity"></a>Dodawanie jednostki wbudowane

1. Otwórz aplikację, klikając jej nazwę na **Moje aplikacje** , a następnie kliknij przycisk **jednostek** po lewej stronie. 
2. Na **jednostek** kliknij przycisk **Zarządzanie wbudowane jednostek**.

    ![Strona jednostek — Zarządzanie wbudowane jednostek](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. W **Dodaj jednostki wbudowane** okna dialogowego kliknij wbudowane jednostki, które chcesz dodać (na przykład "datetimeV2"). Następnie kliknij przycisk **Save** (Zapisz).

    ![Dodaj jednostki wbudowane — okno dialogowe](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Użyj wbudowane, liczba jednostek
Gdy jednostka wbudowane jest dołączony do aplikacji, jego prognoz znajdują się w opublikowanej aplikacji. Zachowanie wbudowane jednostek jest wstępnie przeszkolone i **nie** można modyfikować. Wykonaj następujące kroki, aby zobaczyć, jak działa wbudowane jednostki:

1. Dodaj **numer** jednostki do aplikacji, następnie [pociągu](interactive-test.md) i [publikowania](PublishApp.md) aplikacji.
2. Kliknij adres URL punktu końcowego w **publikowania aplikacji** strony, aby otworzyć punktu końcowego LUIS w przeglądarce sieci web. 
3. Dołącz utterance do adresu URL, który zawiera wyrażenie liczbowe. Na przykład można wpisać w `buy two plane ticktets`i zobacz, który identyfikuje LUIS `two` jako `builtin.number` jednostki i identyfikuje `2` jako jego wartość `resolution` pola. `resolution` Pole ułatwia rozpoznać liczb i dat forma kanoniczna, ułatwiający aplikacji klienta do użycia. 

    ![utterance w przeglądarce, zawierającą numer jednostki](./media/luis-use-prebuilt-entity/browser-query.png)

LUIS inteligentnie może rozpoznawać numery, które nie są w formie niestandardowych. Wypróbować różne wyrażenia liczbowe w Twojej zniesławiających, a następnie sprawdzić LUIS zwraca.

W poniższym przykładzie przedstawiono odpowiedź w formacie JSON z LUIS, zawierającą rozpoznanie wartość 24 utterance "dwadzieścia".

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
## <a name="use-a-prebuilt-datetimev2-entity"></a>Użyj jednostki datetimeV2 wbudowane
**DatetimeV2** wbudowane jednostki rozpoznaje daty, godziny, zakres dat i okresach czasu. Wykonaj następujące kroki, aby zobaczyć, jak `datetimeV2` jednostki wbudowane działania:

1. Dodaj **datetimeV2** jednostki do aplikacji, następnie [pociągu](interactive-test.md) i [publikowania](PublishApp.md) aplikacji.
2. Kliknij adres URL punktu końcowego w **publikowania aplikacji** strony, aby otworzyć punktu końcowego LUIS w przeglądarce sieci web. 
3. Dołącz utterance do adresu URL, który zawiera zakres dat. Na przykład można wpisać w `book a flight tomorrow`i zobacz, który identyfikuje LUIS `tomorrow` jako `builtin.datetimeV2.date` jednostki i określa datę w przyszłości jako jego wartość `resolution` pola. 

W poniższym przykładzie przedstawiono odpowiedzi JSON z LUIS przykładową 2017 31 października gdyby dzisiaj.

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
> [Odwołanie do jednostki wbudowane](./luis-reference-prebuilt-entities.md)
