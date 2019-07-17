---
title: Sprawdź wystąpienie kontenera analizy tonacji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak sprawdzić wystąpienia kontenera analizy tonacji.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f69d573e9e70a505018e94cca354f363097cc1b8
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229186"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Sprawdź wystąpienie kontenera analizy tonacji

1. Wybierz **Przegląd** kartę i skopiuj adres IP.
1. Otwórz nową kartę przeglądarki i wpisz adres IP. Adapterem, wprowadź `http://<IP-address>:5000 (http://55.55.55.55:5000`). Zostanie wyświetlona strona główna kontenera, informacją o tym, kontener jest uruchomiony.

    ![Wyświetl stronę główną kontenera, aby sprawdzić, czy jest uruchomiona](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. Wybierz **opis interfejsu API usługi** link, aby przejść do strony programu swagger kontenera.

1. Wybierz dowolną z **WPIS** interfejsów API, a następnie wybierz pozycję **wypróbuj działanie rozwiązania**.  Parametry są wyświetlane, łącznie z tym przykładowe dane wejściowe:

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

1. Zastąp dane wejściowe o następującej zawartości JSON:

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

1. Wybierz **Execute** ustalenie tonacji tekstu.

    Model, który jest spakowany w kontenerze generuje wynik z zakresu od 0 do 1, gdzie 0 jest ujemna, a 1 jest dodatni.

    Odpowiedź JSON, który jest zwracany zawiera wskaźniki nastrojów klientów dotyczące wprowadzania tekstu zaktualizowane:

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

Firma Microsoft może teraz skorelować dokumentu `id` ładunek odpowiedzi danych JSON, oryginalnym dokumencie ładunku żądania `id`. Widzimy ocenę ponad `.98`, wskazując silnie pozytywną tonację.