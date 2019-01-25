---
title: Omówienie transmisji strumieniowej na żywo za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Dzięki temu artykuł z omówieniem transmisja strumieniowa na żywo za pomocą usługi Azure Media Services v3.
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
ms.date: 01/22/2019
ms.author: juliako
ms.openlocfilehash: e7e5ddf06fc98f13b9848bf5ede208eef47efd69
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886666"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Przesyłanie strumieniowe przy użyciu usługi Azure Media Services v3 na żywo

Usługa Azure Media Services umożliwia dostarczanie wydarzeń na żywo dla klientów w chmurze Azure. Aby przesyłać strumieniowo zdarzenia na żywo za pomocą usługi Media Services, potrzebne są następujące elementy:  

- Kamera, która jest używana do przechwytywania zdarzeń na żywo.
- Koder wideo na żywo, który konwertuje sygnały z kamery (lub innego urządzenia, takie jak laptop) na udział źródła danych są wysyłane do usługi Media Services. Kanał informacyjny udział może obejmować sygnały związane z reklamy, takie jak znaczniki SCTE 35.
- Składniki w usłudze Media Services pozwalają pozyskiwać, w wersji zapoznawczej, pakiet, rejestrowanie, szyfrowania i emisję wydarzenia na żywo dla klientów lub do sieci CDN w celu dalszej dystrybucji.

W tym artykule szczegółowo omówiono, uzyskać wskazówki i zawiera diagramy główne składniki zaangażowane w transmisji strumieniowej na żywo za pomocą usługi Media Services.

## <a name="live-streaming-workflow"></a>Przepływ pracy transmisji strumieniowej na żywo

Poniżej przedstawiono kroki, aby uzyskać przepływ pracy transmisji strumieniowej na żywo:

1. Upewnij się, że **punkt końcowy przesyłania strumieniowego** jest uruchomiona. 
2. Tworzenie **wydarzenie na żywo**. <br/>Podczas tworzenia zdarzenia, można określić automatyczne uruchamianie go. Alternatywnie możesz rozpocząć zdarzenie, gdy jesteś gotowy rozpocząć przesyłanie strumieniowe.<br/> Gdy autostart jest ustawiona na wartość true, wydarzenie na żywo zostanie uruchomiony prawo po utworzeniu. Oznacza to, rozliczeń rozpoczyna się zaraz po uruchomieniu wydarzenie na żywo. Należy jawnie wywołać Stop zasobu wydarzenie na żywo, aby zatrzymać dalsze rozliczeń. Aby uzyskać więcej informacji, zobacz [stany wydarzenie na żywo i rozliczenia](live-event-states-billing.md).
3. Uzyskaj adresy URL pozyskiwania i skonfigurować koder lokalnych wysyłać wkład źródła danych przy użyciu adresu URL.<br/>Zobacz [zalecane kodery na żywo](recommended-on-premises-live-encoders.md).
4. Adres URL (wersja zapoznawcza) i weryfikować, czy rzeczywiście są odbierane dane wejściowe z kodera.
5. Utwórz nową **zasobów** obiektu.
6. Tworzenie **na żywo dane wyjściowe** i użyj nazwy zasobu, który został utworzony.<br/>**Na żywo dane wyjściowe** spowoduje zarchiwizowanie strumienia do **zasobów**.
7. Tworzenie **lokalizatora przesyłania strumieniowego** dzięki wbudowanej **przesyłania strumieniowego zasad** typów.<br/>Jeśli zamierzasz szyfrowanie zawartości, zapoznaj się z [Omówienie ochrony zawartości](content-protection-overview.md).
8. Wyświetlanie listy ścieżek na **lokalizatora przesyłania strumieniowego** odzyskać adresy URL, aby użyć (są to deterministyczne).
9. Pobieranie nazwy hosta dla **punkt końcowy przesyłania strumieniowego** chcesz przesyłać strumieniowo z.
10. Adres URL w kroku 8 należy połączyć z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Jeśli chcesz zatrzymać, dzięki czemu Twoje **wydarzenie na żywo** widoczne, należy zatrzymać przesyłanie strumieniowe zdarzeń i Usuń **lokalizatora przesyłania strumieniowego**.<br/>Aby uzyskać więcej informacji, zobacz [transmisji strumieniowej na żywo w ramach samouczka](stream-live-tutorial-with-api.md).

