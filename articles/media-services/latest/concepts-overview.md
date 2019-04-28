---
title: Pojęcia dotyczące usługi Azure Media Services — Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera krótkie omówienie koncepcji usługi Azure Media Services i zawiera łącza, aby uzyskać szczegółowe informacje.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 74a4ee03562963c8a50159f085e4b76b6d461ed9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103856"
---
# <a name="media-services-concepts"></a>Pojęcia dotyczące usługi Media Services

Ten temat zawiera krótkie omówienie koncepcji usługi Azure Media Services i zawiera linki do artykułów o szczegółowe informacje z usługi Media Services v3 pojęć i funkcji. Przed rozpoczęciem programowania należy zapoznać się z opisami podstawowych pojęć zawartymi w tych tematach.

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](developers-guide.md).

## <a name="cloud-upload-and-storage"></a>Przekazywanie do chmury i magazynowanie w niej

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowania i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto usługi Media Services i przekazać pliki cyfrowe do **zasoby**.

- [Przekazywanie do chmury i magazynowanie w niej](storage-account-concept.md)
- [Koncepcja zasobów](assets-concept.md)

## <a name="encoding"></a>Kodowanie

Po przekazaniu plików multimediów cyfrowych wysokiej jakości do zasobów, zakodować je do formatów, które można odtwarzać na różnych przeglądarek i urządzeń. 

Kodowanie za pomocą usługi Media Services v3, musisz utworzyć **przekształca** i **zadań**.

![Przekształcenia](./media/encoding/transforms-jobs.png)

