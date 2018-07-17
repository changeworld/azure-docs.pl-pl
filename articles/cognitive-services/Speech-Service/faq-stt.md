---
title: Często zadawane pytania dotyczące zamiana mowy na tekst usługi na platformie Azure | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono odpowiedzi na najpopularniejsze pytania dotyczące zamiana mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: ad4b31fa7b4f4b062f01935c34f7cacf82f99106
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072130"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Custom Speech Service — często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tym artykule, spróbuj pytanie społeczności usługi Custom Speech Service na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) i [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Ogólne

**Pytanie**: jaka jest różnica między linii bazowej i custom Speech modeli tekstu?

**Odpowiedź**: modeli podstawowych przeszkoleni należące do danych firmy Microsoft i są już wdrożone w chmurze. Modele niestandardowe umożliwiają użytkownikom dostosowanie modelu, aby lepiej dopasować określonego środowiska za pomocą określonych hałasu otoczenia albo języka. Fabryk, samochodów, streets hałas wymagałoby dostosowany model akustyczny, natomiast określonych tematów, takich jak biologii, fizyki, radiologii, nazwy produktów i niestandardowe akronimów wymagałoby modelu językowego dostosowane.

**Pytanie**: gdzie rozpocząć jeśli chcę korzystać z modelu linii bazowej?

**Odpowiedź**: najpierw musisz pobrać [klucz subskrypcji](get-started.md). Jeśli chcesz wykonywać wywołania REST do modeli predeployed linii bazowej, zapoznaj się z [szczegółowych informacji w tym miejscu](rest-apis.md). Jeśli chcesz użyć funkcji WebSockets pobierania [zestawu SDK](speech-sdk.md)

**Pytanie**: mogę zawsze potrzebne do tworzenia modelu mowy niestandardowej?

**Odpowiedź**: nie, jeśli aplikacja korzysta ogólnych języka dnia na dzień, a następnie nie należy dostosować model. Ponadto, jeśli w środowisku, w którym istnieje niewielki lub hałas w tle, a następnie trzeba dostosować albo korzystania z aplikacji. Portal pozwala użytkownikom na wdrażanie linii bazowej i niestandardowe modele i uruchomić testy dokładności względem nich. Użytkowników ta funkcja służy do mierzenia dokładności vs linii bazowej modelu niestandardowego.

**Pytanie**: skąd mam wiedzieć, po zakończeniu przetwarzania zestawu danych lub modelu?

**Odpowiedź**: obecnie stan modelu lub zestawu danych w tabeli jest tylko chcesz wiedzieć.
Po zakończeniu przetwarzania stan będzie mieć stan "Powodzenie".

**Pytanie**: można utworzyć więcej niż jednego modelu?

**Odpowiedź**: nie ma żadnego limitu, na ile modeli znajdują się w kolekcji.

**Pytanie**: czy mogę zrealizowane, czy w przypadku popełnienia. Jak anulować Moje importowania danych lub modelu tworzenia, który jest w toku? 

**Odpowiedź**: obecnie nie można wycofać procesem adaptacji akustyczne lub języka. Zaimportowane dane i modeli, można usunąć po umieszczeniu ich w stan końcowy.

**Pytanie**: jaka jest różnica między wyszukiwania & dyktowanie modeli i modele konwersacji?

**Odpowiedź**: istnieją więcej niż jeden punkt odniesienia modele mogą wybierać usługi mowy. Model konwersacji jest odpowiednia dla rozpoznawania mowy, używany w stylu konwersacji. W tym modelu będzie idealne rozwiązanie w przypadku wywołania trnascribing podczas wyszukiwania i dyktowanie jest idealne dla aplikacji głosu wyzwolone. Uniwersalny jest nowy model, który ma na celu obu scenariuszy.

**Pytanie**: czy mogę zaktualizować Mój istniejący model (model układania)?

**Odpowiedź**: nie można zaktualizować istniejących modeli. Jako obejścia łączyć stary zestaw danych przy użyciu nowego i readapt.

Stare i nowe zestawy danych muszą można połączyć w pojedynczy plik zip (jeśli jest dane akustyczne) lub plik z rozszerzeniem txt, jeśli są to dane języka. Po dostosowaniu odbywa się nowych musi aktualizowanego modelu można cofnąć wdrożone do uzyskania nowego punktu końcowego

**Pytanie**: co zrobić, jeśli potrzebujesz wyższej współbieżności dla mojego wdrożony model niż treści oferowanych w portalu. 

**Odpowiedź**: można zwiększać z przyrostem równym 20 równoczesnych żądań modelu. 

Skontaktuj się z nami, jeśli potrzebujesz większej skali.

**Pytanie**: można pobrać swój model i uruchomić go lokalnie?

**Odpowiedź**: modeli nie mogą być pobierane i wykonywany lokalnie.

**Pytanie**: czy Moje żądania rejestrowane?

**Odpowiedź**: masz do wyboru podczas tworzenia wdrożenia na wyłączenie śledzenia, w tym momencie nie audio lub transkrypcje będą rejestrowane. W przeciwnym razie żądania zwykle są rejestrowane na platformie Azure w bezpiecznym magazynie. Jeśli masz więcej prywatności, które uniemożliwiają przy użyciu Custom Speech Service, skontaktuj się z jednego z kanałów pomocy technicznej.

## <a name="importing-data"></a>Importowanie danych

**Pytanie**: co to jest limit rozmiaru zestawu danych? Dlaczego? 

**Odpowiedź**: bieżący limit dla zestawu danych wynosi 2 GB, ze względu na ograniczenia rozmiaru pliku do przekazania HTTP. 

