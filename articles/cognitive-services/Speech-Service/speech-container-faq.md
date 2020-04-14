---
title: Kontenery usługi mowy często zadawane pytania (CZĘSTO ZADAWANE PYTANIA)
titleSuffix: Azure Cognitive Services
description: Instalowanie i uruchamianie kontenerów mowy. zamiana mowy na tekst transkrybuje strumienie audio do tekstu w czasie rzeczywistym, które mogą wykorzystywać lub wyświetlać aplikacje, narzędzia lub urządzenia. Zamiana tekstu na mowę konwertuje tekst wejściowy na mowę syntetyzowaną podobną do człowieka.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 4d597b872cf31a823f314d9f3c67c9f45201c542
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258477"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Kontenery usługi mowy często zadawane pytania (CZĘSTO ZADAWANE PYTANIA)

Podczas korzystania z usługi mowy z kontenerami, polegać na tej kolekcji często zadawane pytania przed eskalacją do obsługi. W tym artykule w tym artykule wyzdmywany jest pytania w różnym stopniu, od ogólnych do technicznych. Aby rozwinąć odpowiedź, kliknij pytanie.

## <a name="general-questions"></a>Pytania ogólne

<details>
<summary>
<b>Jak działają kontenery mowy i jak je skonfigurować?</b>
</summary>

**Odpowiedź:** Podczas konfigurowania klastra produkcyjnego, istnieje kilka rzeczy, które należy wziąć pod uwagę. Po pierwsze, konfigurowanie jednego języka, wiele kontenerów, na tym samym komputerze, nie powinno być dużym problemem. Jeśli występują problemy, może to być problem związany ze sprzętem - więc najpierw przyjrzymy się zasobom, czyli; specyfikacji procesora i pamięci.

Zastanów się przez `ja-JP` chwilę, kontener i najnowszy model. Model akustyczny jest najbardziej wymagającym elementem cpu-wise, podczas gdy model językowy wymaga najwięcej pamięci. Kiedy porównaliśmy użycie, potrzeba około 0,6 rdzeni procesora, aby przetworzyć pojedyncze żądanie zamiany mowy na tekst, gdy dźwięk przepływa w czasie rzeczywistym (jak z mikrofonu). Jeśli przesyłasz dźwięk szybciej niż w czasie rzeczywistym (jak z pliku), to użycie może się podwoić (1,2 x rdzeni). Tymczasem pamięć wymieniona poniżej jest pamięć operacyjna do dekodowania mowy. *Nie* bierze pod uwagę rzeczywistego pełnego rozmiaru modelu języka, który będzie rezonowany w pamięci podręcznej plików. Do `ja-JP` tego dodatkowe 2 GB; może `en-US`być więcej (6-7 GB).

Jeśli masz komputer, gdzie pamięć jest ograniczona i próbujesz wdrożyć wiele języków na nim, jest możliwe, że pamięć podręczna plików jest pełna, a system operacyjny jest zmuszony do modelowania i wyjęcia stron. W przypadku transkrypcji uruchomionej może to być katastrofalne i może prowadzić do spowolnień i innych konsekwencji wydajności.

