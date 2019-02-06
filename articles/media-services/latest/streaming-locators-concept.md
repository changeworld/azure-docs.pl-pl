---
title: Przesyłanie strumieniowe lokalizatorów w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera opis Lokalizatory przesyłania strumieniowego są i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: be66dcf8115258b6f593ec913e75785a3f8dbe1f
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743484"
---
# <a name="streaming-locators"></a>Lokalizatory przesyłania strumieniowego

Aby wprowadzić filmów wideo w danych wyjściowych dostępne dla klientów do odtwarzania elementu zawartości, należy utworzyć [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) i późniejszego kompilowania adresów URL przesyłania strumieniowego. Aby uzyskać przykład .NET, zobacz [uzyskać Lokalizator przesyłania strumieniowego](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia **lokalizatora przesyłania strumieniowego**, należy określić [zasobów](https://docs.microsoft.com/rest/api/media/assets) nazwy i [przesyłania strumieniowego zasad](https://docs.microsoft.com/rest/api/media/streamingpolicies) nazwy. Można użyć jednej z wstępnie zdefiniowane zasady przesyłania strumieniowego lub utworzenia niestandardowych zasad. Wstępnie zdefiniowane zasady obecnie dostępne są: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" i "Predefined_MultiDrmStreaming". Korzystając z niestandardowego przesyłania strumieniowego zasady, należy zaprojektować ograniczony zestaw tych zasad dla swojego konta usługi Media i ponownie ich użyć dla Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje i protokołów. 

Jeśli chcesz określić opcje szyfrowania na strumień, należy utworzyć [zasad klucza zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies) , konfiguruje sposób dostarczania klucza zawartości dla klientów za pośrednictwem składnika dostarczania klucza usługi Media Services końcowych. Skojarzenia usługi lokalizatora przesyłania strumieniowego za pomocą **zasad klucza zawartości** i klucza zawartości. Usługi Media Services można pozostawić opcję automatycznego generowania klucza. W poniższym przykładzie .NET zawiera instrukcje dotyczące konfigurowania szyfrowania AES z tokenu ograniczeń w wersji 3 usługa Media Services: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **Zasady kluczy zawartości** są aktualizowalne, możesz chcieć zaktualizować zasady, jeśli musisz przeprowadzić rotację kluczy. Może upłynąć do 15 minut w przypadku pamięci podręcznych dostarczania klucza do aktualizacji i wybierze zaktualizowane zasady. Zalecane jest, aby nie tworzyć nowe zasady klucz zawartości dla każdego lokalizatora przesyłania strumieniowego. Należy spróbować ponownie użyć istniejących zasad, zawsze wtedy, gdy potrzebne są te same opcje.

> [!IMPORTANT]
> * Właściwości **Lokalizatory przesyłania strumieniowego** będące daty/godziny są zawsze w formacie UTC.
> * Należy zaprojektować ograniczony zestaw zasad dla swojego konta usługi multimediów i ponownie ich użyć dla Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje. 

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek: Przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo przy użyciu platformy .NET](stream-files-tutorial-with-api.md)
* [Użyj DRM dynamiczne szyfrowanie i licencji usługi dostarczania](protect-with-drm.md)
