---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120506"
---
Urządzenie jest skojarzone z kontem magazynu, który jest używany jako miejsce docelowe dla danych na platformie Azure. Dostęp do konta magazynu jest kontrolowany przez subskrypcję i dwa magazynu 512-bitowe klucze skojarzone z tym kontem magazynu dostępu.

Jeden z kluczy jest używany do uwierzytelniania, gdy urządzenie brzegowe pole danych uzyskuje dostęp do konta magazynu. Drugi klucz jest przechowywany w rezerwie, aby okresowo zmieniać klucze.

Ze względów bezpieczeństwa wiele centrów danych wymagają wymiany kluczy. Firma Microsoft zaleca, aby wykonać te najlepsze praktyki dotyczące wymiany kluczy:

- Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Starannie Chroń klucz konta. Nie dystrybucji haseł do innych użytkowników, ciężko zakodować go lub zapisać go w dowolnym miejscu w postaci zwykłego tekstu, który jest dostępny dla innych użytkowników.
- [Wygeneruj ponownie klucz konta](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) za pośrednictwem platformy Azure portal, jeśli sądzisz, że może to mieć negatywny.
- Administrator platformy Azure należy okresowo zmiany lub ponownie wygenerować klucz podstawowy lub pomocniczy przy użyciu sekcji magazyn w witrynie Azure Portal, aby bezpośrednio dostęp do konta magazynu.