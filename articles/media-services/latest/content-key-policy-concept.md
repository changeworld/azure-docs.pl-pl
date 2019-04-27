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
ms.date: 02/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d9e86c45d535862e0c3d02b3f331bc40ebb7f6c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733049"
---
# <a name="content-key-policies"></a>Zasady kluczy zawartości

Usługa Media Services można dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128), lub z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów.

Aby określić opcje szyfrowania na strumień, należy utworzyć [zasad klucza zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies) i powiąż ją z Twojej **lokalizatora przesyłania strumieniowego**. **Zasad klucza zawartości** konfiguruje sposób dostarczania klucza zawartości dla klientów za pośrednictwem składnika dostarczania klucza usługi Media Services końcowych. Usługi Media Services można pozwolić, aby automatycznie generuje klucz zawartości. Zazwyczaj będzie używać klucza długotrwałych, a do sprawdzania istnienia zasad za pomocą Get. Aby uzyskać klucz, należy wywołać metodę oddzielnej akcji służącej do pobrania wpisów tajnych lub poświadczeń, zobacz przykład znajdujący się.

**Zasady kluczy zawartości** są aktualizowalne. Na przykład możesz chcieć aktualizowania zasad, jeśli musisz przeprowadzić rotację kluczy. Możesz zaktualizować podstawowy klucz weryfikacji i listę kluczy weryfikacji alternatywne w istniejących zasad. Może upłynąć do 15 minut w przypadku pamięci podręcznych dostarczania klucza do aktualizacji i wybierze zaktualizowane zasady. 

> [!IMPORTANT]
> * Właściwości **zasad dotyczących zawartości klucza** będące daty/godziny są zawsze w formacie UTC.
> * Należy zaprojektować ograniczony zestaw zasad dla swojego konta usługi multimediów i ponownie używać ich na potrzeby Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje. 

## <a name="example"></a>Przykład

Aby uzyskać dostęp do klucza, należy użyć **GetPolicyPropertiesWithSecretsAsync**, jak pokazano w poniższym przykładzie.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="next-steps"></a>Kolejne kroki

* [Dynamiczne szyfrowanie AES-128 i usługę dostarczania kluczy](protect-with-aes128.md)
* [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
