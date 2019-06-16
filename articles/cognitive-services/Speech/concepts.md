---
title: Pojęcia dotyczące funkcji rozpoznawania mowy Bing | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Podstawowe pojęcia używane w usługi mowy firmy Microsoft.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1cbf1514ac5eba4e288ecb78944878217fc5ba3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65954522"
---
# <a name="basic-concepts"></a>Podstawowe pojęcia

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Na tej stronie opisano niektóre podstawowe pojęcia związane z usługi rozpoznawania mowy firmy Microsoft. Zalecamy przeczytać tę stronę przed rozpoczęciem korzystania z interfejsu API rozpoznawania mowy firmy Microsoft w swojej aplikacji.

## <a name="understanding-speech-recognition"></a>Opis funkcji rozpoznawania mowy

Jeśli po raz pierwszy tworzysz aplikację z funkcją mowy, czy jest po raz pierwszy dodajesz możliwości mowy do istniejącej aplikacji, ta sekcja ułatwia rozpoczęcie pracy. Jeśli masz już pewne doświadczenie w zakresie aplikacje obsługujące rozpoznawanie mowy, możesz po prostu wyszukanie w tej sekcji, lub może go pominąć, jeśli jesteś sił stary w mowy i chcesz uzyskać prawo, aby uzyskać szczegółowe informacje protokołu.

### <a name="audio-streams"></a>Strumieni audio

Przede wszystkim między podstawowe pojęcia mowy *strumienia audio*. Inaczej niż w przypadku naciśnięcia klawisza, który występuje w jednym punkcie w czasie i zawiera pojedynczy informacje, żądanie prowadzone jest rozłożona na setki milisekund i zawiera wiele kilobajtów informacji. Czas trwania prowadzone wypowiedzi przedstawia niektóre problemy, którzy chcą zapewnić środowisko prostsze i elegancki mowy w swojej aplikacji. Współczesnych komputerów i algorytmy wykonywania transkrypcja mowy w około połowę czas trwania wypowiedź, więc może być transkrybowanego wypowiedź 2-sekundowych w przybliżeniu 1 sekundę, ale są każdą aplikację, która napotyka 1-sekundowe opóźnienie przetwarzania użytkownika Usprawnione ani elegancki.

Na szczęście istnieją sposoby "Ukrywanie" czas transkrypcji, wykonując transkrypcji w jednej części wypowiedź, gdy użytkownik mówiącego Prezydenta innej części. Na przykład, dzieląc wypowiedź 1 sekundę, do 10 fragmentów 100 milisekund i wykonując transkrypcji na każdy fragment z kolei ponad 450 z całkowitej 500 milisekund wymagany dla transkrypcji może być "ukryte", aby użytkownik nie rozpoznaje transkrypcji jest Trwa wykonywanie, gdy są one mówić. Gdy myśleć o tym przykładzie, pamiętaj o tym, czy usługa działa transkrypcji na poprzednim 100 milisekund audio, gdy użytkownik mówiącego Prezydenta następne 100, więc gdy użytkownik zatrzymuje rzecz biorąc, usługa będzie miał tylko także około 100 Liczba milisekund audio w celu utworzenia wyniku.

Uzyskanie tego środowiska użytkownika, prowadzone informacje audio są zbierane we fragmentach i transkrybowanego jak mówi użytkownik. Te fragmenty audio, łącznie z *strumienia audio*, a proces wysyłania tych fragmentów audio z usługą jest nazywana *przesyłanie strumieniowe audio.* Przesyłanie strumieniowe audio jest ważną częścią dowolnej aplikacji z obsługą mowy; Dostosowywanie rozmiaru fragmentu i optymalizacja przesyłania strumieniowego implementacji kilka największy sposobów poprawy środowiska użytkownika aplikacji.

### <a name="microphones"></a>Mikrofon

Osoby przetwarzania dźwięk mówiony przy użyciu ich uszy, ale inanimate sprzętu używa mikrofon. Aby włączyć mowy w dowolnej aplikacji, należy zintegrować z mikrofonu, zapewniając strumienia audio w aplikacji.

