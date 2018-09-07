---
title: Pojęcia dotyczące konwersji danych usługi LUIS — Language Understanding
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak można zmienić wypowiedzi przed prognozy w Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: e1d0e0a0205190846612d727fbf34404e33c3ad4
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027082"
---
# <a name="data-conversion-concepts-in-luis"></a>Pojęcia dotyczące konwersji danych usługi LUIS
LUIS umożliwia konwertowanie wypowiedzi wypowiedzi mówiony na wypowiedzi tekstu przed prognozowania. 

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

