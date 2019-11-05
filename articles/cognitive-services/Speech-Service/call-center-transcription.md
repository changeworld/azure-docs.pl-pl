---
title: Transkrypcja centrum wywołania — usługa mowy
titleSuffix: Azure Cognitive Services
description: Typowy scenariusz dla zamiany mowy na tekst to jego przepisywania duże ilości danych telefonii, które mogą pochodzić z różnych systemów, takich jak interaktywna Odpowiedź głosowa (IVR). Dźwięk może być stereo lub mono oraz RAW z niewielką ilością przetwarzania post na sygnał. Przy użyciu usług mowy i ujednoliconego modelu mowy firma może uzyskać transkrypcje o wysokiej jakości z wieloma systemami przechwytywania audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 858ca114ca4c4b469ce4a5dd5275c9ac9874feb5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464994"
---
# <a name="speech-services-for-telephony-data"></a>Usługi mowy dla danych telefonii

Dane telefoniczne generowane za pomocą telefonu stacjonarnego, telefonów komórkowych i odbiorników radiowych są zwykle niskiej jakości i są wydawane w zakresie 8 KHz, które tworzą wyzwania podczas konwertowania zamiany mowy na tekst. Najnowsze modele rozpoznawania mowy z programu Excel usługi Azure Speech Services pod adresem jego przepisywania te dane, nawet w przypadkach, gdy dane są trudne do zrozumienia przez człowieka. Modele te są przeszkolone z dużymi ilościami danych telefonicznych i najlepiej są zgodne z dokładnością rozpoznawania rynku, nawet w środowiskach z zakłóceniami.

Typowy scenariusz dla zamiany mowy na tekst to jego przepisywania duże ilości danych telefonii, które mogą pochodzić z różnych systemów, takich jak interaktywna Odpowiedź głosowa (IVR). Te systemy mogą być stereofoniczne stereo lub mono oraz RAW z niewielką ilością operacji przetwarzania post na tym sygnale. Korzystając z usług mowy i ujednoliconego modelu mowy, firma może uzyskać transkrypcje o wysokiej jakości, niezależnie od systemów używanych do przechwytywania audio.

Dane telefonii mogą służyć do lepszego zrozumienia potrzeb klientów, zidentyfikowania nowych szans marketingowych lub oceny wydajności agentów centrum połączeń. Po uzyskanego danych firma może użyć danych wyjściowych dla ulepszonej telemetrii, identyfikacji kluczowych fraz lub analizowania tonacji klienta.

Technologie opisane na tej stronie są wewnętrznie przez firmę Microsoft w przypadku różnych usług przetwarzania wywołań w czasie rzeczywistym i w trybie wsadowym.

Zapoznaj się z tematem oferta usługi Azure Speech Services związana z technologią.

> [!IMPORTANT]
> Ujednolicony model usługi Speech Services jest szkolony z różnymi danymi i oferuje jedno rozwiązanie modelu w wielu scenariuszach od dyktowania do analizy telefonii.

## <a name="azure-technology-for-call-centers"></a>Technologia platformy Azure dla centrów wywołań

Poza funkcjonalnym aspektem usług mowy, których głównym celem jest zastosowana do centrum wywołania — jest poprawa środowiska klienta. W tej kwestii istnieją trzy puste domeny:

* Analiza po wywołaniu, która polega na przetwarzaniu wsadowym nagrań wywołań
* Przetwarzanie danych analitycznych w czasie rzeczywistym w celu wyodrębnienia różnych szczegółowych informacji w trakcie wywołania (z tonacjiem jest widocznym przypadkiem użycia) i
* Asystentów głosowych (botów), które umożliwiają przeprowadzenie dialogu między klientem a bot w celu rozwiązania problemu klienta bez udziału agenta lub zastosowania protokołów AI do ułatwienia agentowi.