Interfejsy API z mikrofonu musi umożliwiają uruchamianie i zatrzymywanie odbieranie bajtów dźwięku z mikrofonu. Musisz zdecydować, jakie akcje użytkownika spowoduje wyzwolenie mikrofon rozpocząć nasłuchiwania rozpoznawania mowy. Możesz naciśnięcie przycisku, wyzwalanie początek nasłuchiwania lub chcesz mieć *słowa kluczowego* lub *wake word* spotter zawsze nasłuchiwania mikrofon i korzystać z danych wyjściowych tego modułu do Wyzwalanie wysyłanie audio do firmy Microsoft usługi mowy.

### <a name="end-of-speech"></a>Koniec mowy

Wykrywanie *podczas* ma prelegenta *zatrzymana* wypowiedzi dla ludzi wydaje się prosta, ale jest dość trudne problem poza warunkach laboratoryjnych. Nie jest wystarczająco, aby po prostu wyszukaj czystego wyciszenia po wypowiedź ponieważ często istnieje wiele hałasu otoczenia do skomplikować rzeczy. Usługa Microsoft Speech zapewnia doskonałą szybkiego wykrywania po użytkownik zatrzymał rzecz biorąc, usługa może poinformować o tym fakcie aplikacji, ale to rozmieszczenie oznacza, że aplikacja jest ostatni wiedzieć, kiedy użytkownik Przestań mówić. To nie jest w ogóle podobnie jak inne formy wprowadzania gdzie aplikacja jest *pierwszy* wiedzieć, kiedy użytkownik dane wejściowe podane przez rozpoczyna się *i* kończy.

### <a name="asynchronous-service-responses"></a>Odpowiedzi asynchronicznych usługi

Fakt, że Twoja aplikacja potrzebuje do uzyskania informacji o po zakończeniu wprowadzania danych przez użytkownika nie nakłada żadnych spadku wydajności lub programowania trudności w swojej aplikacji, ale wymaga, należy rozważać żądaniami funkcji rozpoznawania mowy inaczej niż żądanie danych wejściowych / za pomocą których wiesz już, jak wzorce odpowiedzi. Ponieważ aplikacja nie będzie wiedzieć, gdy użytkownik zatrzymuje wypowiedzi, aplikacja musi nadal do przesyłania strumieniowego audio z usługą podczas równocześnie i asynchronicznie oczekuje na odpowiedź z usługi. Ten wzorzec jest w przeciwieństwie do innych żądań/odpowiedzi protokołów sieci web, takich jak HTTP. W tych protokołów należy wykonać żądanie przed odbierał odpowiedzi; za pomocą protokołu Microsoft Speech Service możesz otrzymywać odpowiedzi *podczas są nadal strumieniowe przesyłanie audio dla żądania*.

> [!NOTE]
> Ta funkcja nie jest obsługiwana w przypadku korzystania z interfejsu API REST protokołu HTTP mowy.

### <a name="turns"></a>Włącza

Rozpoznawanie mowy jest operatora informacji. Powiesz próbujesz przekazać informacje, który jest przesyłany do osoby, która nasłuchuje tych informacji. Podczas przekazywania informacji, zazwyczaj zajmują włącza wypowiedzi i nasłuchuje. Podobnie aplikacji z obsługą mowy współdziała z użytkowników na przemian nasłuchiwania i odpowiada, mimo że aplikacja zwykle wykonuje większość nasłuchiwania. Prowadzone danych wejściowych użytkownika i odpowiedzi usługi do tych danych wejściowych nosi nazwę *Włącz*. A *Włącz* rozpoczyna się, gdy użytkownik mówi i kończy się, gdy aplikacja została zakończona Obsługa odpowiedź usługi mowy.

### <a name="telemetry"></a>Telemetria