Ponadto wstępnie pakietujemy pliki wykonywalne dla komputerów z [zaawansowanym zestawem instrukcji rozszerzenia wektorowego (AVX2).](speech-container-howto.md#advanced-vector-extension-support) Maszyna z zestawem instrukcji AVX512 będzie wymagać generowania kodu dla tego celu, a uruchomienie 10 kontenerów dla 10 języków może tymczasowo wyczerpywać procesor. Komunikat taki jak ten pojawi się w dziennikach docker:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Na koniec można ustawić liczbę dekoderów, które mają `DECODER MAX_COUNT` wewnątrz *pojedynczego* kontenera przy użyciu zmiennej. Tak, w zasadzie, powinniśmy zacząć od jednostki SKU (CPU / pamięci), i możemy zaproponować, jak uzyskać najlepsze z niego. Doskonałym punktem wyjścia jest odniesienie do zalecanych specyfikacji zasobów komputera hosta.

<br>
</details>

<details>
<summary>
<b>Czy możesz pomóc w planowaniu pojemności i szacowaniu kosztów kontenerów mowy przedwzmowej?</b>
</summary>

**Odpowiedź:** W przypadku pojemności kontenera w trybie przetwarzania wsadowego każdy dekoder może obsługiwać 2-3x w czasie rzeczywistym, z dwoma rdzeniami procesora, dla pojedynczego rozpoznawania. Nie zaleca się utrzymywanie więcej niż dwa równoczesnych rozpoznawania na wystąpienie kontenera, ale zaleca się uruchamianie więcej wystąpień kontenerów ze względu na niezawodność/dostępność, za moduł równoważenia obciążenia.

Chociaż możemy mieć każde wystąpienie kontenera uruchomione z większą liczą dekoderami. Na przykład możemy skonfigurować 7 dekoderów na wystąpienie kontenera na komputerze ośmiordzeniowym (po ponad 2 x każdy), uzyskując 15-krotną przepustowość. Istnieje param `DECODER_MAX_COUNT` być świadomi. W skrajnych przypadkach pojawiają się problemy z niezawodnością i opóźnieniami, a przepustowość znacznie wzrosła. W przypadku mikrofonu będzie to 1x czas rzeczywisty. Ogólne użycie powinno wynosić około jednego rdzenia dla pojedynczego rozpoznawania.

W przypadku scenariusza przetwarzania 1 K godzin na dobę w trybie przetwarzania wsadowego w skrajnym przypadku 3 maszyny wirtualne mogą obsługiwać go w ciągu 24 godzin, ale nie są gwarantowane. Aby obsłużyć dni spike, pracy awaryjnej, aktualizacji i zapewnienia minimalnej kopii zapasowej/BCP, zaleca się 4-5 maszyn zamiast 3 na klaster i z 2+ klastrów.

W przypadku sprzętu używamy standardowej maszyny Wirtualnej platformy Azure `DS13_v2` jako odwołania (każdy rdzeń musi mieć 2,6 GHz lub lepszą, z włączonym zestawem instrukcji AVX2).

| Wystąpienie  | procesory wirtualne | Pamięć RAM    | Pamięć masowa temp | Płatność zgodnie z rzeczywistym daszkiem dzięki AHB | Roczna rezerwa z AHB (% oszczędności) | 3-letnia rezerwa z AHB (% oszczędności) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 Gib | 112 Gib      | $0.598/godz.            | $0.3528/godz./godz.)                 | $0.2333/godz./godz.)                  |

Na podstawie odniesienia do projektu (dwa klastry 5 maszyn wirtualnych do obsługi przetwarzania wsadowego audio 1 K/dobę), roczny koszt sprzętu wyniesie:

> 2 (klastry) * 5 (maszyny wirtualne na klaster) * $0.3528/hour * 365 (dni) * 24 (godziny) = $31K / rok

Podczas mapowania na komputer fizyczny, ogólne oszacowanie jest 1 vCPU = 1 Rdzeń fizyczny procesora. W rzeczywistości 1vCPU jest bardziej wydajny niż pojedynczy rdzeń.

W przypadku on-prem, wszystkie te dodatkowe czynniki wchodzą w grę:

- Na jakim typie jest fizyczny procesor i ile rdzeni na nim
- Ile procesorów działających razem na tym samym pudełku/komputerze
- Jak skonfigurowane są maszyny wirtualne
- Jak jest używana hiperwątkowa / wielowątkowa
- Sposób udostępniania pamięci
- System operacyjny itp.

