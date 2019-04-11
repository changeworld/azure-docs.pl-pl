---
title: Pobieranie klucza podpisywania z istniejących zasad za pomocą usługi Media Services v3 .NET SDK — Azure | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak można pobrać klucza podpisywania z istniejących zasad za pomocą usługi Media Services v3 — zestaw SDK platformy .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 49cc2b8c151053377f8f1da0792f10a06695b332
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471175"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Pobieranie klucza podpisywania z istniejących zasad

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. interfejsy API w wersji 3 nie zwracają wpisów tajnych lub poświadczeń na **uzyskać** lub **listy** operacji. Klucze mają zawsze wartość null, są puste lub oczyszczone z odpowiedzi. Użytkownik musi wywołać metodę oddzielnej akcji służącej do pobrania wpisów tajnych lub poświadczenia. **Czytnika** roli nie można wywołać operacji, więc nie można wywoływać operacje, takie jak ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas, StreamingLocator.ListContentKeys,. Masz oddzielne działania umożliwia ustawianie bardziej szczegółowych uprawnień zabezpieczeń RBAC do roli niestandardowej, w razie potrzeby.

Aby uzyskać więcej informacji, zobacz [konta RBAC i Media Services](rbac-overview.md)

W przykładzie w tym artykule pokazano, jak uzyskać klucz podpisywania z istniejących zasad za pomocą platformy .NET. 
 
## <a name="download"></a>Do pobrania 

Sklonuj repozytorium GitHub, który zawiera pełny przykład .NET na maszynie za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
ContentKeyPolicy wpisów tajnych przykładu znajduje się w [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) folderu.

## <a name="get-contentkeypolicy-with-secrets"></a>Pobierz ContentKeyPolicy przy użyciu kluczy tajnych 

Aby uzyskać dostęp do klucza, należy użyć **GetPolicyPropertiesWithSecretsAsync**, jak pokazano w poniższym przykładzie.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Kolejne kroki

[Projekt systemu multi-DRM ochrony zawartości przy użyciu kontroli dostępu](design-multi-drm-system-with-access-control.md) 
