---
title: Wywołaj transkrypcji Centrum — usługi mowy
titleSuffix: Azure Cognitive Services
description: Typowy scenariusz dla mowy na tekst jest przepisywania dużych ilości danych telefonii, które mogą pochodzić z różnych systemów, takich jak interakcyjne odpowiedzi głosowych (IVR). Dźwięk można stereo lub mono i pierwotnych z nieco nie operację przetwarzania końcowego wykonywane na sygnał. Korzystając z usług przetwarzania mowy i ujednoliconego modelu mowy, firmy mogą uzyskać wysokiej jakości, transkrypcje z wielu systemów przechwytywania audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 055d141cab8ece3fcb462573f6ed4d8941c19751
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064111"
---
# <a name="speech-services-for-telephony-data"></a>Usług przetwarzania mowy danych telefonii

Dane telefonii, które jest generowany przy użyciu landlines, telefony komórkowe i urządzeń radiowych są zwykle niska jakość i wąskopasmowych w zakresie 8 KHz, który tworzy wyzwania podczas konwertowania mowy na tekst. Najnowsze modeli rozpoznawania mowy z usług przetwarzania mowy platformy Azure programu excel na przepisywania te dane telefonii, nawet w przypadkach, gdy dane znajdują się trudne dla człowieka zrozumieć. Te modele są uczone z użyciem dużej ilości danych telefonii i mają najlepszych dokładności rozpoznawania na rynku, nawet w środowiskach generujące dużo alertów.

Typowy scenariusz dla mowy na tekst jest przepisywania dużych ilości danych telefonii, które mogą pochodzić z różnych systemów, takich jak interakcyjne odpowiedzi głosowych (IVR). Dźwięk, podane w tych systemach można stereo lub mono i raw z niewielkim nie operację przetwarzania końcowego na sygnał. Korzystając z usług przetwarzania mowy i ujednoliconego modelu mowy, firmy mogą uzyskać transkrypcje wysokiej jakości, niezależnie od systemy używane do przechwytywania audio.

Telefonii danych umożliwia lepsze zrozumienie potrzeb klientów, znaleźć nowe możliwości marketingowe lub oceny wydajności wywołania Centrum agentów. Po danych jest transkrybowanego, firma wykorzystywać dane wyjściowe dla ulepszone telemetrii, zidentyfikowanie kluczowych fraz lub analizowanie nastroje klientów.

Technologie opisane na tej stronie są przez firmę Microsoft wewnętrznie obsługi różnych usług przetwarzania wywołania, zarówno w czasie rzeczywistym, jak i w trybie wsadowym.

Omówmy niektóre technologii i oferty usług przetwarzania mowy platformy Azure powiązanych funkcji.

> [!IMPORTANT]
> Modelu ujednolicone usługi mowy jest uczony przy użyciu różnych danych i oferuje rozwiązania pojedynczego modelu do liczby scenariusza z dyktowanie telefonii analizy.

## <a name="azure-technology-for-call-centers"></a>Technologię Azure w centrach wywołania

Poza aspekt funkcjonalności usług przetwarzania mowy ich głównym celem — w przypadku zastosowania do Centrum telefonicznej — jest poprawy jakości obsługi klienta. Trzy domeny wyczyść istnieje w tym zakresie:

* Po wywołaniu analizy partii oznacza to, przetwarzania nagrania wywołania 
* Przetwarzanie analizy w czasie rzeczywistym sygnału dźwiękowego, aby wyodrębnić różne szczegółowe informacje, jak wywołania odbywa się (z opinii jest przypadek użycia wyraźną) i
* Asystenci wirtualnego (robotom), prowadzenie dialog między klienta i bota w celu podjęcia próby rozwiązania problemu klienta bez udziału agent lub przy zastosowaniu sztucznej Inteligencji protokoły ułatwiają agenta.

