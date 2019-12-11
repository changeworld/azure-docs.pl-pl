---
title: Azure Media Services terminologii i koncepcje — Azure | Microsoft Docs
description: Ten temat zawiera krótkie omówienie Azure Media Services terminologii i koncepcji oraz linki do dalszych szczegółów.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 39bdcc94b785371044b5d49fd844a06a176a8fba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970040"
---
# <a name="media-services-concepts"></a>Koncepcje Media Services

Ten temat zawiera krótkie omówienie Azure Media Services terminologii i koncepcji. Artykuł zawiera również linki do artykułów z szczegółowym wyjaśnieniem pojęć związanych z Media Services i funkcjonalnością programu v3. 

Przed rozpoczęciem programowania należy zapoznać się z opisami podstawowych pojęć zawartymi w tych tematach.

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

## <a name="terminology"></a>Terminologia

W tej sekcji przedstawiono sposób, w jaki niektóre typowe warunki branżowe są mapowane do interfejsu API Media Services v3.

### <a name="live-event"></a>Wydarzenie na żywo

**Wydarzenie na żywo** reprezentuje potok do pozyskiwania, transkodowania (opcjonalnie) i tworzenia pakietów na żywo strumieni wideo, audio i metadanych w czasie rzeczywistym.

W przypadku klientów migrowania z Media Services V2 API **zdarzenie na żywo** zastępuje jednostkę **kanału** w wersji 2. Aby uzyskać więcej informacji, zobacz [Migrowanie z wersji 2 do V3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Punkt końcowy przesyłania strumieniowego (pakowanie i źródło)

**Punkt końcowy przesyłania strumieniowego** reprezentuje dynamiczny (just-in-Time) pakiet i pierwotną usługę, która umożliwia dostarczanie zawartości na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienta przy użyciu jednego z popularnych protokołów multimediów przesyłania strumieniowego (HLS lub kreski). Ponadto **punkt końcowy przesyłania strumieniowego** zapewnia dynamiczne (just-in-Time) szyfrowanie do wiodących w branży protokołów DRM.

W branży przesyłania strumieniowego multimediów ta usługa jest często określana jako **Pakowarka** lub **Źródło**.  Inne typowe warunki w branży dla tej możliwości obejmują JITP ("just-in-Time-deJITEd") lub (just-in-Time-Encryption). 
 
## <a name="cloud-upload-and-storage"></a>Przekazywanie do chmury i magazynowanie w niej

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto Media Services i przekazać pliki cyfrowe do **zasobów**.

- [Przekazywanie do chmury i magazynowanie w niej](storage-account-concept.md)
- [Koncepcja zasobów](assets-concept.md)

## <a name="encoding"></a>Encoding

Po przekazaniu plików multimediów cyfrowych o wysokiej jakości do zasobów można je zakodować do formatów, które mogą być odtwarzane w różnych przeglądarkach i urządzeniach. 

Aby kodować z Media Services v3, należy utworzyć **transformacje** i **zadania**.

![Przekształcenia](./media/encoding/transforms-jobs.png)

- [Transformacje i zadania](transforms-jobs-concept.md)
- [Kodowanie przy użyciu Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Analiza multimediów

Aby analizować pliki wideo i audio, należy również utworzyć **transformacje** i **zadania**.

- [Analizowanie plików wideo i audio](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Tworzenie pakietów, dostarczanie, ochrona

Po zapisaniu zawartości można korzystać z funkcji **dynamicznego tworzenia pakietów**. W Media Services **punkt końcowy przesyłania strumieniowego**/Origin to dynamiczna usługa tworzenia pakietów służąca do dostarczania zawartości multimedialnej do graczy klientów. Aby umożliwić odtwarzanie filmów wideo w danych wyjściowych, należy utworzyć **lokalizator przesyłania strumieniowego** , a następnie skompilować adresy URL przesyłania strumieniowego. 

Podczas tworzenia **lokalizatora przesyłania strumieniowego**oprócz nazwy zasobu należy określić **zasady przesyłania strumieniowego**. **Zasady przesyłania strumieniowego** umożliwiają definiowanie protokołów przesyłania strumieniowego i opcji szyfrowania (jeśli istnieją) dla **lokalizatorów przesyłania strumieniowego**.

Dynamiczne pakowanie służy do przesyłania strumieniowego zawartości na żywo lub na żądanie. Na poniższym diagramie przedstawiono strumień przesyłania strumieniowego na żądanie z dynamicznym przepływem pracy tworzenia pakietów.

![Pakowanie dynamiczne](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Za pomocą Media Services można dostarczać zawartość na żywo i na żądanie zaszyfrowaną dynamicznie przy użyciu Advanced Encryption Standard (AES-128) lub/i dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów.

W przypadku określania opcji szyfrowania w strumieniu Utwórz **zasady dotyczące kluczy zawartości** i skojarz je z **lokalizatorem przesyłania strumieniowego**. **Zasady klucza zawartości** umożliwiają skonfigurowanie sposobu dostarczania klucza zawartości do klientów końcowych.

Na poniższym obrazie przedstawiono przepływ pracy usługi Media Services Ochrona zawartości: 

![Ochrona zawartości](./media/content-protection/content-protection.svg)

&#42;szyfrowanie dynamiczne obsługuje algorytm AES-128 "Wyczyść klucz", CBCS i CENC. 

Media Services **manifestów dynamicznych** można używać do przesyłania strumieniowego tylko określonych elementów lub podklipów wideo. W poniższym przykładzie koder został użyty do zakodowania zasobu Mezzanine do siedmiu plików ISO pliki MP4 (od 180p do 1080p). Zakodowany element zawartości może być dynamicznie spakowany w jednym z następujących protokołów przesyłania strumieniowego: HLS, PAUZy MPEG i gładki.  W górnej części diagramu zostanie wyświetlony manifest HLS dla elementu zawartości bez filtrów (zawiera on wszystkie siedem wersji).  W lewym dolnym rogu jest pokazywany manifest HLS, do którego zastosowano filtr o nazwie "OTT". Filtr "OTT" określa, aby usunąć wszystkie szybkości transmisji bitów poniżej 1 MB/s, co spowodowało odłączenie dwóch dolnych poziomów jakości w odpowiedzi. W prawym dolnym rogu jest pokazywany manifest HLS, do którego zastosowano filtr o nazwie "Mobile". Filtr "mobilny" określa, aby usunąć wersje, w których rozdzielczość jest większa niż 720, co spowodowało odłączenie dwóch oddzielonych wersji.

![Filtrowanie wersji](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Dynamiczne tworzenie pakietów](dynamic-packaging-overview.md)
- [Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md)
- [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)
- [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
- [Zasady kluczy zawartości](content-key-policy-concept.md)
- [Ochrona zawartości](content-protection-overview.md)
- [Manifesty dynamiczne](filters-dynamic-manifest-overview.md)
- [Filtry](filters-concept.md)

> [!NOTE]
> Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo

Azure Media Services pozwala na dostarczanie na żywo wydarzeń klientom w chmurze platformy Azure. **Wydarzenia na żywo** odpowiadają za pozyskiwanie i przetwarzanie strumieni wideo na żywo. Podczas tworzenia zdarzenia na **żywo**jest tworzony wejściowy punkt końcowy, za pomocą którego można wysyłać sygnały na żywo ze zdalnego kodera. Gdy strumień przepływa do **zdarzenia na żywo**, możesz rozpocząć zdarzenie przesyłania strumieniowego, tworząc element **zawartości**, **dane wyjściowe**i **lokalizator przesyłania strumieniowego**. Na **żywo dane wyjściowe** będą archiwizowane w **strumieniu i udostępniane** użytkownikom za pomocą **punktu końcowego przesyłania strumieniowego**. **Wydarzenie na żywo** może być jednym z dwóch typów: **przekazywaniem** i **kodowaniem na żywo**.

Na poniższej ilustracji przedstawiono przepływ pracy typu pass-through:

![Przekazywanie](./media/live-streaming/pass-through.svg)

- [Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
- [Wydarzenia i dane wyjściowe na żywo](live-events-outputs-concept.md)

## <a name="monitoring"></a>Monitorowanie

### <a name="event-grid"></a>Event Grid

Aby wyświetlić postęp zadania, należy użyć **Event Grid**. Media Services również emituje typy zdarzeń na żywo. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń i reagować na zdarzenia w praktycznie wszystkich usługach platformy Azure, a także źródłach niestandardowych. 

- [Obsługa zdarzeń Event Grid](reacting-to-media-services-events.md)
- [Punktu](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Monitoruj metryki i dzienniki diagnostyczne, które pomagają zrozumieć, jak aplikacje działają w Azure Monitor.

- [Metryki i dzienniki diagnostyczne](media-services-metrics-diagnostic-logs.md)
- [Schematy dzienników diagnostycznych](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Odtwarzacze klienckie

Azure Media Player umożliwia odtwarzanie zawartości multimedialnej przesyłanej przez Media Services na wielu różnych przeglądarkach i urządzeniach. Azure Media Player wykorzystuje standardy branżowe, takie jak HTML5, rozszerzenia źródła nośników (MSE) i rozszerzenia nośników zaszyfrowanych (EME), aby zapewnić wzbogacone, adaptacyjne środowisko przesyłania strumieniowego. 

- [Omówienie usługi Azure Media Player](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

* [Kodowanie zdalnego pliku i wideo strumieniowego — REST](stream-files-tutorial-with-rest.md)
* [Kodowanie przekazanego pliku i strumienia wideo — .NET](stream-files-tutorial-with-api.md)
* [Przesyłanie strumieniowe na żywo — .NET](stream-live-tutorial-with-api.md)
* [Analizowanie wideo — .NET](analyze-videos-tutorial-with-api.md)
* [Szyfrowanie dynamiczne AES-128 — .NET](protect-with-aes128.md)
* [Szyfrowanie dynamiczne za pomocą technologii wielowątkowości platformy .NET](protect-with-drm.md) 
