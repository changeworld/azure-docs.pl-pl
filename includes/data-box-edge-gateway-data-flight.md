---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754332"
---
Aby uzyskać dane transmitowane:

- Standardowy protokół TLS 1.2 jest używany dla danych przesyłanych między urządzeniem a platformą Azure. Brak rezerwowe, nie protokołu TLS 1.1 i starszych wersji. Komunikacja agenta zostanie zablokowane, jeśli nie jest obsługiwany protokół TLS 1.2. Wymagany do portalu i zestawu SDK zarządzania jest również protokołu TLS 1.2.
- Gdy klientom dostęp do urządzenia za pomocą lokalnego interfejsu użytkownika przeglądarki sieci web, standardowy protokół TLS 1.2 jest używana jako domyślna bezpiecznego protokołu.

    - Najlepszym rozwiązaniem jest skonfigurowanie przeglądarki do użycia protokołu TLS 1.2.
    - Jeśli przeglądarka nie obsługuje protokołu TLS 1.2, można użyć protokołu TLS 1.1 i TLS 1.0.
- Zalecamy użycie protokołu SMB 3.0 za pomocą szyfrowania do ochrony danych podczas kopiowania z serwerów danych.
