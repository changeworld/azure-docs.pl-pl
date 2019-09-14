---
title: Koncepcje rozpoznawanie mowy Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Podstawowe pojęcia używane w usłudze Microsoft Speech Service.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fba1bbdeaf68bdd45524b336011627a27cd024da
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965712"
---
# <a name="basic-concepts"></a>Podstawowe pojęcia

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Na tej stronie opisano niektóre podstawowe pojęcia dotyczące usługi rozpoznawania mowy firmy Microsoft. Zalecamy zapoznanie się z tą stroną przed użyciem interfejsu API rozpoznawania mowy firmy Microsoft w aplikacji.

## <a name="understanding-speech-recognition"></a>Informacje o rozpoznawaniu mowy

Jeśli tworzysz aplikację obsługującą mowę po raz pierwszy, lub jeśli po raz pierwszy dodasz możliwości mowy do istniejącej aplikacji, ta sekcja pomoże Ci rozpocząć pracę. Jeśli masz już doświadczenie z aplikacjami obsługującymi mowę, możesz zrezygnować z skim tej sekcji lub można ją całkowicie pominąć, jeśli jesteś przestarzałą funkcją mowy i chcesz uzyskać prawo do szczegółów protokołu.

### <a name="audio-streams"></a>Strumienie audio

Przede wszystkim podstawowe koncepcje mowy to *strumień audio*. W przeciwieństwie do naciśnięć klawiszy, która występuje w jednym punkcie w czasie i zawiera jedną część informacji, żądanie wymawiane jest rozłożone na setki milisekund i zawiera wiele kilobajtów informacji. Czas wymawiania wyrażenia długości przedstawia pewne trudności dla deweloperów, którzy chcą zapewnić usprawnione i eleganckie środowisko mowy dla swojej aplikacji. Obecnie komputery i algorytmy wykonują transkrypcję mowy w około połowie czasu trwania wypowiedź, więc 2-sekundowe wypowiedź mogą być uzyskanego w przybliżeniu 1 sekundy, ale wszystkie aplikacje, które napotykają 1-sekundowe opóźnienie w przetwarzaniu, to nie są one usprawnione ani elegancki.

Na szczęście istnieją sposoby "ukrycia" czasu transkrypcji przez wykonanie transkrypcji jednej części wypowiedź, gdy użytkownik znajduje się w innej części. Na przykład przez podzielenie 1-sekundowej wypowiedź na 10 fragmentów 100 milisekund i przez wykonanie transkrypcji na każdym z nich, ponad 450 całkowitej 500 milisekund wymaganych do transkrypcji może być "ukryte", dzięki czemu użytkownik nie jest w stanie transkrypcji wykonywane podczas rozmowy. Podczas rozważania tego przykładu należy pamiętać, że usługa wykonuje transkrypcję w ciągu poprzednich 100 milisekund audio, podczas gdy użytkownik będzie mówić o następnej 100, więc gdy użytkownik przestanie mówić, usługa będzie musiała jedynie transkrypcja wyłącznie 100 milisekundy audio umożliwiające wygenerowanie wyniku.

W celu osiągnięcia tego środowiska użytkownika informacje o dźwięku mówiony są zbierane w fragmentach i uzyskanego w miarę mówienia użytkownika. Te fragmenty dźwięku są zbiorczo ze *strumienia audio*, a proces wysyłania tych fragmentów audio do usługi jest nazywany *strumieniiem audio.* Przesyłanie strumieniowe audio jest ważną częścią dowolnej aplikacji z obsługą mowy; Dostrajanie rozmiaru fragmentu i optymalizowanie implementacji przesyłania strumieniowego to niektóre najbardziej wpływ na sposób ulepszania środowiska użytkownika aplikacji.

### <a name="microphones"></a>Mikrofonów

Ludzie przetwarzają dźwięk mówiony przy użyciu ich Ears, ale nie Animuj sprzęt używa mikrofonów. Aby włączyć mowę dla każdej aplikacji, należy przeprowadzić integrację z mikrofonem dostarczającym strumień audio dla aplikacji.