Typowy diagram architektury implementacji scenariusza wsadowego jest przedstawiony na poniższej ilustracji ![architekturze transkrypcji centrum wywołania](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Składniki technologii analizy mowy

Bez względu na to, czy domena jest wywoływana po wywołaniu, czy w czasie rzeczywistym, platforma Azure oferuje zestaw codziennych i pojawiających się zestawów technologii usprawniających obsługę klientów.

### <a name="speech-to-text-stt"></a>Zamiana mowy na tekst (monitora STT)

Funkcja [zamiany mowy na tekst](speech-to-text.md) jest najbardziej poszukiwana po funkcji w dowolnym rozwiązaniu centrum połączeń. Ze względu na to, że wiele procesów analizy z przedziału bazuje na tekście uzyskanego, współczynnik błędów wyrazów (WER) ma największą ważność. Jednym z najważniejszych wyzwań związanych z transkrypcją centrum rozmów jest szum, który jest powszechnie rozpowszechniony w centrum wywołania (na przykład inne agenci mówiący w tle), bogaty zakres ustawień regionalnych i dialektów języka oraz niska jakość rzeczywistego sygnału telefonu. Funkcja Raportowanie błędów systemu Windows jest wysoce skorelowana z tym, jak dobrze są przeszkolone modele akustyczne i językowe dla danego ustawienia regionalnego, dzięki czemu można dostosować model do ustawień regionalnych. Nasze najnowsze ujednolicone modele w wersji 4. x to rozwiązanie do dokładności i opóźnień transkrypcji. Nauczenie się z dziesiątki tysięcy godzin danych akustycznych i miliardów ujednoliconych modeli informacji leksykalnych to najbardziej dokładne modele na rynku do transkrypcja danych centrum połączeń.

### <a name="sentiment"></a>Opinia
Oceny, czy klient był dobrym doświadczeniem, jest jednym z najważniejszych obszarów analizy mowy w przypadku zastosowania do obszaru Centrum wywołania. Nasz [interfejs API transkrypcji usługi Batch](batch-transcription.md) oferuje analizę tonacji na wypowiedź. Można agregować zbiór wartości uzyskanych jako część transkrypcji wywołania, aby określić tonacji wywołań dla agentów i klienta.

### <a name="silence-non-talk"></a>Wyciszenie (bez rozmowy)
Nie jest to nietypowe w przypadku 35% wywołania pomocy technicznej, aby wywoływać czas braku rozmowy. Niektóre scenariusze, które są niekomunikowane: agenci szukający wcześniejszej historii przypadków u klienta, agenci korzystający z narzędzi, które umożliwiają im dostęp do pulpitu klienta i wykonują funkcje, klienci oczekują na wstrzymanie się w celu przeniesienia i tak dalej. Niezwykle ważne jest, aby można było ocenić, kiedy wystąpiło wyciszenie w wywołaniu, ponieważ istnieje wiele ważnych sensitivities klientów, które występują w tym typie scenariuszy i gdzie występują w wywołaniu.

### <a name="translation"></a>Tłumaczenie
Niektóre firmy są eksperymentować z przekazywaniem przetłumaczonych transkrypcji z obsługi języków obcych, dzięki czemu menedżerowie ds. dostarczania mogą zrozumieć światowe środowisko klientów. Nasze możliwości [tłumaczenia](translation.md) są niezbyt nadmiarowe. Możemy przetłumaczyć dźwięk na dźwięk lub dźwięk na tekst z dużej liczby ustawień regionalnych.

### <a name="text-to-speech"></a>Zamiana tekstu na mowę
[Zamiana tekstu na mowę](text-to-speech.md) jest innym ważnym obszarem wdrażania botów, które współdziałają z klientami. Typowym profilem jest to, że klient mówi, że jego głos jest uzyskanego do tekstu, a tekst jest analizowany pod kątem intencji, odpowiedź jest obliczana na podstawie rozpoznanego celu, a następnie jest przyznana do klienta lub z syntezą głosu. wytwarza. Oczywiście należy szybko wykonać wszystkie te działania, co oznacza, że opóźnienie jest ważnym składnikiem w sukcesie tych systemów.

Nasze opóźnienia kompleksowe są dość małe, biorąc pod uwagę różne technologie, takie jak [Zamiana mowy na tekst](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [bot Framework](https://dev.botframework.com/), [Zamiana tekstu na mowę](text-to-speech.md).

Nasze nowe głosy są również odróżniane od głosów ludzkich. Możesz użyć głosów out, aby bot swój unikatowy charakter.

### <a name="search"></a>Wyszukiwanie
Kolejną zszywaniem funkcji analitycznych jest zidentyfikowanie interakcji w przypadku wystąpienia konkretnego zdarzenia lub doświadczenia. Zwykle odbywa się to przy użyciu jednego z dwóch metod — wyszukiwanie ad hoc, gdzie użytkownik po prostu wpisuje frazę i system odpowiada, lub bardziej strukturalne zapytanie, gdzie analityk może utworzyć zestaw instrukcji logicznych, które identyfikują scenariusz w wywołaniu , a następnie każde wywołanie można indeksować względem tego zestawu zapytań. Dobrym przykładem wyszukiwania jest Powszechna Deklaracja zgodności "to wywołanie jest rejestrowane w celach jakościowych... "— tak wiele firm chce, aby upewnić się, że ich agenci świadczą tę klauzulę klientom przed faktycznym zarejestrowaniem połączenia. Większość systemów analitycznych ma możliwość trendu zachowań znalezionych w ramach algorytmów/Search zapytania — ponieważ takie raportowanie trendów jest ostatecznie jedną z najważniejszych funkcji systemu analitycznego. Za pomocą [katalogu usług poznawczych](https://azure.microsoft.com/services/cognitive-services/directory/search/) kompleksowe rozwiązanie można znacznie rozszerzyć za pomocą funkcji indeksowania i wyszukiwania.

### <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz
Ten obszar jest jednym z trudniejszych aplikacji analitycznych, które korzystają z aplikacji AI i ML. Głównym scenariuszem jest wywnioskowanie intencji klienta. Dlaczego klient dzwoni? Co to jest problem z klientem? Dlaczego klient ma negatywny wpływ? Nasza [Usługa analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) udostępnia zestaw analiz, dzięki którym można szybko uaktualnić kompleksowe rozwiązanie, aby wyodrębnić te ważne słowa kluczowe lub frazy.

Teraz przyjrzyjmy się przetwarzaniu wsadowemu i potokom w czasie rzeczywistym na potrzeby rozpoznawania mowy na nieco bardziej szczegółowym poziomie.

## <a name="batch-transcription-of-call-center-data"></a>Transkrypcja partii danych centrum połączeń

Aby uzyskać jego przepisywaniay za dźwięk, opracowano [interfejs API transkrypcji usługi Batch](batch-transcription.md). Interfejs API transkrypcji usługi Batch został opracowany z myślą o transkrypcja dużej ilości danych dźwiękowych asynchronicznie. W odniesieniu do danych centrum połączeń jego przepisywania nasze rozwiązanie jest oparte na następujących filarach:

* **Dokładność**: przy użyciu ujednoliconych modeli z czwartą generację oferujemy przekroczenie jakości transkrypcji.
* **Opóźnienie**: wiemy, że podczas przeprowadzania zbiorczych transkrypcji, transkrypcje są zbędne szybko. Zadania transkrypcji zainicjowane za pośrednictwem [interfejsu API transkrypcji usługi Batch](batch-transcription.md) będą natychmiast umieszczane w kolejce, a po uruchomieniu zadania są wykonywane szybciej niż transkrypcja w czasie rzeczywistym.
* **Zabezpieczenia**: rozumiemy, że wywołania mogą zawierać poufne dane. Zagwarantowanie, że bezpieczeństwo jest jednym z naszych największych priorytetów. Nasza usługa uzyskała certyfikaty ISO, SOC, HIPAA i PCI.

Centra wywołań generują codziennie duże ilości danych audio. Jeśli firma przechowuje dane telefoniczne w centralnej lokalizacji, takiej jak usługa Azure Storage, można użyć [interfejsu API transkrypcji usługi Batch](batch-transcription.md) do asynchronicznego żądania i otrzymywania transkrypcji.

Typowe rozwiązanie używa tych usług:

* Usługi Azure Speech Services umożliwiają transkrypcja zamiany mowy na tekst. Do korzystania z interfejsu API transkrypcji usługi Batch jest wymagana Standardowa subskrypcja usług mowy. Bezpłatne subskrypcje (F0) nie będą działały.
* [Usługa Azure Storage](https://azure.microsoft.com/services/storage/) służy do przechowywania danych telefonicznych i transkrypcji zwróconych przez interfejs API transkrypcji partii. To konto magazynu powinno używać powiadomień, w odniesieniu do gdy dodawane są nowe pliki. Te powiadomienia są używane do wyzwalania procesu transkrypcji.
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) służy do tworzenia identyfikatora URI sygnatury dostępu współdzielonego (SAS) dla każdego rejestrowania i wyzwalania żądania HTTP post w celu rozpoczęcia transkrypcji. Ponadto Azure Functions służy do tworzenia żądań pobrania i usunięcia transkrypcji przy użyciu interfejsu API transkrypcji usługi Batch.
* Elementy [webhook](webhooks.md) są używane do uzyskiwania powiadomień po zakończeniu transkrypcji.

Wewnętrznie korzystamy z powyższych technologii do obsługi wywołań klientów firmy Microsoft w trybie wsadowym.
![](media/scenarios/call-center-batch-pipeline.png) architektury

## <a name="real-time-transcription-for-call-center-data"></a>Transkrypcja w czasie rzeczywistym dla danych centrum wywołań

Niektóre firmy muszą transkrypcja konwersacje w czasie rzeczywistym. Transkrypcja w czasie rzeczywistym może służyć do identyfikowania słów kluczowych i wyzwalania wyszukiwania zawartości i zasobów związanych z konwersacją, monitorowania tonacji, w celu usprawnienia ułatwienia dostępu lub do przekazywania tłumaczeń dla klientów i agentów, którzy nie są natywni SŁUCHAW.

W scenariuszach wymagających transkrypcji w czasie rzeczywistym zalecamy użycie [zestawu Speech SDK](speech-sdk.md). Obecnie funkcja zamiany mowy na tekst jest dostępna w [ponad 20 językach](language-support.md), a zestaw SDK jest dostępny w C++językach, C#, Java, Python, Node. js, celu-C i JavaScript. Przykłady są dostępne w każdym języku w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Najnowsze wiadomości i aktualizacje znajdują się w temacie [Informacje o wersji](releasenotes.md).

Wewnętrznie korzystamy z powyższych technologii, aby analizować w czasie rzeczywistym wywołania klientów firmy Microsoft w miarę ich występowania.

![Architektura partii](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Słowo w IVRs

Usługi mowy można łatwo zintegrować w dowolnym rozwiązaniu za pomocą [zestawu Speech SDK](speech-sdk.md) lub [interfejsu API REST](rest-apis.md). Jednak transkrypcja centrum połączeń może wymagać dodatkowych technologii. Zwykle jest wymagane połączenie między systemem IVR i platformą Azure. Mimo że te składniki nie są oferowane, chcemy opisać, co wiąże się z IVR.

Kilka produktów usługi IVR lub usług telefonii (takich jak Genesys lub AudioCodes) oferuje możliwości integracji, których można użyć do włączenia przychodzącego i wychodzącego przekazywania audio do usługi platformy Azure. W zasadzie niestandardowa usługa platformy Azure może udostępnić określony interfejs do definiowania sesji połączeń telefonicznych (takich jak wywołanie uruchomienia lub wywołania końcowego) i uwidocznić interfejs API protokołu WebSocket, który będzie odbierać strumienie audio dla ruchu przychodzącego, który jest używany z usługami mowy. Odpowiedzi wychodzące, takie jak transkrypcja konwersacji lub połączenia z platformą bot, można wyszukiwać za pomocą usługi zamiany tekstu na mowę firmy Microsoft i zwrócić do IVR na potrzeby odtwarzania.

Innym scenariuszem jest bezpośrednia integracja z SIP. Usługa platformy Azure nawiązuje połączenie z serwerem SIP, dzięki czemu uzyskuje strumień przychodzący i strumień wychodzący, który jest używany na potrzeby faz zamiany mowy na tekst i zamiany tekstu na mowę. Aby nawiązać połączenie z serwerem SIP, istnieją komercyjne oferty oprogramowania, takie jak Ozeki SDK, lub [zespoły wywołujące i zajmujące się interfejsem API](/graph/api/resources/communications-api-overview) (obecnie w wersji beta), które są przeznaczone do obsługi tego typu scenariusza dla wywołań audio.

## <a name="customize-existing-experiences"></a>Dostosowywanie istniejących środowisk

Usługi Azure Speech Services dobrze sprawdzają się w połączeniu z wbudowanymi modelami, ale warto dodatkowo dostosowywać i dostrajać środowisko dla danego produktu lub środowiska. Opcje dostosowania przestają być od modelu akustycznego na unikatowe czcionki głosowe dla marki. Po skompilowaniu modelu niestandardowego można go używać z dowolną usługą Azure Speech Services w czasie rzeczywistym lub w trybie wsadowym.

| Usługa mowy | Modelowanie | Opis |
|----------------|-------|-------------|
| Zamiana mowy na tekst | [Model akustyczny](how-to-customize-acoustic-models.md) | Utwórz niestandardowy model akustyczny dla aplikacji, narzędzi lub urządzeń używanych w określonych środowiskach, takich jak samochód lub piętro fabryki, z których każdy ma określone warunki rejestrowania. Przykłady obejmują akcentowanie mowy, konkretne zakłócenia w tle lub użycie określonego mikrofonu do nagrywania. |
| | [Model językowy](how-to-customize-language-model.md) | Utwórz niestandardowy model języka w celu usprawnienia transkrypcji słownictwa i gramatyki właściwych dla branży, takich jak Terminologia medyczna lub żargon IT. |
| | [Model wymowy](how-to-customize-pronunciation.md) | Przy użyciu niestandardowego modelu wymowy można zdefiniować formularz fonetyczny i wyświetlić wyraz lub termin. Jest to przydatne do obsługi niestandardowych warunków, takich jak nazwy produktów lub akronimy. Wszystko, co musisz zrobić, to plik wymowy — prosty plik. txt. |
| Zamiana tekstu na mowę | [Czcionka głosowa](how-to-customize-voice-font.md) | Niestandardowe czcionki głosowe umożliwiają utworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Rozpoczęcie pracy wymaga jedynie niewielkiej ilości danych. Im więcej danych zapewniasz, tym bardziej naturalna i przypominająca Twoja czcionka głosu będzie dźwiękowa. |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod jest dostępny w witrynie GitHub dla każdej usługi Azure Speech Services. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi. Użyj tych linków, aby wyświetlić zestaw SDK i przykłady REST:

* [Przykłady zamiany mowy na tekst i mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw SDK urządzeń mowy](speech-devices-sdk.md)
* [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
* [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
* [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
