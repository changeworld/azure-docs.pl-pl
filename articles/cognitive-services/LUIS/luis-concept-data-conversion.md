---
title: Konwersja danych — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak można zmienić wypowiedzi przed przewidywaniem w zrozumieni języku (LUIS)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68619804"
---
# <a name="convert-data-format-of-utterances"></a>Konwertowanie formatu danych wypowiedzi
Usługa LUIS udostępnia następujące konwersje wypowiedzi użytkownika przed przewidywaniem"

* Mowa do tekstu przy użyciu usługi [Cognitive Services Speech.](../Speech-Service/overview.md) 

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zamiana mowy na tekst jest dostarczana jako integracja z usługą LUIS. 

### <a name="intent-conversion-concepts"></a>Koncepcje konwersji intencji
Konwersja mowy do tekstu w usłudze LUIS umożliwia wysyłanie wypowiedzi mówionych do punktu końcowego i odbieranie odpowiedzi przewidywania usługi LUIS. Proces jest integracją usługi [mowy](https://docs.microsoft.com/azure/cognitive-services/Speech) z usługą LUIS. Dowiedz się więcej o mowy do intencji z [samouczka](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Podstawowe wymagania
Nie trzeba tworzyć klucz **interfejsu API mowy Bing** dla tej integracji. Klucz **zrozumienia języka** utworzony w witrynie Azure portal działa dla tej integracji. Nie należy używać klucza startowego usługi LUIS.

### <a name="pricing-tier"></a>Warstwa cenowa
Ta integracja używa innego modelu [cenowego](luis-boundaries.md#key-limits) niż zwykłe warstwy cenowe opisu języka. 

### <a name="quota-usage"></a>Użycie przydziału
Informacje [można znaleźć w zakresie limitów](luis-boundaries.md#key-limits) kluczy. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie danych](luis-concept-data-extraction.md)