Interfejsy API dla mikrofonu muszą zezwalać na uruchamianie i zatrzymywanie otrzymywania bajtów audio z mikrofonu. Należy zdecydować, jakie akcje użytkownika będą wyzwalać mikrofon, aby rozpocząć nasłuchiwanie mowy. Możesz wybrać naciśnięcie przycisku, aby wymusić początek nasłuchiwania lub wybrać opcję wymuszania *słowa kluczowego* lub *wznowienia przez program Word* Spotter do mikrofonu i używania danych wyjściowych tego modułu do wyzwalania wysyłania dźwięku do mowy Microsoft Speech Usługi.

### <a name="end-of-speech"></a>Koniec mowy

Wykrywanie *momentu* , w którym głośnik *przestał* mówić, jest bardzo mały dla ludzi, ale jest trudnym problemem poza warunkami laboratorium. Nie wystarczy po prostu wyszukać czystego obejścia po wypowiedź, ponieważ często wiele szumów otoczenia pozwala na ich skomplikowanie. Usługa Microsoft Speech Service wykonuje doskonałe zadanie szybkiego wykrywania, gdy użytkownik przestanie mówić, a usługa może poinformować aplikację o tym fakcie, ale to rozwiązanie oznacza, że aplikacja jest ostatnią wiedzą, kiedy użytkownik przestanie mówić. Nie jest to tak samo, jak w przypadku innych form wprowadzania, w których aplikacja jest *pierwszą* wiedzą, gdy dane wejściowe użytkownika zaczynają się *i* kończą.

### <a name="asynchronous-service-responses"></a>Asynchroniczne odpowiedzi usługi

Fakt, że aplikacja musi być informowana o tym, gdy dane wprowadzane przez użytkownika nie narzucają żadnych kar lub trudności związanych z programowaniem w aplikacji, ale wymaga to, aby użytkownik sądził o żądaniach mowy inaczej niż żądanie wejściowe/ wzorce odpowiedzi, z którymi znasz. Ponieważ aplikacja nie będzie wiedzieć, gdy użytkownik przestanie mówić, aplikacja musi nadal przesyłać strumieniowo dźwięk do usługi, jednocześnie i asynchronicznie oczekując na odpowiedź z usługi. Ten wzorzec różni się w przeciwieństwie do innych protokołów sieci Web żądania/odpowiedzi, takich jak HTTP. W tych protokołach należy wykonać żądanie przed odebraniem odpowiedzi; w protokole Microsoft Speech Service, otrzymujesz odpowiedzi, *gdy nadal przesyłasz strumieniowo dźwięk dla żądania*.

> [!NOTE]
> Ta funkcja nie jest obsługiwana w przypadku korzystania z interfejsu API REST protokołu HTTP usługi Speech.

### <a name="turns"></a>Bierze

Speech to przewoźnik informacji. Gdy mówisz, próbujesz przekazać informacje, które znajdują się w posiadaniu, osobie, która nasłuchuje tych informacji. Podczas przekazywania informacji zwykle bierze się mówić i nasłuchuje. Podobnie aplikacja obsługująca mowę współdziała z użytkownikami, nasłuchuje i odpowiada, chociaż aplikacja zwykle wykonuje większość nasłuchiwania. Wypowiadane dane wejściowe użytkownika i odpowiedź usługi do tego danych wejściowych są wywoływane *.* *Włącza* się, gdy użytkownik zacznie i kończy pracę, gdy aplikacja zakończyła obsługę odpowiedzi usługi mowy.

### <a name="telemetry"></a>Telemetria

