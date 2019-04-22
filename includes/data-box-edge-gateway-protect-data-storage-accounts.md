---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684451"
---
Urządzenie jest skojarzone z kontem magazynu, który jest używany jako miejsce docelowe dla danych na platformie Azure. Dostęp do konta magazynu jest kontrolowany przez subskrypcję i dwa magazynu 512-bitowe klucze skojarzone z tym kontem magazynu dostępu.

Jeden z kluczy jest używany do uwierzytelniania, gdy urządzenie brzegowe pole danych uzyskuje dostęp do konta magazynu. Drugi klucz jest przechowywany w rezerwie, dzięki czemu możesz okresowo wymiany kluczy.

Ze względów bezpieczeństwa wiele centrów danych wymagają wymiany kluczy. Firma Microsoft zaleca, aby wykonać te najlepsze praktyki dotyczące wymiany kluczy:

- Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Starannie Chroń klucz konta. Nie dystrybucji haseł do innych użytkowników, ciężko zakodować go lub zapisać go w dowolnym miejscu w postaci zwykłego tekstu, który jest dostępny dla innych użytkowników.
- [Wygeneruj ponownie klucz konta](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) przy użyciu witryny Azure portal, jeśli Twoim zdaniem mogą zabezpieczenia mogły zostać naruszone.
- Okresowo administratorem platformy Azure, należy zmienić lub ponownie wygenerować klucz podstawowy lub pomocniczy przy użyciu sekcji magazyn w witrynie Azure Portal na bezpośredni dostęp do konta magazynu.