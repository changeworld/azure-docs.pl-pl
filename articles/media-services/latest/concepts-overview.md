---
title: Terminologia i pojęcia usługi Media Services
titleSuffix: Azure Media Services
description: Dowiedz się więcej o terminologii i pojęciach dotyczących usługi Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500089"
---
# <a name="media-services-terminology-and-concepts"></a>Terminologia i pojęcia usługi Media Services

W tym temacie przedstawiono krótkie omówienie terminologii i pojęć usługi Azure Media Services. W tym artykule znajdują się również łącza do artykułów z dogłębnym wyjaśnieniem pojęcia i funkcji usługi Media Services w wersji 3.

Podstawowe pojęcia opisane w tych tematach powinny zostać poddane przeglądowi przed rozpoczęciem rozwoju.

> [!NOTE]
> Obecnie można użyć [witryny Azure Portal](https://portal.azure.com/) do: zarządzania [zdarzeniami na żywo](live-events-outputs-concept.md)usługi Media Services w wersji 3, wyświetlania (nie manage) [zasobów](assets-concept.md)w wersji 3 i [uzyskiwania informacji o uzyskiwaniu dostępu do interfejsów API](access-api-portal.md).
> W przypadku wszystkich innych zadań zarządzania (na przykład [przekształceń i zadań](transforms-jobs-concept.md) oraz [ochrony zawartości)](content-protection-overview.md)użyj [interfejsu API REST,](https://aka.ms/ams-v3-rest-ref) [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z [obsługiwanych pakietów SDK.](media-services-apis-overview.md#sdks)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologia usługi Media Services w wersji 3

|Termin|Opis|
|---|---|
|Wydarzenie na żywo|**Zdarzenie na żywo** reprezentuje potok do pozyskiwania, transkodowania (opcjonalnie) i pakowania strumieni na żywo metadanych wideo, audio i w czasie rzeczywistym.<br/><br/>W przypadku klientów migrujących z interfejsów API usługi Media Services w wersji 2 **zdarzenie na żywo** zastępuje jednostkę **Kanał** w wersji 2. Aby uzyskać więcej informacji, zobacz [Migracja z wersji 2 do v3](migrate-from-v2-to-v3.md).|
|Strumieniowe przesyłanie punktów końcowych/pakowanie/pochodzenie|**Punkt końcowy przesyłania strumieniowego** reprezentuje dynamiczną (just-in-time) usługę pakowania i pochodzenia, która może dostarczać zawartość na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienta. Używa jednego ze wspólnych protokołów multimediów strumieniowych (HLS lub DASH). Ponadto punkt **końcowy przesyłania strumieniowego** zapewnia dynamiczne szyfrowanie (just-in-time) do wiodących w branży systemów zarządzania prawami cyfrowymi (DRM).<br/><br/>W branży strumieniowego przesyłania multimediów usługa ta jest powszechnie określana jako **Packager** lub **Origin**.  Inne typowe terminy w branży dla tej funkcji obejmują JITP (just-in-time-packager) lub JITE (szyfrowanie just-in-time).

## <a name="media-services-v3-concepts"></a>Pojęcia dotyczące usługi Media Services w wersji 3

|Pojęcia|Opis|Linki|
|---|---|---|
|Zasoby i przesyłanie treści|Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości **multimediów**na platformie Azure, należy utworzyć konto usługi Media Services i przekazać pliki cyfrowe do zasobów .|[Przekazywanie do chmury i magazynowanie w niej](storage-account-concept.md)<br/><br/>[Koncepcja zasobów](assets-concept.md)|
|Kodowanie zawartości|Po przesłaniu wysokiej jakości plików multimediów cyfrowych do zasobów można je zakodować w formatach, które można odtwarzać w różnych przeglądarkach i na różnych urządzeniach. <br/><br/>Aby zakodować za pomocą usługi Media Services w wersji 3, należy utworzyć **transformacje** i **zadania**.|[Przekształcenia i zadania](transforms-jobs-concept.md)<br/><br/>[Kodowanie za pomocą usługi Media Services](encoding-concept.md)|
|Analizowanie zawartości (Video Indexer)|Usługi Media Services w wersji 3 umożliwiają wyodrębnianie szczegółowych informacji z plików wideo i audio przy użyciu ustawień predefiniowanych usługi Media Services w wersji 3. Aby analizować zawartość przy użyciu ustawień predefiniowanych usługi Media Services w wersji 3, należy utworzyć **transformacje** i **zadania**.<br/><br/>Jeśli chcesz uzyskać bardziej szczegółowe informacje, użyj [wideoindymatora](https://docs.microsoft.com/azure/media-services/video-indexer/) bezpośrednio.|[Analizowanie plików wideo i audio](analyzing-video-audio-files-concept.md)|
|Tworzenie pakietów i dostarczanie|Po zakodowaniu zawartości można skorzystać z **dynamicznego pakowania.** W usłudze Media Services **punkt końcowy przesyłania strumieniowego** to usługa dynamicznego pakowania używana do dostarczania zawartości multimedialnej graczom klienckim. Aby udostępnić klientom filmy w zasobie wyjściowym do odtwarzania, należy utworzyć **lokalizator przesyłania strumieniowego,** a następnie utworzyć adresy URL przesyłania strumieniowego. <br/><br/>Podczas tworzenia **lokalizatora przesyłania strumieniowego,** oprócz nazwy zasobu, należy określić **zasady przesyłania strumieniowego**. **Zasady przesyłania strumieniowego** umożliwiają definiowanie protokołów przesyłania strumieniowego i opcji szyfrowania (jeśli istnieją) dla **lokalizatorów przesyłania strumieniowego**. Dynamiczne pakowanie jest używane niezależnie od tego, czy przesyłasz strumieniowo zawartość na żywo, czy na żądanie. <br/><br/>Za pomocą **manifestów dynamicznych** usługi Media Services można przesyłać strumieniowo tylko określone wersje lub podkompansy wideo.|[Dynamiczne pakowanie](dynamic-packaging-overview.md)<br/><br/>[Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md)<br/><br/>[Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)<br/><br/>[Zasady przesyłania strumieniowego](streaming-policy-concept.md)<br/><br/>[Manifesty dynamiczne](filters-dynamic-manifest-overview.md)<br/><br/>[Filtry](filters-concept.md)|
|Ochrona zawartości|Usługa Media Services umożliwia dynamiczne szyfrowanie zawartości na żywo i na żądanie za pomocą zaawansowanego standardu szyfrowania (AES-128) lub/128) lub dowolnego z trzech głównych systemów DRM: Microsoft PlayReady, Google Widevine i Apple FairPlay. Usługa Media Services zapewnia również usługę dostarczania licencji kluczy AES i DRM (PlayReady, Widevine i FairPlay) autoryzowanym klientom. <br/><br/>Jeśli określisz opcje szyfrowania w strumieniu, utwórz **zasady klucza zawartości** i skojarz je z **lokalizatorem przesyłania strumieniowego**. **Zasady klucza zawartości** umożliwiają skonfigurowanie sposobu dostarczania klucza zawartości klientom końcowym.<br/><br/> Spróbuj ponownie użyć zasad, gdy są potrzebne te same opcje.| [Zasady klucza zawartości](content-key-policy-concept.md)<br/><br/>[Ochrona zawartości](content-protection-overview.md)|
|Transmisja strumieniowa na żywo|Usługa Media Services umożliwia dostarczanie zdarzeń na żywo do klientów w chmurze platformy Azure. **Wydarzenia na żywo** odpowiadają za pozyskiwanie i przetwarzanie strumieni wideo na żywo. Podczas tworzenia **zdarzenia na żywo**tworzony jest wejściowy punkt końcowy, którego można użyć do wysłania sygnału na żywo ze zdalnego kodera. Po przejściu strumienia do **zdarzenia na żywo**można rozpocząć wydarzenie przesyłania strumieniowego, tworząc **zasób**, **wyjście na żywo**i **lokalizator przesyłania strumieniowego**. **Wyjście na żywo** zarchiwizuje strumień do **zasobu** i udostępni go widzom za pośrednictwem **punktu końcowego przesyłania strumieniowego.** Zdarzenie na żywo można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień wielu bitrate) lub *kodowanie* na żywo (lokalny koder na żywo wysyła pojedynczy strumień szybkości transmisji bitów). |[Omówienie transmisji na żywo](live-streaming-overview.md)<br/><br/>[Wydarzenia i dane wyjściowe na żywo](live-events-outputs-concept.md)|
|Monitorowanie za pomocą siatki zdarzeń|Aby wyświetlić postęp zadania, użyj siatki **zdarzeń**. Usługa Media Services emituje również typy zdarzeń na żywo. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. |[Obsługa zdarzeń usługi Event Grid](reacting-to-media-services-events.md)<br/><br/>[Schematy](media-services-event-schemas.md)|
|Monitorowanie za pomocą usługi Azure Monitor|Monitoruj metryki i dzienniki diagnostyczne, które pomagają zrozumieć, jak aplikacje działają za pomocą usługi Azure Monitor.|[Metryki i dzienniki diagnostyczne](media-services-metrics-diagnostic-logs.md)<br/><br/>[Schematy dzienników diagnostycznych](media-services-diagnostic-logs-schema.md)|
|Odtwarzacze klienckie|Za pomocą programu Azure Media Player można odtwarzać zawartość multimedialną przesyłaną strumieniowo przez usługę Media Services w różnych przeglądarkach i na różnych urządzeniach. Usługa Azure Media Player używa standardów branżowych, takich jak HTML5, Rozszerzenia źródeł multimediów (MSE) i Rozszerzenia zaszyfrowanych multimediów (EME), aby zapewnić wzbogacone środowisko adaptacyjnego przesyłania strumieniowego. |[Omówienie usługi Azure Media Player](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

* [Kodowanie pliku zdalnego i strumieniowe przesyłanie wideo — REST](stream-files-tutorial-with-rest.md)
* [Kodowanie przekazanego pliku i strumieniowe przesyłanie wideo — .NET](stream-files-tutorial-with-api.md)
* [Przesyłanie strumieniowe na żywo — .NET](stream-live-tutorial-with-api.md)
* [Analizowanie wideo — .NET](analyze-videos-tutorial-with-api.md)
* [Szyfrowanie dynamiczne AES-128 — .NET](protect-with-aes128.md)
* [Dynamiczne szyfrowanie za pomocą multi-DRM - .NET](protect-with-drm.md)
