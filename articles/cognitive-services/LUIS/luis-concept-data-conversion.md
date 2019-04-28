---
title: Konwersja danych
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dowiedz się, jak można zmienić wypowiedzi przed prognozy w Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: bdf2d87e558726937fa4221a0d95179216c66051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813816"
---
# <a name="convert-data-format-of-utterances"></a>Konwertuj wypowiedzi format danych
Usługa LUIS używa usługi mowy w usłudze Cognitive Services konwersji wypowiedzi prowadzone wypowiedzi wypowiedzi tekstu przed prognozy. 

## <a name="speech-to-intent-conversion-concepts"></a>Zamiana mowy na pojęcia konwersji elementu intent
Konwersja mowy na tekst w LUIS umożliwia wysyłania wypowiedzi mowy do punktu końcowego i odbierania odpowiedzi prognoz usługi LUIS. Ten proces jest integracja [mowy](https://docs.microsoft.com/azure/cognitive-services/Speech) usługi z użyciem usługi LUIS. Dowiedz się więcej o zamiana mowy na intencję z [samouczek](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Podstawowe wymagania
Nie musisz utworzyć **modułu Speech API Bing** klucza dla tej integracji. A **Language Understanding** klucza utworzonego w portalu Azure działa w przypadku tej integracji. Nie należy używać klucza starter usługi LUIS.

### <a name="pricing-tier"></a>Warstwa cenowa
Integracja ta używa innego [ceny](luis-boundaries.md#key-limits) modelu niż zwykle Language Understanding z warstw cenowych. 

### <a name="quota-usage"></a>Użycie przydziału
Zobacz [klucza limity](luis-boundaries.md#key-limits) informacji. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Używaj mowy na tekst](luis-tutorial-speech-to-intent.md)

