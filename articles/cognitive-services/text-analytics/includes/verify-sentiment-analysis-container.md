---
title: Weryfikowanie wystąpienia kontenera analizy tonacji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zweryfikować wystąpienie kontenera analizy tonacji.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1303d753b1cbfabe7ddd3442e0880b0bffe089b3
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377401"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Weryfikowanie wystąpienia kontenera analizy tonacji

1. Wybierz kartę **Przegląd** i skopiuj adres IP.
1. Otwórz nową kartę przeglądarki, a następnie wprowadź adres IP. Na przykład wpisz `http://<IP-address>:5000 (http://55.55.55.55:5000`). Zostanie wyświetlona strona główna kontenera, która informuje o tym, że kontener jest uruchomiony.

    ![Wyświetl stronę główną kontenera, aby sprawdzić, czy jest uruchomiona](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. Wybierz link **opis interfejsu API usługi** , aby przejść do strony struktury Swagger kontenera.

1. Wybierz dowolny z **wpisów** interfejsów API, a następnie wybierz opcję **Wypróbuj**. Wyświetlane są parametry, które obejmują następujące przykładowe dane wejściowe:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Zastąp dane wejściowe następującą zawartością JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Ustaw **showStats** na wartość true.

1. Wybierz pozycję **Execute (wykonaj** ), aby określić tonacji tekstu.

    Model, który znajduje się w kontenerze, generuje wynik, który mieści się w zakresie od 0 do 1, gdzie 0 jest ujemna i 1 jest dodatnia.

    Zwracana odpowiedź JSON zawiera tonacji dla zaktualizowanych danych wejściowych tekstu:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Teraz można skorelować dokument `id` danych JSON ładunku odpowiedzi do oryginalnego dokumentu `id`ładunku żądania. Wynik więcej niż `.98` wskazuje silnie dodatnią tonacji.