Tworzenie obsługujących mowę urządzenie lub aplikacja może stanowić wyzwanie, nawet w przypadku doświadczonych deweloperów. Odpowiednich protokołów opartych na Stream wydawać się często, żmudnym na pierwszy rzut oka oraz ważne szczegóły, takie jak wykrywanie wyciszenia może być całkowicie nowe. Za pomocą tak wiele komunikatów o konieczności pomyślnie wysłane i odebrane do wykonania parę pojedynczego żądania/odpowiedzi, jest *bardzo* ważne, aby zbierać kompletne i dokładne dane dotyczące tych komunikatów. Protokół usługi mowy firmy Microsoft zapewnia zbierania tych danych. Należy wszelkich starań, aby podać wymagane dane, jak to możliwe; podając kompletne i dokładne dane, możesz będzie można ułatwienia sobie — nigdy nie potrzebujesz pomocy z zespołu usługi mowy firmy Microsoft w rozwiązywaniu problemów z implementacji klienta, jakość zostały zebrane dane telemetryczne będą krytyczne dla problemu Analiza.

> [!NOTE]
> Ta funkcja nie jest obsługiwana w przypadku korzystania z interfejsu REST API rozpoznawania mowy.

### <a name="speech-application-states"></a>Stany aplikacji funkcji rozpoznawania mowy

Kroki, które należy wykonać, aby umożliwić wprowadzanie mowy do aplikacji są nieco inne niż kroki inne formy wprowadzania takich jak kliknięcia myszy lub naciśnięcia finger. Użytkownik musi zachować informacje o gdy aplikacja jest nasłuchiwanie mikrofon i wysyłanie danych do usługi rozpoznawania mowy, gdy trwa oczekiwanie na odpowiedź z usługi, i gdy jest on w stanie bezczynności. Na poniższym diagramie przedstawiono relacji między tymi stanami.

![Diagram stanu aplikacji funkcji rozpoznawania mowy](Images/speech-application-state-diagram.png)

Od Microsoft usługi mowy uczestniczy w niektóre stany, protokół usługi definiuje wiadomości, które pomagają aplikacji przejście między stanami. Wymaganych przez aplikację do interpretacji i działają w tych komunikatach protokołów, śledzenie i zarządzanie nimi Stany aplikacji mowy.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Przy użyciu usługi rozpoznawania mowy w aplikacjach

Usługa rozpoznawania mowy Microsoft udostępnia dwa sposoby deweloperzy mogą dodać mowy do aplikacji.

