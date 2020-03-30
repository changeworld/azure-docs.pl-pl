---
title: Przesyłaj strumieniowo na żywo za pomocą koderów lokalnych, które tworzą strumienie o wielu szybkościach transmisji bitów — Azure | Dokumenty firmy Microsoft
description: W tym temacie opisano sposób konfigurowania kanału, który odbiera strumień na żywo o wielu szybkościach transmisji bitów z kodera lokalnego.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f6366f162cb09898b694b14440718401c57c0adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251039"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Praca z kanałami odbierania wielu transmisji bitów na żywo z enkoderów lokalnych

> [!NOTE]
> Od 12 maja 2018 r. kanały na żywo nie będą już obsługiwać protokołu pozyskiwania strumienia transportu RTP/MPEG-2. Przemiń z RTP/MPEG-2 do RTMP lub pofragmentowanych protokołów pozyskiwania MP4 (Smooth Streaming).

## <a name="overview"></a>Omówienie
W usłudze Azure Media Services *kanał* reprezentuje potok przetwarzania zawartości przesyłania strumieniowego na żywo. Kanał odbiera transmisje wejściowe na żywo na jeden z dwóch sposobów:

* Lokalny koder na żywo wysyła strumień RTMP o wielu szybkościach transmisji bitów lub płynnego przesyłania strumieniowego (pofragmentowany plik MP4) do kanału, który nie jest włączony do wykonywania kodowania na żywo za pomocą usługi Media Services. Pozyskane strumienie przechodzą przez kanały bez dalszego przetwarzania. Ta metoda jest nazywana *pass-through*. Koder na żywo można również wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie jest włączony do kodowania na żywo, ale nie zaleca się tego. Usługa Media Services dostarcza strumień do klientów, którzy go zażądają.

  > [!NOTE]
  > Korzystanie z metody pass-through jest najbardziej ekonomicznym sposobem przesyłania strumieniowego na żywo.


* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do kanału, który jest włączony do wykonywania kodowania na żywo za pomocą usługi Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany format MP4). Następnie kanał wykonuje kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do strumienia wideo o wielu szybkościach transmisji bitów (adaptacyjnej). Usługa Media Services dostarcza strumień do klientów, którzy go zażądają.

Począwszy od wersji Media Services 2.10, podczas tworzenia kanału, można określić, jak chcesz, aby kanał odbierał strumień wejściowy. Można również określić, czy kanał ma wykonywać kodowanie na żywo strumienia. Dostępne są dwie opcje:

* **Pass Through:** Określ tę wartość, jeśli planujesz użyć lokalnego kodera na żywo, który ma strumień o wielu szybkościach transmisji bitów (strumień przekazu) jako dane wyjściowe. W takim przypadku przychodzące strumienia przechodzi do danych wyjściowych bez kodowania. Jest to zachowanie kanału przed wydaniem 2.10. W tym artykule podano szczegółowe informacje na temat pracy z kanałami tego typu.
* **Kodowanie na żywo:** Wybierz tę wartość, jeśli usługa Media Services ma być używana do kodowania strumienia na żywo o pojedynczej szybkości transmisji bitów do strumienia o wielu szybkościach transmisji bitów. Pozostawienie kanału kodowania na żywo w stanie **Uruchomione** wiąże się z opłatami rozliczeniowymi. Zalecamy natychmiastowe zatrzymanie uruchamiania kanałów po zakończeniu transmisji na żywo, aby uniknąć dodatkowych opłat godzinowych. Usługa Media Services dostarcza strumień do klientów, którzy go zażądają.

> [!NOTE]
> W tym artykule omówiono atrybuty kanałów, które nie są włączone do wykonywania kodowania na żywo. Aby uzyskać informacje dotyczące pracy z kanałami, które są włączone do wykonywania kodowania na żywo, zobacz [Przesyłanie strumieniowe na żywo przy użyciu usługi Azure Media Services do tworzenia strumieni o wielu szybkościach transmisji bitów](media-services-manage-live-encoder-enabled-channels.md).
>
>Aby uzyskać informacje na temat zalecanych koderów lokalnych, zobacz [Zalecane kodery lokalne](media-services-recommended-encoders.md).

