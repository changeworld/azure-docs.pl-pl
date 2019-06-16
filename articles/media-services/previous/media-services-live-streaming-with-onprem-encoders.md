---
title: Stream na żywo za pomocą koderów lokalnych, które tworzą strumienie różnej szybkości transmisji bitów — Azure | Dokumentacja firmy Microsoft
description: 'W tym temacie opisano sposób konfigurowania kanału, który odbiera strumień na żywo o różnych szybkościach transmisji bitów z kodera w środowisku lokalnym. Strumień, następnie mogą być dostarczane aplikacjom klienckim odtwarzania za pomocą jednego lub więcej punktów końcowych przesyłania strumieniowego przy użyciu jednej z następujących adaptacyjnych protokołów przesyłania strumieniowego: HLS, Smooth Streaming, DASH.'
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
ms.author: cenkd;juliako
ms.openlocfilehash: da20e4601b75bcb22546d21f6ad218ac9ba2728b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463812"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych

> [!NOTE]
> Od 12 maja 2018 r. Usługa kanały na żywo będzie już obsługę strumienia transportu RTP/MPEG-2 protokołu pozyskiwania. Przeprowadź migrację z protokołu RTP/MPEG-2 do protokołu RTMP lub plików MP4 (Smooth Streaming) protokołów pozyskiwania.

## <a name="overview"></a>Omówienie
W usłudze Azure Media Services *kanału* reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Kanał odbiera strumieni danych wejściowych w jeden z dwóch sposobów:

* Koder na żywo w środowisku lokalnym wysyła różnych szybkościach transmisji bitów RTMP lub Smooth Streaming (pofragmentowany MP4) strumienia do kanału, który nie jest włączona kodowanie na żywo za pomocą usługi Media Services. Pozyskiwane strumienie są przekazywane za pośrednictwem kanałów bez dalszego przetwarzania. Ta metoda jest wywoływana *przekazywanego*. Koder na żywo może także wysłać strumień o pojedynczej szybkości bitów do kanału, który nie jest włączona dla kodowania na żywo, ale nie jest to zalecane. Usługi Media Services dostarcza strumień do klientów, którzy wysyłają żądanie.

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczny sposób transmisja strumieniowa na żywo.


* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości bitów do kanału, który jest skonfigurowany do przeprowadzania kodowania na żywo z usługą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany MP4). Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia pojedynczej do wielokrotnej szybkości transmisji bitów (adaptacyjnej) strumienia wideo. Usługi Media Services dostarcza strumień do klientów, którzy wysyłają żądanie.

Począwszy od Media Services w wersji 2.10 wersji, podczas tworzenia kanału, można określić sposób kanał w taki sposób, aby otrzymywać strumień wejściowy. Można również określić, czy kanał do przeprowadzania kodowania na żywo strumienia. Dostępne są dwie opcje:

* **Przekazuj**: Należy określić tę wartość, jeśli zamierzasz używać na lokalny koder na żywo, która ma strumień o różnych szybkościach transmisji bitów (strumień przekazujące) jako dane wyjściowe. W tym przypadku przychodzącego strumienia przechodzi przez w danych wyjściowych bez żadnych kodowania. Jest to zachowanie kanału przed wydaniem 2.10. Ten artykuł zawiera szczegółowe informacje o Praca z kanałami tego typu.
* **Kodowanie na żywo**: Wybierz tę wartość, jeśli planujesz używać usługi Media Services do zakodowania pojedynczej szybkości transmisji strumieniowej na żywo na strumień o wielokrotnej szybkości transmisji bitów. Pozostawienie kanału kodowania na żywo w **systemem** stan spowoduje naliczenie opłat rozliczeniowych. Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu wydarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych stawki godzinowe. Usługi Media Services dostarcza strumień do klientów, którzy wysyłają żądanie.

