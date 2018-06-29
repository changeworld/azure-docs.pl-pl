---
title: Często zadawane pytania dotyczące mowy usługi tekst na platformie Azure | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono odpowiedzi na pytania najpopularniejszych o mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 64e505889ef9472603471d67a961985c1290663a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045847"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi mowy niestandardowych

Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, spróbuj pytaniem społeczności niestandardowej usługi rozpoznawania mowy na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) i [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Ogólne

**Pytanie**: jaka jest różnica między linii bazowej i mowy niestandardowych modeli tekstu?

**Odpowiedzi**: modele linii bazowej przeprowadzono uczenia z firmą Microsoft, których właścicielem danych i są już wdrożone w chmurze. Modele niestandardowe umożliwiają użytkownikom dostosowania modelu w celu lepszego dopasowania określonym środowisku z określonych hałasu otoczenia lub języka. Fabryka piętra, samochodów, naprawienie streets wymagają dostosowane modelu akustycznego określonych tematów, takich jak biologii, fizycznych radiologii, nazwy produktów i niestandardowych akronimów będzie wymagać model języka.

**Pytanie**: gdzie uruchomić Aby korzystać z modelu linii bazowej?

**Odpowiedzi**: najpierw należy pobrać [klucza subskrypcji](get-started.md). Jeśli chcesz wykonywać wywołania REST modele predeployed linii bazowej, należy skontaktować się [szczegóły tutaj](rest-apis.md). Jeśli chcesz użyć pobierania Websocket [zestawu SDK](speech-sdk.md)

**Pytanie**: wykonaj I zawsze jest konieczne do tworzenia modelu mowy niestandardowych?

**Odpowiedzi**: nie, jeśli aplikacja używa ogólnego języka codziennie bez słownika niestandardowego lub ten może następnie rzadkich nie musisz dostosować modelu. Ponadto, jeśli aplikacja ma być używany w środowisku, w którym istnieje niewielkiego lub żadnego szumu tła, a następnie nie trzeba dostosować albo. Portal umożliwia użytkownikom wdrażać linię bazową i dostosowane modeli i uruchamiać testy dokładności przed nimi. Użytkownicy można użyć tej funkcji do mierzenia dokładność vs linii bazowej niestandardowych modelu.

**Pytanie**: jak można ustalić, po zakończeniu przetwarzania zestawu danych lub modelu?

**Odpowiedzi**: Stan modelu lub zestawu danych w tabeli jest obecnie tylko chcesz wiedzieć.
Po zakończeniu przetwarzania stan będzie "Gotowe".

**Pytanie**: można utworzyć więcej niż jednego modelu w czasie?

**Odpowiedzi**: nie ma żadnego limitu liczby modeli znajdują się w kolekcji, ale tylko jeden z nich można tworzyć w czasie na każdej stronie.
Na przykład nie można uruchomić procesu tworzenia modelu języka, jeśli jest aktualnie model języka etap procesu.
Można jednak mieć modelu akustycznego i model języka przetwarzania w tym samym czasie. 

**Pytanie**: I zrealizowany wprowadzone błędu. Jak anulować Moje importu danych lub modelu tworzenia, która jest w toku? 

**Odpowiedzi**: obecnie nie można cofnąć procesu dostosowania akustycznego lub języka.
Importowane dane mogą zostać usunięte, po zakończeniu importowania

**Pytanie**: jaka jest różnica między wyszukiwania i modeli dyktowania i konwersacji modeli?

**Odpowiedzi**: istnieją dwa Base akustycznego & języka modeli do wyboru w niestandardowej usługi rozpoznawania mowy.
zapytania wyszukiwania lub dyktowania. Microsoft Conversational AM jest rozpoznawania mowy używany w stylu konwersacji.
Tego typu mowy jest zazwyczaj kierowane na innej osobie, takich jak w centrach wywołania lub spotkania.

**Pytanie**: można aktualizować Mój istniejący model (model układania)?

**Odpowiedzi**: nie można zaktualizować istniejącej modeli. Jako obejście połączyć stary zestaw danych z nowym i readapt.

Zestawy danych w starym i nowym musi można połączyć w jednym zip (jeśli są to dane akustycznego) lub pliku txt, jeśli są to dane języka. Raz dostosowania odbywa się można cofnąć wdrożone, można uzyskać nowy punkt końcowy nowe konieczność zaktualizowanym modelu.

**Pytanie**: gdzie należy współbieżności wyższa niż wartość domyślna lub co to jest oferowana w portalu. 

**Odpowiedzi**: można skalować modelu w przyrostach 20 równoczesnych żądań. 

Skontaktuj się z nami, jeśli wymaga zwiększenia skali.

**Pytanie**: można pobrać moich modelu i uruchom lokalnie?

**Odpowiedzi**: modeli nie może zostać pobrana i wykonywane lokalnie.

**Pytanie**: Moje żądania rejestrowane?

**Odpowiedzi**: dostępne są opcje podczas tworzenia wdrożenia można wyłączyć śledzenia, w którym brak audio lub transcriptions będą rejestrowane. W przeciwnym razie żądania zwykle są rejestrowane w usłudze Azure w bezpiecznym magazynie. Jeśli masz więcej prywatności, które uniemożliwiają korzystanie z usługi rozpoznawania mowy niestandardowe, skontaktuj się z kanałów pomocy technicznej.

## <a name="importing-data"></a>Importowanie danych

**Pytanie**: co to jest limit rozmiaru zestawu danych? Dlaczego? 

**Odpowiedzi**: bieżący limit dla zestawu danych wynosi 2 GB, z powodu ograniczeń rozmiaru pliku dla operacji przekazywania HTTP. 

**Pytanie**: można I zip pliki tekstowe, aby przekazać plik tekstowy większych? 

**Odpowiedzi**: nie, obecnie dozwolone są tylko pliki nieskompresowanych tekstu.

**Pytanie**: raport danych mówi wystąpiły zniesławiających nie powiodło się. Co to jest problem?

**Odpowiedzi**: nie można przekazać 100% zniesławiających w pliku nie jest problemem.
Jeśli ustawiona większość zniesławiających akustycznego lub język danych (na przykład > 95%) zostały pomyślnie zaimportowane, zestaw danych można używać. Jednak zaleca się, że próby wyjaśnienia przyczyn niepowodzenia zniesławiających i rozwiązać problemy. Najbardziej typowe problemy, takie jak formatowanie błędy, można łatwo rozwiązać. 

## <a name="creating-am"></a>Tworzenie AM

**Pytanie**: ilość danych akustycznych zbędna?

**Odpowiedzi**: zalecamy Rozpoczynanie od 30 minut do 1 godziny akustycznego danych

**Pytanie**: jakie dane należy zebrać?

**Odpowiedzi**: zbieranie danych, która jest maksymalnie zbliżony scenariusza aplikacji i przypadek użycia, jak to możliwe.
Zbieranie danych powinien być zgodny aplikacji docelowej i użytkownicy pod względem urządzenie lub urządzenia, środowisk i typy głośników. Ogólnie rzecz biorąc należy zebrać dane z jako szerokiego zakresu głośników, jak to możliwe. 

**Pytanie**: jak należy zebrać go? 

**Odpowiedzi**: można utworzyć oddzielną aplikację zbierania danych lub korzystać z niektórych poza półki oprogramowania nagrania audio.
Można również utworzyć wersję aplikacji, która rejestruje dane audio i użyty. 

**Pytanie**: należy wykonać transkrypcji danych dostosowania samodzielnie? 

**Odpowiedzi**: dane muszą zostać przetłumaczone. Można wykonać transkrypcji ją samodzielnie lub użyć usługi professional przekształcania. Niektóre z tych transcribers professional Użyj oraz innym osobom Użyj crowdsourcing.

**Pytanie**: jak długo trwa do utworzenia niestandardowych modelu akustycznego?

**Odpowiedzi**: czas przetwarzania służący do tworzenia niestandardowych modelu akustycznego nastąpi taka sama jak długość akustycznego zestawu danych.
Tak, dostosowane modelu akustycznego utworzone na podstawie pięciu godzinnego zestawu danych potrwa około pięciu godzin do procesu. 

## <a name="offline-testing"></a>Testowanie offline

**Pytanie**: wykonywać, w trybie offline testowanie Moje niestandardowe modelu akustycznego przy użyciu modelu języków niestandardowych?

**Odpowiedzi**: tak, wystarczy zaznaczyć model niestandardowych języka w listy rozwijanej, po skonfigurowaniu testu w trybie offline

**Pytanie**: wykonywać, w trybie offline testowanie Moje modelu języków niestandardowych przy użyciu niestandardowych modelu akustycznego?

**Odpowiedzi**: tak, wystarczy zaznaczyć modelu akustycznego niestandardowych w menu rozwijanym po skonfigurowaniu testu w trybie offline.

**Pytanie**: co to jest współczynnik błędów programu Word i jak jest ona obliczana?

**Odpowiedzi**: Częstotliwość błędów Word to metryki oceny rozpoznawania mowy. Jest on liczone jako całkowita liczba błędów, w tym wstawienia, usunięcia i podstawień podzielona przez całkowitą liczbę słów w przekształcania odwołania.

**Pytanie**: jak ustalić, czy wyniki testu dokładność jest dobrym?

**Odpowiedzi**: porównanie modelu linii bazowej i co można dostosować w wynikach.
Należy dążyć do modelu linii bazowej, aby dostosowań zastanowić Ci

**Pytanie**: jak wiem, raportowanie błędów systemu Windows z modeli bazowych tak widać, jeśli został poprawy? 

**Odpowiedzi**: wyników testu w trybie offline Pokaż dokładność linii bazowej dokładności modelu niestandardowych oraz poprawy za pośrednictwem linii bazowej

## <a name="creating-lm"></a>Tworzenie LM

**Pytanie**: ilość danych tekstowych należy przekazać?

**Odpowiedzi**: zależy od różnych słownictwa i wyrażenia używane w aplikacji z początkowy modeli języka. Dla wszystkich nowych wyrazów warto zawierają przykłady tyle możliwie użycia tych słów. Typowych wyrażeń, które są używane w aplikacji w tym fraz w danych języka jest również przydatne jako informuje system do nasłuchiwania również niniejsze postanowienia. Jest to często mają co najmniej jeden 100 i zwykle kilka zniesławiających kilkuset w zestawie danych języka lub więcej. Również niektórych typów kwerend powinny być częściej niż inne, można wstawić wiele kopii typowych kwerend w zestawie danych.

**Pytanie**: można tylko przekazać listę słów?

**Odpowiedzi**: przekazywanie listę słów będzie wyrazów do słownika, ale nie uczy systemu jak wyrazy są zazwyczaj używane.
Przez zapewnienie pełnej lub częściowej zniesławiających (zdań lub fraz rzeczy, które użytkownicy mogą powiedzieć) model języka można dowiedzieć się nowych słów i jak to zrobić, są one używane. Model języka niestandardowy jest prawidłowa, nie tylko do pobierania nowych słów w systemie, ale także dostosowywania prawdopodobieństwo znane słowa dla aplikacji. Zapewnienie pełnej zniesławiających pomaga systemu lepiej Dowiedz się więcej. 

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)
