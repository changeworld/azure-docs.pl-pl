---
title: Sprawdź wystąpienie kontenera wyodrębniania frazy kluczowej
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zweryfikować wystąpienie kontenera wyodrębniania fraz kluczowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876448"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Sprawdź wystąpienie kontenera wyodrębniania frazy kluczowej

1. Wybierz kartę **Przegląd** i skopiuj adres IP.
1. Otwórz nową kartę przeglądarki i wprowadź adres IP. Na przykład `http://<IP-address>:5000 (http://55.55.55.55:5000`wprowadź ). Zostanie wyświetlona strona główna kontenera, która informuje, że kontener jest uruchomiony.

    ![Wyświetl stronę główną kontenera, aby sprawdzić, czy jest uruchomiona](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Wybierz **łącze Opis interfejsu API usługi,** aby przejść do strony Swagger kontenera.

1. Wybierz dowolny z **interfejsów** API POST i wybierz pozycję **Wypróbuj**. Wyświetlane są parametry, które obejmują ten przykładowy wkład:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
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

1. Ustaw **showStats** na `true`.

1. Wybierz **polecenie Wykonaj,** aby określić tonację tekstu.

    Model, który jest pakowany w kontenerze generuje wynik, który waha się od 0 do 1, gdzie 0 jest ujemna, a 1 jest dodatnia.

    Zwracana odpowiedź JSON zawiera tonację dla zaktualizowanego wprowadzania tekstu:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
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

Możemy teraz skorelować `id` dokument danych JSON ładunku odpowiedzi z oryginalnym `id`dokumentem ładunku żądania . Wynikowy dokument ma `keyPhrases` tablicę, która zawiera listę fraz kluczowych, które zostały wyodrębnione z odpowiedniego dokumentu wejściowego. Ponadto istnieją różne statystyki, takie `characterCount` `transactionCount` jak i dla każdego wynikowego dokumentu.