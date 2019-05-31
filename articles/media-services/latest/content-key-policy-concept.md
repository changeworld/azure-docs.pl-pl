---
title: Zawartość zasady kluczy w usłudze Media Services — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wyjaśnienie, co to są zasady klucz zawartości i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a1d2cc50b405df2c71d94e74973b3291a4e908cb
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393484"
---
# <a name="content-key-policies"></a>Zasady kluczy zawartości

Usługa Media Services można dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128), lub z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. 

Aby określić opcje szyfrowania na strumień, należy utworzyć [przesyłania strumieniowego zasad](streaming-policy-concept.md) i powiąż ją z Twojej [lokalizatora przesyłania strumieniowego](streaming-locators-concept.md). Możesz utworzyć [zasad klucza zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies) do skonfigurowania jak klucz zawartości (zapewniająca bezpieczny dostęp do Twojego [zasoby](assets-concept.md)) są dostarczane dla klientów końcowych. Należy ustawić wymagania (ograniczenia) zasady klucza zawartości, które muszą zostać spełnione w kolejności kluczy przy użyciu określonej konfiguracji, które mają zostać dostarczone klientom. Ta zawartość zasad klucza nie są potrzebne do zwykłego przesyłania strumieniowego lub pobierania. 

Zazwyczaj należy skojarzyć swoje **zasad klucza zawartości** za pomocą usługi **lokalizatora przesyłania strumieniowego**. Alternatywnie można określić zawartości zasady klucz wewnątrz zasadę przesyłania strumieniowego, (tworząc niestandardowe zasady przesyłania strumieniowego dla zaawansowanych scenariuszy). 

Zaleca się, aby umożliwić usługi Media Services do automatycznego generowania kluczy zawartości. Zazwyczaj będzie używać klucza długotrwałych, a do sprawdzania istnienia zasad za pomocą **uzyskać**. Aby uzyskać klucz, należy wywołać metodę oddzielnej akcji służącej do pobrania wpisów tajnych lub poświadczeń, zobacz przykład znajdujący się.

**Zasady kluczy zawartości** są aktualizowalne. Może upłynąć do 15 minut w przypadku pamięci podręcznych dostarczania klucza do aktualizacji i wybierze zaktualizowane zasady. 

> [!IMPORTANT]
> * Właściwości **zasad dotyczących zawartości klucza** będące daty/godziny są zawsze w formacie UTC.
> * Należy zaprojektować ograniczony zestaw zasad dla swojego konta usługi multimediów i ponownie używać ich na potrzeby Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](limits-quotas-constraints.md).

## <a name="example"></a>Przykład

Aby uzyskać dostęp do klucza, należy użyć **GetPolicyPropertiesWithSecretsAsync**, jak pokazano na [pobieranie klucza podpisywania z istniejących zasad](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) przykład.

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="next-steps"></a>Kolejne kroki

* [Dynamiczne szyfrowanie AES-128 i usługę dostarczania kluczy](protect-with-aes128.md)
* [Użyj DRM dynamiczne szyfrowanie i licencji usługi dostarczania](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
