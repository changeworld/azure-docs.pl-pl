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
ms.openlocfilehash: 3009948c72b0cad6ba603a3ccbe35db8d93fe2c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664627"
---
## <a name="storage"></a>Magazyn

|  |  |
|---------|---------|
| [Allowed SKUs for Storage Accounts and Virtual Machines](../articles/azure-policy/scripts/allowed-skus-storage.md) (Dozwolone jednostki SKU dla kont magazynu i maszyn wirtualnych) | Wymaga, aby konta magazynu i maszyny wirtualne używały zatwierdzonych jednostek SKU. Używają wbudowanych zasad w celu zapewnienia zatwierdzonych jednostek SKU. Do Ciebie należy określenie tablicy z zatwierdzonymi jednostkami SKU maszyn wirtualnych i tablicy z zatwierdzonymi jednostkami SKU kont magazynu. |
| [Allowed storage account SKUs](../articles/azure-policy/scripts/allowed-stor-acct-skus.md) (Dozwolone jednostki SKU konta magazynu) | Wymaga, aby konta magazynu używały zatwierdzonej jednostki SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [Deny cool access tiering for storage accounts](../articles/azure-policy/scripts/deny-cool-access-tiering.md) (Uniemożliwianie użycia warstwy dostępu Chłodna dla kont magazynu) | Uniemożliwia użycie warstwy dostępu Chłodna dla kont usługi Blob Storage.  |
| [Ensure https traffic only for storage account](../articles/azure-policy/scripts/ensure-https-stor-acct.md) (Zapewnienie używania tylko ruchu https dla konta magazynu) | Wymaga używania ruchu HTTPS przez konta magazynu.  |
| [Ensure storage file encryption](../articles/azure-policy/scripts/ensure-store-file-enc.md) (Zapewnianie szyfrowania plików w magazynie) | Wymaga włączenia szyfrowania plików na kontach magazynu.  |
| [Require storage account encryption](../articles/azure-policy/scripts/req-store-acct-enc.md) (Wymaganie szyfrowania konta magazynu) | Wymaga szyfrowania obiektów blob na koncie magazynu.  |