**Pytanie**: może I zip Moje pliki tekstowe, aby przekazać plik tekstowy większych? 

**Odpowiedź**: nie. obecnie dozwolone są tylko pliki nieskompresowanych tekstu.

**Pytanie**: raport danych mówi wystąpiły wypowiedzi nie powiodło się. Na czym polega problem?

**Odpowiedź**: nie można przekazać 100% wypowiedzi w pliku nie jest problemem.
Jeśli większość wypowiedzi dane akustyczne i języka zestawu (na przykład > 95%) pomyślnie zaimportowane, zestaw danych może być użyteczne. Jednak zalecane jest, że próby zrozumieć, dlaczego wypowiedzi nie powiodło się i rozwiązać problemy. Najbardziej typowe problemy, takie jak błędy, formatowania można łatwo rozwiązać. 

## <a name="creating-am"></a>Tworzenie AM

**Pytanie**: ile dane akustyczne potrzebuję?

**Odpowiedź**: zalecamy Rozpoczynanie od 30 minut do 1 godziny dane akustyczne.

**Pytanie**: jakie dane należy zbierać?

**Odpowiedź**: zbieranie danych, które są jak najbliższe danym scenariuszu aplikacji i przypadek użycia, jak to możliwe.
Zbieranie danych powinien być zgodny w aplikacji docelowej i użytkowników pod kątem urządzeń lub urządzeń, środowisk i typy głośników. Ogólnie rzecz biorąc należy zebrać dane z jako szerokiego zakresu mówiące, jak to możliwe. 

**Pytanie**: jak należy zebrać go? 

**Odpowiedź**: możesz utworzyć oddzielną aplikację zbierania danych lub korzystać z niektórych off półki oprogramowania nagrywania dźwięku.
Można również utworzyć wersję aplikacji, który rejestruje dane audio i użyty. 

**Pytanie**: należy samodzielnie transkrypcja dostosowania danych? 

**Odpowiedź**: tak! Można także samodzielnie lub użyj usługi profesjonalne transkrypcji. Niektórzy użytkownicy preferują professional transcribers, podczas gdy inne Użyj crowdsourcing lub wykonaj transkrypcje, samodzielnie.

## <a name="accuracy-testing"></a>Testowanie dokładności

**Pytanie**: wykonaj, testowanie offline Moje niestandardowy model akustyczny przy użyciu modelu języka niestandardowego?

**Odpowiedź**: tak, po prostu wybierz model języka niestandardowego listy rozwijanej, po skonfigurowaniu testu w trybie offline

**Pytanie**: wykonaj, testowanie offline mojego języka niestandardowego modelu z użyciem niestandardowy model akustyczny?

**Odpowiedź**: tak, wystarczy wybrać niestandardowy model akustyczny w menu rozwijanym, podczas konfigurowania testu w trybie offline.

**Pytanie**: co to jest współczynnik błędów programu Word (WER, Windows Management Instrumentation) i jak jest ona obliczana?

**Odpowiedź**: współczynnik błędów programu Word (WER, Windows Management Instrumentation) jest metryki oceny funkcji rozpoznawania mowy. Jest ona traktowana jako liczba błędów, w tym liczba operacji wstawienia, usuwania i podstawienia, podzielona przez całkowitą liczbę słów w transkrypcji odwołania. Więcej szczegółów można znaleźć [tutaj](https://en.wikipedia.org/wiki/Word_error_rate).

**Pytanie**: jak ustalić, czy wyniki testu dokładności jest dobra?

**Odpowiedź**: Wyniki pokazują porównania między modelem linii bazowej i co można dostosować.
Należy dążyć do zapewniała modelu odniesienia umożliwiają dostosowanie cenna.

**Pytanie**: jak wiem, współczynnik błędów programu Word z modeli bazowych, aby można było zobaczyć jeśli było poprawy jakości obsługi? 

**Odpowiedź**: wyniki testu w trybie offline Pokaż dokładność linii bazowej dokładności modelu niestandardowego, a także ulepszanie względem punktu odniesienia.

## <a name="creating-lm"></a>Tworzenie LM

**Pytanie**: jak dużo danych tekstu należy przekazać?

**Odpowiedź**: zależy od różnych słownictwo i wyrażenia używane w aplikacji pochodzą od początkowego modeli językowych. Dla wszystkich nowych wyrazów warto zawierają przykłady tyle możliwie użycia tych słów. Dla typowych fraz, które są używane w aplikacji w tym zwrotów w danym języku jest również przydatne jako informuje system do nasłuchiwania tych warunków również. Jest to często mają co najmniej jeden hudrend i zazwyczaj kilka kilkuset wypowiedzi w zestawie danych języka lub więcej. Również niektórych rodzajów zapytań powinny być częściej niż inne, można wstawić wiele kopii typowych zapytań w zestawie danych.

**Pytanie**: czy mogę po prostu przekazać listę słów?

**Odpowiedź**: przekazywanie listę słów, które będą wyrazów do słownika, ale uczą system jak wyrazy są zwykle używane.
Dostarczając pełnej lub częściowej wypowiedzi (zdań lub fraz rzeczy, które użytkownicy mogą podać) model języka można Dowiedz się nowych słów i jak są one używane. Model języka niestandardowego jest dobra, nie tylko w celu uzyskania nowych wyrazów w systemie, ale także dostosowywania prawdopodobieństwo znanych wyrazy dla aplikacji. Zapewnienie pełnej wypowiedzi pomaga systemu, Dowiedz się, lepiej. 

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)