Na poniższym diagramie przedstawiono przepływ pracy przesyłania strumieniowego na żywo, który używa lokalnego kodera na żywo, aby mieć wiele bitrate RTMP lub pofragmentowane strumienie MP4 (Płynne przesyłanie strumieniowe) jako dane wyjściowe.

![Przepływ pracy na żywo][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Typowy scenariusz transmisji na żywo
W poniższych krokach opisano zadania związane z tworzeniem typowych aplikacji do przesyłania strumieniowego na żywo.

1. Podłącz kamerę wideo do komputera. Uruchom i skonfiguruj lokalny koder na żywo, który ma strumień RTMP o wielu szybkościach transmisji bitów lub pofragmentowany strumień MP4 (Smooth Streaming) jako wyjście. Aby uzyskać więcej informacji, zobacz temat [Obsługa protokołu RTMP i kodery na żywo w usłudze Azure Media Services](https://go.microsoft.com/fwlink/?LinkId=532824).

    Ten krok można również wykonać po utworzeniu kanału.
2. Tworzenie i uruchamianie kanału.

3. Pobierz adres URL pozyskiwania kanału.

    Koder na żywo używa adresu URL pozyskiwania do wysyłania strumienia do kanału.
4. Pobierz adres URL podglądu kanału.

    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
5. Tworzenie programu.

    Podczas korzystania z witryny Azure portal, tworzenie programu również tworzy zasób.

    Podczas korzystania z pliku .NET SDK lub REST należy utworzyć zasób i określić, aby używać tego zasobu podczas tworzenia programu.
6. Opublikuj zasób skojarzony z programem.   

    >[!NOTE]
    >Po utworzeniu konta usługi Azure Media Services **domyślny** punkt końcowy przesyłania strumieniowego jest dodawany do twojego konta w stanie **Zatrzymane.** Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

7. Uruchom program, gdy będziesz gotowy do rozpoczęcia przesyłania strumieniowego i archiwizacji.

8. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.

9. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.

10. Usuń program (i opcjonalnie usuń zasób).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Opis kanału i powiązanych z nim składników
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Konfiguracje wprowadzania kanału (pozyskiwania)
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Połknienie protokołu przesyłania strumieniowego
Usługa Media Services obsługuje pozyskiwania kanałów na żywo przy użyciu wielostytułowego pofragmentowanego mp4 i wielu bitów RTMP jako protokołów przesyłania strumieniowego. Po wybraniu protokołu przesyłania strumieniowego pozyskiwania RTMP dla kanału tworzone są dwa punkty końcowe pozyskiwania (wejściowe):

* **Podstawowy adres URL:** Określa w pełni kwalifikowany adres URL podstawowego punktu końcowego pozyskiwania RTMP kanału.
* **Pomocniczy adres URL** (opcjonalnie): określa w pełni kwalifikowany adres URL pomocniczego punktu końcowego pozyskiwania RTMP kanału.

Użyj pomocniczego adresu URL, jeśli chcesz poprawić trwałość i odporność na uszkodzenia strumienia pozyskiwania (a także kodera pracy awaryjnej i odporności na uszkodzenia), szczególnie w następujących scenariuszach:

- Pojedynczy koder dwukrotnie przesuwany do podstawowych i pomocniczych adresów URL:

    Głównym celem tego scenariusza jest zapewnienie większej odporności na wahania sieci i trema. Niektóre kodery RTMP nie obsługują sieci rozłącza dobrze. Gdy nastąpi rozłączenie sieci, koder może zatrzymać kodowanie, a następnie nie wysłać buforowanych danych, gdy nastąpi ponowne połączenie. Powoduje to nieciągłości i utratę danych. Rozłączenia sieci mogą się zdarzyć z powodu złej sieci lub konserwacji po stronie platformy Azure. Podstawowe/pomocnicze adresy URL zmniejszają problemy z siecią i zapewniają kontrolowany proces uaktualniania. Za każdym razem, gdy nastąpi zaplanowane rozłączenie sieci, usługa Media Services zarządza rozłączanymi podstawowymi i pomocniczymi i zapewnia opóźnione rozłączenie między nimi. Kodery mają czas, aby zachować wysyłanie danych i ponownie połączyć. Kolejność rozłączenia może być losowa, ale zawsze będzie opóźnienie między podstawowymi/pomocniczymi lub pomocniczymi/podstawowymi adresami URL. W tym scenariuszu koder jest nadal pojedynczy punkt awarii.

- Wiele koderów, z każdym koderem wypychanym do dedykowanego punktu:

    W tym scenariuszu zapewnia nadmiarowość znawarowania i pozyskiwania. W tym scenariuszu koder1 wypycha do podstawowego adresu URL, a koder2 wypycha do pomocniczego adresu URL. Gdy koder ulegnie awarii, drugi koder może zachować wysyłanie danych. Nadmiarowość danych może być zachowana, ponieważ usługa Media Services nie rozłącza jednocześnie podstawowych i pomocniczych adresów URL. W tym scenariuszu zakłada się, że kodery są synchronizowane czas i zapewniają dokładnie te same dane.  

- Wiele koderów podwójnego wypychania do podstawowych i pomocniczych adresów URL:

    W tym scenariuszu oba kodery wypychają dane zarówno do podstawowych, jak i pomocniczych adresów URL. Zapewnia to najlepszą niezawodność i odporność na uszkodzenia, a także nadmiarowość danych. W tym scenariuszu można tolerować zarówno błędy kodera i rozłącza, nawet jeśli jeden koder przestaje działać. Zakłada, że kodery są synchronizowane czas i zapewniają dokładnie te same dane.  

Aby uzyskać informacje na temat koderów na żywo RTMP, zobacz [Pomoc techniczna rtmp usługi Azure Media Services i kodery na żywo](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Pochwyć adresy URL (punkty końcowe)
Kanał zapewnia wejściowy punkt końcowy (adres URL pozyskiwania), który można określić w koderze na żywo, dzięki czemu koder może wypychać strumienie do kanałów.   

Podczas tworzenia kanału można uzyskać adresy URL pozyskiwania. Aby uzyskać te adresy URL, kanał nie musi być w stanie **uruchamiania.** Gdy będziesz gotowy, aby rozpocząć wypychanie danych do kanału, kanał musi być w stanie **uruchomiony.** Po rozpoczęciu pozyskiwania danych przez kanał można wyświetlić podgląd strumienia za pomocą adresu URL wersji zapoznawczej.

Masz możliwość pozyskiwania pofragmentowanego strumienia na żywo MP4 (Smooth Streaming) za połączenieM SSL. Aby łowić ssl, upewnij się, że można zaktualizować adres URL pozyskiwania do protokołu HTTPS. Obecnie nie można łykać RTMP przez SSL.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Interwał klatki kluczowej
Podczas generowania strumienia z wieloma szybkościami transmisji bitów lokalnie używany jest interwał klatek kluczowych określający czas trwania grupy obrazów (GOP) używany przez ten koder zewnętrzny. Po otrzymaniu tego kanału przychodzącego strumienia, można dostarczyć strumień na żywo do aplikacji odtwarzania klienta w dowolnym z następujących formatów: Płynne przesyłanie strumieniowe, Dynamiczne adaptacyjne przesyłanie strumieniowe przez HTTP (DASH) i HTTP Live Streaming (HLS). Podczas przesyłania strumieniowego na żywo hls jest zawsze pakowany dynamicznie. Domyślnie usługa Media Services automatycznie oblicza współczynnik pakowania segmentu HLS (fragmenty na segment) na podstawie interwału klatki kluczowej otrzymanego z kodera na żywo.

W poniższej tabeli przedstawiono sposób obliczania czasu trwania segmentu:

| Interwał klatki kluczowej | Stosunek opakowań segmentu HLS (FragmentsPerSegment) | Przykład |
| --- | --- | --- |
| Mniej niż lub równych 3 sekundom |3:1 |Jeśli KeyFrameInterval (lub GOP) wynosi 2 sekundy, domyślny współczynnik pakowania segmentu HLS wynosi od 3 do 1. Tworzy to 6-sekundowy segment HLS. |
| 3 do 5 sekund |2:1 |Jeśli KeyFrameInterval (lub GOP) wynosi 4 sekundy, domyślny współczynnik pakowania segmentu HLS wynosi od 2 do 1. Tworzy to 8-sekundowy segment HLS. |
| Większa niż 5 sekund |1:1 |Jeśli KeyFrameInterval (lub GOP) wynosi 6 sekund, domyślny współczynnik pakowania segmentu HLS wynosi od 1 do 1. Tworzy to 6-sekundowy segment HLS. |

Można zmienić współczynnik fragmentów na segment, konfigurując dane wyjściowe kanału i ustawiając FragmentsPerSegment na ChannelOutputHls.

Można również zmienić wartość interwału klatki kluczowej, ustawiając właściwość KeyFrameInterval na kanale ChannelInput. Jeśli jawnie ustawić KeyFrameInterval, współczynnik pakowania segmentu HLS FragmentsPerSegment jest obliczany za pomocą reguł opisanych wcześniej.  

Jeśli jawnie ustawić zarówno KeyFrameInterval i FragmentsPerSegment, Media Services używa wartości, które można ustawić.

#### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą publikować wideo na tym kanale. Dozwolony adres IP można określić jako jeden z następujących:

* Pojedynczy adres IP (na przykład 10.0.0.1)
* Zakres adresów IP, który używa adresu IP i maski podsieci CIDR (na przykład 10.0.0.1/22)
* Zakres adresów IP, który używa adresu IP i kropkowanego podsieci dziesiętnej (na przykład 10.0.0.1(255.255.252.0))

Jeśli nie określono żadnych adresów IP i nie ma definicji reguły, nie jest dozwolony adres IP. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.

### <a name="channel-preview"></a>Podgląd kanału
#### <a name="preview-urls"></a>Wyświetlanie adresów URL w wersji zapoznawczej
Kanały zapewniają punkt końcowy podglądu (adres URL w wersji zapoznawczej), który służy do podglądu i sprawdzania poprawności strumienia przed dalszym przetwarzaniem i dostarczaniem.

Podczas tworzenia kanału możesz uzyskać adres URL podglądu. Aby uzyskać adres URL, kanał nie musi być w stanie **uruchamiania.** Po rozpoczęciu pozyskiwania danych przez kanał można wyświetlić podgląd strumienia.

Obecnie strumień podglądu może być dostarczany tylko w formacie MP4 (Smooth Streaming), niezależnie od określonego typu wejściowego. Odtwarzacz Smooth [Streaming Health Monitor](https://playready.directtaps.net/smoothstreaming/) umożliwia przetestowanie płynnego strumienia. Można również użyć odtwarzacza, który jest obsługiwany w witrynie Azure Portal, aby wyświetlić strumień.

#### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą łączyć się z punktem końcowym podglądu. Jeśli nie określono żadnych adresów IP, każdy adres IP jest dozwolony. Dozwolony adres IP można określić jako jeden z następujących:

* Pojedynczy adres IP (na przykład 10.0.0.1)
* Zakres adresów IP, który używa adresu IP i maski podsieci CIDR (na przykład 10.0.0.1/22)
* Zakres adresów IP, który używa adresu IP i kropkowanego podsieci dziesiętnej (na przykład 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Wyjście kanału
Aby uzyskać informacje o danych wyjściowych kanału, zobacz sekcję [Interwał klatki kluczowej.](#keyframe_interval)

### <a name="channel-managed-programs"></a>Programy zarządzane przez kanały
Kanał jest skojarzony z programami, za pomocą których można sterować publikowaniem i przechowywaniem segmentów w strumieniu na żywo. Kanały zarządzają programami. Relacja kanału i programu jest podobna do tradycyjnych mediów, gdzie kanał ma stały strumień zawartości, a program jest ograniczony do pewnego zdarzenia czasowego na tym kanale.

Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie długości **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwum określa również maksymalną liczbę czasu, przez jaki klienci mogą szukać w czasie z bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z zasobem, który przechowuje zawartość przesyłaną strumieniowo. Zasób jest mapowany do kontenera bloku obiektów blob na koncie magazynu platformy Azure, a pliki w zasobie są przechowywane jako obiekty blob w tym kontenerze. Aby opublikować program, aby klienci mogli wyświetlić strumień, należy utworzyć lokalizator OnDemand dla skojarzonego zasobu. Za pomocą tego lokalizatora można utworzyć adres URL przesyłania strumieniowego, który można podać klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można utworzyć wiele archiwów tego samego strumienia przychodzącego. W razie potrzeby można publikować i archiwizować różne części wydarzenia. Załóżmy na przykład, że twoim wymaganiem biznesowym jest archiwizowanie 6 godzin programu, ale emitowanie tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden program jest ustawiony do archiwizacji sześciu godzin zdarzenia, ale program nie jest publikowany. Drugi program jest ustawiony na archiwum przez 10 minut, a ten program jest publikowany.

Nie należy używać ponownie istniejących programów dla nowych zdarzeń. Zamiast tego utwórz nowy program dla każdego zdarzenia. Uruchom program, gdy będziesz gotowy do rozpoczęcia przesyłania strumieniowego i archiwizacji. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.

Aby usunąć zarchiwizowana zawartość, zatrzymaj i usuń program, a następnie usuń skojarzony zasób. Nie można usunąć zasobu, jeśli program go używa. Program musi zostać najpierw usunięty.

Nawet po zatrzymaniu i usunięciu programu użytkownicy mogą przesyłać strumieniowo zarchiwizowanej zawartości jako wideo na żądanie, dopóki nie usuniesz zasobu. Jeśli chcesz zachować zarchiwizowanej zawartości, ale nie masz jej dostępnej do przesyłania strumieniowego, usuń lokalizator przesyłania strumieniowego.

## <a name="channel-states-and-billing"></a><a id="states"></a>Stany kanałów i rozliczenia
Możliwe wartości dla bieżącego stanu kanału obejmują:

* **Zatrzymane**: Jest to początkowy stan kanału po jego utworzeniu. W tym stanie właściwości kanału mogą być aktualizowane, ale przesyłanie strumieniowe nie jest dozwolone.
* **Uruchamianie**: Kanał jest uruchamiany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe. Jeśli wystąpi błąd, kanał powraca do **stanu Zatrzymane.**
* **Uruchomiony:** Kanał może przetwarzać transmisje na żywo.
* **Zatrzymanie:** Kanał jest zatrzymywany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.
* **Usuwanie:** Kanał jest usuwany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.

W poniższej tabeli przedstawiono sposób mapowania stanów kanału do trybu rozliczeniowego.

| Stan kanału | Wskaźniki interfejsu użytkownika portalu | Rozliczane? |
| --- | --- | --- |
| **Zaczynając** |**Zaczynając** |Nie (stan przejściowy) |
| **Uruchomiono** |**Gotowy** (brak uruchomionych programów)<p><p>lub<p>**Przesyłanie strumieniowe** (co najmniej jeden uruchomiony program) |Tak |
| **Zatrzymywanie** |**Zatrzymywanie** |Nie (stan przejściowy) |
| **Zatrzymano** |**Zatrzymano** |Nie |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Napisy kodowane i wstawianie reklam
W poniższej tabeli przedstawiono obsługiwane standardy dotyczące podpisów kodowanych i wstawiania reklam.

| Standardowa | Uwagi |
| --- | --- |
| CEA-708 i EIA-608 (708/608) |CEA-708 i EIA-608 są standardami podpisów kodowych dla Stanów Zjednoczonych i Kanady.<p><p>Obecnie podpisy są obsługiwane tylko wtedy, gdy są przenoszone w kodowanym strumieniu wejściowym. Należy użyć kodera nośnika na żywo, który można wstawić 608 lub 708 podpisów w zakodowanym strumieniu, który jest wysyłany do usługi Media Services. Usługi Media Services dostarczają widzom treści z wstawionymi podpisami. |
| TTML wewnątrz .ismt (smooth streaming ścieżek tekstowych) |Dynamiczne pakowanie usługi Media Services umożliwia klientom przesyłanie strumieniowe zawartości w dowolnym z następujących formatów: DASH, HLS lub Smooth Streaming. Jednak jeśli pozyskiwania pofragmentowane MP4 (Smooth Streaming) z podpisami wewnątrz .ismt (Smooth Streaming ścieżki tekstowe), można dostarczyć strumień tylko smooth streaming klientów. |
| SCTE-35 |SCTE-35 to cyfrowy system sygnalizacji, który służy do sygnalizacji wstawiania reklam. Odbiorniki niższego szczebla używają sygnału do splatanie reklam w strumieniu przez wyznaczony czas. SCTE-35 musi być wysyłany jako ścieżka rozrzedzona w strumieniu wejściowym.<p><p>Obecnie jedynym obsługiwanym formatem strumienia wejściowego, który przenosi sygnały reklamowe, jest pofragmentowany format MP4 (Smooth Streaming). Jedynym obsługiwanym formatem wyjściowym jest również płynne przesyłanie strumieniowe. |

## <a name="considerations"></a><a id="considerations"></a>Zagadnienia dotyczące
Podczas wysyłania strumienia o wielu szybkościach transmisji bitów do kanału używasz lokalnego kodera na żywo, obowiązują następujące ograniczenia:

* Upewnij się, że masz wystarczającą ilość bezpłatnej łączności z Internetem, aby wysyłać dane do punktów pozyskiwania.
* Użycie pomocniczego adresu URL pozyskiwania wymaga dodatkowej przepustowości.
* Przychodzący strumień o wielu szybkościach transmisji bitów może mieć maksymalnie 10 poziomów jakości wideo (warstw) i maksymalnie 5 ścieżek audio.
* Najwyższa średnia szybkość transmisji bitów dla dowolnego poziomu jakości wideo powinna być niższa niż 10 Mb/s.
* Łączna wartość bitów dla wszystkich strumieni wideo i audio powinna być niższa niż 25 Mb/s.
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Możesz śmigieć pojedynczą szybkość transmisji bitów w swoim kanale. Ale ponieważ kanał nie przetwarza strumienia, aplikacje klienckie otrzymają również pojedynczy strumień szybkości transmisji bitów. (Nie zalecamy tej opcji).

Oto inne zagadnienia związane z pracą z kanałami i powiązanymi składnikami:

* Za każdym razem, gdy ponownie skonfigurujesz koder na żywo, wywołaj metodę **Reset** na kanale. Przed zresetowaniem kanału należy zatrzymać program. Po zresetowaniu kanału uruchom ponownie program.

  > [!NOTE]
  > Po ponownym uruchomieniu programu należy skojarzyć go z nowym zasobem i utworzyć nowy lokalizator. 
  
* Kanał można zatrzymać tylko wtedy, gdy jest w stanie **uruchamiania** i wszystkie programy na kanale zostały zatrzymane.
* Domyślnie można dodać tylko pięć kanałów do konta usługi Media Services. Aby uzyskać więcej informacji, zobacz [Przydziały i ograniczenia](media-services-quotas-and-limitations.md).
* Naliczane są naliczane tylko wtedy, gdy twój kanał jest w stanie **Uruchomione.** Aby uzyskać więcej informacji, zobacz sekcję [Stany kanału i rozliczenia.](media-services-live-streaming-with-onprem-encoders.md#states)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Opinia
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Zalecane w enkoderach lokalowych](media-services-recommended-encoders.md)

[Pofragmentowana specyfikacja pozyskiwania serwera MP4 w usłudze Azure Media Services](../media-services-fmp4-live-ingest-overview.md)

[Przegląd usługi Azure Media Services i typowe scenariusze](media-services-overview.md)

[Pojęcia dotyczące usług multimedialnych](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
