---
title: Zrozumienie pojęcia dotyczące konwersji danych LUIS - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można zmienić zniesławiających przed prognoz opis języka (LUIS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/27/2018
ms.author: v-geberr;
ms.openlocfilehash: 16b0df4b81220885e2c3747470272cee9536e10c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063565"
---
# <a name="data-conversion-concepts-in-luis"></a>Pojęcia dotyczące konwersji danych LUIS
LUIS umożliwia konwertowanie zniesławiających zniesławiających rozmowy na tekst zniesławiających przed prognozowania. 

## <a name="speech-to-intent-conversion-concepts"></a>Mowy do konwersji konwersji pojęć
Konwersja mowy na tekst w LUIS służy do wysyłania zniesławiających rozmowy do punktu końcowego i odbierania odpowiedzi prognozowania LUIS. Proces jest integracja [mowy](https://docs.microsoft.com/azure/cognitive-services/Speech) usługi z LUIS. 

### <a name="key-requirements"></a>Wymagania dotyczące klucza
Nie trzeba tworzyć **API mowy usługi Bing** kluczy dla tej integracji. A **opis języka** klucza utworzone w portalu Azure działa w przypadku tej integracji. Nie należy używać klucza starter LUIS, nie będzie działać dla tej integracji.

### <a name="new-endpoint"></a>Nowy punkt końcowy 
Integracja ta tworzy nowy punkt końcowy i [cennik](luis-boundaries.md#key-limits) modelu. Punkt końcowy, za pomocą [mowy SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk), może odbierać zarówno wymawiane i zniesławiających tekstu, dzięki czemu można używać go jako jeden punkt końcowy. 

### <a name="quota-usage"></a>Użycie przydziału
Zobacz [klucza limity](luis-boundaries.md#key-limits) informacji. 

### <a name="data-retention"></a>Przechowywanie danych
Dane wysyłane do punktu końcowego za pośrednictwem usługi SDK mowy, niezależnie od przypadku mowy lub tekstu, jest używana tylko w celu zwiększenia modelu mowy. Nie jest używana poza modelu w w celu zwiększenia mowy lub LUIS ogólne pojemności. Po usunięciu aplikacji LUIS zachowane dane są także usuwane.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Użyj mowy na tekst.](luis-tutorial-speech-to-intent.md)

