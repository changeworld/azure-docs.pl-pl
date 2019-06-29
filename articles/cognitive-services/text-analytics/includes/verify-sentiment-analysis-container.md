---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak sprawdzić wystąpienia kontenera analizy tonacji.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455113"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Sprawdź wystąpienie kontenera analizy tonacji

1. Wybierz **Przegląd** kartę i skopiuj adres IP.
1. Otwórz nową kartę przeglądarki i użyj adresu IP, na przykład `http://<IP-address>:5000 (http://55.55.55.55:5000`). Strona główna kontenera zostanie wyświetlony, informacją o tym, kontener jest uruchomiony.

    ![Wyświetlanie strony głównej kontenera, aby sprawdzić, czy jest uruchomiona](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Wybierz **opis interfejsu API usługi** link, aby przejść do strony programu swagger kontenerów.

1. Wybierz dowolną z **WPIS** interfejsów API, a następnie wybierz pozycję **wypróbuj działanie rozwiązania**.  Parametry są wyświetlane w tym przykładowe dane wejściowe:

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

1. Zastąp dane wejściowe następującym kodem JSON:

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

    Model spakowane w kontenerze generuje wynik z zakresu od 0 do 1, gdzie 0 jest ujemna, a 1 jest dodatni.

    Zwrócił odpowiedź JSON zawiera wskaźniki nastrojów klientów dotyczące wprowadzania tekstu zaktualizowane:

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

Firma Microsoft może teraz skorelować dokumentu `id` ładunków odpowiedzi JSON, oryginalnym dokumencie ładunku żądania `id`i sprawdź, czy było wynikiem za pośrednictwem `.98` wskazująca wynik bardzo pozytywny wskaźniki nastrojów klientów.