---
title: Pojęcia dotyczące | Dokumentacja firmy Microsoft
description: Podstawowe pojęcia używane w usłudze Microsoft mowy.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: bc23f4fb7dfc045a0f8cc87155c31875c4de8450
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347489"
---
# <a name="basic-concepts"></a>Podstawowe pojęcia

Na tej stronie opisano niektóre podstawowe pojęcia związane z usługą rozpoznawania mowy firmy Microsoft. Firma Microsoft zaleca odczytać tę stronę przed użyciem rozpoznawanie mowy Microsoft interfejsu API w aplikacji.

## <a name="understanding-speech-recognition"></a>Opis rozpoznawania mowy

Jeśli tworzysz aplikację z obsługą mowy po raz pierwszy lub jest dodawany mowę do istniejącej aplikacji po raz pierwszy, ta sekcja ułatwia rozpoczęcie pracy. Jeśli masz już pewne doświadczenie w zakresie aplikacji z obsługą na mowę, można wybrać tylko wyszukanie w tej sekcji lub może go pominąć, jeśli są dostępne stary na mowę i chcesz pobrać prawo, aby szczegóły protokołu.

### <a name="audio-streams"></a>Strumieni audio

Przede wszystkim między podstawowych pojęć dotyczących mowy *strumieniem audio*. W odróżnieniu od naciśnięcia klawisza, który występuje w jednym punkcie w czasie i zawiera pojedynczy informacje, rozmowy żądania jest rozłożona na setki milisekund i zawiera wiele kilobajtów informacji. Czas trwania rozmowy zniesławiających przedstawia trudności deweloperzy wyszukiwania, aby zapewnić środowisko prostsze i elegancki mowy ich stosowania. Komputery i algorytmy współczesnych wykonywania przekształcania mowy w około połowy okresu utterance, utterance 2 sekundy można przetłumaczone w około 1 sekundę, ale dowolnej aplikacji, które napotyka 1-sekundowe opóźnienie w przetwarzania użytkownika jest prostsze ani elegancki.

Na szczęście istnieją sposoby "Ukrywanie" czas zapisu, wykonując zapisu w jednej części utterance, gdy użytkownik jest czytanie innej części. Na przykład dzieląc utterance 1 sekundę na fragmenty 10 milisekund 100 i wykonując przekształcania dla każdego fragmentu z kolei ponad 450 całkowita 500 milisekund wymagane do zapisu może być "ukryte", dzięki czemu użytkownik nie zna przekształcania jest wykonywana, gdy jest on mówiąc. Podczas planowania w tym przykładzie, pamiętaj o tym, czy usługa działa zapisu na poprzednie 100 milisekund dźwięku, podczas, gdy użytkownik jest czytanie dalej 100, dlatego gdy użytkownik przestanie rzecz biorąc, usługi będą musieli wykonać transkrypcji około 100 milisekund audio w celu utworzenia wyniku.

Do osiągnięcia tej czynności użytkownika, rozmowy audio informacje są zbierane w fragmentów i przetłumaczone jako użytkownik mówi. Tych fragmentów audio, łącznie z *strumieniem audio*, i proces przesyłania tych fragmentów audio do usługi jest nazywany *przesyłania strumieniowego audio.* Przesyłanie strumieniowe audio jest ważnym elementem dowolnej aplikacji z obsługą mowy; dostrajanie rozmiar fragmentu i optymalizacji transmisji strumieniowej implementacji się najbardziej istotna sposobów poprawy środowisko użytkownika aplikacji.

### <a name="microphones"></a>Mikrofonów

Osoby przetworzyć audio rozmowy za pomocą ich uszy, ale inanimate sprzętu używają mikrofonów. Aby włączyć mowy w dowolnej aplikacji, należy zintegrować z dostarczanie aplikacji strumieniem audio mikrofonu.

