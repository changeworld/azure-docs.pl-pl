---
title: Transkrypcja centrum obsługi — usługa mowy
titleSuffix: Azure Cognitive Services
description: Typowym scenariuszem dla zamiany mowy na tekst jest transkrypcja dużych ilości danych telefonii pochodzących z różnych systemów, takich jak Interaktywna odpowiedź głosowa (IVR). Korzystając z usługi mowy i modelu mowy Unified, firma może uzyskać wysokiej jakości transkrypcje za pomocą systemów przechwytywania dźwięku.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806324"
---
# <a name="speech-service-for-telephony-data"></a>Usługa mowy dla danych telefonii

Dane telefoniczne generowane za pośrednictwem telefonów stacjonarnych, telefonów komórkowych i radia są zazwyczaj niskiej jakości i wąskopasmowe w zakresie 8 KHz, co stwarza wyzwania podczas konwersji mowy na tekst. Najnowsze modele rozpoznawania mowy z usługi mowy excel do transkrypcji tych danych telefonii, nawet w przypadkach, gdy dane są trudne do zrozumienia dla człowieka. Modele te są szkolone z dużą ilością danych telefonicznych i mają najlepszą dokładność rozpoznawania na rynku, nawet w hałaśliwym otoczeniu.

Typowym scenariuszem dla zamiany mowy na tekst jest transkrypcja dużych ilości danych telefonii, które mogą pochodzić z różnych systemów, takich jak Interactive Voice Response (IVR). Audio te systemy zapewniają może być stereo lub mono, i surowe z mało do żadnego przetwarzania końcowego odbywa się na sygnał. Korzystając z usługi Mowy i modelu mowy Unified, firma może uzyskać wysokiej jakości transkrypcje, niezależnie od systemów używanych do przechwytywania dźwięku.

Dane telefonii mogą być wykorzystywane do lepszego zrozumienia potrzeb klientów, identyfikacji nowych możliwości marketingowych lub oceny wydajności agentów call center. Po transkrypcji danych firma może używać danych wyjściowych do celów, takich jak ulepszona telemetria, identyfikowanie kluczowych fraz lub analizowanie nastrojów klientów.

Technologie opisane na tej stronie są przez firmę Microsoft wewnętrznie dla różnych usług przetwarzania wywołań pomocy technicznej, zarówno w czasie rzeczywistym, jak i w trybie wsadowym.

Przejrzyjmy niektóre z technologii i powiązanych funkcji, które oferuje usługa Mowy.

> [!IMPORTANT]
> Ujednolicony model usługi mowy jest przeszkolony w zakresie różnych danych i oferuje rozwiązanie jednomodelowe dla wielu scenariuszy, od dyktowania po analizę telefonii.

## <a name="azure-technology-for-call-centers"></a>Technologia platformy Azure dla centrów obsługi

Poza funkcjonalnym aspektem funkcji usługi Mowy, ich głównym celem — po zastosowaniu do biura obsługi — jest poprawa jakości obsługi klienta. W tym względzie istnieją trzy jasne dziedziny:

- Analiza po połączeniu, która zasadniczo jest przetwarzaniem wsadowym nagrań rozmów po wywołaniu.
- Analiza w czasie rzeczywistym, która przetwarza sygnał audio, aby wyodrębnić różne spostrzeżenia podczas połączenia (z sentymentem jest widocznym przypadkiem użycia).
- Asystenci głosowi (boty), albo napędzają dialog między klientem a botem, próbując rozwiązać problem klienta bez udziału agenta, albo są zastosowaniem protokołów sztucznej inteligencji (AI) w celu pomocy agentowi.

