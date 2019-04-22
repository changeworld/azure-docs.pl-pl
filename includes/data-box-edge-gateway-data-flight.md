---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684437"
---
Aby uzyskać dane w lotu:

- Dane, które przechodzi między urządzeniem a platformą Azure używany jest standardowy protokół TLS 1.2. Brak rezerwowe, nie protokołu TLS 1.1 i starszych wersji. Komunikacja agenta zostanie zablokowane, jeśli nie jest obsługiwany protokół TLS 1.2. Wymagany dla portalu i operacje zestawu SDK jest również protokołu TLS 1.2.
- Gdy klienci uzyskują dostęp do urządzenia za pomocą lokalnego internetowego interfejsu użytkownika w przeglądarce, standardowy protokół TLS 1.2 jest używana jako domyślna bezpiecznego protokołu.

    - Najlepszym rozwiązaniem jest skonfigurowanie przeglądarki do użycia protokołu TLS 1.2.
    - Jeśli przeglądarka nie obsługuje protokołu TLS 1.2, można użyć protokołu TLS 1.1 i TLS 1.0.
- Aby chronić dane podczas kopiowania z serwerów danych, zaleca się korzystania z protokołu SMB 3.0 za pomocą szyfrowania.