Interfejsy API do mikrofonu muszą zezwalać na uruchamianie i zatrzymywanie odbierania audio bajtów z mikrofon. Należy zdecydować, co akcje użytkownika spowoduje mikrofon rozpocząć nasłuchiwania rozpoznawania mowy. Możesz wyzwolić początku nasłuchiwania naciśnięcie klawisza lub mogą wybrać opcję *słowa kluczowego* lub *wake word* spotter zawsze nasłuchiwania mikrofon i używania danych wyjściowych tego modułu do wyzwalacz wysyłania audio do usługi Microsoft mowy.

### <a name="end-of-speech"></a>Koniec mowy

Wykrywanie *podczas* ma prelegenta *zatrzymana* mówiąc prawdopodobnie prosta dla ludzi, ale raczej trudnych problemów poza warunkach laboratoryjnych. Nie jest wystarczająco, aby po prostu wyszukaj czysty wyciszenia po utterance, ponieważ jest często dużo otoczenia hałas skomplikować rzeczy. Usługa rozpoznawania mowy Microsoft ma znakomity zadania szybko wykrycia, gdy użytkownik zatrzymał rzecz biorąc, usługi mogą informować o tym fakcie aplikacji, ale to rozmieszczenie oznacza, że aplikacja jest ostatnie wiedzieć, kiedy użytkownik Zatrzymaj czytanie. To nie jest w ogóle podobnie jak inne formy wprowadzania gdy aplikacja jest *pierwszy* wiedzieć, kiedy użytkownik wejściowych uruchamia *i* kończy się.

### <a name="asynchronous-service-responses"></a>Usługa asynchroniczna odpowiedzi

Fakt, że aplikacja musi mieć świadomość, po zakończeniu wprowadzania danych przez użytkownika nie nakładają żadnych spadku wydajności i programowania trudności w swojej aplikacji, ale wymaga zastanowić żądań mowy inaczej niż żądanie wejściowe / wzorce odpowiedzi, z którymi są Ci jeszcze znane. Ponieważ aplikacja dowiedzieć się, gdy użytkownik przestanie mówiąc, aplikacja musi nadal do przesyłania strumieniowego audio do usługi podczas równocześnie i asynchronicznie oczekiwania na odpowiedź z usługi. Ten wzorzec jest w odróżnieniu od innych protokołów sieci web żądania/odpowiedzi, takich jak HTTP. Te protokoły żądanie należy wykonać przed otrzymaniem odpowiedzi; w protokole mowy usługa otrzymaniu odpowiedzi *podczas są nadal przesyłania strumieniowego audio dla żądania*.

> [!NOTE]
> Ta funkcja nie jest obsługiwana przy użyciu interfejsu API REST protokołu HTTP mowy.

### <a name="turns"></a>Włącza

Rozpoznawanie mowy jest operator informacji. Gdy mówi, chcesz przekazać informacje, który znajduje się w posiadaniu użytkownika do osoby, która oczekuje na informacje. Podczas przekazywania informacji, zazwyczaj należy podjąć włącza mówiąc, nasłuchiwania. Podobnie aplikacja z obsługą mowy współdziała z użytkowników przemiennie nasłuchiwania i odpowiada, mimo że aplikacja jest zazwyczaj większość nasłuchiwania. Rozmowy danych wejściowych użytkownika i odpowiedzi usługi do tych danych wejściowych jest nazywany *włączyć*. A *włączyć* rozpoczyna się, gdy użytkownik mówi i kończy się po zakończeniu obsługi mowy odpowiedzi usługi aplikacji.

### <a name="telemetry"></a>Telemetria

Tworzenie mowy urządzenie lub aplikacja może być wyzwaniem, nawet w przypadku doświadczonych programistów. Na podstawie strumienia protokołów prawdopodobnie często na pierwszy rzut oka lada i ważne informacje, takie jak wyciszenia wykrywania może być całkowicie nowe. Z wiadomości tyle konieczności pomyślnie wysłane i odebrane przeprowadzenie parę pojedynczego żądania/odpowiedzi, jest *bardzo* należy zebrać kompletne i dokładne dane dotyczące tych wiadomości. Protokół usługi mowy firmy Microsoft zapewnia zbierania tych danych. Należy wszelkich starań, aby podać wymagane dane, jak to możliwe; podając kompletne i dokładne dane można będzie można ułatwienia sobie — kiedykolwiek potrzebujesz pomocy od zespołu Microsoft mowy usługi przy rozwiązywaniu problemów z implementacji klienta, jakość dane telemetryczne po zebraniu będą krytyczne dla problemu Analiza.