Diagram typowej architektury realizacji scenariusza usługi batch jest przedstawiony na ilustracji poniżej ![architektura transkrypcji Centrum wywołania](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Składniki technologii analizy mowy

Czy domena jest wywołanie po lub w czasie rzeczywistym, platforma Azure oferuje zestaw zestaw dojrzałych i rozwijających się technologii, w celu poprawy jakości obsługi klienta. 

### <a name="speech-to-text-stt"></a>Zamiana mowy na tekst (STT) 

[Zamiany mowy na tekst](speech-to-text.md) będzie najczęściej używanych po funkcji w żadnym rozwiązaniu do Centrum połączenia. Ponieważ zależne wiele procesów podrzędnych analizy uzyskanego tekstu, współczynnik błędów programu word (WER) ma priorytetowe znaczenie. Jedną z kluczowych wyzwań w wywołaniu Centrum transkrypcji jest szumu, która jest powszechnie znane w Centrum telefonicznej (na przykład inni agenci wypowiedzi w tle), sformatowany różnych ustawień regionalnych języka i dialekty oraz niskiej jakości sygnału rzeczywistego telefonu. Raportowanie błędów systemu Windows są ściśle powiązane z stopnia akustyczne i językowe modele są uczone dla danego ustawienia regionalnego, dlatego możliwość dostosować model, który ma ustawienia regionalne jest ważne. Nasze najnowsze modeli 4.x wersji ujednoliconego są rozwiązaniem transkrypcji dokładności i opóźnienie. Uczone z użyciem dziesiątek tysięcy godzin dane akustyczne i miliardów leksykalne informacji ujednoliconej modele są najdokładniejszych modele na rynku także wywołania centrum danych.

### <a name="sentiment"></a>Opinia
Oceny, czy klient miał dobre środowisko jest jednym z najważniejszych obszarów analizy mowy, po zastosowaniu do obszaru roboczego wywołania. Nasze [interfejsu API usługi Batch transkrypcji](batch-transcription.md) oferuje analizę tonacji na wypowiedź. Można zagregować zbiór wartości uzyskane w ramach transkrypcję wywołanie, aby określić tonacji wywołanie zarówno klient, jak i agentów.

### <a name="silence-non-talk"></a>Wyciszenia (inne niż dyskretne)
nie jest niczym niezwykłym 35 procent z pomocy technicznej to tak zwany czas bez połączenia. Są sytuacje, w których bez dyskretne występuje: agentów wyszukanie wcześniejsze historia przypadku z klientem, agentów przy użyciu narzędzia, które zezwolić na dostęp klienta pulpitu i wykonywania funkcji, klienci "siedzieć" na przechowywania oczekiwanie na przekazanie i tak dalej. Bardzo ważne jest, można mierzyć, gdy wyciszenia odbywa się w wywołaniu, ponieważ istnieją liczby wrażliwość ważnego klienta, które występować wokół tego rodzaju scenariuszy i gdzie występują one w wywołaniu.

### <a name="translation"></a>{1&gt;Translacja&lt;1}
Niektóre firmy eksperymentują z udostępnianiem przetłumaczone transkrypcje z języków obcych interwencji obsługi tak, aby menedżerów może zrozumieć środowisko swoich klientów na całym świecie. Nasze [tłumaczenia](translation.md) możliwości jest taki sam. Firma Microsoft może dokonywać translacji audio, audio lub audio na tekst z dużą liczbą ustawień regionalnych.

### <a name="text-to-speech"></a>Zamiana tekstu na mowę
[Zamiana tekstu na mowę](text-to-speech.md) jest kolejnym obszarem istotne we wdrażaniu Boty, które wchodzić w interakcje z klientami. Typowe ścieżki polega na komunikuje się klient, głosu jest przetłumaczone na tekst, analizy tekstu na intencje, odpowiedź jest przekształcony oparty na celu rozpoznawanym i następnie zasób albo jest udostępniane klientom lub odpowiedzi głosowych syntetyzowany wygenerowany. Oczywiście to wszystko musi nastąpić szybko — dlatego opóźnienie jest ważnym elementem powodzenia tych systemów. 

Nasza end-to-end opóźnieniami jest bardzo niska, biorąc pod uwagę różne technologie związane, takie jak [mowy na tekst](speech-to-text.md), [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [platformy Bot Framework](https://dev.botframework.com/), [ Zamiana tekstu na mowę](text-to-speech.md). 

Nasz nowy głosy są nie do odróżnienia od głosów ludzi. Umożliwia się głosów Boty są naprawdę jego unikatowy osobowość.

### <a name="search"></a>Wyszukiwanie
Inny zszywanie analizy cel to zidentyfikowanie interakcje w przypadku, gdy wystąpi określone zdarzenie lub wystąpił środowisko. Zazwyczaj jest to wykonywane przy użyciu jednej z dwóch metod, albo wyszukaj ad-hoc, gdy użytkownik po prostu wpisuje frazę i zachowuje się system lub bardziej ustrukturyzowane zapytania, w którym analityk można utworzyć zestaw instrukcji logiczne, identyfikowanie scenariusza, w wywołaniu , a następnie każdego wywołania mogą być indeksowane względem tych zestawu zapytań. Przykład dobrą wyszukiwanie jest instrukcja wszechobecne zgodności "tego wywołania są zapisywane do celów jakości... "— jak wiele firm chce upewnij się, że ich agentami udostępniają to zastrzeżenie dla klientów przed wywołanie jest faktycznie zarejestrowane. Większość systemów analytics mają możliwość zachowania znalezione przez algorytmy /search zapytania — trend, zgodnie z tym raportów trendów ostatecznie to klient jest jedną z najważniejszych funkcji systemu analizy. Za pomocą [katalog usług Cognitive services](https://azure.microsoft.com/services/cognitive-services/directory/search/) rozwiązania typu end to end może znacznie rozszerzyć możliwości indeksowania i wyszukiwania.

### <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz
Ten obszar jest jednym z bardziej wymagająca aplikacji do analizowania i jedną, która oferuje korzyści ze stosowania sztucznej Inteligencji i uczenia Maszynowego. Jest to podstawowy scenariusz wywnioskowania zamiarach klienta. Dlaczego wywołuje klienta? Co to jest problem klienta? Dlaczego klient ma ujemną środowisko? Nasze [usługa analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) zapewnia zestawu analiz gotowych szybko uaktualniania rozwiązania typu end to end, aby wyodrębnić te ważne słów kluczowych lub fraz.

Przejdźmy teraz zapoznaj się z przetwarzania wsadowego i potoki w czasie rzeczywistym, rozpoznawania mowy nieco bardziej szczegółowo.

## <a name="batch-transcription-of-call-center-data"></a>Batch transkrypcji wywołanie centrum danych

Dla przepisywania zbiorcze audio opracowaliśmy [interfejsu API usługi Batch transkrypcji](batch-transcription.md). Interfejs API usługi Batch transkrypcji został opracowany, aby asynchronicznie transkrypcja dużych ilości danych audio. W odniesieniu do przepisywania wywołanie centrum danych, nasze rozwiązanie opiera się na tych filarów:

* **Dokładność**: Przy użyciu modeli ujednoliconej czwarty generacji firma Microsoft oferuje jakości najwyższych transkrypcji.
* **Opóźnienie**: Rozumiemy, że podczas ustalania transkrypcje zbiorcze, transkrypcje są potrzebne szybko. Zadania przekształcania są inicjowane za pośrednictwem [interfejsu API usługi Batch transkrypcji](batch-transcription.md) zostaną umieszczone w kolejce natychmiast po uruchomieniu zadania jest przeprowadzane szybciej niż w czasie rzeczywistym transkrypcji.
* **Bezpieczeństwo**: Rozumiemy, że połączenia mogą zawierać poufne dane. Miej pewność, że bezpieczeństwo to jeden z naszych najistotniejszych. Nasza usługa uzyskał ISO, SOC, HIPAA, PCI certyfikaty.

Biurem obsługi generują duże ilości danych audio codziennie. Jeśli firmy przechowuje dane telefonii w centralnej lokalizacji, takich jak usługi Azure Storage, możesz użyć [interfejsu API usługi Batch transkrypcji](batch-transcription.md) asynchronicznie żądania i otrzymywania transkrypcji.

Typowe rozwiązanie korzysta z tych usług:

* Usługi mowy platformy Azure są używane do transkrypcja mowy na tekst. Standardowa subskrypcja (tak) dla usług przetwarzania mowy jest wymagana do korzystania z interfejsu API usługi Batch transkrypcji. Bezpłatnej subskrypcji (F0) nie będzie działać.
* [Usługa Azure Storage](https://azure.microsoft.com/services/storage/) służy do przechowywania danych telefonii i zapisy zwracane przez interfejs API usługi Batch transkrypcji. To konto magazynu należy użyć powiadomienia, specjalnie pod kątem, po dodaniu nowych plików. Te powiadomienia są używane do wyzwalania procesu transkrypcji.
* [Usługa Azure Functions](https://docs.microsoft.com/azure/azure-functions/) służy do tworzenia sygnatury dostępu współdzielonego (SAS) identyfikator URI dla każdego rejestrowania i wyzwalacza żądania HTTP POST uruchomienia transkrypcji. Ponadto usługi Azure Functions jest używany do tworzenia żądań pobierania i usuwania transkrypcje przy użyciu interfejsu API usługi Batch transkrypcji.
* [Elementy Webhook](webhooks.md) umożliwia otrzymywanie powiadomień po zakończeniu transkrypcji.

Wewnętrznie użyto powyższych technologii do obsługi wywołań klienta firmy Microsoft w trybie wsadowym.
![Architektura usługi Batch](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Tekst w czasie rzeczywistym dla danych Centrum połączeń

Niektóre firmy muszą także rozmowy w czasie rzeczywistym. Tekst w czasie rzeczywistym może służyć do identyfikowania słów kluczowych i wyzwolić wyszukuje zawartość i zasoby dotyczą konwersacji, do monitorowania opinii, poprawić dostępność lub Podaj tłumaczenia dla klientów i agentów, którzy nie są natywne Prelegenci.

W przypadku scenariuszy, które wymagają transkrypcji w czasie rzeczywistym firma Microsoft zaleca używanie [zestaw SDK rozpoznawania mowy](speech-sdk.md). Obecnie usługa mowy na tekst jest dostępna w [ponad 20 języków](language-support.md), i zestawu SDK jest dostępny w C++, C#, Java, Python, Node.js, Objective-C i JavaScript. Przykłady są dostępne w każdym języku na [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Aby uzyskać najnowsze wiadomości i aktualizacji, zobacz [informacje o wersji](releasenotes.md).

Wewnętrznie użyto powyższych technologii do analizy w w czasie rzeczywistym telefonów od klientów firmy Microsoft, po ich wprowadzeniu.

![Architektura usługi Batch](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Word na IVRs

Usługi mowy można łatwo zintegrować w żadnym rozwiązaniu przy użyciu [zestaw SDK rozpoznawania mowy](speech-sdk.md) lub [interfejsu API REST](rest-apis.md). Jednak transkrypcji Centrum wywołanie może wymagać dodatkowych technologii. Zazwyczaj połączenie między usługą systemu IVR i na platformie Azure jest wymagana. Mimo że firma Microsoft nie oferuje takie składniki, chcielibyśmy opisują, jakie połączenie IVR pociąga za sobą.

Kilka produktów usługi IVR lub telefoniczne (na przykład Genesys lub AudioCodes) oferuje funkcje integracji, które mogą być wykorzystywane do ruchu przychodzącego i wychodzącego audio przekazywanie do usługi platformy Azure. Po prostu niestandardowych usługi Azure może zapewnić określonego interfejsu, aby zdefiniować sesji rozmowy telefonicznej (na przykład wywołać Start lub na końcu wywołania) i udostępnić interfejs API WebSocket, aby otrzymywać przychodzący strumień audio, który jest używany z usług przetwarzania mowy. Wychodzące odpowiedzi, takie jak transkrypcji konwersacji lub połączenia przy użyciu platformy Bot Framework może być przekształcony w usłudze zamiany tekstu na mowę firmy Microsoft i zwracane IVR do odtwarzania.

Inny scenariusz polega na bezpośrednie SIP integracji. Usługi systemu Azure łączy na serwerze SIP, w związku z tym wprowadzenie strumień przychodzący i wychodzący strumienia, który jest używany dla fazy mowy na tekst i zamiany tekstu na mowę. Połączyć się z SIP serwera są ofert komercyjne oprogramowanie, takie jak Ozeki SDK lub [zespoły wywoływania i interfejsu API spotkań](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (obecnie dostępna w wersji beta), te zostały zaprojektowane do obsługi tego rodzaju scenariusza dla połączeń audio.

## <a name="customize-existing-experiences"></a>Dostosowywanie istniejących funkcji

Usługi mowy platformy Azure działa dobrze z wbudowanych modeli, jednak warto dalsze dostosowywanie i dostrojenie środowiska dla danego produktu lub środowiska. Dostosowywanie opcje z zakresu od model akustyczny dostroić czcionki głosowe unikatowy dla Twojej marki. Po utworzeniu modelu niestandardowego służy za pomocą dowolnej z usług przetwarzania mowy platformy Azure, zarówno w czasie rzeczywistym, lub w trybie wsadowym.

| Usługa rozpoznawania mowy | Modelowanie | Opis |
|----------------|-------|-------------|
| Zamiany mowy na tekst | [Model akustyczny](how-to-customize-acoustic-models.md) | Utwórz niestandardowy model akustyczny dla aplikacji, narzędzi, lub urządzenia, które są używane w szczególności środowiskach, takich jak jadąca samochodem, lub w fabrykach, każdy z rejestrowania określonych warunków. Przykłady obejmują akcentowanych mowy, określonych szum lub przy użyciu określonych mikrofonu rejestrowania. |
| | [Model językowy](how-to-customize-language-model.md) | Utwórz model języka niestandardowego usprawniających transkrypcji słownika specyficzne dla branży i gramatyki, takie jak terminologia medycznych lub żargon IT. |
| | [Model wymowy](how-to-customize-pronunciation.md) | Za pomocą modelu niestandardowego Wymowa można zdefiniować fonetycznych formularza i wyświetlanie słowa lub terminy. Jest to przydatne do obsługi warunki niestandardowe, takie jak nazwy produktów lub akronimów. Wszystko, czego potrzebujesz do rozpoczęcia jest plikiem Wymowa — plik prosty txt. |
| Zamiana tekstu na mowę | [Czcionka głosowa](how-to-customize-voice-font.md) | Czcionki głosowe niestandardowe umożliwiają tworzenie mówiącą, jeden z rodzajem głosu dla Twojej marki. Trwa tylko niewielką ilość danych, aby rozpocząć pracę. Im więcej danych, możesz podać więcej naturalnego i przypominającej ludzką dźwiękowe czcionki głosowe. |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod jest dostępny w witrynie GitHub dla każdej z usług przetwarzania mowy platformy Azure. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie dźwięku z pliku lub strumienia, ciągłe i pojedynczego zrzutu ręcznego i Praca z modelami niestandardowych. Użyj poniższych linków, aby wyświetlić przykłady zestawu SDK i REST:

* [Przykłady tłumaczenia mowy na tekst i mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Przykłady transkrypcji dla usługi Batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw Speech Devices SDK](speech-devices-sdk.md)
* [INTERFEJS API REST: Zamiany mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
* [INTERFEJS API REST: Dostosowywanie i transkrypcji usługi Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Bezpłatnie Uzyskaj klucz subskrypcji usług przetwarzania mowy](get-started.md)
