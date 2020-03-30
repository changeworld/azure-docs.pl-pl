---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183868"
---
Dane w locie:

- Standard TLS 1.2 jest używany do danych, które przemieszczają się między urządzeniem a platformą Azure. Nie ma rezerwowego do TLS 1.1 i wcześniej. Komunikacja agenta zostanie zablokowana, jeśli TLS 1.2 nie jest obsługiwany. TLS 1.2 jest również wymagany do zarządzania portalem i SDK.
- Gdy klienci uzyskują dostęp do urządzenia za pośrednictwem lokalnego internetowego interfejsu użytkownika przeglądarki, standardowy protokół TLS 1.2 jest używany jako domyślny bezpieczny protokół.

    - Najlepszym rozwiązaniem jest skonfigurowanie przeglądarki do używania protokołu TLS 1.2.
    - Jeśli przeglądarka nie obsługuje protokołu TLS 1.2, można użyć protokołu TLS 1.1 lub TLS 1.0.
- Zaleca się używanie SMB 3.0 z szyfrowaniem do ochrony danych podczas kopiowania ich z serwerów danych.
