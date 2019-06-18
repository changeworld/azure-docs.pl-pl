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
ms.openlocfilehash: fe6c6d461fd6b99ce2ce57ebdd61a3f3e961489c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183394"
---
## <a name="storage"></a>Magazyn

|  |  |
|---------|---------|
| [Allowed SKUs for Storage Accounts and Virtual Machines](../articles/governance/policy/samples/allowed-skus-storage.md) (Dozwolone jednostki SKU dla kont magazynu i maszyn wirtualnych) | Wymaga, aby konta magazynu i maszyny wirtualne używały zatwierdzonych jednostek SKU. Używają wbudowanych zasad w celu zapewnienia zatwierdzonych jednostek SKU. Do Ciebie należy określenie tablicy z zatwierdzonymi jednostkami SKU maszyn wirtualnych i tablicy z zatwierdzonymi jednostkami SKU kont magazynu. |
| [Allowed storage account SKUs](../articles/governance/policy/samples/allowed-storage-account-skus.md) (Dozwolone jednostki SKU konta magazynu) | Wymaga, aby konta magazynu używały zatwierdzonej jednostki SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [Deny cool access tiering for storage accounts](../articles/governance/policy/samples/deny-cool-access-tiering.md) (Uniemożliwianie użycia warstwy dostępu Chłodna dla kont magazynu) | Uniemożliwia użycie warstwy dostępu Chłodna dla kont usługi Blob Storage.  |
| [Ensure https traffic only for storage account](../articles/governance/policy/samples/ensure-https-storage-account.md) (Zapewnienie używania tylko ruchu https dla konta magazynu) | Wymaga używania ruchu HTTPS przez konta magazynu.  |
| [Ensure storage file encryption](../articles/governance/policy/samples/ensure-storage-file-encryption.md) (Zapewnianie szyfrowania plików w magazynie) | Wymaga włączenia szyfrowania plików na kontach magazynu.  |
| [Require storage account encryption](../articles/governance/policy/samples/require-storage-account-encryption.md) (Wymaganie szyfrowania konta magazynu) | Wymaga szyfrowania obiektów blob na koncie magazynu.  |