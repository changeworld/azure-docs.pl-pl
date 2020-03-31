---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460566"
---
## <a name="protect-your-access-keys"></a>Ochrona kluczy dostępu

Klucze dostępu do konta magazynu są podobne do hasła głównego konta magazynu. Zawsze należy zachować ostrożność, aby chronić klucze dostępu. Użyj usługi Azure Key Vault, aby bezpiecznie zarządzać kluczami i obracać je. Unikaj dystrybucji kluczy dostępu do innych użytkowników, ich kodowania na miejscu lub zapisywania ich w dowolnym miejscu w postaci zwykłego tekstu, który jest dostępny dla innych osób. Obróć klawisze, jeśli uważasz, że mogły zostać naruszone.

Jeśli to możliwe, użyj usługi Azure Active Directory (Azure AD), aby autoryzować żądania do magazynu obiektów Blob i kolejek zamiast klucza udostępnionego. Usługa Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia za pomocą klucza udostępnionego. Aby uzyskać więcej informacji na temat autoryzowania dostępu do danych za pomocą usługi Azure AD, zobacz [Autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
