---
title: Niestandardowe Przegląd Speech Service na platformie Azure | Dokumentacja firmy Microsoft
description: Custom Speech Service jest oparta na chmurze usługi, która umożliwia użytkownikom dostosowywanie modele mowy na tekst zamiany mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: da88989753069f7ba8ca2c2e2806a648f3df4e3c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948370"
---
# <a name="what-is-custom-speech-service"></a>Czym jest usługa Custom Speech Service?

Usługa Custom Speech Service to usługa oparta na chmurze, która dostarcza użytkownikom możliwość dostosowania modele mowy na tekst zamiany mowy na tekst.
Aby użyć Custom Speech Service, zobacz [Custom Speech portalu](https://cris.ai).

Custom Speech Service umożliwia tworzenie modeli języków niestandardowych i modeli akustycznych dostosowane do aplikacji i użytkowników. Przez przekazanie określonych mowy i/lub dane tekstowe, aby usługa Custom Speech Service, możesz utworzyć niestandardowe modele, które mogą być używane w połączeniu z istniejących modeli mowy z najnowocześniejszych firmy Microsoft.

Na przykład w przypadku dodawania interakcji głosowej, telefonu komórkowego, tabletu lub aplikacji na komputerze, można utworzyć modelu języka niestandardowego, który może być łączone z model akustyczny firmy Microsoft, aby utworzyć punkt końcowy rozpoznawania mowy na tekst, przeznaczone dla aplikacji. Jeśli aplikacja jest przeznaczony dla określonego środowiska lub populacji określonego użytkownika, można również tworzyć i wdrożyć niestandardowy model akustyczny z tą usługą.


## <a name="how-do-speech-recognition-systems-work"></a>Jak działają systemy rozpoznawania mowy
Systemy rozpoznawania mowy składają się z kilku składników, które współpracują ze sobą. Są dwa najważniejsze składniki, model akustyczny i modelu językowego.

Model akustyczny to Klasyfikator oznaczający krótkie fragmenty audio jako jeden z liczbą fonemów lub jednostek dźwięku w danym języku. Na przykład słowo "mowa" składa się z czterech fonemów "s p iy ch". Te klasyfikacje są dokonywane z szybkością ok. 100 na sekundę.

Model języka to rozkład prawdopodobieństwa dla sekwencji słów. Model języka pomaga systemowi wybierać między sekwencjami słów, które brzmią podobnie, na podstawie prawdopodobieństwa wystąpienia danych sekwencji. Na przykład sekwencje „rozpoznawanie mowy” i „ospo zna Wanię nowy” brzmią podobnie, ale pierwsza hipoteza jest znacznie bardziej prawdopodobna i dlatego model języka przypisze jej wyższą ocenę.

Modele akustyczne i języka są statystyczne modeli z danych szkoleniowych. W rezultacie one działają najlepiej, gdy mowy, jakie napotykają, gdy są używane w aplikacji jest podobne do danych zaobserwowane podczas szkolenia. Modele akustyczne i językowe w aparacie Microsoft mowy na tekst przeszkoleni na ogromny zbiór mowy i tekst i zapewniają wydajność najbardziej typowych scenariuszy użycia, takie jak interakcji z Cortana w swojej inteligentnych stanu grafiki Telefon, tablet lub PC, wyszukiwania sieci web głosowego lub wiadomości SMS znajomego dyktowanie.

## <a name="why-use-the-custom-speech-service"></a>Dlaczego warto używać Custom Speech Service?
W trakcie światowej klasy aparatu Microsoft mowy na tekst go jest przeznaczona dla scenariuszy opisanych powyżej. Jednak jeśli oczekujesz, że będą kierowane zapytania głosowe do swojej aplikacji w celu zawierające konkretne słownictwo, np. nazwy produktów lub żargon, które rzadko występują w typowej mowie, jest prawdopodobne, że można uzyskać lepszą wydajność przez dostosowanie modelu języka.

Jeśli na przykład tworzysz aplikację do głosowego przeszukiwania witryny MSDN, najprawdopodobniej terminy takie jak „obiektowe”, „przestrzeń nazw” lub „dot net” będą występować częściej niż w typowych aplikacjach głosowych. Dostosowanie modelu języka umożliwi systemowi nauczenie się tego.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o sposobie używania Custom Speech Service, zobacz [Custom Speech portalu usługi] (https://cris.ai).

* [Rozpoczęcie pracy](cognitive-services-custom-speech-get-started.md)
* [Często zadawane pytania](cognitive-services-custom-speech-faq.md)
* [Słownik](cognitive-services-custom-speech-glossary.md)