Tworzenie urządzenia lub aplikacji z obsługą mowy może być trudne, nawet dla doświadczonych deweloperów. Protokoły oparte na strumieniach często pojawiają się zniechęcające na pierwszy rzut oka, a ważne szczegóły, takie jak wykrywanie cisza, mogą być całkowicie nowe. Dzięki temu wiele komunikatów, które muszą zostać pomyślnie wysłane i odebrane w celu ukończenia pojedynczej pary żądanie/odpowiedź, jest *bardzo* ważne, aby zebrać kompletne i dokładne dane dotyczące tych komunikatów. Protokół Microsoft Speech Service jest dostępny dla kolekcji tych danych. Należy podejmować wszelkie wysiłki, aby dostarczyć wymagane dane tak dokładnie, jak to możliwe. dostarczając kompletne i dokładne dane, skontaktuj się z pomocą techniczną, aby uzyskać pomoc w zakresie rozwiązywania problemów z implementacją klienta przy użyciu zespołu usługi Microsoft Speech, jakość zebranych danych telemetrycznych będzie ważna dla problemu analizy.

> [!NOTE]
> Ta funkcja nie jest obsługiwana w przypadku korzystania z interfejsu API REST rozpoznawania mowy.

### <a name="speech-application-states"></a>Stany aplikacji mowy

Kroki, które należy wykonać, aby włączyć wprowadzanie mowy w aplikacji, są nieco inne niż kroki dla innych form wprowadzania, takie jak kliknięcia myszą lub palcem. Należy śledzić, kiedy aplikacja nasłuchuje na mikrofonu i wysyła dane do usługi mowy, gdy oczekuje na odpowiedź z usługi i gdy jest w stanie bezczynności. Relacje między tymi Stanami przedstawiono na poniższym diagramie.

![Diagram stanu aplikacji mowy](Images/speech-application-state-diagram.png)

Ponieważ usługa rozpoznawania mowy firmy Microsoft uczestniczy w niektórych stanach, protokół usługi definiuje komunikaty, które ułatwiają przejście aplikacji między Stanami. Aplikacja musi interpretować te komunikaty protokołu i wykonywać na nich działania, aby śledzić Stany aplikacji mowy i zarządzać nimi.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Korzystanie z usługi rozpoznawania mowy z aplikacji

Usługa rozpoznawania mowy firmy Microsoft udostępnia dwa sposoby dodawania mowy do aplikacji przez deweloperów.

