---
title: Kontrola dostępu oparta na rolach dla kont usługi Media Services — platforma Azure | Dokumenty firmy Microsoft
description: W tym artykule omówiono kontroli dostępu opartej na rolach (RBAC) dla kont usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236914"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Kontrola dostępu oparta na rolach (RBAC) dla kont usługi Media Services

Obecnie usługa Azure Media Services nie definiuje żadnych ról niestandardowych specyficznych dla usługi. Aby uzyskać pełny dostęp do konta usługi Media Services, klienci mogą korzystać z wbudowanych ról **właściciela** lub **współautora**. Główna różnica między tymi rolami jest: **Właściciel** może kontrolować, kto ma dostęp do zasobu i **współautor** nie może. Wbudowana rola **czytnika** może być również używana, ale użytkownik lub aplikacja będzie miała dostęp do odczytu tylko do interfejsów API usług Media Services. 

## <a name="design-principles"></a>Zasady projektowania

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. Interfejsy API w wersji 3 nie zwracają wpisów tajnych ani poświadczeń w operacjach **Pobierz** lub **Lista.** Klucze mają zawsze wartość null, są puste lub oczyszczone z odpowiedzi. Użytkownik musi wywołać metodę akcji oddzielne, aby uzyskać wpisy tajne lub poświadczenia. Rola **czytnika** nie może wywoływać operacji, takich jak Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Posiadanie oddzielnych akcji umożliwia ustawienie bardziej szczegółowych uprawnień zabezpieczeń RBAC w roli niestandardowej w razie potrzeby.

Aby wyświetlić listę operacji, które obsługuje program Media Services, wykonaj następujące czynności:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Wbudowany artykuł [definicje ról](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) mówi dokładnie, co przyznaje roli. 

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Role klasycznego administratora subskrypcji, role kontroli na podstawie ról (RBAC) platformy Azure i role administratora usługi Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Co to jest RBAC dla zasobów platformy Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Zarządzanie dostępem za pomocą funkcji RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operacje dostawcy zasobów usługi Media Services](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie za pomocą interfejsów API usługi Media Services w wersji 3](media-services-apis-overview.md)
- [Pobierz zasady klucza zawartości przy użyciu usługi Media Services .NET](get-content-key-policy-dotnet-howto.md)
