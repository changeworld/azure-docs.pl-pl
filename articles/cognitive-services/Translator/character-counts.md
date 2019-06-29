---
title: Znak liczby - API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Jak interfejs API tekstu usługi Translator liczby znaków.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cfd5823009b66b6b525c7add1fb56953d3c1a507
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445263"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak interfejs API tekstu usługi Translator liczby znaków

Interfejs API tekstu usługi Translator liczy każdy punkt kodu Unicode tekst wejściowy jako znak. Każde tłumaczenie tekstu, język jest liczona jako oddzielny translacji, nawet wtedy, gdy żądanie zostało wystosowane w pojedynczy interfejs API wywołanie tłumaczenia na wiele języków. Długość odpowiedzi nie ma znaczenia.

Co jest liczone są:

* Tekst jest przekazywany do interfejsu API tłumaczenia tekstu w treści żądania
   * `Text` Korzystając z metody Przetłumacz, Transliterate i Lookup słownika
   * `Text` i `Translation` podczas korzystania z metody przykłady słowników
* Wszystkich znaczników: HTML, znaczniki XML, itp., w polu tekstowym w treści żądania. Notacja JSON używany do tworzenia żądania (na przykład "tekst:") nie jest liczony.
* Jeden list
* Znaki interpunkcyjne
* Spację, karta, znaczników i dowolny biały znak
* Każdy punkt kodowy zdefiniowane w formacie Unicode
* Powtórzony translacji, nawet wtedy, gdy ten sam tekst ma przetłumaczone wcześniej

W przypadku skryptów oparte na ideogramów, takich jak chiński i japoński Kanji interfejsu API tłumaczenia tekstu będzie nadal liczby punkty kodowe Unicode, jeden znak na ideogram. Wystąpił wyjątek: Surogaty Unicode liczba jako dwa znaki.

Liczba żądań, słowa lub zdania bajtów jest bez znaczenia w liczbie znaków.

Wywołania metod wykrywania i BreakSentence nie są wliczane do użycia znaku. Jednak oczekujemy, że wywołania do metod wykrywania i BreakSentence znajdują się w rozsądnej proporcji do korzystania z innych funkcji, które są uwzględniane. Jeśli liczba wywołań wykrywanie lub BreakSentence, wprowadzone przekracza liczbę innych metod zliczono przez firmę Microsoft 100 razy zastrzega sobie prawo do ograniczenia używania metod wykrywania i BreakSentence.


Aby uzyskać więcej informacji dotyczących liczby znaków [często zadawane pytania dotyczące programu Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
