---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468734"
---
Urządzenie jest skojarzone z kontem magazynu, które jest używane jako miejsce docelowe dla danych na platformie Azure. Dostęp do konta magazynu jest kontrolowany przez subskrypcję i dwa 512-bitowe klucze dostępu do magazynu skojarzone z tym kontem magazynu.

Jeden z kluczy jest używany do uwierzytelniania, gdy urządzenie Data Box Edge uzyskuje dostęp do konta magazynu. Drugi klucz jest przechowywany w rezerwie, dzięki czemu można okresowo obracać klawisze.

Ze względów bezpieczeństwa wiele centrów danych wymaga rotacji kluczy. Zaleca się przestrzeganie tych najlepszych rozwiązań dotyczących rotacji kluczy:

- Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Starannie chroń swój klucz konta. Nie rozpowszechniaj hasła do innych użytkowników, nie koduj go na miejscu ani nie zapisuj w dowolnym miejscu w postaci zwykłego tekstu, który jest dostępny dla innych użytkowników.
- Ponownie wygeneruj klucz konta za pośrednictwem witryny Azure portal, jeśli uważasz, że może zostać naruszony. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../articles/storage/common/storage-account-keys-manage.md).
- Administrator platformy Azure należy okresowo zmieniać lub ponownie wygenerować klucz podstawowy lub pomocniczy przy użyciu sekcji magazynu w witrynie Azure portal, aby uzyskać bezpośredni dostęp do konta magazynu.