> [!NOTE]
> W tym artykule omówiono atrybuty kanały, które nie są włączone do przeprowadzania kodowania na żywo. Aby uzyskać informacje dotyczące pracy z kanałami obsługującymi kodowanie na żywo, zobacz [transmisja strumieniowa na żywo ze strumieniami o różnych szybkościach transmisji bitów przy użyciu usługi Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).
>
>Zaleca się koderów lokalnych można znaleźć informacje o [zalecane w kodery lokalne](media-services-recommended-encoders.md).

Poniższy diagram przedstawia transmisji strumieniowej na żywo przepływu pracy, który używa lokalny koder na żywo, aby mieć RTMP o różnej szybkości transmisji bitów lub plików MP4 (Smooth Streaming) strumieni jako dane wyjściowe.

![Przepływ pracy na żywo][live-overview]

## <a id="scenario"></a>Typowy scenariusz transmisji strumieniowej na żywo
W następujących krokach opisano zadania związane z tworzeniem typowych aplikacji transmisji strumieniowej na żywo.

1. Podłącz kamerę wideo do komputera. Uruchom i skonfiguruj lokalny koder na żywo, zawierającej RTMP o różnej szybkości transmisji bitów lub plików MP4 (Smooth Streaming) strumienia jako dane wyjściowe. Aby uzyskać więcej informacji, zobacz temat [Obsługa protokołu RTMP i kodery na żywo w usłudze Azure Media Services](https://go.microsoft.com/fwlink/?LinkId=532824).

    Po utworzeniu kanału, można również wykonać ten krok.
2. Tworzenie i uruchamianie kanału.

3. Adres URL pozyskiwania kanału pobierania.

    Koder na żywo używa adresu URL pozyskiwania do wysyłania strumienia do kanału.
4. Pobierz adres URL podglądu kanału.

    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
5. Utwórz program.

    Korzystając z witryny Azure portal, tworzenie programu tworzy również element zawartości.

    Gdy używasz zestawu SDK platformy .NET lub REST, należy utworzyć element zawartości i określanie użycia tego zasobu, podczas tworzenia programu.
6. Publikowanie elementu zawartości, która jest skojarzona z programu.   

    >[!NOTE]
    >Po utworzeniu konta usługi Azure Media Services **domyślne** punkt końcowy przesyłania strumieniowego zostanie dodany do Twojego konta w **zatrzymane** stanu. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

7. Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji.

8. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.

9. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.

10. Usuń program (i opcjonalnie można również usunąć element zawartości).     

## <a id="channel"></a>Opis kanału i jego składników powiązane
### <a id="channel_input"></a>Kanał danych wejściowych (odbieranie) konfiguracji
#### <a id="ingest_protocols"></a>Pozyskiwanie protokołu przesyłania strumieniowego
Usługa Media Services obsługuje, umożliwiając pozyskiwanie kanały na żywo przy użyciu plików MP4 z wieloma szybkościami transmisji bitów podzielonej zawartości i RTMP o różnej szybkości transmisji bitów jako protokołów przesyłania strumieniowego. Podczas pozyskiwania RTMP protokół przesyłania strumieniowego jest wybrany, pozyskiwania dwa punkty końcowe (wejścia) są tworzone dla kanału:

* **Podstawowy adres URL**: Określa, w pełni kwalifikowany adres URL z podstawowego protokołu RTMP kanału pozyskiwania punktu końcowego.
* **Adres URL dodatkowej** (opcjonalnie): Określa w pełni kwalifikowany adres URL z kanału dodatkowej RTMP pozyskiwania punktu końcowego.

Użyj dodatkowej adresu URL, jeśli chcesz zwiększyć niezawodność i odporności na uszkodzenia usługi pozyskiwania strumienia (a także kodera trybu failover i odporności na uszkodzenia), szczególnie w przypadku następujących scenariuszy:

- Koder jednego podwójnego Wypychanie do podstawowych i pomocniczych adresów URL:

    Głównym celem tego scenariusza jest zapewnienie odporności więcej wahania sieci i tłumienie. Niektóre koderów RTMP nie obsługują również zamknie połączenie sieciowe. W przypadku rozłączenia sieci koder może się zatrzymać, kodowanie i nie wyśle buforowane dane w przypadku ponownego połączenia. Powoduje to przerw i utraty danych. Sieci może się zdarzyć z powodu nieprawidłowych sieci lub konserwacji na stronie platformy Azure. Adresy URL podstawowy/pomocniczy ograniczyć problemy z siecią i zapewnić kontrolowany procesu uaktualniania. Każdorazowo, Rozłącz zaplanowane sieci występuje, Media Services zarządza podstawowego i pomocniczego rozłącza i zapewnia opóźnione rozłączyć między nimi. Kodery miał czas nadal wysyłać dane, a następnie ponownie. Kolejność odłącza może być losowych, ale zawsze nastąpi opóźnienie między podstawowy/pomocniczy lub podstawowy/pomocniczy adresów URL. W tym scenariuszu kodera nadal jest pojedynczym punktem awarii.

- Wiele koderów, z użyciem kodera w każdym Wypychanie do dedykowanych punkt:

    W tym scenariuszu zawiera zarówno encoder i pozyskuje nadmiarowości. W tym scenariuszu encoder1 Wypychanie do podstawowego adresu URL, a encoder2 Wypychanie do pomocniczego adresu URL. Gdy kodera nie powiodło się, inne kodera można nadal wysyłać dane. Nadmiarowość danych mogą być obsługiwane, ponieważ usługa Media Services nie rozłączy podstawowe i pomocnicze adresy URL w tym samym czasie. W tym scenariuszu założono, że koderów jest zsynchronizowany i podaj dokładnie tych samych danych.  

- Wiele koderów double Wypychanie do podstawowych i pomocniczych adresów URL:

    W tym scenariuszu oba koderów wypychanie danych do podstawowych i pomocniczych adresów URL. Dzięki temu można uzyskać większą niezawodność i odporności na uszkodzenia, jak również nadmiarowość danych. W tym scenariuszu może tolerować zarówno awariami kodera i zamknie połączenie, nawet wtedy, gdy jeden koder zostanie zatrzymane. Przyjęto założenie, że koderów jest synchronizowane i zapewnia te same dane.  

Aby uzyskać informacji na temat protokołu RTMP koderów na żywo, zobacz [pomocy technicznej systemu Azure Media Services protokołu RTMP i kodery na żywo](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Adresy URL (punkty końcowe) pozyskiwania
Kanał oferuje wejściowy punkt końcowy (adres URL pozyskiwania) określisz w koder na żywo, dzięki czemu można wypchnąć kodera strumienie do kanałów.   

Po utworzeniu kanału możesz uzyskać adresy URL pozyskiwania. Możesz uzyskać te adresy URL kanału nie muszą znajdować się w **systemem** stanu. Gdy wszystko będzie gotowe rozpocząć wypychanie danych do kanału, musi należeć do kanału **systemem** stanu. Po uruchomieniu kanału dane wprowadzane można wyświetlać podgląd strumienia za pomocą adresu URL w wersji zapoznawczej.

Istnieje możliwość wprowadzania podzielonej zawartości w formacie MP4 (Smooth Streaming) transmisji strumieniowej na żywo za pośrednictwem połączenia SSL. Pozyskanie za pośrednictwem protokołu SSL, upewnij się zaktualizować adres URL pozyskiwania protokołu HTTPS. Nie można obecnie pozyskiwania RTMP, za pośrednictwem protokołu SSL.

#### <a id="keyframe_interval"></a>Interwał klatki kluczowej
Podczas korzystania z lokalny koder na żywo do generowania strumień o wielokrotnej szybkości transmisji bitów, interwał ramki kluczowej określa czas trwania grupy obrazów (GOP) używane przez tego kodera. Po kanał odbiera strumień przychodzących z tym, można dostarczać transmisji strumieniowej na żywo aplikacjom klienckim odtwarzania w dowolnym z następujących formatów: Smooth Streaming, Dynamic Adaptive Streaming za pośrednictwem protokołu HTTP (DASH) i HTTP Live przesyłania strumieniowego (HLS). Podczas prowadzenia transmisji strumieniowych na żywo, HLS, jest zawsze dostarczana dynamicznie. Domyślnie usługa Media Services automatycznie oblicza współczynnik pakowania segmentu HLS (fragmenty według segmentu) na podstawie interwału ramki kluczowej odebrane z kodera na żywo.

W poniższej tabeli przedstawiono, jak jest obliczana segmentu czas trwania:

| Interwał klatki kluczowej | Współczynnik pakowania segmentu HLS (FragmentsPerSegment) | Przykład |
| --- | --- | --- |
| Mniej niż 3 sekundy |3:1 |W przypadku KeyFrameInterval (lub GOP) 2 sekundy, współczynnik pakowania segmentu HLS domyślną jest 3-do-1. Spowoduje to utworzenie segmentu HLS 6 sekundy. |
| 3 do 5 sekund |2:1 |W przypadku KeyFrameInterval (lub GOP) 4 sekundy, współczynnik pakowania segmentu HLS domyślną jest 2: 1. Spowoduje to utworzenie segmentu HLS 8 sekund. |
| Większa niż 5 sekund |1:1 |W przypadku KeyFrameInterval (lub GOP) 6 w ciągu kilku sekund, współczynnik pakowania segmentu HLS domyślną jest 1 do 1. Spowoduje to utworzenie segmentu HLS 6 sekundy. |

Konfigurowanie danych wyjściowych tego kanału i ustawienie FragmentsPerSegment ChannelOutputHls, można zmienić współczynnik fragmentów na segment.

Można również zmienić wartość interwału klatki kluczowej, ustawiając właściwość KeyFrameInterval na ChannelInput. Jeśli została jawnie ustawiona KeyFrameInterval HLS segmentu współczynnik pakowania, obliczonego FragmentsPerSegment za pomocą reguł opisanych powyżej.  

Jeśli jawnie ustawiono KeyFrameInterval i FragmentsPerSegment, Media Services używa wartości, które można ustawić.

#### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą publikować pliki wideo do tego kanału. Dozwolony adres IP można określić jako jeden z następujących czynności:

* Pojedynczy adres IP (np. 10.0.0.1)
* Zakres adresów IP, który korzysta z adresu IP i maski podsieci CIDR (na przykład 10.0.0.1/22)
* Zakres adresów IP, który korzysta z adresu IP i maski podsieci dziesiętna kropkami (na przykład 10.0.0.1(255.255.252.0))

Brak adresów IP są określone, jeśli nie ma żadnych definicji reguły, żaden adres IP jest dozwolone. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.

### <a name="channel-preview"></a>Podgląd kanału
#### <a name="preview-urls"></a>Adresy URL podglądu
Kanały zapewniają punktu końcowego (wersja zapoznawcza) (adres URL w wersji zapoznawczej), która umożliwia podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem.

Po utworzeniu kanału możesz uzyskać adres URL podglądu. Możesz uzyskać adres URL kanału nie muszą znajdować się w **systemem** stanu. Po uruchomieniu kanału dane wprowadzane można wyświetlać podgląd strumienia.

Obecnie Podgląd strumienia mogą być dostarczane tylko w podzielonej zawartości w formacie MP4 (Smooth Streaming) format, niezależnie od określonego typu danych wejściowych. Możesz użyć [Smooth Streaming Monitor kondycji](https://playready.directtaps.net/smoothstreaming/) odtwarzacza, aby przetestować smooth stream. Można również użyć odtwarzacz, który znajduje się w witrynie Azure portal, aby wyświetlić strumień.

#### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą nawiązywać połączenie z punktem końcowym (wersja zapoznawcza). Jeśli nie określono żadnych adresów IP, dowolny adres IP jest dozwolony. Dozwolony adres IP można określić jako jeden z następujących czynności:

* Pojedynczy adres IP (np. 10.0.0.1)
* Zakres adresów IP, który korzysta z adresu IP i maski podsieci CIDR (na przykład 10.0.0.1/22)
* Zakres adresów IP, który korzysta z adresu IP i maski podsieci dziesiętna kropkami (na przykład 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Kanał danych wyjściowych
Aby uzyskać informacji na temat kanału danych wyjściowych, zobacz [Interwał ramki kluczowej](#keyframe_interval) sekcji.

### <a name="channel-managed-programs"></a>W przypadku programów zarządzanych kanału
Kanał jest skojarzony z programami, które umożliwiają kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami. Relacja kanału i programu jest podobny do tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.

Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie długości **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwizacji również mówią, że maksymalna liczba klientów można wyszukać w czasie od bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z elementem zawartości, która przechowuje zawartości przesyłanej strumieniowo. Element zawartości jest mapowany na blok kontenera obiektów blob na koncie Azure storage, a pliki w elemencie zawartości są przechowywane jako obiekty BLOB w kontenerze. Aby opublikować program, dzięki czemu klienci mogą wyświetlać strumień, należy utworzyć Lokalizator OnDemand dla skojarzonego elementu zawartości. Ten Lokalizator służy do tworzenia adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Możesz publikować i archiwizację różnych części wydarzenia, zgodnie z potrzebami. Na przykład Wyobraź sobie, że z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden program jest skonfigurowane do archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Inny program jest skonfigurowane do archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Nie należy używać ponownie istniejących programów dla nowych zdarzeń. Zamiast tego należy utworzyć nowy program dla każdego zdarzenia. Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.

Aby usunąć zarchiwizowaną zawartość, Zatrzymaj i Usuń program, a następnie usuń skojarzony element zawartości. Nie można usunąć elementu zawartości, jeśli program używa go. Najpierw należy usunąć program.

Nawet po zatrzymaniu i usunięciu programu, użytkownicy mogą strumieniowo zarchiwizowaną zawartość wideo na żądanie, dopóki nie usuniesz element zawartości. Jeśli chcesz zachować zarchiwizowaną zawartość, ale nie będzie on dostępny do przesyłania strumieniowego, Usuń Lokalizator przesyłania strumieniowego.

## <a id="states"></a>Stany kanału i rozliczeń
Możliwe wartości dla bieżącego stanu kanału:

* **Zatrzymano**: To jest wstępny stan kanału po jego utworzeniu. W tym stanie właściwości kanału mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.
* **Uruchamianie**: Kanał jest uruchamiany. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, kanał wróci do **zatrzymane** stanu.
* **Uruchamianie**: Kanał może przetwarzać transmisje strumieniowe na żywo.
* **Zatrzymywanie**: Kanał jest zatrzymywany. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.
* **Usuwanie**: Kanał jest usuwany. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.

W tabeli poniżej pokazano, jak stany kanału przekładają się na naliczanie opłat.

| Stan kanału | Wskaźniki w interfejsie użytkownika portalu | Naliczanie opłat? |
| --- | --- | --- |
| **Uruchamianie** |**Uruchamianie** |Nie (stan przejściowy) |
| **Uruchomiono** |**Gotowe** (Brak uruchomionych programów)<p><p>lub<p>**Przesyłanie strumieniowe** (co najmniej jeden uruchomiony program) |Yes |
| **Zatrzymywanie** |**Zatrzymywanie** |Nie (stan przejściowy) |
| **Zatrzymana** |**Zatrzymana** |Nie |

## <a id="cc_and_ads"></a>Zamknięte podpisy i ad wstawiania
Poniższa tabela przedstawia obsługiwane standardy dla zamkniętej podpisy i ad wstawiania.

| Standardowa (Standard) | Uwagi |
| --- | --- |
| Napisy kodowane CEA-708 i EIA 608 (708/608) |Napisy kodowane CEA-708 EIA 608 są i napisów standardy dotyczące Stanów Zjednoczonych i Kanady.<p><p>Obecnie podpisy jest obsługiwana tylko wtedy, gdy w zakodowany strumień wejściowy. Należy używać kodera multimedialnych na żywo, który może wstawić 608 lub 708 podpisów w zakodowany strumień, który jest wysyłany do usługi Media Services. Media Services dostarcza zawartość z podpisami wstawiony do swojej przeglądarki. |
| TTML wewnątrz .ismt (Smooth Streaming ścieżki tekstowe) |Funkcję dynamicznego tworzenia pakietów usługi Media Services umożliwia klientom przesyłanie strumieniowe zawartości w jednym z następujących formatów: DASH, HLS i Smooth Streaming. Jednak jeśli użytkownik pozyskiwania pofragmentowany plik MP4 (Smooth Streaming) z podpisami wewnątrz .ismt (Smooth Streaming ścieżki tekstowe), można dostarczać klientom tylko Smooth Streaming strumienia. |
| SCTE 35 |SCTE 35 jest systemem sygnalizowanie cyfrowego, który służy do wstawiania reklam podpowiedzi. Odbiorniki podrzędne umożliwiają sygnał splice reklamy w strumieniu wyznaczonym czasie. SCTE 35 muszą być wysyłane jako rozrzedzony śledzenia w strumieniu wejściowym.<p><p>Obecnie obsługiwane tylko strumień wejściowy formatowania tego wykonuje sygnały ad jest pofragmentowana. w formacie MP4 (Smooth Streaming). Obsługiwana jest tylko dane wyjściowe format jest także Smooth Streaming. |

## <a id="considerations"></a>Zagadnienia dotyczące
Podczas korzystania z lokalny koder na żywo do wysyłania strumienia o różnych szybkościach transmisji bitów do kanału, obowiązują następujące ograniczenia:

* Upewnij się, że masz wystarczające łączność z Internetem bezpłatne do wysyłania danych do punktów odbierania.
* Przy użyciu pomocniczego adres URL pozyskiwania wymaga dodatkowej przepustowości.
* Strumień przychodzących z wieloma szybkościami transmisji bitów może mieć maksymalnie 10 poziomów jakość wideo (warstwy) i maksymalnie 5 ścieżki audio.
* Najwyższy średnia szybkość transmisji bitów do dowolnego poziomu jakości wideo powinno być niższe niż 10 MB/s.
* Agregacja średniej szybkości transmisji dla wszystkich strumieni audio i wideo powinno być niższe niż 25 MB/s.
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Pojedyncza szybkość transmisji bitów umożliwia pobieranie w kanale usługi. Jednak ponieważ kanał nie przetwarza strumień, aplikacje klienckie będą otrzymywane również strumień o pojedynczej szybkości transmisji bitów. (Nie zaleca się tej opcji.)

Poniżej przedstawiono inne zagadnienia związane z pracy, korzystając z kanałów i powiązane składniki:

* Za każdym razem, gdy skonfigurujesz koder na żywo, wywołaj **resetowania** metody w kanale. Przed zresetowaniem kanał, musisz zatrzymać program. Po zresetowaniu kanał, ponownie uruchom program.

  > [!NOTE]
  > Po ponownym uruchomieniu program, musisz skojarzyć go z nowego elementu zawartości i utworzyć nowy. 
  
* Kanał można zatrzymać tylko wtedy, gdy jest on **systemem** stan i wszystkie programy na kanale zostały zatrzymane.
* Domyślnie tylko pięć kanałów można dodać do konta usługi Media Services. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).
* Opłaty są naliczane tylko wtedy, gdy kanał w **systemem** stanu. Aby uzyskać więcej informacji, zobacz [kanałów stanów i rozliczenia](media-services-live-streaming-with-onprem-encoders.md#states) sekcji.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Opinia
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tematy pokrewne
[Zalecane w kodery lokalne](media-services-recommended-encoders.md)

[Specyfikacja odbierania Azure Media Services pofragmentowane życie w formacie MP4](media-services-fmp4-live-ingest-overview.md)

[Omówienie usługi Azure Media Services i typowe scenariusze](media-services-overview.md)

[Pojęcia dotyczące usługi Media Services](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
