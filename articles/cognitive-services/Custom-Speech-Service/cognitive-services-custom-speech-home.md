---
title: Niestandardowe Przegląd Speech Service na platformie Azure | Dokumentacja firmy Microsoft
description: Custom Speech Service to oparta na chmurze usługa, która umożliwia użytkownikom dostosowywanie modeli mowy na potrzeby zapisywania transkrypcji po zamianie mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ms.openlocfilehash: 00c9a89bea9deafe4096566032cc9685df8c111c
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228002"
---
# <a name="what-is-custom-speech-service"></a>Czym jest usługa Custom Speech Service?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Custom Speech Service to oparta na chmurze usługa, która daje użytkownikom możliwość dostosowywania modeli mowy na potrzeby zapisywania transkrypcji po zamianie mowy na tekst.
Aby dowiedzieć się, jak używać usługi Custom Speech Service, zobacz [Portal usługi Custom Speech Service](https://cris.ai).

Usługa Custom Speech Service umożliwia tworzenie niestandardowych modeli języków i modeli akustycznych dostosowanych do aplikacji i użytkowników. Przekazując własne dane mowy i/lub tekstu do usługi Custom Speech Service, możesz tworzyć niestandardowe modele, których można używać w połączeniu z gotowymi modelami mowy firmy Microsoft najwyższej klasy.

Na przykład w przypadku dodawania interakcji głosowej do aplikacji dla telefonów komórkowych, tabletów lub komputerów możesz utworzyć niestandardowy model języka, a następnie połączyć go z modelem akustycznym firmy Microsoft, aby utworzyć punkt końcowy zamiany mowy na tekst zaprojektowany specjalnie dla Twojej aplikacji. Jeśli aplikacja jest przeznaczona do użytku w określonym środowisku lub przez określone grono użytkowników, za pomocą tej usługi możesz również utworzyć i wdrożyć niestandardowy model akustyczny.


## <a name="how-do-speech-recognition-systems-work"></a>Jak działają systemy rozpoznawania mowy?
Systemy rozpoznawania mowy składają się z kilku składników, które współpracują ze sobą. Dwoma najważniejszymi składnikami są model akustyczny i model języka.

Model akustyczny to klasyfikator oznaczający krótkie fragmenty audio jako jeden z kilku fonemów (jednostek dźwięku) w danym języku. Na przykład słowo „mowa” składa się z czterech fonemów: m, o, w, a. Te klasyfikacje są dokonywane z szybkością ok. 100 na sekundę.

Model języka to rozkład prawdopodobieństwa dla sekwencji słów. Model języka pomaga systemowi wybierać między sekwencjami słów, które brzmią podobnie, na podstawie prawdopodobieństwa wystąpienia danych sekwencji. Na przykład sekwencje „rozpoznawanie mowy” i „ospo zna Wanię nowy” brzmią podobnie, ale pierwsza hipoteza jest znacznie bardziej prawdopodobna i dlatego model języka przypisze jej wyższą ocenę.

Oba modele, akustyczny i języka, to modele statystyczne wyuczone na danych szkoleniowych. W rezultacie działają one najlepiej, gdy mowa, jaką napotykają podczas używania w aplikacji, jest zbliżona do danych zaobserwowanych podczas szkolenia. Modele akustyczne i języka w aparacie zamiany mowy na tekst firmy Microsoft zostały przeszkolone na ogromnym zbiorze danych mowy i tekstu, dzięki czemu gwarantują wydajność na najwyższym poziomie dla najbardziej typowych scenariuszy użycia, takich jak interakcja z Cortaną na smartfonie, tablecie lub komputerze, głosowe wyszukiwanie w Internecie lub dyktowanie wiadomości SMS do znajomego.

## <a name="why-use-the-custom-speech-service"></a>Dlaczego warto używać usługi Custom Speech Service?
Aparat zamiany mowy na tekst firmy Microsoft, choć cechuje się najwyższą jakością, jest przeznaczony dla scenariuszy opisanych powyżej. Jeśli spodziewasz się w swojej aplikacji zapytań głosowych zawierających specyficzne słownictwo, na przykład nazwy produktów lub żargon rzadko występujące w codziennej mowie, lepszą wydajność możesz osiągnąć, dostosowując model języka.

Jeśli na przykład tworzysz aplikację do głosowego przeszukiwania witryny MSDN, najprawdopodobniej terminy takie jak „obiektowe”, „przestrzeń nazw” lub „dot net” będą występować częściej niż w typowych aplikacjach głosowych. Dostosowanie modelu języka umożliwi systemowi nauczenie się tego.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o sposobie używania Custom Speech Service, zobacz [Custom Speech portalu](https://cris.ai).

* [Rozpoczęcie pracy](cognitive-services-custom-speech-get-started.md)
* [Często zadawane pytania](cognitive-services-custom-speech-faq.md)
* [Słownik](cognitive-services-custom-speech-glossary.md)
