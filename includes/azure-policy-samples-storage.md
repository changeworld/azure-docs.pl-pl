---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b967558828c42331b3702fe90ce842fd1c0032bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003827"
---
## <a name="storage"></a>Magazyn

|  |  |
|---------|---------|
| [Allowed SKUs for Storage Accounts and Virtual Machines](../articles/governance/policy/samples/allowed-skus-storage.md) (Dozwolone jednostki SKU dla kont magazynu i maszyn wirtualnych) | Wymaga, aby konta magazynu i maszyny wirtualne używały zatwierdzonych jednostek SKU. Używają wbudowanych zasad w celu zapewnienia zatwierdzonych jednostek SKU. Do Ciebie należy określenie tablicy z zatwierdzonymi jednostkami SKU maszyn wirtualnych i tablicy z zatwierdzonymi jednostkami SKU kont magazynu. |
| [Allowed storage account SKUs](../articles/governance/policy/samples/allowed-stor-acct-skus.md) (Dozwolone jednostki SKU konta magazynu) | Wymaga, aby konta magazynu używały zatwierdzonej jednostki SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [Deny cool access tiering for storage accounts](../articles/governance/policy/samples/deny-cool-access-tiering.md) (Uniemożliwianie użycia warstwy dostępu Chłodna dla kont magazynu) | Uniemożliwia użycie warstwy dostępu Chłodna dla kont usługi Blob Storage.  |
| [Ensure https traffic only for storage account](../articles/governance/policy/samples/ensure-https-stor-acct.md) (Zapewnienie używania tylko ruchu https dla konta magazynu) | Wymaga używania ruchu HTTPS przez konta magazynu.  |
| [Ensure storage file encryption](../articles/governance/policy/samples/ensure-store-file-enc.md) (Zapewnianie szyfrowania plików w magazynie) | Wymaga włączenia szyfrowania plików na kontach magazynu.  |
| [Require storage account encryption](../articles/governance/policy/samples/req-store-acct-enc.md) (Wymaganie szyfrowania konta magazynu) | Wymaga szyfrowania obiektów blob na koncie magazynu.  |