---
title: Pojęcia dotyczące konwersji danych usługi LUIS — Language Understanding
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak można zmienić wypowiedzi przed prognozy w Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 3862a0dbd94b5764cf506b05201d8dc60430fc7d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038973"
---
# <a name="data-conversion-concepts-in-luis"></a>Pojęcia dotyczące konwersji danych usługi LUIS
Usługa LUIS używa usługi mowy w usłudze Cognitive Services konwersji wypowiedzi prowadzone wypowiedzi wypowiedzi tekstu przed prognozy. 

## <a name="speech-to-intent-conversion-concepts"></a>Zamiana mowy na pojęcia konwersji elementu intent
Konwersja mowy na tekst w LUIS umożliwia wysyłania wypowiedzi mowy do punktu końcowego i odbierania odpowiedzi prognoz usługi LUIS. Ten proces jest integracja [mowy](https://docs.microsoft.com/azure/cognitive-services/Speech) usługi z użyciem usługi LUIS. 

### <a name="key-requirements"></a>Wymagania dotyczące klucza
Nie musisz utworzyć **modułu Speech API Bing** klucza dla tej integracji. A **Language Understanding** klucza utworzonego w portalu Azure działa w przypadku tej integracji. Nie należy używać klucza starter LUIS, nie będzie działać dla tej integracji.

### <a name="new-endpoint"></a>Nowy punkt końcowy 
Integracja ta tworzy nowy punkt końcowy i [ceny](luis-boundaries.md#key-limits) modelu. Punkt końcowy, za pośrednictwem [zestaw SDK rozpoznawania mowy](https://github.com/Azure-Samples/cognitive-services-speech-sdk), jest możliwość odbierania zarówno wymawiane i wypowiedzi tekstu, co pozwala używać go jako pojedynczego punktu końcowego. 

### <a name="quota-usage"></a>Użycie przydziału
Zobacz [klucza limity](luis-boundaries.md#key-limits) informacji. 

### <a name="data-retention"></a>Przechowywanie danych
Dane wysyłane do punktu końcowego za pośrednictwem zestawu SDK rozpoznawania mowy, niezależnie od tego, jeśli mowy lub tekstu, jest używana tylko w celu zwiększenia modelu mowy. Nie jest używany poza modelu w celu mowy lub usługi LUIS w ogólne pojemności. Po usunięciu aplikacji usługi LUIS zachowane dane są także usuwane.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Używaj mowy na tekst](luis-tutorial-speech-to-intent.md)