> [!NOTE]
> Ta funkcja nie jest obsługiwana, gdy rozpoznawanie mowy interfejsu API REST.

### <a name="speech-application-states"></a>Stany aplikacji mowy

Czynności, które należy wykonać w celu włączenia wejście mowy w aplikacji są nieco inne niż kroki inne formy wprowadzania, takich jak kliknięcie myszą lub naciska palca. Użytkownik musi zachować informacje o gdy aplikacja jest nasłuchiwanie mikrofon i wysyłanie danych do usługi mowy podczas oczekiwania na odpowiedź z usługi i gdy jest on w stanie bezczynności. Relacja między te stany przedstawiono na poniższym diagramie.

![Diagram stanu aplikacji mowy](Images/speech-application-state-diagram.png)

Ponieważ usługa mowy uczestniczy w niektórych stanów, protokół usługi definiuje wiadomości, które pomagają Twojej aplikacji przejścia między stanami. Aplikacja musi interpretacji i działają na tych wiadomości protokołu śledzenie i zarządzanie nimi Stany aplikacji mowy.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Korzystanie z usługi rozpoznawania mowy z aplikacji

Usługa rozpoznawania mowy Microsoft udostępnia dwa sposoby dla deweloperów dodać swoje aplikacje mowy.

- [Interfejsów API REST](GetStarted/GetStartedREST.md): deweloperzy mogą używać połączenia HTTP z ich aplikacji z usługą rozpoznawania mowy.
- [Biblioteki klienta](GetStarted/GetStartedClientLibraries.md): dla zaawansowanych funkcji, deweloperzy można pobrać bibliotek klienta Microsoft Speech i link do ich aplikacji.  Biblioteki klienta są dostępne na różnych platformach (z systemem Windows, Android, iOS) przy użyciu różnych języków (C#, Java, JavaScript, ObjectiveC).

| Przypadki zastosowań | [Interfejsy API REST](GetStarted/GetStartedREST.md) | [Biblioteki klienta](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konwertuj krótko rozmowy audio, na przykład poleceń (audio długość < 15 s) bez wyników pośrednich | Yes | Yes |
| Konwertuj audio długa (> 15 s) | Nie | Yes |
| Strumień audio z wyników pośrednich potrzeby | Nie | Yes |
| Zrozumienie tekst przekonwertowany z przy użyciu LUIS audio | Nie | Yes |

 Jeśli go do języka lub platformy nie ma jeszcze zestawu SDK, możesz utworzyć własne wdrożenia na podstawie [protokołu dokumentacji](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Tryby rozpoznawania

Istnieją trzy tryby rozpoznawania: `interactive`, `conversation`, i `dictation`. Tryb rozpoznawania dopasowuje oparte na jak użytkownicy mogą mowy rozpoznawania mowy. Wybierz tryb rozpoznawania odpowiednie dla aplikacji.

> [!NOTE]
> Tryby rozpoznawania może mieć różne zachowania w [REST protokołu](#rest-speech-recognition-api) niż w [protokół WebSocket](#webSocket-speech-recognition-api). Na przykład interfejsu API REST nie obsługuje ciągłego rozpoznawania, nawet w trybie konwersacji lub dyktowania.
> [!NOTE]
> Te tryby są stosowane, gdy bezpośrednio używać protokołu REST lub protokołu WebSocket. [Bibliotek klienckich](GetStarted/GetStartedClientLibraries.md) użyć innych parametrów, aby określić tryb rozpoznawania. Aby uzyskać więcej informacji zobacz Biblioteka klienta wybranych przez użytkownika.

Usługa Microsoft mowy zwraca tylko jeden wynik frazy rozpoznawania dla wszystkich metod rozpoznawania. Ma limitu 15 sekund dla dowolnego pojedynczego utterance.

### <a name="interactive-mode"></a>Tryb interaktywny

W `interactive` trybie użytkownik zgłasza żądania dotyczące krótki i oczekuje aplikacji do wykonywania akcji w odpowiedzi.

Następujące właściwości są typowe dla aplikacji w trybie interakcyjnym:

- Użytkownicy wiedzieli, że rozmowy do komputera, a nie do innego człowieka.
- Użytkownicy aplikacji znać wcześniejsze chcą, oparte na to, czego szukają aplikacji w celu.
- Zniesławiających zwykle ostatni o 2-3 sekundy.

### <a name="conversation-mode"></a>Tryb konwersacji

W `conversation` tryb, użytkownicy są zaangażowane w konwersacji człowieka człowieka.

Następujące właściwości są typowe dla aplikacji w trybie konwersacji:

- Użytkownicy wiedzieli, że są komunikują się do innej osoby.
- Rozpoznawanie mowy podnosi poziom konwersacje ludzi, uczestnicy jeden lub oba rozmowy tekstu.
- Użytkownicy nie zawsze jest planowana mają być powiedzieć.
- Użytkownicy często używane żargon i innych nieformalne mowy.

### <a name="dictation-mode"></a>Tryb dyktowania

W `dictation` tryb, użytkownicy przez dłuższy zniesławiających do aplikacji dla dalszego przetwarzania.

Następujące właściwości są typowe dla aplikacji w trybie dyktowania:

- Użytkownicy wiedzieli, że są komunikują się z maszyną.
- Użytkownicy są wyświetlane wyniki tekstu rozpoznawania mowy.
- Użytkownicy będą często mają być powiedzieć i użyj bardziej formalnych języka.
- Pełna u użytkowników zdania, który ostatnich 5 – 8 sekund.

> [!NOTE]
> W trybach dyktowania i konwersacji usługa mowy nie zwraca wyników częściowych. Zamiast tego usługa zwraca wyniki stabilna frazy po granice wyciszenia w strumieniem audio. Microsoft może zwiększyć ulepszyć środowisko użytkownika w tych trybach ciągłego rozpoznawania protokołu mowy.

## <a name="recognition-languages"></a>Języki rozpoznawania

*Języka rozpoznawania* określa język, który świadczy użytkownika aplikacji. Określ *języka rozpoznawania* z *języka* parametru zapytania URL dla połączenia. Wartość *języka* zapytania parametru taga IETF języka [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag), i **musi** się jeden z języków obsługiwanych przez rozpoznawania mowy interfejsu API. Pełną listę języków obsługiwanych przez usługę mowy można znaleźć na stronie [obsługiwane języki](API-Reference-REST/supportedlanguages.md).

Usługę mowy firmy Microsoft odrzuca żądania nieprawidłowe połączenie za pomocą wyświetlania `HTTP 400 Bad Request` odpowiedzi. Nieprawidłowe żądanie jest jednym który:

- Nie ma *języka* wartość parametru zapytania.
- Obejmuje *języka* zapytania parametr, który jest niepoprawnie sformatowane.
- Obejmuje *języka* parametr, który nie jest jednym z języków pomocy technicznej zapytania.

Możesz utworzyć aplikację, która obsługuje jednego lub wszystkich języków, które są obsługiwane przez usługę.

### <a name="example"></a>Przykład

W poniższym przykładzie aplikacja używa *konwersacji* trybu rozpoznawania mowy dla Stanów Zjednoczonych prelegenta angielskiej wersji językowej.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Przekształcenia odpowiedzi

Odpowiedzi przekształcania zwracają tekst skonwertowany z audio klientom. Przekształcenia odpowiedzi zawiera następujące pola:

- `RecognitionStatus` Określa stan rozpoznawania. Możliwe wartości są podane w poniższej tabeli.

| Stan | Opis |
| ------------- | ---------------- |
| Powodzenie | Uznanie zakończyło się pomyślnie, a znajdują się pola Wyświetlany_tekst |
| NoMatch | Wykryto mowy w strumieniem audio, ale nie oznacza z języka docelowego były zgodne. Zobacz [NoMatch rozpoznawania Status(#nomatch-recognition-status) więcej szczegółów  |
| InitialSilenceTimeout | Początek strumieniem audio zawarte tylko wyciszenia i usługę upłynął limit czasu oczekiwania mowy |
| BabbleTimeout | Początek strumieniem audio zawarte tylko szumu i usługę upłynął limit czasu oczekiwania mowy |
| Błąd | Usługa rozpoznawania napotkał błąd wewnętrzny i nie może kontynuować działania |

- `DisplayText` reprezentuje rozpoznaną frazę po wielkie litery, znaki interpunkcyjne i odwrotność tekst normalizacji zostały zastosowane i niestosownych wyrażeń zostały maskowane gwiazdki. W polu Wyświetlany_tekst jest obecny *tylko* Jeśli `RecognitionStatus` pole ma wartość `Success`.

- `Offset` Określa przesunięcie (w jednostkach 100 nanosekund), jaką frazę został rozpoznany względem początku strumieniem audio.

- `Duration`Określa czas trwania (w jednostkach 100 nanosekund) Ta fraza mowy.

Odpowiedź przekształcania zwraca więcej informacji, w razie potrzeby. Zobacz [format danych wyjściowych](#output-format) dla sposób zwracania bardziej szczegółowe dane wyjściowe.

Microsoft mowy usługi obsługuje dodatkowe przekształcania proces, który polega na dodawaniu do użycia wielkich liter i znaków interpunkcyjnych, maskowania niestosownych wyrażeń i tekst, który często występującą formą normalizacji. Na przykład jeśli użytkownik mówi reprezentowanego przez wyrażenie wyrazy "Przypomnij kupić iPhone 6", usług firmy Microsoft mowy zwróci transcribed tekst "Przypomnij mi kupić iPhone 6". Proces, który konwertuje słowo "sześć" na wartość "6" jest nazywany *odwrotność funkcji normalizacji tekst* (*ITN* skrócie).

### <a name="nomatch-recognition-status"></a>Stan rozpoznawania NoMatch

Zwraca odpowiedź przekształcania `NoMatch` w `RecognitionStatus` po mowy usługi Microsoft wykrywa mowy w strumieniem audio, ale nie odpowiada tym mowy do gramatyki języka używany przez żądanie. Na przykład *NoMatch* sytuacja może wystąpić, jeśli użytkownik mówi w języku niemieckim podczas US English jako język rozmowy oczekuje aparat rozpoznawania. Wzorzec fali utterance wskazuje na obecność mowy ludzi, ale żadna słów, używany będzie nie zgadza się leksykonie angielskie US używany przez aparat rozpoznawania.

Inny *NoMatch* stan występuje, gdy Algorytm rozpoznawania nie może znaleźć dokładne dopasowanie dźwięków zawarte w strumieniem audio. Gdy ten stan występuje, może utworzyć usługi mowy Microsoft *speech.hypothesis* komunikaty, które zawierają *eksperyment tekst* , ale powodują *speech.phrase*komunikatu, w którym *RecognitionStatus* jest *NoMatch*. Ten warunek jest normalnie. Nie można wprowadzać żadnych założenia dotyczące dokładności lub wierności tekstu w *speech.hypothesis* wiadomości. Ponadto użytkownik musi nie zakładać, że ponieważ usługa mowy Microsoft daje *speech.hypothesis* wiadomości, które usługa jest w stanie utworzyć *speech.phrase* komunikatów z  *RecognitionStatus* *Powodzenie*.

## <a name="output-format"></a>Format danych wyjściowych

Usługa Microsoft mowy może przywrócić różnych formatach ładunku zapisu odpowiedzi. Wszystkie ładunki są struktury JSON.

Format wyników frazy można kontrolować, określając `format` parametru zapytania URL. Domyślnie usługa zwraca `simple` wyników.

| Format | Opis |
|-----|-----|
| `simple` | Wynik uproszczony frazy, zawierający stan rozpoznawania i rozpoznany tekst w formularzu wyświetlania. |
| `detailed` | Stan rozpoznawania i najlepiej N lista wyników frazy, gdzie każdy wynik wyrażenie zawiera wszystkie cztery formularze rozpoznawania i wynik zaufania. |

`detailed` Format zawiera [wartości N najlepiej](#n-best-values), oprócz `RecognitionStatus`, `Offset`, i `duration`, w odpowiedzi.

### <a name="n-best-values"></a>N-najlepiej wartości

Detektory, czy człowieka lub maszyny, nigdy nie może być niektórych one heard *dokładnie* co był używany. Odbiornik można przypisać *prawdopodobieństwo* tylko do określonego interpretacja utterance. 

W normalnych warunkach, mówiąc do innych osób, z których często współdziałają osoby mają wysokie prawdopodobieństwo rozpoznawać wyrazy, które zostały używany. Odbiorniki opartych na maszynach mowy dążyć do osiągnięcia podobne poziomów dokładności i warunkach prawo [osiągną parzystości z ludzi](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Algorytmy, które są używane podczas rozpoznawania mowy Poznaj alternatywnych interpretacji utterance w ramach normalnego przetwarzania. Zazwyczaj te możliwości są odrzucane wraz ze wzrostem utrudnione dowód na rzecz pojedynczego interpretacji. W mniej niż optymalnych warunkach jednak rozpoznawania mowy kończy się listę alternatywnej interpretacje możliwe. Górnej *N* alternatyw na tej liście są nazywane *listy najlepszych N*. Przypisano każdego alternatywna [wynik zaufania](#confidence). Zaufanie wyniki z zakresu od 0 do 1. Wynik 1 oznacza najwyższy poziom zaufania. Wynik 0 reprezentuje najniższego poziomu zaufania.

> [!NOTE]
> Liczba wpisów na liście najlepszych N różnią się w wielu zniesławiających. Liczba wpisów może się różnić między wieloma uznania z *tego samego* utterance. Taka zmiana jest fizyczną i oczekiwany wynik prawdopodobieństwa charakter Algorytm rozpoznawania mowy.

Zawiera każdego wpisu zwracane na liście najlepszych N

- `Confidence`, która reprezentuje [wyniki zaufania](#confidence) tego wpisu.
- `Lexical`, która jest [leksykalne formularza](#lexical-form) rozpoznanego tekstu.
- `ITN`, która jest [formularza ITN](#itn-form) rozpoznanego tekstu.
- `MaskedITN`, która jest [maskowane formularza ITN](#masked-itn-form) rozpoznanego tekstu.
- `Display`, która jest [Wyświetl formularz](#display-form) rozpoznanego tekstu.

### Wyniki zaufania <a id="confidence"></a>

Wyniki zaufania są integralną częścią systemów rozpoznawania mowy. Usługa rozpoznawania mowy Microsoft uzyskuje wyniki zaufania z *klasyfikatora zaufania*. Microsoft przygotowuje klasyfikatora zaufania w zestawie funkcje, które są przeznaczone do maksymalnie rozróżniania między poprawne i niepoprawne rozpoznawania. Zaufanie wyniki są oceniane pod kątem poszczególnych wyrazów, a cały zniesławiających.

Jeśli wybierzesz wyniki zaufania, które są zwracane przez usługę, należy pamiętać o następujących zasadach:

- Wyniki zaufania można porównywać tylko w obrębie tego samego trybu rozpoznawania i języka. Nie Porównaj wyniki od innych języków lub rozpoznawania różne tryby. Na przykład wynik zaufania w trybie interakcyjne rozpoznawania ma *nie* korelacji do wyniku zaufania w trybie dyktowania.
- Wyniki zaufania najlepiej są używane w ograniczonym zestawem zniesławiających. Naturalnie jest doskonałym stopień zmienności wyniki dla dużych zestawów zniesławiających.

Jeśli zdecydujesz się użyć wartość zaufania wynik jako *próg* , na którym działa aplikacja, należy użyć rozpoznawania mowy ustanowienie wartości progowe.

- Rozpoznawanie mowy należy wykonać na reprezentatywna próbka zniesławiających aplikacji.
- Zbieraj wyniki zaufania dla każdego rozpoznawania zestawu próbki.
- Podstawowa wartość progu na niektórych percentyl zaufania dla tego przykładu.

Żadna wartość próg jest odpowiednia dla wszystkich aplikacji. Wynik dopuszczalne zaufania dla jednej aplikacji może być niedopuszczalne dla innej aplikacji.

### <a name="lexical-form"></a>Formularz leksykalne

Formularz leksykalne jest rozpoznany, dokładnie tak jak wystąpi w utterance, bez znaków interpunkcyjnych lub wielkość liter. Na przykład, będzie leksykalne formę adres "1020 Enterprise sposób" *dziesięć dwudziestu enterprise sposób*, przy założeniu, że był używany w ten sposób. Leksykalne formularza zdania "Przypomnij kupić ołówki 5" jest *Przypomnij kupić pięć ołówki*.

Formularz leksykalne jest najbardziej odpowiednie dla aplikacji, które należy wykonać normalizacji niestandardowy tekst. Formularz leksykalne jest również odpowiedni dla aplikacji, które muszą nieprzetworzone rozpoznawania słów.

Niestosownych wyrażeń nigdy nie jest zamaskowana w formularzu leksykalne.

### <a name="itn-form"></a>Formularz ITN

Tekst normalizacji jest proces konwersji tekstu z jednego formularza do innej formy "canonical". Na przykład numer telefonu "555-1212" może być przekonwertowany do postaci kanonicznej *pięć pięć pięć jeden dwa co dwie*. *Odwrotny* normalizacji tekst (ITN) odwraca ten proces konwersji wyrazy "pięć pięć pięć jeden dwa jeden dwa" do odwróconego forma kanoniczna *555-1212*. Formularz ITN wyniku rozpoznawania nie ma wielkich liter i znaków interpunkcyjnych.

Formularz ITN jest najbardziej odpowiednie dla aplikacji, które mają wpływ na rozpoznany. Na przykład aplikacja, która umożliwia użytkownikowi mowy terminy wyszukiwania, a następnie używa tych warunków w zapytaniu sieci web użyje ITN formularza. Niestosownych wyrażeń nigdy nie jest zamaskowana w formularzu ITN. Aby zamaskować niestosownych wyrażeń, użyj *formularza maskowanego ITN*.

### <a name="masked-itn-form"></a>Maskowanego ITN formularza

Ponieważ niestosownych wyrażeń naturalnie jest częścią język rozmowy, mowy usługi Microsoft rozpoznaje takich słów i wyrażeń, gdy są one używany. Niestosownych wyrażeń nie może jednak być odpowiednie dla wszystkich aplikacji, zwłaszcza tych aplikacji z odbiorców użytkownika ograniczeń, niż osoba dorosła.

Maskowanego formularza ITN stosuje niestosownych wyrażeń maskowania do forma normalizacji odwrotny tekstu. Aby zamaskować niestosownych wyrażeń, ustaw wartość wartości parametru niestosownych wyrażeń `masked`. Maskę niestosownych wyrażeń słowa, które są rozpoznawane jako część leksykonie niestosownych wyrażeń języka są zastępowane gwiazdki. Na przykład: *Przypomnij kupić 5 *** ołówki*. Maskowanego formularza ITN wyniku rozpoznawania nie ma wielkich liter i znaków interpunkcyjnych.

> [!NOTE]
> Jeśli ustawiono wartość parametru zapytania niestosownych wyrażeń `raw`, maskowanego formularza ITN jest taka sama jak formularz ITN. Niestosownych wyrażeń jest *nie* maskowania.

### <a name="display-form"></a>Wyświetl formularz

Znaki interpunkcyjne i wielkość liter sygnalizuje, gdzie umieścić wyróżnienie, gdzie należy Wstrzymaj i tak dalej, co sprawia, że tekst jest łatwiejsze do zrozumienia. Wyświetl formularz dodaje znaki interpunkcyjne i wielkość liter do rozpoznawania, dzięki czemu formularzu najbardziej odpowiedni dla aplikacji, które wyświetlania rozmowy tekstu.

Ponieważ Wyświetl formularz rozszerza maskowanego formularz ITN, można ustawić wartości parametru niestosownych wyrażeń `masked` lub `raw`. Jeśli wartość jest równa `raw`, wyniki rozpoznawania obejmują wszystkie niestosownych wyrażeń używany przez użytkownika. Jeśli wartość jest równa `masked`, rozpoznany jako część leksykonie niestosownych wyrażeń języka wyrazy z gwiazdką.

### <a name="sample-responses"></a>Przykładowe odpowiedzi

Wszystkie ładunki są struktury JSON.

Format ładunku `simple` zwrot wyników:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Format ładunku `detailed` zwrot wyników:

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

## <a name="profanity-handling-in-speech-recognition"></a>Rozpoznawanie mowy niestosownych wyrażeń — Obsługa

Usługa Microsoft mowy rozpoznaje wszystkich formularzy mowy ludzi, w tym słów i wyrażeń, które wiele osób może rozpoznać jako "niestosownych wyrażeń." Można kontrolować, jak usługa obsługuje niestosownych wyrażeń przy użyciu *niestosownych wyrażeń* parametr zapytania. Domyślnie usługa maski niestosownych wyrażeń w *speech.phrase* wyników i nie może zwracać *speech.hypothesis* komunikaty, które zawierają niestosownych wyrażeń.

| *Niestosownych wyrażeń* wartość | Opis |
| - | - |
| `masked` | Maski niestosownych wyrażeń z gwiazdką. To zachowanie jest ustawieniem domyślnym. | 
| `removed` | Usuwa niestosownych wyrażeń z wszystkie wyniki. |
| `raw` | Rozpoznaje i zwraca niestosownych wyrażeń w wszystkie wyniki. |

### <a name="profanity-value-masked"></a>Wartość niestosownych wyrażeń `Masked`

Aby zamaskować niestosownych wyrażeń, ustaw *niestosownych wyrażeń* parametr na wartość zapytania *maskowane*. Gdy *niestosownych wyrażeń* parametr zapytania ma tę wartość lub nie jest określony dla żądania usługi *maski* niestosownych wyrażeń. Usługa wykonuje maskowania przez zamianę niestosownych wyrażeń w wynikach rozpoznawania gwiazdki. Po określeniu obsługi maskowania niestosownych wyrażeń usługa nie zwróciła *speech.hypothesis* komunikaty, które zawierają niestosownych wyrażeń.

### <a name="profanity-value-removed"></a>Wartość niestosownych wyrażeń `Removed`

Gdy *niestosownych wyrażeń* zapytania parametr ma wartość *usunięte*, usługa usuwa niestosownych wyrażeń z obu *speech.phrase* i *speech.hypothesis* wiadomości. Wyniki są takie same *tak, jakby nie są wymawiane wyrazy niestosownych wyrażeń*.

#### <a name="profanity-only-utterances"></a>Zniesławiających tylko niestosownych wyrażeń

Użytkownik może mowy *tylko* niestosownych wyrażeń, gdy aplikacja została skonfigurowana usługi do usunięcia niestosownych wyrażeń. W tym scenariuszu, jeśli jest to tryb rozpoznawania *dyktowania* lub *konwersacji*, usługa nie zwróciła *speech.result*. Jeśli jest to tryb rozpoznawania *interakcyjne*, zwraca usługi *speech.result* z kodem stanu *NoMatch*. 

### <a name="profanity-value-raw"></a>Wartość niestosownych wyrażeń `Raw`

Gdy *niestosownych wyrażeń* zapytania parametr ma wartość *raw*, usługi nie Usuń lub zamaskować niestosownych wyrażeń w jednym *speech.phrase* lub  *Speech.hypothesis* wiadomości.
