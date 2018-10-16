---
title: Często zadawane pytania dotyczące Custom Speech Service na platformie Azure | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono odpowiedzi na najpopularniejsze pytania dotyczące Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ms.openlocfilehash: 8e3d5e0e2b70d8f97099103ed369e48dd74d56e2
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341365"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Custom Speech Service — często zadawane pytania

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)] 

Jeśli nie możesz znaleźć odpowiedzi na pytania w tym artykule, spróbuj pytanie społeczności usługi Custom Speech Service na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) i [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Ogólne

**Pytanie**: skąd mam wiedzieć, po zakończeniu przetwarzania zestawu danych lub modelu?

**Odpowiedź**: obecnie stan modelu lub zestawu danych w tabeli jest tylko chcesz wiedzieć.
Po zakończeniu przetwarzania stan będzie "Gotowy".
Pracujemy nad udoskonalone metody komunikacji przetwarzania stanu, takich jak powiadomienia e-mail.

**Pytanie**: czy mogę utworzyć więcej niż jednego modelu w czasie?

**Odpowiedź**: nie ma żadnego limitu, na ile modeli znajdują się w kolekcji, ale tylko jeden można utworzyć w czasie na każdej stronie.
Na przykład jeśli obecnie jest model języka na etapie procesu nie można uruchomić procesu tworzenia modelu języka.
Mogą jednak mieć model akustyczny i model języka, przetwarzanie w tym samym czasie. 

**Pytanie**: czy mogę zrealizowane, czy w przypadku popełnienia. Jak anulować Moje importowania danych lub modelu tworzenia, który jest w toku? 

**Odpowiedź**: obecnie nie można wycofać procesu dostosowywania akustyczne lub języka.
Importowane dane mogą zostać usunięte, po zakończeniu importowania

**Pytanie**: jaka jest różnica między wyszukiwania & dyktowanie modeli i modele konwersacji?

**Odpowiedź**: istnieją dwie Base akustyczne i modeli języków do wyboru w usłudze Custom Speech Service.
zapytania wyszukiwania lub dyktowanie. Microsoft Conversational AM jest odpowiednia dla rozpoznawania mowy, używany w stylu konwersacji.
Tego rodzaju mowy jest zwykle ze wskazówkami zawartymi w innej osobie, takie jak w spotkań lub wyznaczonych centrów wywołania.

**Pytanie**: czy mogę zaktualizować Mój istniejący model (model układania)?

**Odpowiedź**: Firma Microsoft nie oferuje możliwość aktualizowania istniejącego modelu przy użyciu nowych danych.
Jeśli masz nowy zestaw danych i chcesz dostosować istniejący model ponownie należy go dostosować za pomocą nowych danych i starego zestawem danych, który był używany.
Stare i nowe zestawy danych muszą być połączone w pojedynczy plik zip (jeśli jest dane akustyczne) lub pliku txt, jeśli język danych po dostosowaniu odbywa się nowych musi aktualizowanego modelu można cofnąć wdrożone do uzyskania nowego punktu końcowego

**Pytanie**: co zrobić, jeśli konieczne zapewnienie większej współbieżności niż domyślna wartość. 

**Odpowiedź**: możesz skalować w górę modelu z przyrostem równym 5 współbieżnych żądań, które nazywamy jednostek skalowania. Każda jednostka skalowania gwarantuje, że modelu może jednocześnie przetwarzać 5 strumienia audio. Możesz kupić do 100 jednostek skalowania (lub 500 żądań).

Skontaktuj się z nami, jeśli potrzebujesz wyższej niż.

**Pytanie**: można pobrać swój model i uruchomić go lokalnie?

**Odpowiedź**: Firma Microsoft nie należy włączać modele, aby być pobierane i wykonywany lokalnie.

**Pytanie**: czy Moje żądania rejestrowane?

**Odpowiedź**: masz do wyboru podczas tworzenia wdrożenia na wyłączenie śledzenia, w tym momencie nie audio lub transkrypcje będą rejestrowane. W przeciwnym razie żądania zwykle są rejestrowane na platformie Azure w bezpiecznym magazynie. Jeśli masz więcej prywatności, które uniemożliwiają przy użyciu Custom Speech Service, skontaktuj się z nami.

## <a name="importing-data"></a>Importowanie danych

**Pytanie**: co to jest limit rozmiaru zestawu danych? Dlaczego? 

**Odpowiedź**: bieżący limit dla zestawu danych wynosi 2 GB, ze względu na ograniczenia rozmiaru pliku do przekazania HTTP. 

**Pytanie**: może I zip Moje pliki tekstowe, aby przekazać plik tekstowy większych? 

**Odpowiedź**: nie. obecnie dozwolone są tylko pliki nieskompresowanych tekstu.

**Pytanie**: raport danych mówi wystąpiły wypowiedzi nie powiodło się. To jest problem?

**Odpowiedź**: Jeśli tylko kilka wypowiedzi nie można pomyślnie zaimportowane, nie jest problemem.
Jeśli większość wypowiedzi dane akustyczne i języka (np. > 95%) pomyślnie zaimportowane, zestaw danych może być użyteczne. Jednak zalecane jest, że próby zrozumieć, dlaczego wypowiedzi nie powiodło się i rozwiązać problemy.
Najbardziej typowe problemy, takie jak błędy, formatowania można łatwo rozwiązać. 

## <a name="creating-am"></a>Tworzenie AM

**Pytanie**: ile dane akustyczne potrzebuję?

**Odpowiedź**: zalecamy Rozpoczynanie od 30 minut do 1 godziny dane akustyczne

**Pytanie**: jaki rodzaj danych należy zbierać?

**Odpowiedź**: należy zebrać dane, które są jak najbliższe danym scenariuszu aplikacji i przypadek użycia, jak to możliwe.
Oznacza to, że zbieranie danych powinien być zgodny aplikacji docelowej i użytkowników pod kątem urządzeń lub urządzeń, środowisk i typy głośników. Ogólnie rzecz biorąc należy zebrać dane z jako szerokiego zakresu mówiące, jak to możliwe. 

**Pytanie**: jak należy zebrać go? 

**Odpowiedź**: możesz utworzyć oddzielną aplikację zbierania danych lub korzystać z niektórych off półki oprogramowania nagrywania dźwięku.
Można również utworzyć wersję aplikacji, który rejestruje dane audio i użyty. 

**Pytanie**: należy samodzielnie transkrypcja dostosowania danych? 

**Odpowiedź**: dane muszą być przetłumaczone. Można także samodzielnie lub użyj usługi profesjonalne transkrypcji. Niektóre z tych transcribers profesjonalnych użycia i innym osobom Użyj crowdsourcing. Firma Microsoft zaleca także transkrypcji usługa na żądanie.

**Pytanie**: jak długo trwa utworzyć niestandardowy model akustyczny?

**Odpowiedź**: czas przetwarzania do tworzenia niestandardowy model akustyczny jest prawie taki sam jak długość akustyczny zestawu danych.
Dlatego dostosowany model akustyczny utworzony na podstawie zestawu danych pięć godzin zajmie około pięciu godzin do procesu. 

## <a name="offline-testing"></a>Testowanie offline

**Pytanie**: wykonaj, testowanie offline Moje niestandardowy model akustyczny przy użyciu modelu języka niestandardowego?

**Odpowiedź**: tak, po prostu wybierz model języka niestandardowego na liście rozwijanej podczas konfigurowania testu w trybie offline

**Pytanie**: wykonaj, testowanie offline mojego języka niestandardowego modelu z użyciem niestandardowy model akustyczny?

**Odpowiedź**: tak, wystarczy wybrać niestandardowy model akustyczny w menu rozwijanym, podczas konfigurowania testu w trybie offline.

**Pytanie**: co to jest współczynnik błędów programu Word i jak jest ona obliczana?

**Odpowiedź**: Częstotliwość błędów programu Word to metryki oceny funkcji rozpoznawania mowy. Jest ona traktowana jako liczba błędów, w tym liczba operacji wstawienia, usuwania i podstawienia, podzielona przez całkowitą liczbę słów w transkrypcji odwołania.

**Pytanie**: teraz mogę wiedzieć wyników testów Moje modelu niestandardowego, jest to liczba dobre i złe?

**Odpowiedź**: Wyniki pokazują porównania między modelem linii bazowej i co można dostosować.
Należy dążyć do zapewniała modelu odniesienia umożliwiają dostosowanie zwiększonej

**Pytanie**: jak wiem, raportowanie błędów systemu Windows, podstawowy modeli, aby można było zobaczyć jeśli było poprawy jakości obsługi? 

**Odpowiedź**: wyniki testu w trybie offline Pokaż dokładność linii bazowej dokładności modelu niestandardowego, a także ulepszanie względem punktu odniesienia

## <a name="creating-lm"></a>Tworzenie LM

**Pytanie**: jak dużo danych tekstu należy przekazać?

**Odpowiedź**: to trudne pytania, aby zapewnić dokładne odpowiedzi na, ponieważ jest ona zależna w jak różny sposób słownictwo i wyrażenia używane w aplikacji z początkową modeli językowych. Dla wszystkich nowych wyrazów warto zawierają przykłady tyle możliwie użycia tych słów. Do typowych fraz, które są używane w aplikacji łącznie z programami znajdującymi się na danych języka jest również przydatne jako informuje system do nasłuchiwania tych warunków również.
Jest to często mają co najmniej 100 i zazwyczaj kilka kilkuset wypowiedzi w zestawie danych języka lub więcej.
Również Jeśli niektóre typy * z zapytania powinny być częściej niż inne, możesz wstawić wiele kopii typowych zapytań w zestawie danych.

**Pytanie**: czy mogę po prostu przekazać listę słów?

**Odpowiedź**: przekazywanie listę słów, które będą wyrazów do słownika, ale uczą system jak wyrazy są zwykle używane.
Dostarczając pełnej lub częściowej wypowiedzi (zdań lub fraz rzeczy, które użytkownicy mogą podać) modelu językowego dowiedzieć się nowych słów i sposobu ich używania. Model języka niestandardowego jest dobra, nie tylko w celu uzyskania nowych wyrazów w systemie, ale także dostosowywania prawdopodobieństwo znanych wyrazy dla aplikacji. Zapewnienie pełnej wypowiedzi pomaga systemu, Dowiedz się, to. 

-----

 * [Omówienie](cognitive-services-custom-speech-home.md)
 * [Pracę](cognitive-services-custom-speech-get-started.md)
 * [Słownik](cognitive-services-custom-speech-glossary.md)
