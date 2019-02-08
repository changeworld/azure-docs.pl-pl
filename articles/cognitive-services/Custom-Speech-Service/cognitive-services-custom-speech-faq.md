---
title: Często zadawane pytania dotyczące Custom Speech Service na platformie Azure | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono odpowiedzi na najpopularniejsze pytania dotyczące Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: nitinme
ms.openlocfilehash: 757fb6c1315940b73a8f9d7d28f8926cb7ccfd7b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884413"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Custom Speech Service — często zadawane pytania

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)] 

Jeśli nie możesz znaleźć odpowiedzi na pytania w tym artykule, spróbuj pytanie społeczności usługi Custom Speech Service na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) i [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Ogólne

**Pytanie**: Skąd mam wiedzieć, po zakończeniu przetwarzania zestawu danych lub modelu?

**Odpowiedź**: Obecnie stan modelu lub zestawu danych w tabeli jest tylko chcesz wiedzieć.
Po zakończeniu przetwarzania stan będzie "Gotowy".
Pracujemy nad udoskonalone metody komunikacji przetwarzania stanu, takich jak powiadomienia e-mail.

**Pytanie**: Jednocześnie można utworzyć więcej niż jednego modelu?

**Odpowiedź**: Nie ma żadnego limitu, na ile modeli znajdują się w kolekcji, ale tylko jeden można utworzyć w czasie na każdej stronie.
Na przykład jeśli obecnie jest model języka na etapie procesu nie można uruchomić procesu tworzenia modelu języka.
Mogą jednak mieć model akustyczny i model języka, przetwarzanie w tym samym czasie. 

**Pytanie**: Czy mogę rzędu milionów dolarów, czy I w przypadku popełnienia. Jak anulować Moje importowania danych lub modelu tworzenia, który jest w toku? 

**Odpowiedź**: Obecnie nie można wycofać procesu dostosowywania akustyczne lub języka.
Importowane dane mogą zostać usunięte, po zakończeniu importowania

**Pytanie**: Jaka jest różnica między wyszukiwania & dyktowanie modeli i modele konwersacji?

**Odpowiedź**: Istnieją dwie Base akustyczne i modeli języków do wyboru w usłudze Custom Speech Service.
zapytania wyszukiwania lub dyktowanie. Microsoft Conversational AM jest odpowiednia dla rozpoznawania mowy, używany w stylu konwersacji.
Tego rodzaju mowy jest zwykle ze wskazówkami zawartymi w innej osobie, takie jak w spotkań lub wyznaczonych centrów wywołania.

**Pytanie**: Czy mogę zaktualizować Mój istniejący model (model układania)

**Odpowiedź**: Firma Microsoft nie oferuje możliwość aktualizowania istniejącego modelu przy użyciu nowych danych.
Jeśli masz nowy zestaw danych i chcesz dostosować istniejący model ponownie należy go dostosować za pomocą nowych danych i starego zestawem danych, który był używany.
Stare i nowe zestawy danych muszą być połączone w pojedynczy plik zip (jeśli jest dane akustyczne) lub pliku txt, jeśli język danych po dostosowaniu odbywa się nowych musi aktualizowanego modelu można cofnąć wdrożone do uzyskania nowego punktu końcowego

**Pytanie**: Co zrobić, jeśli potrzebuję współbieżności wyższa niż wartość domyślna. 

**Odpowiedź**: Możesz skalować w górę modelu z przyrostem równym 5 współbieżnych żądań, które nazywamy jednostek skalowania. Każda jednostka skalowania gwarantuje, że modelu może jednocześnie przetwarzać 5 strumienia audio. Możesz kupić do 100 jednostek skalowania (lub 500 żądań).

Skontaktuj się z nami, jeśli potrzebujesz wyższej niż.

**Pytanie**: Czy mogę pobrać swój model i uruchomić go lokalnie?

**Odpowiedź**: Nie należy włączać modele, aby być pobierane i wykonywany lokalnie.

**Pytanie**: Moje żądania są rejestrowane?

**Odpowiedź**: Masz do wyboru podczas tworzenia wdrożenia na wyłączenie śledzenia, w tym momencie nie audio lub transkrypcje będą rejestrowane. W przeciwnym razie żądania zwykle są rejestrowane na platformie Azure w bezpiecznym magazynie. Jeśli masz więcej prywatności, które uniemożliwiają przy użyciu Custom Speech Service, skontaktuj się z nami.

## <a name="importing-data"></a>Importowanie danych

**Pytanie**: Co to jest limit rozmiaru zestawu danych? Dlaczego? 

**Odpowiedź**: Bieżący limit dla zestawu danych wynosi 2 GB, z powodu ograniczeń rozmiaru pliku dla protokołu HTTP przekazywania. 

**Pytanie**: Czy można zip Moje pliki tekstowe, aby przekazać plik tekstowy większych? 

**Odpowiedź**: Nie, obecnie tylko tekst nieskompresowanych plików są dozwolone.

**Pytanie**: Raport danych mówi wystąpiły wypowiedzi nie powiodło się. To jest problem?

**Odpowiedź**: Jeśli tylko kilka wypowiedzi nie można pomyślnie zaimportowane, nie jest problemem.
Jeśli większość wypowiedzi dane akustyczne i języka (np. > 95%) pomyślnie zaimportowane, zestaw danych może być użyteczne. Jednak zalecane jest, że próby zrozumieć, dlaczego wypowiedzi nie powiodło się i rozwiązać problemy.
Najbardziej typowe problemy, takie jak błędy, formatowania można łatwo rozwiązać. 

## <a name="creating-am"></a>Tworzenie AM

**Pytanie**: Ile dane akustyczne potrzebuję?

**Odpowiedź**: Zalecamy rozpoczęcie od 30 minut do 1 godziny dane akustyczne

**Pytanie**: Jakiego rodzaju dane należy zbierać?

**Odpowiedź**: Należy zebrać dane, które są jak najbliższe danym scenariuszu aplikacji i przypadek użycia, jak to możliwe.
Oznacza to, że zbieranie danych powinien być zgodny aplikacji docelowej i użytkowników pod kątem urządzeń lub urządzeń, środowisk i typy głośników. Ogólnie rzecz biorąc należy zebrać dane z jako szerokiego zakresu mówiące, jak to możliwe. 

**Pytanie**: Jak należy je zebrać? 

**Odpowiedź**: Można tworzenie autonomicznych aplikacji zbierania danych lub korzystać z niektórych off półki oprogramowania nagrywania dźwięku.
Można również utworzyć wersję aplikacji, który rejestruje dane audio i użyty. 

**Pytanie**: Należy także dostosowania danych, samodzielnie? 

**Odpowiedź**: Dane muszą być przetłumaczone. Można także samodzielnie lub użyj usługi profesjonalne transkrypcji. Niektóre z tych transcribers profesjonalnych użycia i innym osobom Użyj crowdsourcing. Firma Microsoft zaleca także transkrypcji usługa na żądanie.

**Pytanie**: Jak długo trwa utworzyć niestandardowy model akustyczny?

**Odpowiedź**: Czas przetwarzania do tworzenia niestandardowy model akustyczny jest prawie taki sam jak długość akustyczny zestawu danych.
Dlatego dostosowany model akustyczny utworzony na podstawie zestawu danych pięć godzin zajmie około pięciu godzin do procesu. 

## <a name="offline-testing"></a>Testowanie offline

**Pytanie**: Czy można wykonać testowanie offline Moje niestandardowy model akustyczny przy użyciu modelu języka niestandardowego?

**Odpowiedź**: Tak, po prostu wybierz model języka niestandardowego na liście rozwijanej podczas konfigurowania testu w trybie offline

**Pytanie**: Czy mogę przeprowadzić testowanie offline mojego języka niestandardowego modelu z użyciem niestandardowy model akustyczny?

**Odpowiedź**: Tak, po prostu wybierz niestandardowy model akustyczny w menu rozwijanym podczas konfigurowania testu w trybie offline.

**Pytanie**: Co to jest współczynnik błędów programu Word i jak jest ona obliczana?

**Odpowiedź**: Częstotliwość błędów programu Word to metryki oceny funkcji rozpoznawania mowy. Jest ona traktowana jako liczba błędów, w tym liczba operacji wstawienia, usuwania i podstawienia, podzielona przez całkowitą liczbę słów w transkrypcji odwołania.

**Pytanie**: Teraz mogę wiedzieć, wyników testów Moje modelu niestandardowego, to jest dobrą lub nieprawidłowy numer?

**Odpowiedź**: Wyniki pokazują porównania między modelem linii bazowej i tego, który można dostosować.
Należy dążyć do zapewniała modelu odniesienia umożliwiają dostosowanie zwiększonej

**Pytanie**: Jak wiem, raportowanie błędów systemu Windows, podstawowy modeli, aby można było zobaczyć jeśli było poprawy jakości obsługi? 

**Odpowiedź**: Wyniki testu w trybie offline Pokaż dokładność linii bazowej dokładności modelu niestandardowego, a także ulepszanie względem punktu odniesienia

## <a name="creating-lm"></a>Tworzenie LM

**Pytanie**: Jak dużo danych tekstu należy przekazać?

**Odpowiedź**: Jest trudne pytania, aby zapewnić dokładne odpowiedzi na to, ponieważ jest ona zależna w jak różny sposób słownictwo i wyrażenia używane w aplikacji pochodzących od początkowego modeli językowych. Dla wszystkich nowych wyrazów warto zawierają przykłady tyle możliwie użycia tych słów. Do typowych fraz, które są używane w aplikacji łącznie z programami znajdującymi się na danych języka jest również przydatne jako informuje system do nasłuchiwania tych warunków również.
Jest to często mają co najmniej 100 i zazwyczaj kilka kilkuset wypowiedzi w zestawie danych języka lub więcej.
Również Jeśli niektóre typy * z zapytania powinny być częściej niż inne, możesz wstawić wiele kopii typowych zapytań w zestawie danych.

**Pytanie**: Czy po prostu przekazać z listy słów?

**Odpowiedź**: Przekazywanie listę słów, które będą wyrazów do słownika, ale uczą system jak wyrazy są zwykle używane.
Dostarczając pełnej lub częściowej wypowiedzi (zdań lub fraz rzeczy, które użytkownicy mogą podać) modelu językowego dowiedzieć się nowych słów i sposobu ich używania. Model języka niestandardowego jest dobra, nie tylko w celu uzyskania nowych wyrazów w systemie, ale także dostosowywania prawdopodobieństwo znanych wyrazy dla aplikacji. Zapewnienie pełnej wypowiedzi pomaga systemu, Dowiedz się, to. 

-----

 * [Omówienie](cognitive-services-custom-speech-home.md)
 * [Pracę](cognitive-services-custom-speech-get-started.md)
 * [Słownik](cognitive-services-custom-speech-glossary.md)
