---
title: Kontrola dostępu oparta na rolach dla konta usługi Media Services — Azure | Dokumentacja firmy Microsoft
description: W tym artykule omówiono kontrola dostępu oparta na rolach (RBAC) dla konta usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f72e98d8874a5a5dc94deb882affdf66388b13c9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548529"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Kontrola dostępu oparta na rolach (RBAC) dla konta usługi Media Services

Obecnie usługa Azure Media Services Niezdefiniowany żadnych szczególnych ról niestandardowych do usługi. Klienci mogą użyć wbudowanych ról **właściciela** lub **Współautor** do uzyskania pełnego dostępu do konta usługi Media Services. Jest główną różnicą między tymi rolami: **właściciela** można kontrolować, kto ma dostęp do zasobów i **Współautor** nie. Konto czytnika wbudowanych tylko ma dostęp do odczytu do konta usługi Media Services. 

## <a name="design-principles"></a>Zasady projektowania

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. interfejsy API w wersji 3 nie zwracają wpisów tajnych lub poświadczeń na **uzyskać** lub **listy** operacji. Klucze mają zawsze wartość null, są puste lub oczyszczone z odpowiedzi. Użytkownik musi wywołać metodę oddzielnej akcji służącej do pobrania wpisów tajnych lub poświadczenia. **Czytnika** roli nie można wywołać operacji, więc nie można wywoływać operacje, takie jak ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas, StreamingLocator.ListContentKeys,. Masz oddzielne działania umożliwia ustawianie bardziej szczegółowych uprawnień zabezpieczeń RBAC do roli niestandardowej, w razie potrzeby.

Aby wyświetlić listę Media Services obsługuje operacje, należy wykonać:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[Definicje ról wbudowanych](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) artykule wyjaśniono, dokładnie co rola przyznaje. 

Zobacz następujące artykuły, aby uzyskać więcej informacji:

- [Role klasycznego administratora subskrypcji, role kontroli na podstawie ról (RBAC) platformy Azure i role administratora usługi Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Co to jest RBAC dla zasobów platformy Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Zarządzanie dostępem przy użyciu kontroli RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie aplikacji za pomocą usługi Media Services v3 interfejsów API](media-services-apis-overview.md)
- [Pobieranie zawartości zasad kluczy przy użyciu platformy .NET usług Media Services](get-content-key-policy-dotnet-howto.md)
