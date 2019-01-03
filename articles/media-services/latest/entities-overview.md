---
title: Omówienie jednostek w usłudze Azure Media Services — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie jednostek usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1d309bb550282d5245a2fbf74f14a931cf5c2279
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53746132"
---
# <a name="azure-media-services-entities-overview"></a>Omówienie jednostek w usłudze Azure Media Services

Ten artykuł zawiera krótkie omówienie punktów i jednostki usługi Azure Media Services do artykułu, aby uzyskać więcej informacji na temat każdej jednostki. 

| Temat | Opis |
|---|---|
| [Filtry kont i zasobów filtrów](filters-dynamic-manifest-overview.md)|Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) klienta może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie [filtrów kont](https://docs.microsoft.com/rest/api/media/accountfilters) i [filtry zasobów](https://docs.microsoft.com/rest/api/media/assetfilters). Następnie należy użyć **manifestów dynamicznych** oparte na wstępnie zdefiniowanych filtrów. |
| [Zasoby](assets-concept.md)|[Zasobów](https://docs.microsoft.com/rest/api/media/assets) w jednostce (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów) pliki cyfrowe oraz metadane dotyczące tych plików. Przekazane pliki cyfrowe do elementu zawartości używać w usłudze Media Services, kodowanie, przesyłanie strumieniowe, analizowanie zawartości przepływów pracy.|
| [Zasady kluczy zawartości](content-key-policy-concept.md)|Usługa Media Services umożliwia zabezpieczenie multimediów od momentu wysłania komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Usługa Media Services można dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128), lub z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów.|
| [LiveEvents i LiveOutputs](live-events-outputs-concept.md)|Usługa Media Services umożliwia dostarczanie wydarzeń na żywo dla klientów w chmurze Azure. Aby skonfigurować usługi przesyłania strumieniowego wydarzeń na żywo usługi Media Services v3, musisz Dowiedz się więcej o [zdarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents) i [na żywo dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs).|
| [Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md)|A [punkty końcowe przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) jednostki reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub aby Content Delivery Network (CDN) w celu dalszego rozpowszechniania. Wychodzące strumienia usługi punktu końcowego przesyłania strumieniowego może być strumień na żywo lub element zawartości wideo na żądanie w ramach konta usługi Media Services. Po utworzeniu konta usługi Media Services, **domyślne** punkt końcowy przesyłania strumieniowego zostanie utworzony w stanie zatrzymania. Nie można usunąć **domyślne** punkt końcowy przesyłania strumieniowego. Dodatkowe punkty końcowe przesyłania strumieniowego mogą być tworzone w ramach konta. Aby rozpocząć przesyłanie strumieniowe filmów wideo, należy uruchomić punkt końcowy przesyłania strumieniowego, z którego chcesz atream filmu wideo. |
| [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)|Konieczne jest zapewnienie klientom za pomocą adresu URL, który może służyć do odtwarzania zakodowane pliki audio lub wideo, musisz utworzyć [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) i tworzyć adresy URL przesyłania strumieniowego.|
| [Zasady przesyłania strumieniowego](streaming-policy-concept.md)| [Przesyłanie strumieniowe zasady](https://docs.microsoft.com/rest/api/media/streamingpolicies) umożliwiają definiowanie protokołów przesyłania strumieniowego i opcje szyfrowania dla Twojego StreamingLocators. Można określić nazwę zasad niestandardowych przesyłania strumieniowego i został utworzony lub użyj jednej z wstępnie zdefiniowane zasady przesyłania strumieniowego, oferowane przez usługę Media Services. <br/><br/>Za pomocą niestandardowych zasad usługi przesyłania strumieniowego, należy zaprojektować ograniczony zestaw tych zasad dla swojego konta usługi Media i ponownie ich użyć dla Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje szyfrowania i protokołów. Należy nie można utworzeniem nowej zasady przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.|
| [Transformacje i zadania](transforms-jobs-concept.md)|Użyj [przekształca](https://docs.microsoft.com/rest/api/media/transforms) skonfigurować typowych zadań związanych z kodowaniem lub analizowanie filmów wideo. Każdy **Przekształcanie** opisuje młyna lub przepływu pracy zadań przetwarzania plików wideo lub audio.<br/><br/>A [zadania](https://docs.microsoft.com/rest/api/media/jobs) jest rzeczywistego żądania do usługi Azure Media Services, aby zastosować **Przekształcanie** do danego wejściowego zawartości wideo lub audio. **Zadania** określa informacje, takie jak lokalizacja wejściowych plików wideo i lokalizację danych wyjściowych. Można określić lokalizację je wideo przy użyciu: Adresy URL HTTPS, adresów URL sygnatury dostępu Współdzielonego lub zasobu.|

## <a name="next-steps"></a>Kolejne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