- [Interfejsy API REST](GetStarted/GetStartedREST.md): Deweloperzy mogą używać połączeń HTTP z ich aplikacji do usługi rozpoznawania mowy.
- [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md): Dla zaawansowanych funkcji deweloperzy mogą pobrać bibliotek klienta Microsoft Speech i link do ich aplikacji.  Biblioteki klienckie są dostępne na różnych platformach (Windows, Android, iOS) przy użyciu różnych języków, (C#, Java, JavaScript, ObjectiveC).

| Przypadki zastosowań | [Interfejsy API REST](GetStarted/GetStartedREST.md) | [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konwertuj krótko wypowiedzi audio, na przykład poleceń (audio długość < 15 s) bez wyników pośrednich | Tak | Tak |
| Konwertuj długie audio (> 15 s) | Nie | Tak |
| Dźwięk Stream z wyników pośrednich żądanego | Nie | Tak |
| Zrozumienie tekstu skonwertowaniu dźwięku za pomocą usługi LUIS | Nie | Tak |

 Jeśli potrzebnego języka lub platformy nie ma jeszcze zestawu SDK, możesz utworzyć Twojej własnej implementacji na podstawie [dokumentacji protokołu](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Tryby rozpoznawania

Istnieją trzy tryby rozpoznawania: `interactive`, `conversation`, i `dictation`. Tryb rozpoznawania dostosowuje rozpoznawania mowy, w oparciu o jak użytkownicy będą mówić. Wybierz tryb rozpoznawania odpowiednie dla twojej aplikacji.

> [!NOTE]
> Tryby rozpoznawania może mieć różne zachowania za pomocą protokołu REST, niż na ich za pomocą protokołu WebSocket. Na przykład interfejsu API REST nie obsługuje ciągłego rozpoznawanie, nawet w trybie konwersacji lub dyktowanie.
> [!NOTE]
> Te tryby są stosowane, gdy bezpośrednio korzystać z protokołu REST lub protokołu WebSocket. [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md) użyć innych parametrów, aby określić tryb rozpoznawania. Aby uzyskać więcej informacji zobacz Biblioteka klienta wybranych przez użytkownika.

Usługa rozpoznawania mowy zwraca tylko jeden wynik frazy rozpoznawania we wszystkich trybach rozpoznawania. Ma limitu 15 sekund dla dowolnego pojedynczego wypowiedź.

### <a name="interactive-mode"></a>Tryb interaktywny

W `interactive` trybie użytkownik zgłasza krótka żądania i oczekuje, że aplikacja do wykonania akcji w odpowiedzi.

Następujące właściwości są typowe dla aplikacji w trybie interakcyjnym:

- Użytkownicy o tym, że rozmowy maszynę, a nie inny człowieka.
- Użytkownicy aplikacji znać wcześniej, mają być Powiedz, oparte na to, czego oczekują aplikacji w celu.
- Wypowiedzi zazwyczaj ostatnie o 2-3 sekundy.

### <a name="conversation-mode"></a>Tryb konwersacji

W `conversation` tryb, użytkownicy są zaangażowane w konwersacji międzyludzkich —.

Następujące właściwości są typowe dla aplikacji w trybie konwersacji:

- Użytkownicy wiedzą, że komunikują się z inną osobą.
- Rozpoznawanie mowy podnosi uczestnicy jeden lub oba Zobacz tekstu mówionego rozmowy przez ludzi.
- Użytkownicy nie zawsze jest planowana mają powiedzieć.
- Użytkownicy często korzystają z żargonu i innych nieformalne mowy.

### <a name="dictation-mode"></a>Tryb dyktowania

W `dictation` tryb, użytkownicy recytować wypowiedzi dłuższe do aplikacji w celu dalszego przetwarzania.

Następujące właściwości są typowe dla aplikacji w trybie dyktowania:

- Użytkownicy wiedzą, że komunikują się z maszyną.
- Użytkownicy są wyświetlane wyniki tekstu rozpoznawania mowy.
- Użytkownicy planu często mają być powiedzieć i używać bardziej formalnych języka.
- Użytkownicy stosują pełną zdania, ostatnich 5 – 8 sekund.

> [!NOTE]
> W trybie dyktowania i konwersacji Microsoft Speech Service nie zwracać wyniki częściowe. Zamiast tego usługa zwraca wyniki stabilne frazy po wyciszenia granic w strumienia audio. Microsoft może zwiększyć protokołu rozpoznawania mowy do udoskonalenia środowiska użytkownika w tych trybach ciągłe rozpoznawania.

## <a name="recognition-languages"></a>Języki rozpoznawania

*Rozpoznawania języka* określa język, który świadczy użytkowników aplikacji. Określ *rozpoznawania języka* z *języka* parametr zapytania adresu URL dla połączenia. Wartość *języka* zapytania parametr używa tagu języka IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag), i **musi** być jednym z języków, które są obsługiwane przez interfejs API rozpoznawania mowy. Pełną listę języków obsługiwanych przez usługę rozpoznawania mowy, można znaleźć na stronie [językach obsługiwanych](API-Reference-REST/supportedlanguages.md).

Usługa rozpoznawania mowy firmy Microsoft odrzuca żądania nieprawidłowe połączenie, wyświetlając `HTTP 400 Bad Request` odpowiedzi. Nieprawidłowe żądanie jest jednym który:

- Nie obejmuje *języka* wartość parametru zapytania.
- Obejmuje *języka* zapytania parametr, który jest niepoprawnie sformatowana.
- Obejmuje *języka* parametr, który nie jest jednym z języków Obsługa zapytania.

Można utworzyć aplikację, która obsługuje co najmniej wszystkie języki, które są obsługiwane przez usługę.

### <a name="example"></a>Przykład

W poniższym przykładzie aplikacja używa *konwersacji* trybu rozpoznawania mowy do osoby mówiącej angielskie US.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Tekst odpowiedzi

Odpowiedzi transkrypcji Zwróć tekst skonwertowany audio do klientów. Tekst odpowiedzi zawiera następujące pola:

- `RecognitionStatus` Określa stan rozpoznawanie. Możliwe wartości są podane w poniższej tabeli.

| Stan | Opis |
| ------------- | ---------------- |
| Powodzenie | Rozpoznawanie zakończyło się pomyślnie i pola Wyświetlany_tekst jest obecny |
| NoMatch | Wykryto mowy strumienia audio, ale bez wyrazów z języka docelowego zostały dopasowane. Zobacz temat [NoMatch rozpoznawania Status(#nomatch-recognition-status) Aby uzyskać więcej informacji  |
| InitialSilenceTimeout | Początek strumienia audio zawiera tylko wyciszenia, a usługa upłynął limit czasu oczekiwania podczas rozpoznawania mowy |
| BabbleTimeout | Początek strumienia audio zawiera tylko hałasu i usługi, upłynął limit czasu oczekiwania podczas rozpoznawania mowy |
| Błąd | Usługa rozpoznawania napotkał błąd wewnętrzny i nie może kontynuować działania |

- `DisplayText` reprezentuje rozpoznaną frazę po zastosowano wielkość liter, znaki interpunkcyjne i normalizacji odwrotność — tekst i ma została zamaskowana wulgaryzmów gwiazdkami. Występuje pole Wyświetlany_tekst *tylko* Jeśli `RecognitionStatus` pole ma wartość `Success`.

- `Offset` Określa przesunięcie (w jednostkach 100-nanosekundowych), jaką frazę został rozpoznany względem początku strumienia audio.

- `Duration`Określa czas trwania (w jednostkach 100-nanosekundowych) Ta fraza mowy.

Tekst odpowiedzi zwraca więcej informacji, w razie potrzeby. Zobacz [format danych wyjściowych](#output-format) dla sposób zwracania bardziej szczegółowe dane wyjściowe.

Usługa rozpoznawania mowy firmy Microsoft obsługuje proces dodatkowy tekst, który obejmuje dodawanie wielkich liter i znaków interpunkcyjnych, maskowanie wulgaryzmów i normalizowanie tekst do wspólnego formularze. Na przykład jeśli użytkownik mówi reprezentowane przez wyrażenie słowa "Przypomnij mi kupić iPhone 6", usług przetwarzania mowy firmy Microsoft będzie zwracać uzyskanego tekstu "Przypomnij mi kupić iPhone 6". Nosi nazwę procesu, który konwertuje wyraz, "6" numer "6" *odwrotność normalizacji tekstu* (*podczas* w skrócie).

### <a name="nomatch-recognition-status"></a>Stan rozpoznawania NoMatch

Zwraca odpowiedź transkrypcji `NoMatch` w `RecognitionStatus` podczas rozpoznawania mowy usługi Microsoft wykrywa mowy w strumienia audio, ale nie może odpowiadający tej zamiana mowy na gramatyki języka używany dla żądania. Na przykład *NoMatch* sytuacja może wystąpić, jeśli użytkownik jest coś w języku niemieckim, gdy aparat rozpoznawania oczekuje US English jako język mówiony. Wzorzec fali wypowiedź może wskazywać na obecność ludzkiej mowy, ale żaden z wyrazów, używany będzie odpowiadać leksykonu angielskie US używany przez aparat rozpoznawania.

Inny *NoMatch* stan występuje, gdy Algorytm rozpoznawania nie będzie mógł znalezione dokładne dopasowanie dźwięków zawarte w strumienia audio. Gdy ten stan występuje, może generować mowy usługi Microsoft *speech.hypothesis* komunikaty zawierające *średnich hipotetycznych. tekst* , ale będzie *speech.phrase*wiadomości, w którym *RecognitionStatus* jest *NoMatch*. Ten warunek występuje, normal; nie należy czynić żadnych założeń o dokładności lub wierności tekstu w *speech.hypothesis* wiadomości. Ponadto, użytkownik musi nie przyjęto założenie, że ponieważ Microsoft Speech Service daje *speech.hypothesis* wiadomości, które usługa jest możliwość generowania *speech.phrase* komunikatu o  *RecognitionStatus* *Powodzenie*.

## <a name="output-format"></a>Format danych wyjściowych

Usługa Microsoft Speech Service może zwracać różne formaty ładunków w odpowiedziach transkrypcji. Wszystkich ładunków są strukturami JSON.

Można kontrolować format wyniku frazy, określając `format` parametr zapytania adresu URL. Domyślnie usługa zwraca `simple` wyników.

| Format | Opis |
|-----|-----|
| `simple` | Wynik uproszczony frazy, zawierający stan rozpoznawanie i rozpoznany tekst w formularzu wyświetlania. |
| `detailed` | Status ocen i listy N najlepszych wyników frazy, gdzie każdy wynik wyrażenie zawiera wszystkie cztery formularze rozpoznawanie i współczynnik ufności. |

`detailed` Format zawiera [wartości N najlepszych](#n-best-values), oprócz `RecognitionStatus`, `Offset`, i `duration`, w odpowiedzi.

### <a name="n-best-values"></a>Wartości N najlepszych

Odbiorniki, czy maszynowo, lub nigdy nie może być niektórych one słyszeli *dokładnie* co był używany. Odbiornik można przypisać *prawdopodobieństwo* tylko do określonego interpretacji wypowiedź.

W normalnych warunkach, rozmawiając z innymi osobami, z którymi są często wchodzić w interakcje osoby mają wysokie prawdopodobieństwo rozpoznawać wyrazy, które zostały używany. Odbiorniki maszynowej mowy wszelkich starań, aby osiągnąć podobny poziomów dokładności i w warunkach prawo [osiągną zgodność z ludzi](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Algorytmy, które są używane w funkcji rozpoznawania mowy zapoznaj się z alternatywnych interpretacji wypowiedź jako część normalnego przetwarzania. Zazwyczaj te możliwości są odrzucane, dowód na rzecz pojedynczego interpretacji staje się trudne. Nieoptymalnych warunków jednak aparatu rozpoznawania mowy kończy listę alternatywnych możliwości interpretacji. U góry *N* alternatywy na tej liście są nazywane *listy N najlepszych*. Przypisano każdego rozwiązania alternatywnego [współczynnik ufności](#confidence). Zaufanie wyniki z zakresu od 0 do 1. Wynik 1 oznacza najwyższy poziom zaufania. Wynik 0 reprezentuje najniższy poziom zaufania.

> [!NOTE]
> Liczba wpisów na liście N najlepszych różnią się między wieloma wypowiedzi. Liczba wpisów mogą się różnić między wieloma rozpoznawań z *tego samego* wypowiedź. Taka zmiana jest naturalną i oczekiwany wynik Probabilistyczne charakter Algorytm rozpoznawania mowy.

Każdy wpis zwrócony na liście N najlepszych zawiera

- `Confidence`, który reprezentuje [oceny zaufania](#confidence) tego wpisu.
- `Lexical`, która jest [leksykalne formularza](#lexical-form) rozpoznanego tekstu.
- `ITN`, która jest [formularza podczas](#itn-form) rozpoznanego tekstu.
- `MaskedITN`, która jest [maskowane podczas formularza](#masked-itn-form) rozpoznanego tekstu.
- `Display`, która jest [formularz wyświetlania](#display-form) rozpoznanego tekstu.

### Wyniki ufności <a id="confidence"></a>

Wyniki zaufania są integralną częścią systemów rozpoznawania mowy. Usługa Microsoft Speech uzyskuje oceny zaufania z *klasyfikatora ufności*. Microsoft przygotowuje klasyfikatora zaufania za pośrednictwem zestaw funkcji, które są przeznaczone do maksymalnie rozróżnienia między rozpoznawania poprawne i niepoprawne. Wyniki zaufania są oceniane pod kątem poszczególnych wyrazów, a cały wypowiedzi.

Jeśli zdecydujesz się używać oceny zaufania, które są zwracane przez usługę, należy pamiętać o następujących zasadach:

- Wyniki zaufania można porównać tylko w obrębie tego samego trybu rozpoznawanie i język. Nie Porównuj wyniki między różnych języków lub rozpoznawania różne tryby. Na przykład współczynnik ufności, w trybie rozpoznawania interaktywne ma *nie* korelacji współczynnik ufności, w trybie dyktowania.
- Wyniki ufności najlepiej sprawdzają się na ograniczonym zestawie wypowiedzi. Naturalnie jest doskonałym stopień zmienności wyniki dla szerokiej gamy wypowiedzi.

Jeśli zdecydujesz się używać wartość ocena ufności jako *próg* , na którym działa aplikacja, nawiązywania przy użyciu funkcji rozpoznawania mowy wartości progowe.

- Wykonać rozpoznawanie mowy w została przeanalizowana reprezentatywna próbka wypowiedzi dla swojej aplikacji.
- Zbieraj wyniki zaufania dla każdej funkcji rozpoznawania Przykładowy zestaw.
- Podstawowa wartość progu na niektórych percentyla obaw dla tego przykładu.

Nie wartości progowej pojedynczego jest odpowiednia dla wszystkich aplikacji. Współczynnik ufności dopuszczalne, w jednej aplikacji może być niedopuszczalne w innej aplikacji.

### <a name="lexical-form"></a>Formularz leksykalne

Leksykalne formularz jest rozpoznany tekst, dokładnie tak jak wystąpił w wypowiedź i bez znaków interpunkcyjnych i wielkość liter. Na przykład leksykalne postać adresu "1020 Enterprise sposób" będzie *dziesięć dwudziestu enterprise sposób*, przy założeniu, że został on używany w ten sposób. Leksykalne formularza zdania "Przypomnij mi kupić 5 ołówki" jest *Przypomnij mi kupić pięć ołówki*.

Formularz leksykalne jest najbardziej odpowiednie dla aplikacji, które trzeba wykonać normalizacji niestandardowego tekstu. Formularz leksykalne jest również odpowiedni dla aplikacji wymagających nieprzetworzonych rozpoznawania słów.

Wulgaryzmów nigdy nie jest zamaskowana w formularzu leksykalne.

### <a name="itn-form"></a>Podczas formularza

Normalizacja tekstu to proces konwertowania tekstu z jednego formularza do innej formy "canonical". Na przykład, numer telefonu "555-1212" mogą być konwertowane na forma kanoniczna *pięć pięć pięć co dwa jeden dwa*. *Odwrotny* normalizacji tekstu (podczas) odwraca ten proces, konwertowania słów "pięć pięć pięć jeden dwa jeden dwa" do odwróconą forma kanoniczna *555-1212*. Formularz podczas rozpoznawania wyników nie ma wielkość liter i znaków przestankowych.

Formularz podczas jest najbardziej odpowiednia dla aplikacji, które działają w rozpoznany tekst. Na przykład aplikację, która umożliwia użytkownikowi mówić terminy wyszukiwania, a następnie używa niniejsze postanowienia w zapytaniu sieci web będzie formularz podczas. Wulgaryzmów nigdy nie jest zamaskowana w formularzu podczas. Aby zamaskować wulgaryzmów, użyj *formularza maskowane podczas*.

### <a name="masked-itn-form"></a>Maskowane podczas formularza

Ponieważ wulgaryzmów naturalnie części mowy, Microsoft usługi mowy rozpoznaje tych słów i fraz, kiedy będą wyróżniane. Wulgaryzmów nie może jednak być odpowiednia dla wszystkich aplikacji, szczególnie tych aplikacji z odbiorcami ograniczeniami użytkownika — zawartość dla dorosłych.

Zamaskowany formularza podczas dotyczy wulgaryzmów maskowania do formularza normalizacji odwrotność tekstu. Aby zamaskować wulgaryzmów, ustaw wartość wulgaryzmów wartość parametru do `masked`. Maskę wulgaryzmów wyrazy, które są rozpoznawane jako część leksykonu wulgarnego języka są zastępowane gwiazdkami. Na przykład: *Przypomnij mi kupić 5 *** ołówki*. Maskowane podczas formie wynik rozpoznawania nie ma wielkość liter i znaków przestankowych.

> [!NOTE]
> Jeśli ustawiono wartość parametru zapytania wulgaryzmów `raw`, zamaskowanych formularza podczas jest taka sama jak formularz podczas. Jest wulgaryzmów *nie* maskowane.

### <a name="display-form"></a>Formularz wyświetlania

Znaki interpunkcyjne i wielkości liter sygnał miejsce umieszczenia wyróżnienie, gdzie w celu wstrzymania i tak dalej, co sprawia, że tekst jest łatwiejsze do zrozumienia. Formularz wyświetlania dodaje znaki interpunkcyjne i wielkości liter do rozpoznawania wyników, dzięki czemu najbardziej odpowiednią formą dla aplikacji, które wyświetlania tekstu mówionego.

Ponieważ formularz wyświetlania rozszerza maskowanego formularz podczas, można ustawić wartości parametru wulgaryzmów `masked` lub `raw`. Jeśli wartość jest równa `raw`, wyniki rozpoznawania zawierają jakiekolwiek przekleństwa, używany przez użytkownika. Jeśli wartość jest równa `masked`, słowa rozpoznany jako część leksykonu wulgarnego języka są zastępowane gwiazdkami.

### <a name="sample-responses"></a>Przykład odpowiedzi

Wszystkich ładunków są strukturami JSON.

Format ładunku `simple` frazę wynik:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Format ładunku `detailed` frazę wynik:

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

## <a name="profanity-handling-in-speech-recognition"></a>Obsługa wulgaryzmów, rozpoznawanie mowy

Usługa Microsoft Speech rozpoznaje wszystkie formularze ludzkiej mowy, w tym słów i fraz, które wiele osób może sklasyfikować jako "wulgaryzmów." Można kontrolować, jak usługa obsługuje wulgaryzmów przy użyciu *wulgaryzmów* parametr zapytania. Domyślnie usługa maskuje wulgaryzmów w *speech.phrase* wyników i nie może zwracać *speech.hypothesis* komunikaty zawierające wulgaryzmów.

| *Wulgaryzmów* wartość | Opis |
| - | - |
| `masked` | Maski wulgaryzmów gwiazdkami. To zachowanie jest ustawieniem domyślnym. |
| `removed` | Usuwa wulgaryzmów z wszystkie wyniki. |
| `raw` | Rozpoznaje i zwraca wulgaryzmów w wszystkie wyniki. |

### <a name="profanity-value-masked"></a>Wartość niestosownych wyrażeń `Masked`

Aby zamaskować wulgaryzmów, ustaw *wulgaryzmów* parametr na wartość zapytania *maskowane*. Gdy *wulgaryzmów* parametr zapytania ma tę wartość lub nie jest określona dla żądania usługi *maski* wulgaryzmów. Usługa wykonuje maskowanie, zastępując wulgaryzmów w wynikach rozpoznawania gwiazdkami. Po określeniu obsługi maskowania wulgaryzmów, usługa nie może zwracać *speech.hypothesis* komunikaty zawierające wulgaryzmów.

### <a name="profanity-value-removed"></a>Wartość niestosownych wyrażeń `Removed`

Gdy *wulgaryzmów* zapytania parametr ma wartość *usunięte*, usługa usuwa niestosownych wyrażeń z obu *speech.phrase* i *speech.hypothesis* wiadomości. Wyniki są takie same *tak, jakby nie zostały wymawiane wyrazy wulgaryzmów*.

#### <a name="profanity-only-utterances"></a>Tylko do wulgaryzmów wypowiedzi

Użytkownik może mówić *tylko* wulgaryzmów, gdy aplikacja została skonfigurowana usługi do usunięcia wulgaryzmów. W tym scenariuszu, jeśli jest w trybie rozpoznawania *dyktowanie* lub *konwersacji*, usługa nie zwróciła *speech.result*. Jeśli jest w trybie rozpoznawania *interaktywne*, usługa zwraca *speech.result* z kodem stanu *NoMatch*.

### <a name="profanity-value-raw"></a>Wartość niestosownych wyrażeń `Raw`

Gdy *wulgaryzmów* zapytania parametr ma wartość *pierwotne*, usługi nie usunąć ani nie maski wulgaryzmów w jednym *speech.phrase* lub  *Speech.hypothesis* wiadomości.
