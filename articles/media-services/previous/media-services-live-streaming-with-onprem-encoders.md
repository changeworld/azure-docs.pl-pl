---
title: Strumień na żywo za pomocą koderów lokalnych, które tworzą strumienie wielokrotnej szybkości transmisji bitów - Azure | Dokumentacja firmy Microsoft
description: 'W tym temacie opisano konfigurowanie kanału, który odbiera strumień na żywo różnych szybkościach transmisji bitów z kodera lokalnymi. Strumień następnie mogą być dostarczane klienta odtwarzanie aplikacji za pośrednictwem jednego lub więcej punktów końcowych przesyłania strumieniowego przy użyciu jednej z następujących protokołów adaptacyjną: DASH HLS, Smooth Streaming.'
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/12/2017
ms.author: cenkd;juliako
ms.openlocfilehash: d08ac9f2cbdf98493b3132fa9dd3a3e973576451
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098821"
---
# <a name="live-streaming-with-on-premises-encoders-that-create-multi-bitrate-streams"></a>Transmisja strumieniowa za pomocą koderów lokalnych, które tworzą strumienie o różnych szybkościach transmisji bitów na żywo

> [!NOTE]
> Uruchamianie kanałów na żywo 12 maja 2018 będzie już obsługi strumienia transportowego RTP/MPEG-2 protokołu pozyskiwania. Przeprowadź migrację z RTP/MPEG-2 RTMP lub pofragmentowany MP4 (Smooth Streaming) pozyskiwania protokołów.

## <a name="overview"></a>Przegląd
W usłudze Azure Media Services *kanału* reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Kanał otrzymuje na żywo wejściowych strumieni w jeden z dwóch sposobów:

* Koder na żywo lokalnymi wysyła wielokrotnej szybkości transmisji bitów RTMP lub Smooth Streaming (pofragmentowany MP4) strumienia do kanału, który nie jest włączony do przeprowadzania kodowania na żywo w usłudze Media Services. Pozyskiwane strumienie są przekazywane za pośrednictwem kanałów bez dalszego przetwarzania. Ta metoda jest wywoływana *przekazywanego*. Koder na żywo może także wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie jest włączona dla kodowanie na żywo, ale nie jest to zalecane. Usługi Media Services dostarcza strumień do klientów, którzy żądają go.

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczne rozwiązanie wygaśnięcia przesyłania strumieniowego.


* Na lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do kanału, który jest skonfigurowany do przeprowadzania kodowania na żywo w usłudze Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany MP4). Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia pojedynczej szybkości transmisji bitów do wielokrotnej szybkości transmisji bitów (adaptacyjnej) strumienia wideo. Usługi Media Services dostarcza strumień do klientów, którzy żądają go.

Począwszy od Media Services 2.10 wersji, podczas tworzenia kanału, można określić sposób kanału do odbierania strumień wejściowy. Można również określić, czy kanał, do przeprowadzania kodowania na żywo strumienia. Dostępne są dwie opcje:

* **Przekazuj**: podać tę wartość, jeśli planujesz używać kodera na żywo lokalnego ma strumień wielokrotnej szybkości transmisji bitów (strumień przekazujące) jako dane wyjściowe. W takim przypadku strumienia przychodzącego przechodzi do danych wyjściowych bez żadnych kodowania. Jest to zachowanie kanału przed wprowadzeniem 2.10. Ten artykuł zawiera szczegółowe informacje o pracy z kanałami tego typu.
* **Kodowanie na żywo**: Wybierz tę wartość, jeśli planujesz używać usługi Media Services do kodowania transmisji strumieniowej na żywo pojedynczej szybkości transmisji bitów w strumieniu wielokrotnej szybkości transmisji bitów. Pozostawienie kanału kodowania na żywo w **systemem** stanu ponosi rozliczeń opłat. Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu zdarzenie transmisji strumieniowej na żywo, aby zapobiec zmianom bardzo co godzinę. Usługi Media Services dostarcza strumień do klientów, którzy żądają go.