Zwykle nie jest tak dobrze dostrojony jako środowisko azure. Biorąc pod uwagę inne obciążenie, powiedziałbym, że bezpieczne oszacowanie to 10 fizycznych rdzeni procesora = 8 procesora wirtualnego Platformy Azure. Chociaż popularne procesory mają tylko osiem rdzeni. W przypadku wdrożenia wstępnego koszt będzie wyższy niż przy użyciu maszyn wirtualnych platformy Azure. Należy również wziąć pod uwagę stopę amortyzacji.

Koszt usługi jest taki sam jak usługa online: $ 1 / godzinę dla mowy do tekstu. Koszt usługi mowy wynosi:

> $1 * 1000 * 365 = $365K

Koszty konserwacji płacone firmie Microsoft zależą od poziomu usługi i zawartości usługi. To różne od $ 29.99 / miesiąc dla podstawowego poziomu do setek tysięcy, jeśli usługa na miejscu zaangażowanych. Przybliżone liczby jest $300/hour dla usługi / utrzymania. Koszty osób nie są wliczone w cenę. Inne koszty infrastruktury (takie jak magazyn, sieci i moduły równoważenia obciążenia) nie są uwzględniane.

<br>
</details>

<details>
<summary>
<b>Dlaczego w transkrypcji brakuje znaków interpunkcyjnych?</b>
</summary>

**Odpowiedź:** Powinny `speech_recognition_language=<YOUR_LANGUAGE>` być jawnie skonfigurowane w żądaniu, jeśli są one przy użyciu klienta Carbon.

Przykład:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Oto dane wyjściowe:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Czy można używać niestandardowego modelu akustycznego i modelu języka z kontenerem Mowy?</b>
</summary>

Obecnie możemy przekazać tylko jeden identyfikator modelu, niestandardowy model języka lub niestandardowy model akustyczny.

**Odpowiedź:** Podjęto *not* decyzję o nieobjęcie jednocześnie modeli akustycznych i językowych. Pozostanie to w mocy, dopóki nie zostanie utworzony ujednolicony identyfikator w celu zmniejszenia przerw w interfejsie API. Niestety, w tej chwili nie jest to obsługiwane.

<br>
</details>

<details>
<summary>
<b>Czy można wyjaśnić te błędy z niestandardowego kontenera zamiany mowy na tekst?</b>
</summary>

**Błąd 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Odpowiedź 1:** Jeśli trenujesz z najnowszym modelem niestandardowym, obecnie tego nie obsługujemy. Jeśli trenujesz ze starszą wersją, powinno być możliwe użycie. Nadal pracujemy nad obsługą najnowszych wersji.

Zasadniczo kontenery niestandardowe nie obsługują modeli akustycznych opartych na halide lub ONNX (co jest ustawieniem domyślnym w portalu szkolenia niestandardowego). Wynika to z niestandardowych modeli nie są szyfrowane i nie chcemy narazić modeli ONNX, jednak; modele językowe są w porządku. Klient będzie musiał jawnie wybrać starszy model non-ONNX dla niestandardowych szkoleń. Dokładność nie zostanie naruszona. Rozmiar modelu może być większy (o 100 MB).

> Model wsparcia > 20190220 (wersja 4.5 Unified)

**Błąd 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Odpowiedź 2:** Należy podać poprawną nazwę głosową w żądaniu, która jest rozróżniana wielkość liter. Zapoznaj się z mapowaniem pełnej nazwy usługi. Musisz użyć `en-US-JessaRUS`, `en-US-JessaNeural` jak nie jest dostępny w tej chwili w wersji kontenera text-to-speech.

**Błąd 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Odpowiedź 3:** Trztulacie można utworzyć zasób mowy, a nie zasób usług Cognitive Services.


<br>
</details>

<details>
<summary>
<b>Jakie protokoły INTERFEJSU API są obsługiwane, REST lub WS?</b>
</summary>