## <a name="overview-of-main-components"></a>Omówienie głównych składników

Aby dostarczać strumienie na żądanie lub na żywo za pomocą usługi Media Services, musisz mieć co najmniej jedną [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints). Po utworzeniu konta usługi Media Services **domyślne** StreamingEndpoint zostanie dodany do Twojego konta w **zatrzymane** stanu. Należy uruchomić StreamingEndpoint, z którego chcesz strumieniowo przesyłać zawartość do swojej przeglądarki. Można użyć domyślnie **punkt końcowy przesyłania strumieniowego**, lub utworzyć inny dostosowane **punkt końcowy przesyłania strumieniowego** z wymaganą konfiguracją i ustawień sieci CDN. Może zdecydować umożliwić wielu **punkty końcowe przesyłania strumieniowego**, każdy z nich przeznaczone dla różnych sieci CDN i podając unikatową nazwę hosta w dostarczaniu zawartości. 

W usłudze Media Services [zdarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents) jest odpowiedzialny za przyjęciem i przetwarzania na żywo strumieniowych źródeł wideo. Po utworzeniu zdarzenia na żywo, wejściowy punkt końcowy jest tworzony, której można wysyłać sygnał na żywo z kodera zdalnego. Zdalny koder na żywo wysyła wkładu, kanał informacyjny do wejściowy punkt końcowy przy użyciu [RTMP](https://www.adobe.com/devnet/rtmp.html) lub [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) protokołu (pofragmentowany plik MP4). Funkcji Smooth Streaming protokołu pozyskiwania, są obsługiwane schematy adresów URL `http://` lub `https://`. Przypadku protokołu pozyskiwania RTMP, są obsługiwane schematy adresów URL `rtmp://` lub `rtmps://`. Aby uzyskać więcej informacji, zobacz [zalecane strumieniowo koderów na żywo](recommended-on-premises-live-encoders.md).<br/>
Podczas tworzenia **wydarzenie na żywo**, można określić dozwolone adresy IP w jednym z następujących formatów: Adres IpV4 z 4 cyfry, zakres adresów CIDR.

Gdy **wydarzenie na żywo** rozpoczyna odbieranie wkład źródła danych, można użyć punktu końcowego (wersja zapoznawcza) (w wersji zapoznawczej adres URL do wyświetlania podglądu i weryfikowania otrzymują przed opublikowaniem dalsze transmisji strumieniowej na żywo. Po sprawdzeniu, że Podgląd strumienia jest dobra, aby udostępnić transmisji strumieniowej na żywo do dostarczania za pomocą co najmniej jeden (wstępnie utworzone), można użyć element LiveEvent **punkty końcowe przesyłania strumieniowego**. Aby to osiągnąć, należy utworzyć nową [na żywo dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs) na **wydarzenie na żywo**. 

**Na żywo dane wyjściowe** obiektu przypomina rejestratora taśm, który będzie przechwytywać i zarejestruj transmisji strumieniowej na żywo do elementu zawartości w ramach konta usługi Media Services. Zarejestrowana zawartość zostaną utrwalone do konta magazynu platformy Azure dołączonych do konta, do kontenera, zdefiniowane przez zasób trwały.  **Na żywo dane wyjściowe** również pozwala na kontrolowanie niektórych właściwości wychodzącej transmisji strumieniowej na żywo, takie jak ile strumienia jest przechowywana w nagraniu archiwum (na przykład pojemność funkcji DVR w chmurze). Archiwum na dysku jest cykliczna archiwum "okno" zawiera tylko ilość zawartości, który jest określony w **archiveWindowLength** właściwość **na żywo dane wyjściowe**. Zawartość, która wykracza poza tym oknem jest automatycznie odrzucane z kontenera magazynu, a nie jest możliwe do odzyskania. Możesz tworzyć wiele **na żywo dane wyjściowe** (maksymalnie trzy maksymalna) na **wydarzenie na żywo** archiwum różne długości i ustawień.  

Usługa Media Services możesz korzystać z zalet **funkcję dynamicznego tworzenia pakietów**, co pozwala na przeglądanie i emisji strumieni na żywo w [formatów MPEG DASH, HLS i Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z udziału kanału informacyjnego Aby wysłać do usługi. Przeglądającym można odtwarzać transmisji strumieniowej na żywo za pomocą dowolnego odtwarzaczy zgodne HLS, DASH lub Smooth Streaming. Możesz użyć [usługi Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) w sieci web lub aplikacji mobilnych, aby dostarczać strumień we wszystkich tych protokołów.

Usługa Media Services umożliwia dostarczanie zawartości dynamicznie zaszyfrowany (**szyfrowania dynamicznego**) za pomocą Advanced Encryption Standard (AES-128) lub z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM licencje do autoryzowanych klientów. Aby uzyskać więcej informacji na temat sposobu szyfrowanie zawartości przy użyciu usługi Media Services, zobacz [ochrony zawartości — omówienie](content-protection-overview.md)

Jeśli to konieczne, można także zastosować filtrowanie dynamiczne, dzięki której może służyć do kontrolowania liczby ścieżek, formatów, szybkości transmisji i prezentacji okna czasowe, które są wysłane do odtwarzaczy. Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

Aby uzyskać informacje dotyczące nowych możliwości dla transmisji strumieniowej na żywo w wersji 3, zobacz [wskazówek dotyczących migracji do przenoszenia z usługi Media Services v2 do v3](migrate-from-v2-to-v3.md).

## <a name="live-event-types"></a>Typy zdarzeń na żywo

A [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: kodowanie przekazywania i na żywo. 

### <a name="pass-through"></a>Przekazywanie

![przekazywane](./media/live-streaming/pass-through.png)

Przy użyciu przekazywanego **wydarzenie na żywo**, opierają się na swoje lokalny koder na żywo do generowania wielu strumienia wideo o szybkości transmisji bitów i wysyłania, że jako udział Kanał informacyjny do wydarzenie na żywo (przy użyciu protokołu RTMP lub pofragmentowany plik MP4). Wydarzenie na żywo następnie niesie ze sobą za pośrednictwem przychodzących strumieni wideo bez dalszego przetwarzania. Takie przekazywanego element LiveEvent jest zoptymalizowany do wydarzeń na żywo długotrwałych lub 24 x 365 liniowej transmisja strumieniowa na żywo. Podczas tworzenia tego typu zdarzenia na żywo, należy określić Brak (LiveEventEncodingType.None).

Możesz wysłać wkład kanał przy rozdzielczości, maksymalnie 4 K oraz w ramce stopień 60 klatek na sekundę koder H.264/AVC lub H.265/HEVC koderów-dekoderów wideo i AAC (AAC-LC, HE-AACv1 lub HE-AACv2) kodera-dekodera audio.  Zobacz [wydarzenie na żywo typów porównania i ograniczenia](live-event-types-comparison.md) artykuł, aby uzyskać więcej informacji.

> [!NOTE]
> Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
> 

Zobacz przykład połączenia na żywo w [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kodowanie na żywo  

![funkcji Live encoding](./media/live-streaming/live-encoding.png)

Korzystając z kodowania na żywo za pomocą usługi Media Services, należy skonfigurować usługi na lokalny koder na żywo, aby wysłać pojedyncza szybkość transmisji bitów wideo jako udział Kanał informacyjny do wydarzenie na żywo (przy użyciu protokołu RTMP lub podzielonej zawartości w formacie Mp4). Wydarzenie na żywo koduje tej przychodzącej pojedyncza szybkość transmisji bitów do usługi stream [wielu strumienia wideo o szybkości transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), udostępnia dostarczania odtworzyć urządzeń za pośrednictwem protokołów, takich jak MPEG-DASH, HLS i Smooth Streaming. Podczas tworzenia tego typu zdarzenia na żywo, określ typ kodowania jako **standardowa** (LiveEventEncodingType.Standard).

Możesz wysłać wkład kanału informacyjnego w maksymalnie 1080 p rozwiązania z prędkością 30 klatek, / sekundę, przy użyciu kodera-dekodera wideo koder H.264/AVC i AAC (AAC-LC, HE-AACv1 lub HE-AACv2) kodera-dekodera audio. Zobacz [wydarzenie na żywo typów porównania i ograniczenia](live-event-types-comparison.md) artykuł, aby uzyskać więcej informacji.

## <a name="live-event-types-comparison"></a>Porównanie typów zdarzeń na żywo

Następujący artykuł zawiera tabelę, która zawiera porównanie funkcji dwa typy wydarzenie na żywo: [Porównanie](live-event-types-comparison.md).

## <a name="live-output"></a>Dane wyjściowe na żywo

A [na żywo dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs) umożliwia sterowanie właściwości wychodzącej transmisji strumieniowej na żywo, takie jak ilość strumienia jest rejestrowane (na przykład pojemność funkcji DVR w chmurze) i czy osoby oglądające można uruchomić, oglądając transmisji strumieniowej na żywo. Relacja między **wydarzenie na żywo** i jego **na żywo dane wyjściowe**s relacji jest podobny do tradycyjnych telewizyjnych emisji, zgodnie z którą kanału (**wydarzenie na żywo**) reprezentuje stały strumień i nagranie wideo (**na żywo dane wyjściowe**) obejmuje zasięgiem segmencie określony czas (na przykład wieczorami wiadomości od 18:30:00 do 19:00:00). Można rejestrować za pomocą cyfrowego rejestratora wideo (DVR) telewizyjnych — funkcji równoważnej w LiveEvents odbywa się za pomocą właściwości ArchiveWindowLength. Nadszedł czas przedział czasu ISO 8601 (na przykład PTHH:MM:SS) Określa pojemność DVR, która może być równa z co najmniej 3 minuty maksymalnie 25 godzin.

> [!NOTE]
> **Dane wyjściowe na żywo**s start przy tworzeniu i Zatrzymaj po usunięciu. Po usunięciu **na żywo dane wyjściowe**, nie powoduje usunięcia podstawowych **zasobów** i zawartości w elemencie zawartości. 
>
> Po opublikowaniu **na żywo dane wyjściowe** zasobów przy użyciu **lokalizatora przesyłania strumieniowego**, **wydarzenie na żywo** (maksymalna długość okna DVR) nadal będzie widoczny do momentu **Lokalizatora przesyłania strumieniowego**jego wygaśnięcia lub usunięcia, zależnie co nastąpi wcześniej.

Aby uzyskać więcej informacji, zobacz [używanie funkcji DVR w chmurze](live-event-cloud-dvr.md).

## <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego

Po przesłaniu strumienia do **wydarzenie na żywo**, można rozpocząć zdarzenie przesyłania strumieniowego, tworząc **zasobów**, **na żywo dane wyjściowe**, i **lokalizatora przesyłania strumieniowego** . Spowoduje to archiwizację strumienia i udostępnić go użytkownikom za pośrednictwem [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Po utworzeniu konta usługi Media Services domyślny punkt końcowy przesyłania strumieniowego jest dodawany do swojego konta w stanie zatrzymania. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość musi być w stanie uruchomiona.

## <a name="a-idbilling-live-event-states-and-billing"></a><a id="billing" />Stany zdarzenia na żywo i rozliczeń

A **wydarzenie na żywo** rozpoczyna się rozliczeń zaraz po jego stan przechodzi do **systemem**. Aby zatrzymać wydarzenie na żywo z rozliczeniami, musisz zatrzymać wydarzenie na żywo.

Aby uzyskać szczegółowe informacje, zobacz [stanów i rozliczenia](live-event-states-billing.md).

## <a name="latency"></a>Opóźnienie

Aby uzyskać szczegółowe informacje na temat LiveEvents opóźnienia, zobacz [opóźnienie](live-event-latency.md).

## <a name="next-steps"></a>Kolejne kroki

- [Porównanie typów zdarzeń na żywo](live-event-types-comparison.md)
- [Stany i rozliczenia](live-event-states-billing.md)
- [Opóźnienie](live-event-latency.md)