- [Interfejsy API REST](GetStarted/GetStartedREST.md): Deweloperzy mogą używać wywołań HTTP z aplikacji do usługi w celu rozpoznawania mowy.
- [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md): Aby uzyskać zaawansowane funkcje, deweloperzy mogą pobrać biblioteki klienckie Microsoft Speech i połączyć się ze swoimi aplikacjami.  Biblioteki klienckie są dostępne na różnych platformach (Windows, Android, iOS) przy użyciu różnych języków (C#, Java, JavaScript, ObjectiveC).

| Przypadki zastosowań | [Interfejsy API REST](GetStarted/GetStartedREST.md) | [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konwertuj krótki dźwięk mówiony, na przykład polecenia (długość audio < 15 s) bez wyników pośrednich | Tak | Tak |
| Konwertuj długi dźwięk (> 15 s) | Nie | Tak |
| Strumieniowe przesyłanie dźwięku z pożądanymi pośrednimi wynikami | Nie | Tak |
| Zapoznaj się z tekstem konwertowanym z dźwięku przy użyciu LUIS | Nie | Tak |

 Jeśli język lub platforma nie mają jeszcze zestawu SDK, możesz utworzyć własną implementację na podstawie [dokumentacji protokołu](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Tryby rozpoznawania

Istnieją trzy tryby rozpoznawania: `interactive`, `conversation`, i `dictation`. Tryb rozpoznawania umożliwia dostosowanie rozpoznawania mowy w zależności od tego, jak użytkownicy mogą mówić. Wybierz odpowiedni tryb rozpoznawania dla swojej aplikacji.

> [!NOTE]
> Tryby rozpoznawania mogą mieć inne zachowania w protokole REST niż w protokole WebSocket. Na przykład interfejs API REST nie obsługuje ciągłego rozpoznawania, nawet w trybie konwersacji lub dyktowania.
> [!NOTE]
> Te tryby są stosowane w przypadku bezpośredniego użycia protokołu REST lub WebSocket. [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md) używają różnych parametrów do określenia trybu rozpoznawania. Aby uzyskać więcej informacji, zapoznaj się z dowolnie wybraną biblioteką klienta.

Usługa Microsoft Speech zwraca tylko jeden wynik frazy rozpoznawania dla wszystkich trybów rozpoznawania. Dla dowolnej pojedynczej wypowiedź obowiązuje limit 15 sekund.

### <a name="interactive-mode"></a>Tryb interaktywny

W `interactive` trybie, użytkownik wykonuje krótkie żądania i oczekuje, że aplikacja wykonuje akcję w odpowiedzi.

Następujące charakterystyki są typowymi aplikacjami w trybie interaktywnym:

- Użytkownicy wiedzą, że znajdują się na komputerze, a nie do innego.
- Użytkownicy aplikacji wiedzą przed czasem, co chcą powiedzieć, w zależności od tego, co chce aplikacja.
- Wyrażenia długości zazwyczaj Ostatnia o 2-3 sekund.

### <a name="conversation-mode"></a>Tryb konwersacji

W `conversation` trybie, użytkownicy są zaangażowani w konwersacje między ludźmi.

Następujące charakterystyki są typowymi aplikacjami trybu konwersacji:

- Użytkownicy wiedzą, że rozmawiają z inną osobą.
- Funkcja rozpoznawania mowy ulepsza konwersacje ludzi, umożliwiając jednemu lub obu uczestnikom wyświetlanie tekstu mówionego.
- Użytkownicy nie zawsze planują, co chce powiedzieć.
- Użytkownicy często używają żargonu i innych nieformalnej mowy.

### <a name="dictation-mode"></a>Tryb dyktowania

W `dictation` trybie, użytkownicy mogą odszukać więcej wyrażenia długości aplikacji w celu dalszego przetwarzania.

Następujące charakterystyki są typowymi aplikacjami trybu dyktowania:

- Użytkownicy wiedzą, że rozmawiają z maszyną.
- Użytkownicy wyświetlają wyniki tekstu rozpoznawania mowy.
- Użytkownicy często planują, co chcą powiedzieć, i używać bardziej formalnego języka.
- Użytkownicy korzystają z pełnych zdań, w ciągu ostatnich 5-8 sekund.

> [!NOTE]
> W trybach dyktowania i konwersacji usługa Microsoft Speech nie zwraca wyników częściowych. Zamiast tego usługa zwraca stałe wyniki frazy po wyciszeniu granic w strumieniu audio. Firma Microsoft może ulepszyć protokół mowy, aby ulepszyć środowisko użytkownika w tych trybach ciągłego rozpoznawania.

## <a name="recognition-languages"></a>Języki rozpoznawania

*Język rozpoznawania* określa język, w którym użytkownik aplikacji mówi. Określ *język rozpoznawania* przy użyciu parametru zapytania adresu URL *języka* w połączeniu. Wartość parametru zapytania *języka* korzysta ze znacznika języka IETF [47](https://en.wikipedia.org/wiki/IETF_language_tag)i **musi** być jednym z języków obsługiwanych przez interfejs API rozpoznawania mowy. Pełną listę języków obsługiwanych przez usługę mowy można znaleźć na stronie [obsługiwane języki](API-Reference-REST/supportedlanguages.md).

Usługa Microsoft Speech odrzuci nieprawidłowe żądania połączenia, wyświetlając `HTTP 400 Bad Request` odpowiedź. Nieprawidłowe żądanie to:

- Nie zawiera wartości parametru zapytania *języka* .
- Zawiera parametr zapytania *języka* , który jest niepoprawnie sformatowany.
- Zawiera parametr zapytania *języka* , który nie jest jednym z języków obsługi.

Można utworzyć aplikację, która obsługuje jeden lub wszystkie języki obsługiwane przez usługę.

### <a name="example"></a>Przykład

W poniższym przykładzie aplikacja używa trybu rozpoznawania mowy z *konwersacją* dla prezentera angielskiego USA.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Odpowiedzi transkrypcji

Odpowiedzi transkrypcji zwracają przekonwertowany tekst z dźwięk do klientów. Odpowiedź transkrypcji zawiera następujące pola:

- `RecognitionStatus`Określa stan rozpoznawania. Możliwe wartości podano w poniższej tabeli.

| State | Opis |
| ------------- | ---------------- |
| Powodzenie | Rozpoznawanie zakończyło się pomyślnie, a pole DisplayText jest obecne |
| Nomatch | Wykryto mowy strumienia audio, ale bez wyrazów z języka docelowego zostały dopasowane. Aby uzyskać więcej informacji, zobacz [nomatch status rozpoznawania (#nomatch-Recognition-status)  |
| InitialSilenceTimeout | Początek strumienia audio zawiera tylko ciszą, a usługa przekroczyła limit czasu podczas oczekiwania na mowę |
| BabbleTimeout | Początek strumienia audio zawiera tylko szum, a usługa przekroczyła limit czasu podczas oczekiwania na mowę |
| Błąd | Usługa rozpoznawania napotkała błąd wewnętrzny i nie może kontynuować |

- `DisplayText`reprezentuje rozpoznaną frazę, gdy zastosowano wielkie litery, interpunkcję i odwrotność tekstu — Normalizacja i nie została zamaskowany przy użyciu gwiazdek. Pole DisplayText jest obecne *tylko* wtedy, `RecognitionStatus` gdy pole ma wartość. `Success`

- `Offset`Określa przesunięcie (w jednostkach 100-nanosekund), w którym fraza została rozpoznana względem początku strumienia audio.

- `Duration`Określa czas trwania tej frazy mowy (w jednostkach 100-nanosekund).

Odpowiedź transkrypcji zwraca więcej informacji w razie potrzeby. Zobacz [Format danych wyjściowych](#output-format) , aby uzyskać więcej szczegółowych danych wyjściowych.

Usługa Microsoft Speech Service obsługuje dodatkowy proces transkrypcji obejmujący Dodawanie wielkich liter i znaków interpunkcyjnych, maskowanie wulgarności oraz normalizowanie tekstu do wspólnych formularzy. Na przykład, jeśli użytkownik mówi frazę reprezentowaną przez słowa "Przypomnij mi o zakupie sześciu telefonów iPhone", usługi mowy firmy Microsoft zwracają tekst uzyskanego "Przypomnij mi o zakupie 6 iPhone". Proces służący do konwertowania wyrazu "szósty" na liczbę "6" nazywa się *normalizacją tekstu odwrotnego* (*ITN* for Short).

### <a name="nomatch-recognition-status"></a>Brak dopasowania stanu rozpoznawania

Odpowiedź transkrypcji zwraca wartość `NoMatch` w `RecognitionStatus` momencie, gdy usługa Microsoft Speech wykrywa mowę w strumieniu audio, ale nie jest w stanie dopasować tej mowy do gramatyki języka używanej w żądaniu. Na przykład warunek *nomatch* może wystąpić, jeśli użytkownik poinformuje coś w języku niemieckim, gdy aparat rozpoznawania oczekuje języka angielskiego na angielski jako mówiony język. Wzorzec Wave wypowiedź będzie wskazywał obecność człowieka, ale żaden z wyrazów, które nie są wymawiane, jest zgodny z leksykonem w języku polskim używanym przez aparat rozpoznawania.

Inny warunek *nomatch* występuje, gdy algorytm rozpoznawania nie może znaleźć dokładnego dopasowania dla dźwięków zawartych w strumieniu audio. W takim przypadku usługa Microsoft Speech Service może generować komunikaty *mowy. hipotezy* , które zawierają *tekst Różnica średnich hipotetycznych* , ale generuje komunikat *mowy. phrase* , w którym *RecognitionStatus* jest *nomatching* . Ten stan jest normalny; nie należy wprowadzać żadnych założeń dotyczących dokładności lub wierności tekstu w komunikacie *mowy. hipotezy* . Ponadto nie należy zakładać, że ponieważ usługa Microsoft Speech tworzy komunikaty *mowy. hipotezy* , że usługa może wygenerować komunikat *mowy. phrase* z *RecognitionStatus* *sukces*.

## <a name="output-format"></a>Format wyjściowy

Usługa Microsoft Speech Service może zwracać różne formaty ładunku w odpowiedzi na transkrypcję. Wszystkie ładunki są strukturami JSON.

Można kontrolować format wyniku frazy, określając `format` parametr zapytania adresu URL. Domyślnie usługa zwraca `simple` wyniki.

| Format | Opis |
|-----|-----|
| `simple` | Uproszczony wynik frazy zawierający stan rozpoznawania i rozpoznany tekst w formularzu wyświetlania. |
| `detailed` | Stan rozpoznawania i N-Najlepsza lista wyników, gdzie każdy wynik frazy zawiera wszystkie cztery formy rozpoznawania i wynik pewności. |

`Offset` `duration` [](#n-best-values) `RecognitionStatus`Format zawiera N najlepszych wartości (oprócz, i, w odpowiedzi). `detailed`

### <a name="n-best-values"></a>N — najlepsze wartości

Odbiorniki, niezależnie od tego, czy ludzie lub maszyny, nigdy nie mogą się upewnić, że wymawiali *dokładnie* , co było wypowiada Odbiornik może przypisać *prawdopodobieństwo* tylko do określonej interpretacji wypowiedź.

W normalnych warunkach podczas rozmowy z innymi osobami, którym często pracują, ludzie mają duże prawdopodobieństwo rozpoznawania wyrazów, które zostały wypowiadane. Odbiorniki mowy oparte na maszynach dążą do osiągnięcia podobnych poziomów dokładności i, w odpowiednich warunkach, [osiągają parzystość dla ludzi](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Algorytmy, które są używane w funkcji rozpoznawania mowy, eksplorują alternatywne interpretacje wypowiedź w ramach normalnego przetwarzania. Zwykle te alternatywy są odrzucane, ponieważ dowody na korzyść pojedynczej interpretacji staną się przeciążone. Jednak w mniej niż optymalnych warunkach aparat rozpoznawania mowy kończy pracę z listą alternatywnych możliwych interpretacji. Pierwsze *n* alternatyw na tej liście nazywa się *n-najlepszą listą*. Każda alternatywa ma przypisany [wynik zaufania](#confidence). Wyniki pewności mieszczą się w zakresie od 0 do 1. Wynik 1 oznacza najwyższy poziom zaufania. Wynik 0 reprezentuje najniższy poziom zaufania.

> [!NOTE]
> Liczba wpisów w N-najlepszych listach różni się w zależności od wielu wyrażenia długości. Liczba wpisów może się różnić w zależności od wielu rozpoznawania tego *samego* wypowiedź. Ta odmiana to naturalny i oczekiwany wynik probabilistyczneości algorytmu rozpoznawania mowy.

Każdy wpis zwrócony w N-najlepszych listach zawiera

- `Confidence`, która reprezentuje [wyniki pewności](#confidence) dla tego wpisu.
- `Lexical`, która jest [leksykalną formą](#lexical-form) rozpoznanego tekstu.
- `ITN`, która jest [formą ITN](#itn-form) rozpoznanego tekstu.
- `MaskedITN`, który jest [ITNą formą](#masked-itn-form) rozpoznanego tekstu.
- `Display`, który jest [formularzem wyświetlanym](#display-form) rozpoznanego tekstu.

### Wyniki pewności<a id="confidence"></a>

Wyniki pewności są integralnymi systemami rozpoznawania mowy. Usługa Microsoft Speech Service uzyskuje wyniki pewności od *klasyfikatora zaufania*. Firma Microsoft przeniesie klasyfikator zaufania na zestaw funkcji, które są przeznaczone do Maximally dyskryminacji między prawidłowym i niepoprawnym rozpoznawaniem. Wyniki pewności są oceniane dla pojedynczych słów i całych wyrażenia długości.

Jeśli zdecydujesz się na korzystanie z wyników, które są zwracane przez usługę, weź pod uwagę następujące zachowanie:

- Wyniki pewności można porównać tylko w ramach tego samego trybu rozpoznawania i języka. Nie należy porównywać ocen między różnymi językami lub różnymi trybami rozpoznawania. Na przykład Ocena zaufania w trybie rozpoznawania Interactive *nie* ma korelacji z oceną zaufania w trybie dyktowania.
- Wyniki pewności najlepiej używać w ograniczonym zestawie wyrażenia długości. W wyniku dużego zestawu wyrażenia długości istnieje naturalnie znaczący stopień zmienności.

Jeśli zdecydujesz się użyć wartości wyniku pewności jako *progu* , w którym działa aplikacja, użyj funkcji rozpoznawania mowy, aby określić wartości progowe.

- Wykonaj rozpoznawanie mowy na reprezentatywnej próbce wyrażenia długości dla aplikacji.
- Zbierz wyniki pewności dla każdego uznania w zestawie próbek.
- Oparcie wartości progowej na poziomie percentylu dla tego przykładu.

Żadna wartość progu pojedynczego nie jest odpowiednia dla wszystkich aplikacji. Akceptowalny wynik zaufania dla jednej aplikacji może być nieakceptowalny dla innej aplikacji.

### <a name="lexical-form"></a>forma leksykalna

Forma leksykalna jest rozpoznawanym tekstem, dokładnie tak jak w wypowiedź i bez znaków interpunkcyjnych i wielkich liter. Na przykład leksykalna forma adresu "1020 Enterprise Way" będzie *10 20 w sposób korporacyjny*, przy założeniu, że w ten sposób wymawiasz. Leksykalna postać zdania "Przypomnij mi o zakupie 5 ołówków" *przypomina mnie o zakupie pięciu ołówków*.

Forma leksykalna jest najbardziej odpowiednia dla aplikacji, które muszą wykonać niestandardową normalizację tekstu. Forma leksykalna jest również odpowiednia dla aplikacji, które wymagają nieprzetworzonych słów rozpoznawania.

Niewulgarność nigdy nie jest zamaskowane w postaci leksykalnej.

### <a name="itn-form"></a>Formularz ITN

Normalizacja tekstu jest procesem konwertowania tekstu z jednego formularza do innego "kanonicznego" formularza. Na przykład numer telefonu "555-1212" może zostać przekonwertowany na postać kanoniczną *5 5 5 1 2 1 2*. Normalizacja tekstu *odwrotnego* (ITN) odwraca ten proces, konwertując słowa "5 5 5 1 2 1 2" na odwróconą postać kanoniczną *555-1212*. Forma ITN wyniku rozpoznawania nie obejmuje wielkich liter ani znaków interpunkcyjnych.

Formularz ITN jest najbardziej odpowiedni dla aplikacji, które działają na rozpoznany tekst. Na przykład aplikacja, która umożliwia użytkownikowi dyktowanie terminów wyszukiwania, a następnie używa tych terminów w zapytaniu internetowym, użyje formularza ITN. Niewulgarność nigdy nie jest zamaskowane w formularzu ITN. Aby maskować nierówność, użyj *zamaskowanego formularza ITN*.

### <a name="masked-itn-form"></a>Zamaskowany formularz ITN

Ponieważ niewulgarność jest częściowo częścią języka wymawianego, usługa Microsoft Speech rozpoznaje takie słowa i frazy, gdy są one wymawiane. Niepełnoletnie może jednak być odpowiednie dla wszystkich aplikacji, w szczególności tych, które są przeznaczone dla użytkowników z ograniczonym dostępem, którzy nie są użytkownikami.

Zamaskowane formularz ITN stosuje maskowanie nierówność do formy normalizowania tekstu odwrotnego. Aby maskować nierówność, ustaw wartość parametru wulgarności na `masked`. Gdy nie jest maskowany, wyrazy, które są rozpoznawane jako część leksykonu niewulgarności języka, są zastępowane gwiazdkami. Na przykład: *Przypomnij mi o zakupie 5 * * * * ołówek*. Forma zamaskowanej ITN wyniku rozpoznawania nie obejmuje wielkich liter ani znaków interpunkcyjnych.

> [!NOTE]
> Jeśli wartość parametru zapytania o niewulgarność jest ustawiona `raw`na, zamaskowane formularz ITN jest taki sam jak formularz ITN. *Brak maski.*

### <a name="display-form"></a>Formularz wyświetlania

Znaki interpunkcyjne i wielkie litery, w których należy umieścić wyróżnienie, miejsce wstrzymania i tak dalej, co sprawia, że tekst jest łatwiejszy do zrozumienia. Formularz wyświetlania dodaje interpunkcję i wielką literę do wyników rozpoznawania, co sprawia, że jest to najbardziej odpowiedni formularz dla aplikacji, które wyświetlają tekst mówiony.

Ponieważ formularz wyświetlania rozszerza zamaskowany formularz ITN, można ustawić wartość parametru wulgarności na `masked` lub. `raw` Jeśli wartość jest ustawiona na `raw`, wyniki rozpoznawania obejmują wszelkie niewulgarne wymawiane przez użytkownika. Jeśli wartość jest ustawiona na `masked`, wyrazy rozpoznawane jako część leksykonu niewulgarności języka są zastępowane gwiazdkami.

### <a name="sample-responses"></a>Przykład odpowiedzi

Wszystkie ładunki są strukturami JSON.

Format ładunku wyniku `simple` frazy:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Format ładunku wyniku `detailed` frazy:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Obsługa niewulgarności w rozpoznawaniu mowy

Usługa Microsoft Speech rozpoznaje wszystkie formy wypowiedzi człowieka, w tym słowa i frazy, których wiele osób klasyfikuje jako "wulgarne". Można kontrolować sposób, w jaki usługa obsługuje niewulgarność przy użyciu parametru zapytania o *niewulgarność* . Domyślnie w wyniku *funkcji Speech. phrase* są używane maski usługi, które nie zwracają komunikatów *mowy. hipotezy* , które zawierają niewulgarność.

| Wartość *wulgarności* | Opis |
| - | - |
| `masked` | Niemniej maski z gwiazdkami. Jest to zachowanie domyślne. |
| `removed` | Usuwa niewulgarność ze wszystkich wyników. |
| `raw` | Rozpoznaje i zwraca wartość unwulgarną we wszystkich wynikach. |

### <a name="profanity-value-masked"></a>Wartość wulgarności`Masked`

Aby maskować nierówność, należy *ustawić wartość parametru*zapytania o *niewulgarność* . Jeśli parametr zapytania o *niewulgarności* ma tę wartość lub nie jest określony dla żądania, *maska* usług ma nierówność. Usługa wykonuje maskowanie przez zastępowanie niewulgarności w wynikach rozpoznawania przy użyciu gwiazdek. Po określeniu obsługi maskowania z niewymagającym maskowania usługa nie zwraca komunikatów *mowy. hipotezy* , które zawierają braki.

### <a name="profanity-value-removed"></a>Wartość wulgarności`Removed`

Po *usunięciu*wartości parametru zapytania o *niewulgarności* usługa usuwa nierówność z komunikatów *mowy. phrase* i *mowy. hipotez* . Wyniki są takie same, *jak w przypadku braku wyrazów o niewulgarności*.

#### <a name="profanity-only-utterances"></a>Unwulgarne — tylko wyrażenia długości

Użytkownik może mówić *tylko* o Niemniej Niemniej, gdy aplikacja skonfigurował usługę, aby ją usunąć. W tym scenariuszu, jeśli tryb rozpoznawania jest *dyktowaniem* lub *konwersacją*, usługa nie zwraca *mowy. wynik*. Jeśli tryb rozpoznawania jest *interaktywny*, usługa zwraca *mowę. wynik* z kodem stanu *nomatch*.

### <a name="profanity-value-raw"></a>Wartość wulgarności`Raw`

Gdy parametr *zapytania o* nieodpowiedniej wartości ma wartość *RAW*, usługa nie usuwa ani nie maskuje braków w komunikatach *mowy. phrase* ani *mowy. hipotez* .
