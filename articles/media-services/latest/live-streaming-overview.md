---
title: Omówienie przesyłania strumieniowego na żywo w usłudze Azure Media Services w wersji 3 | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie przesyłania strumieniowego na żywo przy użyciu usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e2c4e5b6c10b06d82a1933962cb2d97e031876a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068019"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Przesyłanie strumieniowe na żywo za pomocą usługi Azure Media Services w wersji 3

Usługa Azure Media Services umożliwia dostarczanie zdarzeń na żywo do klientów w chmurze platformy Azure. Aby przesyłać strumieniowo wydarzenia na żywo za pomocą usługi Media Services, potrzebne są następujące elementy:  

- Kamera używana do przechwytywania wydarzenia na żywo.<br/>Aby uzyskać pomysły na konfigurację, zapoznaj się [z prostą i przenośną konfiguracją sprzętu wideo.]( https://link.medium.com/KNTtiN6IeT)

    Jeśli nie masz dostępu do kamery, narzędzia takie jak [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) mogą być używane do generowania transmisji na żywo z pliku wideo.
- Koder wideo na żywo, który konwertuje sygnały z kamery (lub innego urządzenia, takiego jak laptop) na kanał wkładu wysyłany do usługi Media Services. Kanał wkładu może zawierać sygnały związane z reklamami, takie jak znaczniki SCTE-35.<br/>Aby uzyskać listę zalecanych koderów do przesyłania strumieniowego na żywo, zobacz [kodery do przesyłania strumieniowego na żywo](recommended-on-premises-live-encoders.md). Ponadto, sprawdź ten blog: [Produkcja strumieniowa na żywo z OBS](https://link.medium.com/ttuwHpaJeT).
- Składniki w układzie media services, które umożliwiają pozyskiwanie, podgląd, pakiet, nagrywanie, szyfrowanie i emisję zdarzenia na żywo do klientów lub do sieci CDN w celu dalszej dystrybucji.

Ten artykuł zawiera omówienie i wskazówki dotyczące przesyłania strumieniowego na żywo za pomocą usługi Media Services i łącza do innych odpowiednich artykułów.
 
> [!NOTE]
> Za pomocą [witryny Azure Portal](https://portal.azure.com/) można zarządzać [zdarzeniami na żywo](live-events-outputs-concept.md)w wersji 3 , [wyświetlać zasoby w](assets-concept.md)wersji 3 , uzyskać informacje o uzyskiwaniu dostępu do interfejsów API. W przypadku wszystkich innych zadań zarządzania (na przykład Przekształceń i zadań) użyj [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/) [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z [obsługiwanych pakietów SDK.](media-services-apis-overview.md#sdks)

## <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

Za pomocą usługi Media Services można korzystać z [programu Dynamic Packaging,](dynamic-packaging-overview.md)który umożliwia wyświetlanie podglądu i transmitowanie transmisji na żywo w [formatach MPEG DASH, HLS i Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z kanału wkładu wysyłanego do usługi. Twoi widzowie mogą odtwarzać transmisję na żywo z dowolnymi graczami zgodnymi z HLS, DASH lub Smooth Streaming. Za pomocą [programu Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) w aplikacjach sieci web lub aplikacjach mobilnych można dostarczać strumień w dowolnym z tych protokołów.

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

Szyfrowanie dynamiczne umożliwia dynamiczne szyfrowanie zawartości na żywo lub na żądanie za pomocą aes-128 lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Usługa Media Services zapewnia również usługę dostarczania licencji kluczy AES i DRM (PlayReady, Widevine i FairPlay) autoryzowanym klientom. Aby uzyskać więcej informacji, zobacz [Szyfrowanie dynamiczne](content-protection-overview.md).

> [!NOTE]
> Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="dynamic-manifest"></a>Manifest dynamiczny

Filtrowanie dynamiczne służy do kontrolowania liczby ścieżek, formatów, szybkości transmisji bitów i okien czasu prezentacji wysyłanych do odtwarzaczy. Aby uzyskać więcej informacji, zobacz [filtry i manifesty dynamiczne](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Typy zdarzeń na żywo

[Wydarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents) odpowiadają za pozyskiwanie i przetwarzanie strumieni wideo na żywo. Zdarzenie na żywo można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień wielu bitrate) lub *kodowanie* na żywo (lokalny koder na żywo wysyła pojedynczy strumień szybkości transmisji bitów). Aby uzyskać szczegółowe informacje na temat przesyłania strumieniowego na żywo w udziale usługi Media Services w wersji 3, zobacz [Wydarzenia na żywo i wyjścia na żywo](live-events-outputs-concept.md).

### <a name="pass-through"></a>Przekazywanie

![Przekazywanie](./media/live-streaming/pass-through.svg)

Podczas korzystania z usługi Live **Event**, można polegać na lokalnym koderze na żywo do generowania wielu bitrate strumienia wideo i wysłać go jako źródło danych wejściowych do live event (przy użyciu protokołu wejściowego RTMP lub fragmentaryczne MP4). Zdarzenie na żywo następnie przenosi za pośrednictwem przychodzących strumieni wideo do dynamicznego packageera (Streaming Endpoint) bez dalszego transkodowania. Takie wydarzenie na żywo jest zoptymalizowane pod kątem długotrwałych wydarzeń na żywo lub liniowej transmisji na żywo 24x365. 

### <a name="live-encoding"></a>Kodowanie na żywo  

![Kodowanie na żywo](./media/live-streaming/live-encoding.svg)

Korzystając z kodowania w chmurze za pomocą usługi Media Services, należy skonfigurować lokalny koder na żywo, aby wysyłać pojedynczy plik wideo o szybkości transmisji bitów jako źródło danych (agregacja do 32 Mb/s) do zdarzenia na żywo (przy użyciu protokołu wejściowego RTMP lub protokołu wejściowego MP4 pofragmentowanego). Wydarzenie na żywo transkoduje przychodzący strumień o pojedynczej szybkości transmisji bitów do [wielu strumieni wideo o szybkości transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) w różnych rozdzielczościach, aby poprawić dostarczanie i udostępnia je do dostarczania do urządzeń odtwarzających za pośrednictwem standardowych protokołów branżowych, takich jak MPEG-DASH, Apple HTTP Live Streaming (HLS) i Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Transkrypcja na żywo (wersja zapoznawcza)

Transkrypcja na żywo to funkcja, której można używać z wydarzeniami na żywo, które są kodowaniem przekazu lub na żywo. Aby uzyskać więcej informacji, zobacz [transkrypcję na żywo](live-transcription.md). Gdy ta funkcja jest włączona, usługa używa funkcji [Zamiana mowy na tekst](../../cognitive-services/speech-service/speech-to-text.md) usług Cognitive Services do transkrypcji wypowiadanych słów w przychodzącym dźwięku do tekstu. Tekst ten jest następnie udostępniany do dostarczenia wraz z wideo i audio w protokołach MPEG-DASH i HLS.

> [!NOTE]
> Obecnie transkrypcja na żywo jest dostępna jako funkcja podglądu w zachodnie stany USA 2.

## <a name="live-streaming-workflow"></a>Przepływ pracy przesyłania strumieniowego na żywo

Aby zrozumieć przepływ pracy przesyłania strumieniowego na żywo w umiaźnych 3, należy najpierw przejrzeć i zrozumieć następujące pojęcia: 

- [Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md)
- [Wydarzenia i dane wyjściowe na żywo](live-events-outputs-concept.md)
- [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)

### <a name="general-steps"></a>Ogólne kroki

1. Upewnij się, że na koncie usługi Media Services jest uruchomiony **punkt końcowy przesyłania strumieniowego** (Origin). 
2. Utwórz [wydarzenie na żywo](live-events-outputs-concept.md). <br/>Podczas tworzenia zdarzenia można określić jego automatyczne uruchamianie. Alternatywnie można uruchomić zdarzenie, gdy będziesz gotowy do rozpoczęcia przesyłania strumieniowego.<br/> Gdy autostart jest ustawiony na true, live event zostanie uruchomiony zaraz po utworzeniu. Rozliczenia rozpoczynają się natychmiast po uruchomieniu wydarzenia na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Pobierz adresy URL pozyskiwania i skonfiguruj koder lokalny tak, aby używał adresu URL do wysyłania pliku danych o programie.<br/>Zobacz [zalecane kodery na żywo](recommended-on-premises-live-encoders.md).
4. Pobierz adres URL wersji zapoznawczej i użyj go, aby sprawdzić, czy dane wejściowe z kodera są faktycznie odbierane.
5. Utwórz nowy obiekt **zasobu.** 

    Każdy live output jest skojarzony z zasobem, który używa do nagrywania wideo do skojarzonego kontenera magazynu obiektów blob platformy Azure. 
6. Utwórz **dane wyjściowe na żywo** i użyj nazwy zasobu, która została utworzona, aby strumień mógł zostać zarchiwizowany w zasobie.

    Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Po usunięciu danych wyjściowych na żywo nie powoduje usunięcia bazowego zasobu i zawartości środka trwałego.
7. Utwórz **lokalizator przesyłania strumieniowego** z [wbudowanymi typami zasad przesyłania strumieniowego](streaming-policy-concept.md).

    Aby opublikować dane wyjściowe na żywo, należy utworzyć lokalizator przesyłania strumieniowego dla skojarzonego zasobu. 
8. Wyświetl listę ścieżek w **lokalizatorze przesyłania strumieniowego,** aby odzyskać adresy URL do użycia (są one deterministyczne).
9. Pobierz nazwa hosta punktu **końcowego przesyłania strumieniowego** (Origin), z którego chcesz przesyłać strumieniowo.
10. Połącz adres URL z kroku 8 z nazwa hosta w kroku 9, aby uzyskać pełny adres URL.
11. Jeśli chcesz przestać wyświetlać **wydarzenie na żywo,** musisz zatrzymać przesyłanie strumieniowe wydarzenia i usunąć **lokalizator przesyłania strumieniowego**.
12. Aby po zakończeniu strumieniowego przesyłania zdarzeń, wyczyścić udostępnione wcześniej zasoby, postępuj zgodnie z poniższą procedurą.

    * Zatrzymaj wypychanie strumienia z kodera.
    * Zatrzymaj wydarzenie na żywo. Po zatrzymaniu wydarzenia na żywo opłaty nie będą za nie naliczane. W razie potrzeby ponownego uruchomienia kanał będzie miał ten sam adres URL pozyskiwania, więc nie trzeba będzie ponownie konfigurować kodera.
    * Można zatrzymać punkt końcowy przesyłania strumieniowego, chyba że chcesz nadal udostępniać archiwum zdarzenia na żywo w formie przesyłania strumieniowego na żądanie. Jeśli wydarzenie na żywo będzie w stanie zatrzymanym, żadne opłaty nie będą naliczane.

Zasób, do którym jest archiwizowana produkcja na żywo, automatycznie staje się zasobem na żądanie po usunięciu danych wyjściowych na żywo. Przed zatrzymaniem zdarzenia na żywo należy usunąć wszystkie dane wyjściowe na żywo. Można użyć opcjonalnej flagi [removeOutputsOnStop,](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) aby automatycznie usunąć dane wyjściowe na żywo na przystanku. 

> [!TIP]
> Zobacz [samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md), artykuł sprawdza kod, który implementuje kroki opisane powyżej.

## <a name="other-important-articles"></a>Inne ważne artykuły

- [Zalecane kodery na żywo](recommended-on-premises-live-encoders.md)
- [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md)
- [Porównanie funkcji typów zdarzeń na żywo](live-event-types-comparison.md)
- [Stany i rozliczenia](live-event-states-billing.md)
- [Opóźnienie](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Zobacz artykuł [Często zadawane pytania.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

* [Szybki start na żywo] (live-events-wirecast-quickstart.md(
* [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
* [Wskazówki dotyczące migracji dotyczące przechodzenia z usługi Media Services w wersji 2 do wersji 3](migrate-from-v2-to-v3.md)
