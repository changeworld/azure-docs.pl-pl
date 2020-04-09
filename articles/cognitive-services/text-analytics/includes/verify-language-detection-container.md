---
title: Weryfikowanie wystąpienia kontenera wykrywania języka
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zweryfikować wystąpienie kontenera wykrywania języka.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876450"
---
### <a name="verify-the-language-detection-container-instance"></a>Weryfikowanie wystąpienia kontenera wykrywania języka

1. Wybierz kartę **Przegląd** i skopiuj adres IP.
1. Otwórz nową kartę przeglądarki i wprowadź adres IP. Na przykład `http://<IP-address>:5000 (http://55.55.55.55:5000`wprowadź ). Zostanie wyświetlona strona główna kontenera, która informuje, że kontener jest uruchomiony.

    ![Wyświetl stronę główną kontenera, aby sprawdzić, czy jest uruchomiona](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Wybierz **łącze Opis interfejsu API usługi,** aby przejść do strony Swagger kontenera.

1. Wybierz dowolny z **interfejsów** API POST i wybierz pozycję **Wypróbuj**. Wyświetlane są parametry, które obejmują ten przykładowy wkład:

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

1. Ustaw **showStats** na `true`.

1. Wybierz **polecenie Wykonaj,** aby określić tonację tekstu.

    Model, który jest pakowany w kontenerze generuje wynik, który waha się od 0 do 1, gdzie 0 jest negatywna tonacji i 1 jest pozytywne nastroje.

    Zwracana odpowiedź JSON zawiera tonację dla zaktualizowanego wprowadzania tekstu:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Możemy teraz skorelować dokumenty danych JSON ładunku odpowiedzi z oryginalnymi dokumentami `id`ładunku żądania przez ich odpowiednie . Każdy dokument jest traktowany niezależnie, zawierający `characterCount` `transactionCount`różne statystyki, takie jak i . Ponadto każdy wynikowy dokument `detectedLanguages` ma tablicę `iso6391Name`z `score` programem `name`, i dla każdego wykrytego języka. Po wykryciu `score` wielu języków jest używany do określenia najbardziej prawdopodobnego języka.