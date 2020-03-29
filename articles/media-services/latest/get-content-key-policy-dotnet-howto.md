---
title: Pobierz klucz podpisywania z zasad przy użyciu usługi Azure Media Services w wersji 3 .NET
description: W tym temacie pokazano, jak uzyskać klucz podpisywania z istniejących zasad przy użyciu usługi Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065962"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Pobieranie klucza podpisywania z istniejących zasad

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. Interfejsy API w wersji 3 nie zwracają wpisów tajnych ani poświadczeń w operacjach **Pobierz** lub **Lista.** Zobacz szczegółowe wyjaśnienie tutaj: Aby uzyskać więcej informacji, zobacz [RBAC i konta Media Services](rbac-overview.md)

W tym przykładzie przedstawiono sposób użycia platformy .NET w celu uzyskania klucza podpisywania z istniejących zasad. 
 
## <a name="download"></a>Pobierz 

Sklonuj repozytorium GitHub zawierające pełne próbki platformy .NET na komputerze za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
ContentKeyPolicy z wpisami tajnymi przykład znajduje się w folderze [EncryptWithDRM.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)

## <a name="get-contentkeypolicy-with-secrets"></a>Pobierz ContentKeyPolicy z wpisami tajnymi 

Aby uzyskać informacje o kluczu, należy użyć **pliku GetPolicyPropertiesWithSecretsAsync**, jak pokazano w poniższym przykładzie.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Następne kroki

[Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md) 
