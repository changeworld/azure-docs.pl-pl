---
title: Definiowanie zasad dostępu przechowywanego za pomocą usługi .NET — Usługa Azure Storage
description: Dowiedz się, jak zdefiniować zasady dostępu przechowywanego przy użyciu biblioteki klienta platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990743"
---
# <a name="define-a-stored-access-policy-with-net"></a>Definiowanie zasad dostępu przechowywanego za pomocą platformy .NET

Zasady dostępu przechowywanego zapewniają dodatkowy poziom kontroli nad sygnaturami dostępu współdzielonego na poziomie usługi (SAS) po stronie serwera. Definiowanie zasad dostępu przechowywanego służy do grupowania podpisów dostępu współdzielonego i zapewnienia dodatkowych ograniczeń dla podpisów dostępu współdzielonego, które są powiązane przez zasady. Za pomocą zasad dostępu przechowywanego można zmienić godzinę rozpoczęcia, czas wygaśnięcia lub uprawnienia dostępu Współdzielonego lub odwołać go po jego wystawieniu.
  
 Następujące zasoby magazynu obsługują przechowywane zasady dostępu:  
  
- Kontenery obiektów blob  
- Udziały plików  
- Kolejki  
- Tabele  
  
> [!NOTE]
> Zasady dostępu przechowywanego w kontenerze mogą być skojarzone z podpisem dostępu współdzielonego przyznającego uprawnienia do samego kontenera lub do obiektów blob, które zawiera. Podobnie zasady dostępu przechowywanego w udziale plików mogą być skojarzone z podpisem dostępu współdzielonego przyznającego uprawnienia do samego udziału lub do plików, które zawiera.  
>
> Przechowywane zasady dostępu są obsługiwane tylko dla usługi SAS. Przechowywane zasady dostępu nie są obsługiwane dla sygnatury dostępu Współdzielonego konta lub sygnatury dostępu współdzielonego delegowania użytkowników.  

## <a name="create-a-stored-access-policy"></a>Tworzenie zasad dostępu przechowywanego

Poniższy kod tworzy zasady dostępu przechowywane w kontenerze. Za pomocą zasad dostępu można określić ograniczenia dla usługi Sygnatury dostępu Współdzielonego w kontenerze lub jego obiektach blob.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Zobacz też

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
- [Definiowanie przechowywanych zasad dostępu](/rest/api/storageservices/define-stored-access-policy)

