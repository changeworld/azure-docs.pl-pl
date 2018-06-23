---
title: Często zadawane pytania dotyczące usługi rozpoznawania mowy niestandardowe na platformie Azure | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono odpowiedzi na pytania najpopularniejszych o niestandardowej usługi rozpoznawania mowy.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ms.openlocfilehash: a929869b36387b3257b672308ceca36c84ff8cae
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347189"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi mowy niestandardowych

Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, spróbuj pytaniem społeczności niestandardowej usługi rozpoznawania mowy na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) i [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Ogólne

**Pytanie**: jak można ustalić, po zakończeniu przetwarzania zestawu danych lub modelu?

**Odpowiedzi**: Stan modelu lub zestawu danych w tabeli jest obecnie tylko chcesz wiedzieć.
Po zakończeniu przetwarzania stan będzie "Gotowe".
Pracujemy nad ulepszone metod komunikacji przetwarzania stanu, takich jak powiadomienia e-mail.

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

**Odpowiedzi**: Firma Microsoft nie oferuje możliwość aktualizowania istniejącego modelu przy użyciu nowych danych.
Jeśli masz nowy zestaw danych i chcesz dostosować istniejący model musi ponownie dostosowania go z nowymi danymi i starego zestawu danych, który został użyty.
Zestawów danych w starym i nowym musi być łączone w pojedynczy zip (jeśli są to dane akustycznego) lub pliku txt, jeśli są to dane języka po dostosowaniu odbywa się można cofnąć wdrożone, można uzyskać nowy punkt końcowy nowe konieczność zaktualizowanym modelu.

**Pytanie**: gdzie należy współbieżności wyższa niż wartość domyślna. 

**Odpowiedzi**: można skalować modelu w przyrostach 5 współbieżnych żądań, które nazywamy jednostki skalowania. Każdą jednostką skalowania gwarantuje, że modelu może jednocześnie przetwarzać 5 strumieniem audio. Można również kupić 100 jednostek skalowania (lub równoczesnych żądań 500).

Skontaktuj się z nami Jeśli potrzebna jest większa niż.

**Pytanie**: można pobrać moich modelu i uruchom lokalnie?

**Odpowiedzi**: Firma Microsoft nie należy włączać modeli do pobrania i wykonywane lokalnie.

**Pytanie**: Moje żądania rejestrowane?

**Odpowiedzi**: dostępne są opcje podczas tworzenia wdrożenia można wyłączyć śledzenia, w którym brak audio lub transcriptions będą rejestrowane. W przeciwnym razie żądania zwykle są rejestrowane w usłudze Azure w bezpiecznym magazynie. Jeśli masz więcej prywatności, które uniemożliwiają przy użyciu usługi mowy niestandardowe, należy skontaktować się z nami.

## <a name="importing-data"></a>Importowanie danych

**Pytanie**: co to jest limit rozmiaru zestawu danych? Dlaczego? 

**Odpowiedzi**: bieżący limit dla zestawu danych wynosi 2 GB, z powodu ograniczeń rozmiaru pliku dla operacji przekazywania HTTP. 

**Pytanie**: można I zip pliki tekstowe, aby przekazać plik tekstowy większych? 

**Odpowiedzi**: nie, obecnie dozwolone są tylko pliki nieskompresowanych tekstu.

**Pytanie**: raport danych mówi wystąpiły zniesławiających nie powiodło się. Jest to problem?

**Odpowiedzi**: Jeśli tylko kilka zniesławiających nie można pomyślnie zaimportowane, nie jest problemem.
Jeśli ustawiona większość zniesławiających akustycznego lub język danych (np. > 95%) zostały pomyślnie zaimportowane, zestaw danych można używać. Jednak zaleca się, że próby wyjaśnienia przyczyn niepowodzenia zniesławiających i rozwiązać problemy.
Najbardziej typowe problemy, takie jak formatowanie błędy, można łatwo rozwiązać. 

## <a name="creating-am"></a>Tworzenie AM

**Pytanie**: ilość danych akustycznych zbędna?

**Odpowiedzi**: zalecamy Rozpoczynanie od 30 minut do 1 godziny akustycznego danych

**Pytanie**: jakie dane należy zebrać?

**Odpowiedzi**: należy zebrać dane, które jest maksymalnie zbliżony scenariusza aplikacji i przypadek użycia, jak to możliwe.
Oznacza to, że aplikacja docelowa i użytkownicy pod względem urządzenie lub urządzenia, środowisk i typy głośników zbierania danych powinna być zgodna. Ogólnie rzecz biorąc należy zebrać dane z jako szerokiego zakresu głośników, jak to możliwe. 

**Pytanie**: jak należy zebrać go? 

**Odpowiedzi**: można utworzyć oddzielną aplikację zbierania danych lub korzystać z niektórych poza półki oprogramowania nagrania audio.
Można również utworzyć wersję aplikacji, która rejestruje dane audio i użyty. 

**Pytanie**: należy wykonać transkrypcji danych dostosowania samodzielnie? 

**Odpowiedzi**: dane muszą zostać przetłumaczone. Można wykonać transkrypcji ją samodzielnie lub użyć usługi professional przekształcania. Niektóre z tych transcribers professional Użyj oraz innym osobom Użyj crowdsourcing. Zalecamy również zainstalowanie usługi przekształcenia na żądanie.

**Pytanie**: jak długo trwa do utworzenia niestandardowych modelu akustycznego?

**Odpowiedzi**: czas przetwarzania służący do tworzenia niestandardowych modelu akustycznego nastąpi taka sama jak długość akustycznego zestawu danych.
Tak, dostosowane modelu akustycznego utworzone na podstawie zestawu danych pięć godzin potrwa około pięciu godzin do procesu. 

## <a name="offline-testing"></a>Testowanie offline

**Pytanie**: wykonywać, w trybie offline testowanie Moje niestandardowe modelu akustycznego przy użyciu modelu języków niestandardowych?

**Odpowiedzi**: tak, po prostu wybierz model niestandardowych języka na liście rozwijanej po skonfigurowaniu testu w trybie offline

**Pytanie**: wykonywać, w trybie offline testowanie Moje modelu języków niestandardowych przy użyciu niestandardowych modelu akustycznego?

**Odpowiedzi**: tak, wystarczy zaznaczyć modelu akustycznego niestandardowych w menu rozwijanym po skonfigurowaniu testu w trybie offline.

**Pytanie**: co to jest współczynnik błędów programu Word i jak jest ona obliczana?

**Odpowiedzi**: Częstotliwość błędów Word to metryki oceny rozpoznawania mowy. Jest on liczone jako całkowita liczba błędów, w tym wstawienia, usunięcia i podstawień podzielona przez całkowitą liczbę słów w przekształcania odwołania.

**Pytanie**: można teraz znać wyników testu Moje niestandardowe modelu, jest to dobry lub nieprawidłowy numer?

**Odpowiedzi**: porównanie modelu linii bazowej i co można dostosować w wynikach.
Należy dążyć do modelu linii bazowej, aby dostosowań zastanowić Ci

**Pytanie**: jak wiem, raportowanie błędów systemu Windows z modeli bazowych tak widać, jeśli został poprawy? 

**Odpowiedzi**: wyników testu w trybie offline Pokaż dokładność linii bazowej dokładności modelu niestandardowych oraz poprawy za pośrednictwem linii bazowej

## <a name="creating-lm"></a>Tworzenie LM

**Pytanie**: ilość danych tekstowych należy przekazać?

**Odpowiedzi**: jest trudne pytanie, aby zapewnić dokładne odpowiedzi na pytania, ponieważ zależy on od różnych słownictwa i wyrażenia używane w aplikacji są początkowy modeli języka. Dla wszystkich nowych wyrazów warto zawierają przykłady tyle możliwie użycia tych słów. Typowe wyrażeń, które są używane w aplikacji włącznie z zawartymi w danych języka jest również przydatne jako go informuje system, aby nasłuchiwać również niniejsze warunki.
Jest to często mają co najmniej sto i zwykle kilka zniesławiających kilkuset w zestawie danych języka lub więcej.
Także jeśli niektóre typy * z zapytania powinny być częściej niż inne, możesz wstawić wiele kopii typowych kwerend w zestawie danych.

**Pytanie**: można tylko przekazać listę słów?

**Odpowiedzi**: przekazywanie listę słów będzie wyrazów do słownika, ale nie uczy systemu jak wyrazy są zazwyczaj używane.
Zapewniając pełnej lub częściowej zniesławiających (zdań lub fraz rzeczy, które użytkownicy mogą powiedzieć) nowych słów i sposób ich użycia można znaleźć modelu języka. Model języka niestandardowy jest prawidłowa, nie tylko do pobierania nowych słów w systemie, ale także dostosowywania prawdopodobieństwo znane słowa dla aplikacji. Zapewnienie pełnej zniesławiających pomaga systemu to Dowiedz się więcej. 

-----

 * [Omówienie](cognitive-services-custom-speech-home.md)
 * [Rozpoczęto](cognitive-services-custom-speech-get-started.md)
 * [Słownik](cognitive-services-custom-speech-glossary.md)
