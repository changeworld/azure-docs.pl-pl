---
title: Liczba znaków — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak interfejs API tekstu usługi Azure Cognitive Services Translator zlicza znaki, dzięki czemu można zrozumieć, jak pochłonie zawartość.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888159"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak interfejs API tekstu tłumacza zlicza znaki

Interfejs API tekstu tłumacza zlicza każdy punkt kodu Unicode tekstu wejściowego jako znak. Każde tłumaczenie tekstu na język jest liczone jako oddzielne tłumaczenie, nawet jeśli żądanie zostało wykonane w jednym wywołaniu interfejsu API tłumaczenia na wiele języków. Długość odpowiedzi nie ma znaczenia.

Liczy się:

* Tekst przekazany do interfejsu API tekstu tłumacza w treści żądania
   * `Text`podczas korzystania z metod wyszukiwania tłumaczenia, transliteracji i słownika
   * `Text`i `Translation` podczas korzystania z metody Przykłady słowników
* Wszystkie znaczniki: HTML, tagi XML itp. Notacja JSON używana do tworzenia żądania (na przykład "Text:") nie jest zliczona.
* Indywidualny list
* Znaki interpunkcyjne
* Spacja, karta, znaczniki i dowolny rodzaj znaku odstępu
* Każdy punkt kodu zdefiniowany w Unicode
* Tłumaczenie wielokrotne, nawet jeśli wcześniej przetłumaczono ten sam tekst

W przypadku skryptów opartych na ideogramach, takich jak chiński i japoński Kanji, interfejs API tekstu tłumacza nadal będzie zliczać liczbę punktów kodu Unicode, jeden znak na ideogram. Wyjątek: surogaty Unicode liczą się jako dwa znaki.

Liczba żądań, słów, bajtów lub zdań nie ma znaczenia w liczbie znaków.

Wywołania detect i BreakSentence metody nie są liczone w zużyciu znaków. Jednak oczekujemy, że wywołania Detect i BreakSentence metody są w rozsądnej proporcji do korzystania z innych funkcji, które są liczone. Jeśli liczba wywołań Wykrywania lub Przerywania trwa 100 razy przekracza liczbę innych zliczonych metod, firma Microsoft zastrzega sobie prawo do ograniczenia użycia przez użytkownika metod Wykrywania i Przerywania.


Więcej informacji na temat liczby znaków znajduje się w często zadawanych [pytaniach dotyczących usługi Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