> [!NOTE]
> W tym artykule opisano atrybuty kanałów, które nie są włączone kodowanie na żywo. Aby uzyskać informacje dotyczące pracy z kanałami obsługującymi kodowanie na żywo, zobacz [transmisja strumieniowa na żywo przy użyciu usługi Azure Media Services do tworzenia strumieni o wielokrotnej szybkości transmisji bitów](media-services-manage-live-encoder-enabled-channels.md).
>
>Informacji o koderów lokalnych zalecane znajduje się w temacie [zalecane koderów lokalnych](media-services-recommended-encoders.md).

Poniższy diagram przedstawia transmisji strumieniowej na żywo przepływu pracy, który używa na lokalny koder na żywo RTMP o różnych szybkościach transmisji bitów lub pofragmentowany MP4 (Smooth Streaming) strumieni jako dane wyjściowe.

![Przepływ pracy na żywo][live-overview]

## <a id="scenario"></a>Typowy scenariusz transmisji strumieniowej na żywo
W poniższych krokach opisano zadania związane z tworzeniem typowych aplikacji transmisji strumieniowej na żywo.

1. Podłącz kamerę wideo do komputera. Uruchom i skonfiguruj koder na żywo lokalnego, który ma RTMP o różnych szybkościach transmisji bitów lub pofragmentowany MP4 (Smooth Streaming) strumień jako dane wyjściowe. Aby uzyskać więcej informacji, zobacz temat [Obsługa protokołu RTMP i kodery na żywo w usłudze Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=532824).

    Ten krok można również wykonywać po utworzeniu kanału.
2. Tworzenie i uruchamianie kanału.

3. Adres URL pozyskiwania, pobrać kanału.

    Koder na żywo używa adresu URL pozyskiwania do wysyłania strumienia do kanału.
4. Pobiera adres URL podglądu kanału.

    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
5. Utwórz program.

    Korzystając z portalu Azure, tworzenia program tworzy także zasób.

    Gdy używasz zestawu SDK .NET lub REST, należy utworzenie elementu zawartości i określanie użycia tego zasobu, podczas tworzenia programu.
6. Publikowanie elementu zawartości, który został skojarzony z programu.   

    >[!NOTE]
    >Po utworzeniu konta usługi Azure Media Services **domyślne** punktu końcowego przesyłania strumieniowego jest dodany do Twojego konta w **zatrzymane** stanu. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

7. Uruchom program, gdy wszystko jest gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji.

8. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.

9. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.

10. Usuń program (i opcjonalnie można również usunąć zasób).     

## <a id="channel"></a>Opis kanału i jej powiązane składniki
### <a id="channel_input"></a>Kanał danych wejściowych (pozyskiwania) konfiguracji
#### <a id="ingest_protocols"></a>Pozyskiwania protokołu przesyłania strumieniowego
Usługa Media Services obsługuje wprowadzania źródeł danych na żywo przy użyciu MP4 o różnych szybkościach transmisji bitów fragmentacji i RTMP o różnych szybkościach transmisji bitów jako strumieniowe protokołów. Gdy pozyskiwania RTMP przesyłania strumieniowego protokół jest zaznaczone, pozyskiwania dwa punkty końcowe (wejścia) są tworzone dla kanału:

* **Podstawowy adres URL**: Określa w pełni kwalifikowany adres URL RTMP podstawowego kanału pozyskiwania punktu końcowego.
* **Adres URL dodatkowej** (opcjonalnie): Określa w pełni kwalifikowany adres URL z kanału dodatkowej RTMP pozyskiwania punktu końcowego.

Za pomocą dodatkowej adres URL należy poprawić trwałości i odporność na uszkodzenia sieci pozyskiwania strumienia (a także kodera trybu failover i odporność na uszkodzenia), zwłaszcza w przypadku następujących scenariuszy:

- Koder pojedynczego o podwójnej precyzji Wypychanie do głównych i dodatkowych adresów URL:

    Głównym celem tego scenariusza jest zapewnienie większą elastyczność wahań sieci i tłumienie. Niektóre kodery RTMP nie obsługują również zakończy połączenie sieci. W przypadku rozłączenia sieciowej kodera może zatrzymać kodowanie i następnie wysyłają buforowane dane w przypadku ponownego połączenia. Powoduje to przerw i utraty danych. Odłącza sieci może się zdarzyć z powodu nieprawidłowych sieci lub konserwacji na stronie platformy Azure. Adresy URL podstawowe i pomocnicze pozwalają ograniczyć problemy z siecią i kontrolowany przez proces uaktualniania. Zawsze rozłączenia zaplanowane sieciowej sytuacji usługi Media Services zarządza podstawowego i pomocniczego rozłącza oraz zapewnia opóźnione rozłączyć między nimi. Kodery miał czas przechowywać wysyłanie danych, a następnie ponownie. Kolejność zakończy połączenie może być losowych, ale zawsze będzie opóźnienia między podstawowe i pomocnicze lub pomocniczym/podstawowych adresów URL. W tym scenariuszu koder nadal jest pojedynczym punktem awarii.

- Wiele koderów za pomocą kodera każdego Wypychanie do dedykowanych punktu:

    W tym scenariuszu zawiera zarówno koder i wysyła strumień nadmiarowości. W tym scenariuszu encoder1 Wypychanie do podstawowego adresu URL i encoder2 Wypychanie do dodatkowej adresu URL. W przypadku awarii kodera innych koder można zachować wysyłanie danych. Nadmiarowość danych mogą być obsługiwane, ponieważ usługi Media Services nie rozłączy głównych i dodatkowych adresów URL w tym samym czasie. W tym scenariuszu założono, że koderów są synchronizowane czasu i podaj dokładnie tych samych danych.  

- Wiele koderów o podwójnej precyzji Wypychanie do głównych i dodatkowych adresów URL:

    W tym scenariuszu oba koderów wypychanie danych do głównych i dodatkowych adresów URL. Zapewnia najlepszą niezawodność i odporność na uszkodzenia, jak również nadmiarowość danych. W tym scenariuszu może tolerować zarówno awariami kodera i rozłącza, nawet wtedy, gdy jeden koder przestanie działać. Przyjęto założenie, że koderów są synchronizowane czasu i dokładnie tych samych danych.  