- [Transformacje i zadania](transforms-jobs-concept.md)
- [Kodowanie za pomocą usługi Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Analiza multimediów

Do analizowania plików audio i wideo, należy również utworzyć **przekształca** i **zadań**.

- [Analizowanie plików audio i wideo](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Tworzenie pakietów, dostarczanie, ochrona

Gdy zawartość jest zakodowany, możesz korzystać z zalet **funkcję dynamicznego tworzenia pakietów**. **Punkt końcowy przesyłania strumieniowego** usługa funkcję dynamicznego tworzenia pakietów w usłudze Media Services umożliwia dostarczanie zawartości multimedialnej dla graczy klienta. Aby udostępnić pliki wideo w zasobach wyjściowych klientów do odtwarzania, należy utworzyć **lokalizatora przesyłania strumieniowego** i późniejszego kompilowania adresów URL przesyłania strumieniowego. 

Podczas tworzenia **lokalizatora przesyłania strumieniowego**, oprócz nazwy zasobu, należy określić **przesyłania strumieniowego zasad**. **Przesyłanie strumieniowe zasady** umożliwiają definiowanie protokołów przesyłania strumieniowego i szyfrowania opcje (jeśli istnieje) dla Twojego **Lokalizatory przesyłania strumieniowego**.

Funkcję dynamicznego tworzenia pakietów jest używany, czy strumieniowo zawartość na żywo lub na żądanie. Na poniższym diagramie przedstawiono przesyłania strumieniowego na żądanie za pomocą funkcji dynamicznego tworzenia pakietów przepływu pracy.

![Dynamiczne tworzenie pakietów](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Usługa Media Services umożliwia dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128) lub / i systemów zarządzania (prawami cyfrowymi DRM) trzech głównych prawami cyfrowymi: PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów.

W przypadku określenia opcji szyfrowania na strumień, należy utworzyć **zasad klucza zawartości** i powiąż ją z Twojej **lokalizatora przesyłania strumieniowego**. **Zasad klucza zawartości** można skonfigurować sposób dostarczania klucza zawartości dla klientów końcowych.

Na poniższym obrazie przedstawiono przepływ pracy usługi Media Services Ochrona zawartości: 

![Ochrona zawartości](./media/content-protection/content-protection.svg)

&#42;szyfrowanie dynamiczne obsługuje klucza AES-128"Wyczyść", CBCS i CENC. 

Można użyć usługi Media Services **manifestów dynamicznych** do przesyłania strumieniowego tylko określonej wersji lub subclips filmu wideo. W poniższym przykładzie koder użytego do kodowania zasobów mezzanine do siedmiu odwzorowaniami wideo każdego pliku MP4 z ISO (z 180p, aby 1080p). Zakodowanym elementem zawartości można dynamicznie spakowane do żadnego z następujących protokołów: HLS, MPEG DASH i Smooth.  W górnej części diagramu są wyświetlane manifest HLS dla zasobu bez filtrów (zawiera wszystkie wersje siedem).  W lewym dolnym rogu manifest HLS, do którego zastosowano filtr o nazwie "ott" jest wyświetlana. Filtr "ott" Określa, aby usunąć wszystkie różnych poniżej 1 MB/s, co spowodowało dolnej dwa poziomy jakości jest usunięta, a w odpowiedzi. W prawym dolnym rogu manifest HLS, do którego zastosowano filtr o nazwie "mobilnymi" jest wyświetlany. Filtr "przenośnych" Określa usunięcie wersji, gdy rozwiązanie jest większy niż 720p, co spowodowało dwóch wersji 1080p jest odłączony.

![Filtrowania wyświetlania](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [dynamicznego tworzenia pakietów](dynamic-packaging-overview.md)
- [Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md)
- [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)
- [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
- [Zasady kluczy zawartości](content-key-policy-concept.md)
- [Ochrona zawartości](content-protection-overview.md)
- [Manifesty dynamiczne](filters-dynamic-manifest-overview.md)
- [Filtry](filters-concept.md)

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo

Usługa Azure Media Services umożliwia dostarczanie wydarzeń na żywo dla klientów w chmurze Azure. **Wydarzenia na żywo** odpowiadają za pozyskiwanie i przetwarzanie strumieni wideo na żywo. Po utworzeniu **wydarzenie na żywo**, wejściowy punkt końcowy jest tworzona, której można wysyłać sygnał na żywo z kodera zdalnego. Po przesłaniu strumienia do **wydarzenie na żywo**, można rozpocząć zdarzenie przesyłania strumieniowego, tworząc **zasobów**, **na żywo dane wyjściowe**, i **lokalizatora przesyłania strumieniowego** . **Dane wyjściowe na żywo** spowoduje zarchiwizowanie strumienia do **zasobów** i udostępnić go użytkownikom za pośrednictwem **punkt końcowy przesyłania strumieniowego**. A **wydarzenie na żywo** może być jednym z dwóch typów: **przekazywanego** i **kodowanie na żywo**.

Na poniższym obrazie przedstawiono przepływu pracy typu przekazywania:

![Przekazywanie](./media/live-streaming/pass-through.svg)

- [Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
- [Wydarzenia i dane wyjściowe na żywo](live-events-outputs-concept.md)

## <a name="monitoring"></a>Monitorowanie

### <a name="event-grid"></a>Event Grid

Aby wyświetlić postęp zadania, należy użyć **usługi Event Grid**. Usługa Media Services emituje również typy zdarzeń na żywo. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. 

- [Obsługa zdarzeń usługi Event Grid](reacting-to-media-services-events.md)
- [Schematy](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Monitorowanie metryk i dzienników diagnostycznych, które pomagają zrozumieć, jak Twoje aplikacje działają z usługą Azure Monitor.

- [Metryki i dzienniki diagnostyczne](media-services-metrics-diagnostic-logs.md)
- [Schematy dla dzienników diagnostycznych](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Odtwarzacze klienckie

Usługa Azure Media Player umożliwia odtwarzanie multimediów udostępnianych przesyłane strumieniowo przez usługę Media Services na podstawie różnych przeglądarek i urządzeń. Usługa Azure Media Player używa standardów branżowych, takich jak HTML5, rozszerzenia źródło nośnika (MSE) i Encrypted Media Extensions (EME), aby zapewnić zaawansowane adaptacyjne środowisko przesyłania strumieniowego. 

- [Omówienie usługi Azure Media Player](use-azure-media-player.md)

## <a name="provide-feedback"></a>Przekazywanie opinii

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

* [Kodowanie pliku zdalnego i przesyłanie strumieniowe wideo — REST](stream-files-tutorial-with-rest.md)
* [Kodowanie przekazanego pliku i strumienia wideo — .NET](stream-files-tutorial-with-api.md)
* [Stream na żywo — .NET](stream-live-tutorial-with-api.md)
* [Analizowanie filmu wideo — .NET](analyze-videos-tutorial-with-api.md)
* [Szyfrowanie AES-128 - .NET](protect-with-aes128.md)
* [Dynamiczne szyfrowanie przy użyciu technologii multi-DRM — .NET](protect-with-drm.md) 
