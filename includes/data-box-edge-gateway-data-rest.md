---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684468"
---
Dla danych magazynowanych:

- Dla danych magazynowanych szyfrowanie funkcją BitLocker XTS-AES-256 jest używany do ochrony danych lokalnych.
- W przypadku danych, która znajduje się w udziałach dostępu do udziałów jest ograniczona.

    - Dla klientów protokołu SMB uzyskujących dostęp do danych udziału muszą one poświadczenia użytkownika skojarzonego z udziałem. Te poświadczenia są definiowane w czasie tworzenia udziału.
    - Dla klientów systemu plików NFS, uzyskujących dostęp do udziałów adresy IP klientów, należy dodać w czasie tworzenia udziału.