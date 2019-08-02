---
title: Konwersja danych — LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak można zmienić wypowiedzi przed prognozy w Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: aeb9094db83b14af988f70485788934a7854200c
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619804"
---
# <a name="convert-data-format-of-utterances"></a>Konwertuj wypowiedzi format danych
LUIS udostępnia następujące konwersje wypowiedź użytkownika przed przewidywaniam "

* Zamiana mowy na tekst za pomocą usługi [Cognitive Services Speech](../Speech-Service/overview.md) . 

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zamiana mowy na tekst jest zapewniana jako integracja z usługą LUIS. 

### <a name="intent-conversion-concepts"></a>Pojęcia dotyczące konwersji intencji
Konwersja mowy na tekst w LUIS umożliwia wysyłania wypowiedzi mowy do punktu końcowego i odbierania odpowiedzi prognoz usługi LUIS. Ten proces jest integracja [mowy](https://docs.microsoft.com/azure/cognitive-services/Speech) usługi z użyciem usługi LUIS. Dowiedz się więcej na temat zamiany [](../speech-service/how-to-recognize-intents-from-speech-csharp.md)mowy z samouczkiem.

### <a name="key-requirements"></a>Podstawowe wymagania
Nie musisz utworzyć **modułu Speech API Bing** klucza dla tej integracji. A **Language Understanding** klucza utworzonego w portalu Azure działa w przypadku tej integracji. Nie używaj klucza początkowego LUIS.

### <a name="pricing-tier"></a>Warstwa cenowa
Ta integracja korzysta z innego modelu [cen](luis-boundaries.md#key-limits) niż zwykle Language Understanding warstwy cenowe. 

### <a name="quota-usage"></a>Użycie przydziału
Zobacz [klucza limity](luis-boundaries.md#key-limits) informacji. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie danych](luis-concept-data-extraction.md)

