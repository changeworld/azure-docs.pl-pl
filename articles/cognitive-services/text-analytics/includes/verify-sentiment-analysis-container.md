---
title: Weryfikowanie wystąpienia kontenera Analiza tonacji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zweryfikować wystąpienie kontenera analiza tonacji.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 2e201b4ec0d1364ea99b376171efabad65af0a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968600"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Weryfikowanie wystąpienia kontenera Analiza tonacji

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

    Model, który jest pakowany w kontenerze generuje wynik, który waha się od 0 do 1, gdzie 0 jest negatywna tonacji i 1 jest pozytywne nastroje.

    Zwracana odpowiedź JSON zawiera tonację dla zaktualizowanego wprowadzania tekstu:

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

Możemy teraz skorelować `id` dokument danych JSON ładunku odpowiedzi z oryginalnym `id`dokumentem ładunku żądania . Wynik więcej niż `0.98` wskazuje na bardzo pozytywne nastroje.