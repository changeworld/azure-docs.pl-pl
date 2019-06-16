---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120552"
---
Dla danych magazynowanych:

- Szyfrowanie funkcją BitLocker XTS-AES 256-bitowy jest używany do ochrony danych lokalnych.
- Dostęp do danych przechowywanych w udziałach jest ograniczony.

    - Klientów protokołu SMB uzyskujących dostęp do danych udziału potrzebne poświadczenia użytkownika skojarzonego z udziałem. Te poświadczenia są zdefiniowane, po utworzeniu udziału.
    - Adresy IP klientów systemu plików NFS, uzyskujących dostęp do udziału należy dodać po utworzeniu udziału.