**Odpowiedź:** W przypadku kontenerów zamiany mowy na tekst i niestandardowych kontenerów zamiany mowy na tekst, obecnie obsługujemy tylko protokół oparty na websocket. Zestaw SDK obsługuje tylko wywołanie w ws, ale nie REST. Istnieje plan, aby dodać wsparcie REST, ale nie ETA na razie. Zawsze odsyłaj do oficjalnej dokumentacji, zobacz [punkty końcowe przewidywania zapytań](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Czy centos jest obsługiwany dla kontenerów mowy?</b>
</summary>

**Odpowiedź:** CentOS 7 nie jest jeszcze obsługiwany przez SDK Języka Python, również Ubuntu 19.04 nie jest obsługiwany.

Pakiet zestawu Speech SDK dla języka Python jest dostępny dla tych systemów operacyjnych:
- **Windows** - x64 i x86
- **Mac** - macOS X w wersji 10.12 lub nowszej
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 na x64

Aby uzyskać więcej informacji na temat konfiguracji środowiska, zobacz [Konfiguracja platformy Python](quickstarts/setup-platform.md?pivots=programming-language-python). Na razie Ubuntu 18.04 jest zalecaną wersją.

<br>
</details>

<details>
<summary>
<b>Dlaczego podczas próby wywołania punktów końcowych prognozowania usługi LUIS są obydwochy?</b>
</summary>

Używam kontenera usługi LUIS we wdrożeniu usługi IoT Edge i próbuję wywołać punkt końcowy przewidywania usługi LUIS z innego kontenera. Kontener usługi LUIS nasłuchuje na porcie 5001, a adres URL, zemd ą, jest taki:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Błąd, który osądę, to:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Widzę żądanie w dziennikach kontenera usługi LUIS i komunikat mówi:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Co to oznacza? Czego mi brakuje? Podążałem za przykładem SDK mowy, [stąd](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Scenariusz jest, że jesteśmy wykrywania dźwięku bezpośrednio z mikrofonu komputera i próbuje określić intencji, na podstawie aplikacji usługi LUIS trenowaliśmy. Przykład, z jakim się wiązałem, właśnie to robi. I działa dobrze z usługą chmurową usługi LUIS. Za pomocą SDK mowy wydawało się, aby zaoszczędzić nam przed koniecznością wykonania oddzielnego jawnego wywołania interfejsu API mowy do tekstu, a następnie drugie wywołanie usługi LUIS.

Tak więc wszystko, co próbuję zrobić, to przełączyć się ze scenariusza korzystania z usługi LUIS w chmurze do korzystania z kontenera usługi LUIS. Nie wyobrażam sobie, jeśli SDK mowy działa dla jednego, to nie będzie działać dla innych.

**Odpowiedź:** SDK mowy nie powinny być używane w kontenerze usługi LUIS. Do korzystania z kontenera usługi LUIS należy użyć interfejsu API SDK usługi LUIS lub interfejsu SPOCZYN USŁUGI LUIS. SDK mowy powinny być używane względem kontenera mowy.

Chmura jest inna niż kontener. Chmura może składać się z wielu zagregowanych kontenerów (czasami nazywanych mikroserwami). Więc istnieje kontener usługi LUIS, a następnie istnieje speech kontenera — dwa oddzielne kontenery. Kontener Speech tylko mowy. Kontener usługi LUIS tylko nie usługi LUIS. W chmurze, ponieważ oba kontenery są znane do wdrożenia i jest to zła wydajność dla klienta zdalnego, aby przejść do chmury, do mowy, wrócić, a następnie przejść do chmury ponownie i zrobić usługę LUIS, zapewniamy funkcję, która umożliwia klientowi, aby przejść do mowy, pobyt w chmurze, przejdź do usługi LUIS, a następnie wrócić do klienta. W związku z tym nawet w tym scenariuszu SDK mowy przechodzi do kontenera chmury mowy z dźwiękiem, a następnie speech cloud container rozmowy z kontenerem chmury usługi LUIS z tekstem. Kontener usługi LUIS nie ma pojęcia akceptowania dźwięku (nie ma sensu dla kontenera usługi LUIS do akceptowania przesyłania strumieniowego dźwięku — usługa LUIS jest usługą tekstową). Z on-prem, nie mamy pewności, że nasz klient wdrożył oba kontenery, nie zakładamy, aby zorganizować między kontenerami w pomieszczeniach naszych klientów, a jeśli oba kontenery są wdrażane na prem, biorąc pod uwagę, że są one bardziej lokalne dla klienta, nie jest obciążeniem, aby przejść do SR pierwszy, z powrotem do klienta i klienta następnie wziąć ten tekst i przejść do usługi LUIS.

<br>
</details>

<details>
<summary>
<b>Dlaczego otrzymujemy błędy w systemach macOS, kontenerze mowy i sdku języka Python?</b>
</summary>

Kiedy wysyłamy plik *.wav* do transkrypcji, wynik powraca z:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Wiemy, że websocket jest poprawnie skonfigurowany.

**Odpowiedź:** Jeśli tak jest, zobacz [ten problem GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Mamy obejście, [proponowane tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Węgiel naprawił to w wersji 1.8.


<br>
</details>

<details>
<summary>
<b>Jakie są różnice w punktach końcowych kontenera mowy?</b>
</summary>

Czy możesz pomóc wypełnić następujące metryki testu, w tym jakie funkcje do testowania i jak przetestować interfejsy API SDK i REST? Zwłaszcza, różnice w "interaktywne" i "rozmowy", które nie widziałem z istniejących doc / próbki.

| Endpoint                                                | Test funkcjonalny                                                   | SDK | Interfejs API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Syntetyzowanie tekstu (zamiana tekstu na mowę)                                  |     | Tak      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Usługi Cognitive Services on-prem dyktowanie v1 websocket punkt końcowy        | Tak | Nie       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Punkt końcowy interaktywnej sieci Websocket usług Cognitive Services on-prem  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Usługi kognitywne na prem konwersacji v1 websocket punkt końcowy |     |          |

**Odpowiedź:** Jest to połączenie:
- Ludzie próbują punktu końcowego dyktowania kontenerów (nie jestem pewien, jak dostali ten adres URL)
- Punkt końcowy<sup>1 strony</sup> jest punktem końcowym w kontenerze.
- 1<sup>st</sup> punktu końcowego strony zwraca speech.fragment `speech.hypothesis` komunikatów zamiast wiadomości<sup>3</sup> punkty końcowe części zwraca dla punktu końcowego dyktowania.
- Szybki start Carbon (tryb `RecognizeOnce` interaktywny)
- Carbon o twierdzą, `speech.fragment` że dla wiadomości wymagających nie są zwracane w trybie interaktywnym.
- Węgiel o twierdzi ogień w kompilacji uwalniania (zabijając proces).

Obejście jest albo przełączyć się do korzystania z ciągłego rozpoznawania w kodzie lub (szybciej) połączyć się z interaktywnych lub ciągłych punktów końcowych w kontenerze.
Dla kodu ustaw punkt końcowy na <host:port>/speech/recognition/interactive/cognitiveservices/v1

Zobacz też: Tryby mowy — patrz poniżej:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Właściwa poprawka jest nadchodzi z SDK 1.8, który ma on-prem wsparcia (wybierze odpowiedni punkt końcowy, więc nie będziemy gorsze niż usługa online). W międzyczasie, istnieje próbka do ciągłego rozpoznawania, dlaczego nie wskazujemy na to?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Jakiego trybu należy używać do różnych plików audio?</b>
</summary>

**Odpowiedź:** Oto szybki [start za pomocą Pythona](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python). Inne języki połączone można znaleźć w witrynie z plikami dokumentów.

Tylko w celu wyjaśnienia dla interaktywnych, rozmowy i dyktowania; jest to zaawansowany sposób określania konkretnego sposobu, w jaki nasza usługa będzie obsługiwać żądanie mowy. Niestety, dla kontenerów na prem musimy określić pełny identyfikator URI (ponieważ zawiera komputer lokalny), więc te informacje wyciekły z abstrakcji. Współpracujemy z zespołem SDK, aby uczynić to bardziej użytecznym w przyszłości.

<br>
</details>

<details>
<summary>
<b>W jaki sposób możemy porównać przybliżone miary transakcji/drugi/rdzeń?</b>
</summary>

**Odpowiedź:** Oto niektóre z przybliżonych liczb, których można oczekiwać od istniejącego modelu (zmieni się na lepsze w tym, który wyślemy w GA):

- W przypadku plików ograniczanie będzie w sdk mowy, na 2x. Pierwsze pięć sekund dźwięku nie są ograniczane. Dekoder jest w stanie zrobić około 3x w czasie rzeczywistym. W tym celu ogólne użycie procesora CPU będzie zbliżone do 2 rdzeni dla pojedynczego rozpoznawania.
- Dla mikrofonu będzie to 1x czas rzeczywisty. Ogólne użycie powinno wynosić około 1 rdzeń dla pojedynczego rozpoznawania.

To wszystko można zweryfikować z dzienników docker. My rzeczywiście zrzucić wiersz ze statystykami sesji i fraz/wypowiedź, który zawiera numery RTF.


<br>
</details>

<details>
<summary>
<b>Czy często można podzielić pliki audio na uchwyty do użycia w kontenerze mowy?</b>
</summary>

Mój obecny plan jest do podjęcia istniejącego pliku audio i podzielić go na 10 sekund fragmenty i wysłać je za pośrednictwem kontenera. Czy jest to możliwy do przyjęcia scenariusz?  Czy istnieje lepszy sposób przetwarzania większych plików audio z kontenerem?

**Odpowiedź:** Wystarczy użyć SDK mowy i dać mu plik, zrobi to, co trzeba. Dlaczego musisz poskłować plik?


<br>
</details>

<details>
<summary>
<b>Jak sprawić, aby wiele kontenerów działało na tym samym hoście?</b>
</summary>

Doc mówi, aby udostępnić inny port, co robię, ale kontener usługi LUIS nadal nasłuchuje na porcie 5000?

**Odpowiedź:** Spróbuj `-p <outside_unique_port>:5000`. Na przykład `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Pytania techniczne

<details>
<summary>
<b>Jak uzyskać interfejsy API nieprzeszkaniczne do obsługi dźwięku &lt;o długości 15 sekund?</b>
</summary>

**Odpowiedź:** Jest to tryb interaktywny. Jeśli używasz dyktowania lub konwersacji, to nie jest problem.


<br>
</details>

<details>
<summary>
<b>Jakie są zalecane zasoby, procesor i pamięć RAM; dla 50 równoczesnych wniosków?</b>
</summary>

Ile równoczesnych żądań będzie 4-rdzeniowy, 4 GB pamięci RAM uchwyt? Jeśli mamy służyć na przykład, 50 równoczesnych żądań, ile Core i RAM jest zalecane?

**Odpowiedź:** W czasie rzeczywistym 8 `en-US`z naszych najnowszych, więc zalecamy użycie więcej kontenerów docker poza 6 równoczesnych żądań. Robi się bardziej szalony poza 16 rdzeni i staje się niejednolity dostęp do pamięci (NUMA) węzeł czuły. W poniższej tabeli opisano minimalną i zalecaną alokację zasobów dla każdego kontenera mowy.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

| Kontener      | Minimalne             | Zalecane         |
|----------------|---------------------|---------------------|
| Zamiana mowy na tekst | 2-rdzeniowa pamięć 2 GB | 4-rdzeniowa pamięć 4 GB |

# <a name="custom-speech-to-text"></a>[Niestandardowa zamiana mowy na tekst](#tab/cstt)

| Kontener             | Minimalne             | Zalecane         |
|-----------------------|---------------------|---------------------|
| Niestandardowa zamiana mowy na tekst | 2-rdzeniowa pamięć 2 GB | 4-rdzeniowa pamięć 4 GB |

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

| Kontener      | Minimalne             | Zalecane         |
|----------------|---------------------|---------------------|
| Zamiana tekstu na mowę | 1 rdzeń, 2 GB pamięci | 2-rdzeniowa pamięć 3 GB |

# <a name="custom-text-to-speech"></a>[Niestandardowy tekst na mowę](#tab/ctts)

| Kontener             | Minimalne             | Zalecane         |
|-----------------------|---------------------|---------------------|
| Niestandardowy tekst na mowę | 1 rdzeń, 2 GB pamięci | 2-rdzeniowa pamięć 3 GB |

***

- Każdy rdzeń musi mieć co najmniej 2,6 GHz lub więcej.
- W przypadku plików ograniczanie będzie w SDK mowy, na 2x (pierwsze 5 sekund dźwięku nie są ograniczane).
- Dekoder jest w stanie zrobić około 2-3x w czasie rzeczywistym. W tym celu ogólne użycie procesora CPU będzie zbliżone do dwóch rdzeni dla pojedynczego rozpoznania. Dlatego nie zaleca się utrzymywanie więcej niż dwa aktywne połączenia, na wystąpienie kontenera. Skrajną stroną byłoby umieszczenie około 10 dekoderów w 2x `DS13_V2`czasie rzeczywistym w ośmiordzeniowej maszynie, takiej jak . Dla kontenera w wersji 1.3 i nowszej, `DECODER_MAX_COUNT=20`istnieje param można spróbować ustawienia .
- Dla mikrofonu będzie to 1x w czasie rzeczywistym. Ogólne użycie powinno wynosić około jednego rdzenia dla pojedynczego rozpoznawania.

Należy wziąć pod uwagę całkowitą liczbę godzin dźwięku. Jeśli liczba jest duża, aby poprawić niezawodność/dostępność, sugerujemy uruchomienie większej liczby wystąpień kontenerów, w jednym polu lub na wielu polach, za modułem równoważenia obciążenia. Aranżacji można wykonać przy użyciu Kubernetes (K8S) i Helm lub z Docker komponować.

Na przykład do obsługi 1000 godzin/24 godzin, próbowaliśmy skonfigurować 3-4 maszyn wirtualnych, z 10 wystąpień/dekoderów na maszynę wirtualną.

<br>
</details>

<details>
<summary>
<b>Czy kontener Mowy obsługuje znaki interpunkcyjne?</b>
</summary>

**Odpowiedź:** Mamy kapitalizacji (ITN) dostępne w kontenerze on-prem. Znaki interpunkcyjne są zależne od języka i nie są obsługiwane w niektórych językach, w tym w języku chińskim i japońskim.

Mamy *do* niejawną i podstawową obsługę interpunkcji dla `off` istniejących kontenerów, ale jest domyślnie. Oznacza to, że możesz `.` uzyskać postać w swoim `。` przykładzie, ale nie postać. Aby włączyć tę niejawną logikę, oto przykład, jak to zrobić w języku Python przy użyciu naszego SDK mowy (byłoby podobnie w innych językach):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Dlaczego podczas próby wysyłania danych do kontenera zamiany mowy na tekst o błędach jest onikanie 404 błędów?</b>
</summary>

Oto przykład HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Odpowiedź:** Nie obsługujemy interfejsu API REST w kontenerze zamiany mowy na tekst, obsługujemy tylko websockets za pośrednictwem SDK mowy. Zawsze odsyłaj do oficjalnej dokumentacji, zobacz [punkty końcowe przewidywania zapytań](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Dlaczego podczas korzystania z usługi zamiany mowy na tekst pojawia się ten błąd?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Odpowiedź:** Zazwyczaj dzieje się tak, gdy przesyłasz dźwięk szybciej niż kontener rozpoznawania mowy może go zabrać. Bufory klienta zapełniają się, a anulowanie jest wyzwalane. Musisz kontrolować współbieżność i RTF, przy którym wysyłasz dźwięk.

<br>
</details>

<details>
<summary>
<b>Czy można wyjaśnić te błędy kontenera zamiany tekstu na mowę z przykładów języka C++?</b>
</summary>

**Odpowiedź:** Jeśli wersja kontenera jest starsza niż 1.3, należy użyć tego kodu:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Starsze kontenery nie mają wymaganego punktu końcowego `FromHost` dla carbon do pracy z interfejsem API. Jeśli kontenery używane w wersji 1.3, należy użyć tego kodu:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Poniżej znajduje się `FromEndpoint` przykład korzystania z interfejsu API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 Funkcja `SetSpeechSynthesisVoiceName` jest wywoływana, ponieważ kontenery ze zaktualizowanym aparatem zamiany tekstu na mowę wymagają nazwy głosowej.

<br>
</details>

<details>
<summary>
<b>Jak używać wersji 1.7 sdk mowy z kontenerem Mowy?</b>
</summary>

**Odpowiedź:** Istnieją trzy punkty końcowe w kontenerze mowy dla różnych zastosowań, są one zdefiniowane jako tryby mowy — zobacz poniżej:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Są one do różnych celów i są używane w różny sposób.

[Próbki Pythona](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- Dla pojedynczego rozpoznawania (tryb interaktywny) z niestandardowym punktem końcowym (czyli; `SpeechConfig` z parametrem punktu `speech_recognize_once_from_file_with_custom_endpoint_parameters()`końcowego), patrz .
- Aby uzyskać ciągłe rozpoznawanie (tryb konwersacji) i `speech_recognize_continuous_from_file()`po prostu zmodyfikować, aby użyć niestandardowego punktu końcowego, jak powyżej, zobacz .
- Aby włączyć dyktowanie w przykładach, takich jak powyżej (tylko `speech_config`wtedy, `speech_config.enable_dictation()`gdy naprawdę tego potrzebujesz), zaraz po utworzeniu , dodaj kod .

W języku C# aby włączyć `SpeechConfig.EnableDictation()` dyktowanie, wywołać funkcję.

### <a name="fromendpoint-apis"></a>`FromEndpoint`Interfejsów api
| Język | Szczegóły interfejsu API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Obiektowy C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Obecnie nie jest obsługiwany, ani nie jest planowane. |

<br>
</details>

<details>
<summary>
<b>Jak używać wersji 1.8 SDK mowy z kontenerem Mowy?</b>
</summary>

**Odpowiedź:** Jest nowy `FromHost` interfejs API. Nie zastępuje to ani nie modyfikuje żadnych istniejących interfejsów API. To po prostu dodaje alternatywny sposób tworzenia konfiguracji mowy przy użyciu niestandardowego hosta.

### <a name="fromhost-apis"></a>`FromHost`Interfejsów api

| Język | Szczegóły interfejsu API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Obiektowy C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Nie jest obecnie obsługiwana. |

> Parametry: host (obowiązkowe), klucz subskrypcji (opcjonalnie, jeśli można korzystać z usługi bez niego).

Format hosta `protocol://hostname:port` `:port` jest opcjonalny (patrz poniżej):
- Jeśli kontener jest uruchomiony lokalnie, nazwa `localhost`hosta to .
- Jeśli kontener jest uruchomiony na serwerze zdalnym, użyj adresu hostname lub IPv4 tego serwera.

Przykłady parametrów hosta dla zamiany mowy na tekst:
- `ws://localhost:5000`- niezabezpieczenie połączenia z lokalnym kontenerem za pomocą portu 5000
- `ws://some.host.com:5000`- niezabezpieczenie połączenia z kontenerem działającym na serwerze zdalnym

Python próbki z góry, `host` ale użyj `endpoint`parametru zamiast:

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kontenery usług Cognitive Services](speech-container-howto.md)
