---
title: Niestandardowe omówienie mowy usługi na platformie Azure | Dokumentacja firmy Microsoft
description: Usługa rozpoznawania mowy niestandardowy jest oparte na chmurze usługa, która pozwala użytkownikom na dostosowywanie modeli mowy przekształcania mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ms.openlocfilehash: a6139283a555f8f97371c02f9d1f3d53dc6f15d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347885"
---
# <a name="what-is-custom-speech-service"></a>Co to jest usługa mowy niestandardowe?

Niestandardowe mowy usługa jest oparta na chmurze usługi, która dostarcza użytkownikom możliwość dostosowania mowy modeli przekształcania mowy na tekst.
Aby korzystać z usługi rozpoznawania mowy niestandardowe, należy zapoznać się [niestandardowe mowy portalu](https://cris.ai).

Niestandardowej usługi rozpoznawania mowy umożliwia tworzenie dostosowanych języka modeli i modele akustycznego dostosowane do użytkowników i aplikacji. Przekazując określonych mowy i/lub dane tekstowe do niestandardowej usługi rozpoznawania mowy, można utworzyć niestandardowe modeli, które mogą być używane w połączeniu z istniejących modeli mowy stanu grafiki firmy Microsoft.

Na przykład w przypadku dodawania interakcji głosu do telefonu komórkowego, tabletu lub aplikacji komputera, można utworzyć modelu niestandardowych języka, który można łączyć z modelu akustycznego firmy Microsoft, można utworzyć punktu końcowego mowy na tekst, przeznaczonym zwłaszcza do aplikacji. Jeśli aplikacja jest przeznaczona do użytku w określonym środowisku lub populacji określonego użytkownika, można również tworzyć i wdrożyć niestandardowe modelu akustycznego z tą usługą.


## <a name="how-do-speech-recognition-systems-work"></a>Jak działają systemy rozpoznawania mowy?
Systemy rozpoznawania mowy składają się z kilku składników, które współpracują ze sobą. Dwie najważniejsze składniki to modelu akustycznego i model języka.

Modelu akustycznego jest klasyfikatora, która krótkich fragmentów audio do jednego z kilku fonemów lub dźwięku jednostki, w danym języku. Na przykład wyraz "mowy" składa się z czterech fonemów "s p iy ch". Te klasyfikacje są dokonywane z szybkością ok. 100 na sekundę.

Model języka to rozkład prawdopodobieństwa dla sekwencji słów. Model języka pomaga systemowi wybierać między sekwencjami słów, które brzmią podobnie, na podstawie prawdopodobieństwa wystąpienia danych sekwencji. Na przykład sekwencje „rozpoznawanie mowy” i „ospo zna Wanię nowy” brzmią podobnie, ale pierwsza hipoteza jest znacznie bardziej prawdopodobna i dlatego model języka przypisze jej wyższą ocenę.

Modele akustycznego i języka są statystyczne modeli, które zostały uzyskane na podstawie danych szkoleniowych. W związku z tym są wykonywane najlepiej, jeśli one wystąpić, gdy jest używany w aplikacjach mowy jest podobny do danych zaobserwowaną szkolenia. Modele akustycznego i języka aparatu rozpoznawania mowy tekst Microsoft przeprowadzono uczenia na znaczne zbiór mowy i tekst i podaj wydajność najbardziej typowe scenariusze użycia, takie jak interakcji z Cortana na inteligentną stanu grafiki Telefon, tabletu lub komputera, wyszukiwanie w sieci web przez głosu lub Dyktowanie wiadomości tekstowych znajomego.

## <a name="why-use-the-custom-speech-service"></a>Dlaczego warto używać usługi mowy niestandardowe?
Aparat rozpoznawania mowy tekstowego firmy Microsoft jest światowej klasy, jest on skierowany w kierunku opisanych powyżej scenariuszy. Jednak jeśli oczekujesz głosu zapytania, aby aplikacja mogła zawierać elementy określonego słownika, takich jak nazwy produktu lub żargonu, który rzadko występuje w typowych mowy jest prawdopodobne, że możesz uzyskać lepszą wydajność dostosowując model języka.

Jeśli na przykład tworzysz aplikację do głosowego przeszukiwania witryny MSDN, najprawdopodobniej terminy takie jak „obiektowe”, „przestrzeń nazw” lub „dot net” będą występować częściej niż w typowych aplikacjach głosowych. Dostosowanie modelu języka umożliwi systemowi nauczenie się tego.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o sposobie korzystania z usługi rozpoznawania mowy niestandardowych, zobacz [niestandardowe mowy portalu usługi] (https://cris.ai).

* [Rozpoczęcie pracy](cognitive-services-custom-speech-get-started.md)
* [Często zadawane pytania](cognitive-services-custom-speech-faq.md)
* [Słownik](cognitive-services-custom-speech-glossary.md)