Typowy diagram architektury implementacji scenariusza wsadowego jest ![przedstawiony na obrazku poniżej Architektura transkrypcji centrum wywoławości](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Składniki technologii analizy mowy

Niezależnie od tego, czy domena jest po połączeniu, czy w czasie rzeczywistym, platforma Azure oferuje zestaw dojrzałych i nowych technologii w celu poprawy jakości obsługi klienta.

### <a name="speech-to-text-stt"></a>Mowa do tekstu (STT)

[Zamiana mowy na tekst](speech-to-text.md) jest najbardziej poszukiwaną funkcją w każdym rozwiązaniu call center. Ponieważ wiele procesów analizy niższego szczebla opiera się na transkrybowanym tekście, poziom błędu słowa _(WER)_ ma ogromne znaczenie. Jednym z kluczowych wyzwań w transkrypcji call center jest hałas, który jest rozpowszechniony w call center (na przykład inni agenci mówiący w tle), bogata różnorodność ustawień regionalnych i dialektów językowych, a także niska jakość rzeczywistego sygnału telefonicznego. WER jest wysoce skorelowane z jak dobrze akustyczne i modele językowe są przeszkoleni dla danego ustawienia regionalne, w związku z tym możliwość dostosowania modelu do ustawień regionalnych jest ważne. Nasze najnowsze modele Unified w wersji 4.x są rozwiązaniem zarówno dokładności transkrypcji, jak i opóźnienia. Modele Unified, wyszkolone z dziesiątkami tysięcy godzin danych akustycznych i miliardami informacji leksykalne, są najdokładniejszymi modelami na rynku do transkrypcji danych call center.

### <a name="sentiment"></a>Opinia

Ocena, czy klient miał dobre doświadczenia jest jednym z najważniejszych obszarów analizy mowy, gdy stosowane do miejsca w call center. Nasz [interfejs API transkrypcji partii](batch-transcription.md) oferuje analizę tonacji na wypowiedź. Można zagregować zestaw wartości uzyskanych w ramach transkrypcji wywołania, aby określić tonację wywołania zarówno dla agentów, jak i klienta.

### <a name="silence-non-talk"></a>Cisza (bez gadania)

Nie jest rzadkością, że 35 procent wsparcia jest tym, co nazywamy czasem nierozmów. Niektóre scenariusze, dla których nie-talk występuje są: agenci patrząc na historię wcześniejszych spraw z klientem, agentów za pomocą narzędzi, które pozwalają im uzyskać dostęp do pulpitu klienta i wykonywać funkcje, klienci siedzący w trybie wstrzymania oczekiwania na przeniesienie i tak dalej. Jest niezwykle ważne, aby ocenić, kiedy cisza występuje w wywołaniu, ponieważ istnieje wiele ważnych wrażliwości klientów, które występują wokół tego typu scenariuszy i gdzie występują w wywołaniu.

### <a name="translation"></a>Tłumaczenie

Niektóre firmy eksperymentują z dostarczaniem przetłumaczonych transkrypcji z połączeń wsparcia w języku obcym, aby menedżerowie dostaw mogli zrozumieć światowe doświadczenia swoich klientów. Nasze możliwości [tłumaczenia](translation.md) są niezrównane. Możemy przetłumaczyć dźwięk na dźwięk lub audio-to-text dla dużej liczby ustawień regionalnych.

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

[Zamiana tekstu na mowę](text-to-speech.md) to kolejny ważny obszar w implementacji botów, które wchodzą w interakcje z klientami. Typową ścieżką jest to, że klient mówi, jego głos jest transkrybowany do tekstu, tekst jest analizowany pod kątem intencji, odpowiedź jest syntetyzowana na podstawie rozpoznanego zamiaru, a następnie zasób jest albo widoczny dla klienta, albo syntetyzowana odpowiedź głosowa jest Generowane. Oczywiście wszystko to musi nastąpić szybko – w ten sposób niskie opóźnienia są ważnym elementem sukcesu tych systemów.

Nasze opóźnienie end-to-end jest znacznie niskie dla różnych technologii, takich jak [mowa do tekstu,](speech-to-text.md) [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/), [Text-to-speech](text-to-speech.md).

Nasze nowe głosy są również nie do odróżnienia od ludzkich głosów. Możesz użyć naszych głosów, aby nadać swojemu botowi wyjątkową osobowość.

### <a name="search"></a>Wyszukiwanie

Inną podstawą analizy jest zidentyfikowanie interakcji, w których wystąpiło określone zdarzenie lub doświadczenie. Zazwyczaj odbywa się to za pomocą jednego z dwóch podejść; wyszukiwanie ad hoc, w którym użytkownik po prostu wpisuje frazę, a system odpowiada, albo bardziej ustrukturyzowane zapytanie, w którym analityk może utworzyć zestaw instrukcji logicznych identyfikujących scenariusz w wywołaniu, a następnie każde wywołanie może być indeksowane względem tego zestawu zapytań. Dobrym przykładem wyszukiwania jest wszechobecne oświadczenie o zgodności "to wezwanie jest rejestrowane dla celów jakościowych ... ". Wiele firm chce się upewnić, że ich agenci dostarczają to zastrzeżenie klientom, zanim połączenie zostanie faktycznie zarejestrowane. Większość systemów analitycznych ma możliwość trendu zachowań znalezionych przez algorytmy zapytań/wyszukiwania, a to raportowanie trendów jest ostatecznie jedną z najważniejszych funkcji systemu analitycznego. Za pośrednictwem [katalogu usług Cognitive](https://azure.microsoft.com/services/cognitive-services/directory/search/) rozwiązanie end-to-end można znacznie zwiększyć za pomocą możliwości indeksowania i wyszukiwania.

### <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Ten obszar jest jedną z bardziej wymagających aplikacji analitycznych i taką, która korzysta z zastosowania sztucznej inteligencji i uczenia maszynowego. Podstawowym scenariuszem w tym przypadku jest wywnioskować intencji klienta. Dlaczego dzwoni klient? Na czym polega problem klienta? Dlaczego klient miał negatywne doświadczenia? Nasza [usługa analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) zapewnia zestaw analiz po wyjęciu z pudełka do szybkiego uaktualniania kompleksowego rozwiązania do wyodrębniania tych ważnych słów kluczowych lub fraz.

Przyjrzyjmy się teraz przetwarzaniu wsadowego i potokom w czasie rzeczywistym do rozpoznawania mowy w nieco bardziej szczegółowo.

## <a name="batch-transcription-of-call-center-data"></a>Transkrypcja partii danych call center

Do transkrypcji dźwięku zbiorczego opracowaliśmy [interfejs API transkrypcji partii.](batch-transcription.md) Interfejs API transkrypcji partii został opracowany w celu transkrypcji dużych ilości danych audio asynchronicznie. Jeśli chodzi o przepisywanie danych call center, nasze rozwiązanie opiera się na następujących filarach:

- **Dokładność** - Dzięki ujednoliconym modelom czwartej generacji oferujemy niezrównaną jakość transkrypcji.
- **Opóźnienie** — rozumiemy, że podczas wykonywania transkrypcji zbiorczych transkrypcje są potrzebne szybko. Zadania transkrypcji zainicjowane za pośrednictwem [interfejsu API transkrypcji partii](batch-transcription.md) zostaną natychmiast umieszczone w kolejce, a po uruchomieniu zadania zostanie wykonane szybciej niż transkrypcja w czasie rzeczywistym.
- **Bezpieczeństwo** — rozumiemy, że wywołania mogą zawierać poufne dane. Bądźcie pewni, że bezpieczeństwo jest jednym z naszych najwyższych priorytetów. Nasz serwis uzyskał certyfikaty ISO, SOC, HIPAA, PCI.

Centra wywoławcze codziennie generują duże ilości danych audio. Jeśli twoja firma przechowuje dane telefonii w centralnej lokalizacji, takiej jak usługa Azure Storage, można użyć [interfejsu API transkrypcji wsadowej](batch-transcription.md) do asynchronicznie żądania i odbierania transkrypcji.

Typowe rozwiązanie korzysta z tych usług:

- Usługa Mowy służy do transkrypcji mowy na tekst. Standardowa subskrypcja (S0) dla usługi Mowy jest wymagana do korzystania z interfejsu API transkrypcji partii. Bezpłatne subskrypcje (F0) nie będą działać.
- [Usługa Azure Storage](https://azure.microsoft.com/services/storage/) służy do przechowywania danych telefonii i transkrypcji zwracanych przez interfejs API transkrypcji partii. To konto magazynu należy używać powiadomień, w szczególności, gdy nowe pliki są dodawane. Powiadomienia te są używane do wyzwalania procesu transkrypcji.
- [Usługa Azure Functions](https://docs.microsoft.com/azure/azure-functions/) służy do tworzenia identyfikatora URI podpisów dostępu współdzielonego (SAS) dla każdego nagrania i wyzwalania żądania HTTP POST w celu rozpoczęcia transkrypcji. Ponadto usługa Azure Functions służy do tworzenia żądań pobierania i usuwania transkrypcji przy użyciu interfejsu API transkrypcji wsadowej.

Wewnętrznie używamy powyższych technologii do obsługi wywołań klientów firmy Microsoft w trybie wsadowym.
![Architektura wsadowa](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Transkrypcja w czasie rzeczywistym dla danych call center

Niektóre firmy są zobowiązane do transkrypcji rozmów w czasie rzeczywistym. Transkrypcja w czasie rzeczywistym może służyć do identyfikowania słów kluczowych i wyzwalania wyszukiwania treści i zasobów związanych z konwersacją, monitorowania nastrojów, poprawy dostępności lub dostarczania tłumaczeń klientom i agentom, którzy nie są natyle Głośniki.

W przypadku scenariuszy wymagających transkrypcji w czasie rzeczywistym zaleca się użycie [SDK mowy](speech-sdk.md). Obecnie zamiana mowy na tekst jest dostępna w [ponad 20 językach,](language-support.md)a pakiet SDK jest dostępny w językach C++, C#, Java, Python, Node.js, Objective-C i JavaScript. Przykłady są dostępne w każdym języku w [usłudze GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) Aby uzyskać najnowsze wiadomości i aktualizacje, zobacz [Informacje o wersji](releasenotes.md).

Wewnętrznie używamy powyższych technologii do analizowania w czasie rzeczywistym wywołania klientów firmy Microsoft, jak to się dzieje, jak pokazano na poniższym diagramie.

![Architektura wsadowa](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Słowo na IVRs

Usługę Mowy można łatwo zintegrować z dowolnym rozwiązaniem za pomocą [SDK mowy](speech-sdk.md) lub [interfejsu API REST.](rest-apis.md) Transkrypcja call center może jednak wymagać dodatkowych technologii. Zazwyczaj wymagane jest połączenie między systemem IVR a platformą Azure. Chociaż nie oferujemy takich komponentów, oto opis, co pociąga za sobą połączenie z IVR.

Kilka produktów usługi IVR lub telefonii (takich jak Genesys lub AudioCodes) oferuje możliwości integracji, które można wykorzystać, aby umożliwić przychodzące i wychodzące przekazywanie dźwięku do usługi Azure. Zasadniczo niestandardowa usługa platformy Azure może zapewnić określony interfejs do definiowania sesji połączeń telefonicznych (takich jak wywołanie start lub call end) i uwidaczniania interfejsu API WebSocket do odbierania przychodzącego strumienia audio, który jest używany z usługą mowy. Odpowiedzi wychodzące, takie jak transkrypcja konwersacji lub połączenia z platformą Bot Framework, można zsyntetyzować z usługą zamiany tekstu na mowę firmy Microsoft i powrócić do IVR w celu odtwarzania.

Innym scenariuszem jest bezpośrednia integracja z protokołem inicjacji sesji (SIP). Usługa platformy Azure łączy się z serwerem SIP, uzyskując w ten sposób strumień przychodzący i strumień wychodzący, który jest używany dla faz zamiany mowy na tekst i zamiany tekstu na mowę. Aby połączyć się z serwerem SIP, istnieją oferty oprogramowania komercyjnego, takie jak Ozeki SDK lub [interfejs API wywoływania i spotkania zespołów](/graph/api/resources/communications-api-overview) (obecnie w wersji beta), które są przeznaczone do obsługi tego typu scenariuszy dla połączeń audio.

## <a name="customize-existing-experiences"></a>Dostosowywanie istniejących środowisk

 Usługa Mowy działa dobrze z wbudowanymi modelami. Warto jednak dostosować i dostroić środowisko dla produktu lub środowiska. Opcje dostosowywania obejmują zarówno dostrajanie modelu akustycznego, jak i unikalne czcionki głosowe dla twojej marki. Po sbudowaniu modelu niestandardowego można go używać z dowolną funkcją usługi Mowy w trybie w czasie rzeczywistym lub wsadowym.

| Usługa rozpoznawania mowy | Model | Opis |
| -------------- | ----- | ----------- |
| Zamiana mowy na tekst | [Model akustyczny](how-to-customize-acoustic-models.md) | Utwórz niestandardowy model akustyczny dla aplikacji, narzędzi lub urządzeń, które są używane w określonych środowiskach, takich jak samochód lub na hali produkcyjnej, z których każdy ma określone warunki nagrywania. Przykłady obejmują mowę akcentującą, określone odgłosy tła lub użycie określonego mikrofonu do nagrywania. |
|                | [Model językowy](how-to-customize-language-model.md) | Utwórz niestandardowy model języka w celu poprawy transkrypcji słownictwa i gramatyki specyficznej dla branży, takiej jak terminologia medyczna lub żargon IT. |
|                | [Model wymowy](how-to-customize-pronunciation.md) | Za pomocą niestandardowego modelu wymowy można zdefiniować formularz fonetyczny i wyświetlić wyraz lub termin. Jest to przydatne do obsługi niestandardowych terminów, takich jak nazwy produktów lub akronimy. Wszystko, czego potrzebujesz, aby rozpocząć, to plik `.txt` wymowy, który jest prostym plikiem. |
| Zamiana tekstu na mowę | [Czcionka głosowa](how-to-customize-voice-font.md) | Niestandardowe czcionki głosowe umożliwiają tworzenie rozpoznawalnego, jedynego w swoim rodzaju głosu dla twojej marki. Rozpoczęcie pracy zajmuje tylko niewielką ilość danych. Im więcej danych podasz, tym bardziej naturalna i ludzka czcionka głosowa będzie brzmiała. |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod jest dostępny w usłudze GitHub dla każdej funkcji usługi mowy. Te przykłady obejmują typowe scenariusze, takie jak odczyt dźwięku z pliku lub strumienia, ciągłe i pojedyncze ujęcie rozpoznawania i pracy z modelami niestandardowymi. Użyj tych łączy, aby wyświetlić próbki SDK i REST:

- [Przykłady tłumaczenia mowy na tekst i mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Próbki transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Próbki zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Dokumenty referencyjne

- [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
- [Zestaw Speech Devices SDK](speech-devices-sdk.md)
- [INTERFEJS API REST: Zamiana mowy na tekst](rest-speech-to-text.md)
- [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
- [INTERFEJS API REST: transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskaj bezpłatny klucz subskrypcji usługi mowy](get-started.md)