Informacje o RTMP koderów na żywo, zobacz [Obsługa protokołu RTMP usługi multimediów Azure i kodery na żywo](http://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Pozyskiwania adresów URL (punkty końcowe)
Kanał zawiera ono wejściowy punkt końcowy (adres URL pozyskiwania) określenie w kodera na żywo, więc może wypchnąć koder strumieni dla kanałów.   

Adresy URL pozyskiwania można uzyskać po utworzeniu kanału. Umożliwia uzyskiwanie tych adresów URL, kanał nie muszą być **systemem** stanu. Gdy wszystko jest gotowe do uruchomienia, przekazywanie danych do kanału, musi należeć do kanału **systemem** stanu. Po uruchomieniu kanału wprowadzania danych można wyświetlić podgląd strumienia za pomocą adresu URL w wersji zapoznawczej.

Istnieje opcja wprowadzania pofragmentowane MP4 (Smooth Streaming) strumień na żywo za pośrednictwem połączenia SSL. Pozyskiwanie za pośrednictwem protokołu SSL, upewnij się, że aktualizacja adresu URL pozyskiwania HTTPS. Nie można obecnie pozyskiwania RTMP, za pośrednictwem protokołu SSL.

#### <a id="keyframe_interval"></a>Interwał klatki kluczowej
Podczas korzystania z na lokalny koder na żywo można wygenerować strumień o wielokrotnej szybkości transmisji bitów, interwał klatki kluczowej określa czas trwania grupy obrazów (GOP) jako używane przez tego kodera. Gdy kanał otrzyma tego strumienia przychodzącego, można dostarczyć transmisji strumieniowej na żywo do klienta odtwarzanie aplikacji w jednym z następujących formatów: Smooth Streaming, dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (DASH) i HTTP Live Streaming (HLS). Podczas prowadzenia transmisji strumieniowych na żywo, HLS zawsze jest dostarczana dynamicznie. Domyślnie usługi Media Services automatycznie oblicza stosunek pakowania segment HLS (fragmenty według segmentu) na podstawie interwału klatki kluczowej, które zostały odebrane z kodera na żywo.

W poniższej tabeli przedstawiono sposób obliczania segmentu czas trwania:

| Interwał klatki kluczowej | Współczynnik opakowania segment HLS (FragmentsPerSegment) | Przykład |
| --- | --- | --- |
| Mniej niż 3 sekundy |3:1 |W przypadku KeyFrameInterval (lub GOP) 2 sekundy, współczynnik opakowania segmentu HLS domyślne jest 3-1. Spowoduje to utworzenie segment HLS 6 sekundę. |
| 3 do 5 sekund |2:1 |W przypadku KeyFrameInterval (lub GOP) 4 sekundy, współczynnik opakowania segmentu HLS domyślny jest 2: 1. Spowoduje to utworzenie segmentu HLS 8 sekundy. |
| Większa niż 5 sekund |1:1 |W przypadku KeyFrameInterval (lub GOP) sekund 6, współczynnik opakowania segmentu HLS domyślny jest 1 do 1. Spowoduje to utworzenie segment HLS 6 sekundę. |

Konfigurowanie kanału danych wyjściowych i ustawiając FragmentsPerSegment na ChannelOutputHls, można zmienić stosunek fragmenty na segment.

Możesz również zmienić wartość interwału klatki kluczowej przez ustawienie właściwości KeyFrameInterval na ChanneInput. Jeśli ustawisz jawnie KeyFrameInterval, HLS segmentu stosunek pakowania, obliczonego FragmentsPerSegment za pomocą reguł opisanych powyżej.  

Jeśli musisz jawnie ustawić KeyFrameInterval i FragmentsPerSegment, Media Services używa wartości, które można ustawić.

#### <a name="allowed-ip-addresses"></a>Dozwolonych adresów IP
Można zdefiniować adresy IP, które mogą publikować pliki wideo w tym kanale. Dozwolonych adresów IP można określić jedną z następujących czynności:

* Pojedynczy adres IP (np. 10.0.0.1)
* Zakres IP, który korzysta z adresu IP i maski podsieci CIDR (na przykład 10.0.0.1/22)
* Zakres IP, który korzysta z adresu IP i maski podsieci dziesiętną kropkami (na przykład 10.0.0.1(255.255.252.0))

Adresy IP nie są określone, jeśli nie ma żadnych definicji reguły, żaden adres IP nie jest dozwolone. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.

### <a name="channel-preview"></a>Podgląd kanału
#### <a name="preview-urls"></a>Adresy URL w wersji zapoznawczej
Kanały zapewniają punktu końcowego podglądu (adres URL w wersji zapoznawczej), używanej do wyświetlania i weryfikowania strumienia przed dalszego przetwarzania i dostarczania.

Po utworzeniu kanału możesz uzyskać adres URL w wersji zapoznawczej. Aby móc uzyskać adres URL, kanał nie muszą być w **systemem** stanu. Po uruchomieniu kanału wprowadzania danych można wyświetlić podgląd strumienia.

Obecnie strumienia w wersji zapoznawczej mogą być dostarczane tylko w pofragmentowane MP4 (Smooth Streaming) formacie, bez względu na określony typ danych wejściowych. Można użyć [Smooth Streaming Monitor kondycji](http://playready.directtaps.net/smoothstreaming/) player do testowania smooth stream. Umożliwia także player, który znajduje się w portalu Azure, aby wyświetlić strumienia.

#### <a name="allowed-ip-addresses"></a>Dozwolonych adresów IP
Można zdefiniować adresy IP, które mogą łączyć się z punktem końcowym w wersji zapoznawczej. Jeżeli nie określono żadnych adresów IP, adres IP jest dozwolone. Dozwolonych adresów IP można określić jedną z następujących czynności:

* Pojedynczy adres IP (np. 10.0.0.1)
* Zakres IP, który korzysta z adresu IP i maski podsieci CIDR (na przykład 10.0.0.1/22)
* Zakres IP, który korzysta z adresu IP i maski podsieci dziesiętną kropkami (na przykład 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Kanał danych wyjściowych
Informacje dla kanału danych wyjściowych, zobacz [interwał klatki kluczowej](#keyframe_interval) sekcji.

### <a name="channel-managed-programs"></a>Programy zarządzane przez kanał
Kanał jest skojarzony z programami, które umożliwiają kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami. Relacja kanału i programu jest podobny do tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.

Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie długości **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwum określa również dostępny maksymalną liczbę klientów można wyszukiwać Wstecz w czasie z bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z zasobem służący do przechowywania zawartości przesyłanej strumieniowo. Zasób jest mapowana do bloku kontenera obiektów blob na koncie magazynu Azure, a pliki w elementach zawartości są przechowywane jako obiekty BLOB w tym kontenerze. Aby opublikować program, więc klienci mogą wyświetlić strumienia, należy utworzyć Lokalizator OnDemand dla skojarzonego elementu zawartości. Lokalizator ten służy do tworzenia adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Można publikowanie i archiwizację różnych części wydarzenia, zgodnie z potrzebami. Na przykład załóżmy, że z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany do archiwizacji sześciu godzin zdarzenia, ale program nie został opublikowany. Inny program jest ustawiony w celu archiwizacji 10 minut, a ten program zostanie opublikowany.

Nie należy używać ponownie istniejących programów dla nowych zdarzeń. Zamiast tego należy utworzyć nowy program dla każdego zdarzenia. Uruchom program, gdy wszystko jest gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.

Aby usunąć zarchiwizowaną zawartość, Zatrzymaj i Usuń program, a następnie usuń skojarzony element zawartości. Nie można usunąć elementu zawartości, jeśli program używa jej. Najpierw należy usunąć program.

Nawet po zatrzymaniu i usunięciu program, użytkownicy mogą strumieniowo zarchiwizowaną zawartość wideo na żądanie, dopóki zasób nie zostanie usunięty. Jeśli chcesz zachować zarchiwizowaną zawartość, ale nie będzie on dostępny do przesyłania strumieniowego, Usuń Lokalizator przesyłania strumieniowego.

## <a id="states"></a>Stany kanału i rozliczeń
Możliwe wartości dla bieżącego stanu kanału:

* **Zatrzymano**: jest to stan początkowy kanału po jego utworzeniu. W tym stanie właściwości kanału mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.
* **Uruchamianie**: kanał jest uruchamiana. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, kanał zwraca **zatrzymane** stanu.
* **Uruchomiona**: kanału może przetwarzać strumienie na żywo.
* **Zatrzymywanie**: zatrzymaniu kanału. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.
* **Usuwanie**: kanał jest usuwany. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.

W tabeli poniżej pokazano, jak stany kanału przekładają się na naliczanie opłat.

| Stan kanału | Wskaźniki w interfejsie użytkownika portalu | Naliczanie opłat? |
| --- | --- | --- | --- |
| **Uruchamianie** |**Uruchamianie** |Nie (stan przejściowy) |
| **Uruchomiona** |**Gotowe** (nie uruchomione programy)<p><p>lub<p>**Przesyłanie strumieniowe** (co najmniej jeden uruchomiony program) |Yes |
| **Zatrzymywanie** |**Zatrzymywanie** |Nie (stan przejściowy) |
| **Zatrzymana** |**Zatrzymana** |Nie |

## <a id="cc_and_ads"></a>Zamknięte wstawiania podpisów i usługi ad
Poniższa tabela przedstawia obsługiwane standardy dla zamkniętego wstawiania podpisów i usługi ad.

| Standardowa (Standard) | Uwagi |
| --- | --- |
| CEA 708 i EIA 608 (708/608) |CEA 708 i EIA 608 są kodowane standardów dla Stanów Zjednoczonych i Kanady.<p><p>Obecnie podpisów jest obsługiwana tylko wtedy, gdy w zakodowanym strumień wejściowy. Należy używać kodera na żywo nośnika, który można wstawić 608 lub 708 podpisów zakodowanego strumienia, który jest wysyłany do usługi Media Services. Usługi Media Services dostarcza zawartość z napisami wstawiony do widzów. |
| TTML wewnątrz .ismt (Smooth Streaming ścieżek tekstu) |Dynamiczne tworzenie pakietów usługi Media Services umożliwia klientom przesyłanie strumieniowe zawartości w dowolnym z następujących formatów: DASH, HLS lub Smooth Streaming. Jednak jeśli użytkownik pozyskiwania pofragmentowany plik MP4 (Smooth Streaming) z napisami wewnątrz .ismt (Smooth Streaming ścieżek tekstu), można dostarczyć strumienia tylko Smooth Streaming klientów. |
| SCTE-35 |SCTE 35 to cyfrowy system sygnalizowania, który służy do wstawiania reklam sygnalizacji. Podrzędny odbiornikami umożliwia sygnał splice reklamy w strumieniu wyznaczonym czasie. SCTE 35 należy wysłać jako rozrzedzony śledzenie w strumieniu wejściowym.<p><p>Obecnie obsługiwane tylko strumień wejściowy formatu powoduje, że sygnały ad jest pofragmentowana. MP4 (Smooth Streaming). Jedynym obsługiwanym wyjściowy format jest także Smooth Streaming. |

## <a id="considerations"></a>Zagadnienia dotyczące
Jeśli używasz na lokalny koder na żywo można wysłać strumień o wielokrotnej szybkości transmisji bitów do kanału, zastosuj następujące ograniczenia:

* Upewnij się, że masz wystarczające wolne łączności z Internetem wysyłania danych do punktów pozyskiwania.
* Adres URL pozyskiwania, przy użyciu dodatkowej wymaga dodatkowe ograniczenie użycia przepustowości.
* Przychodzący strumień wielokrotnej szybkości transmisji bitów może mieć maksymalnie 10 poziomy jakości wideo (warstwy) i maksymalnie 5 ścieżki audio.
* Najwyższy średniej szybkości transmisji bitów do dowolnego poziomu jakości wideo powinno być niższe niż 10 MB/s.
* Wartości zagregowanej średniej szybkości transmisji bitów dla wszystkich strumieni audio i wideo powinno być niższe niż 25 MB/s.
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Może obsługiwać pojedynczej szybkości transmisji bitów w kanału. Jednak ponieważ kanału nie przetwarza strumień, aplikacje klienckie otrzymają również o pojedynczej szybkości transmisji. (Nie zaleca się tę opcję.)

Oto inne kwestie związane z pracą z kanałów i powiązanych składników:

* Za każdym razem, gdy skonfigurujesz kodera na żywo, wywołaj **zresetować** metody w kanale. Zanim je zresetujesz kanału, należy zatrzymać program. Po zresetowaniu kanału, ponownie uruchom program.

  > [!NOTE]
  > Po ponownym uruchomieniu program, należy ją skojarzyć z nowego elementu zawartości i utworzyć nowy. 
  
* Kanał można zatrzymać tylko wtedy, gdy jest on **systemem** stanu i wszystkie programy na kanale zostały zatrzymane.
* Domyślnie tylko pięć kanałów można dodać do konta usługi Media Services. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).
* Są rozliczane tylko wtedy, gdy kanał w **systemem** stanu. Aby uzyskać więcej informacji, zobacz [kanału stanów i rozliczeń](media-services-live-streaming-with-onprem-encoders.md#states) sekcji.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Opinia
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Koderów lokalnych zalecane](media-services-recommended-encoders.md)

[Azure Media Services pofragmentowane MP4 życie pozyskiwania specyfikacji](media-services-fmp4-live-ingest-overview.md)

[Omówienie usługi Azure Media Services i typowe scenariusze](media-services-overview.md)

[Pojęcia dotyczące usługi Media Services](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
