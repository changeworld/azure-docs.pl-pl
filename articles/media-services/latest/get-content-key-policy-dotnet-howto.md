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
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322505"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Pobieranie klucza podpisywania z istniejących zasad

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. Interfejsy API w wersji 3 nie zwracają wpisów tajnych ani poświadczeń w operacji **Get** lub **List**. Klucze mają zawsze wartość null, są puste lub oczyszczone z odpowiedzi. Należy wywołać oddzielną metodę akcji w celu pobrania wpisów tajnych lub poświadczeń. Oddzielne akcje umożliwiają ustawienie różnych uprawnień zabezpieczeń RBAC w przypadku, gdy niektóre interfejsy API pobierają/wyświetlają wpisy tajne, podczas gdy inne interfejsy API tego nie robią. Aby uzyskać informacje na temat zarządzania dostępem przy użyciu funkcji RBAC, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Przykłady obejmują: 

* niezwracanie wartości ContentKey w operacji Get elementu StreamingLocator, 
* niezwracanie kluczy ograniczeń w operacji get elementu ContentKeyPolicy, 
* niezwracanie części ciągu zapytania adresu URL (aby usunąć podpis) dla wejściowych adresów URL HTTP zadań.

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
