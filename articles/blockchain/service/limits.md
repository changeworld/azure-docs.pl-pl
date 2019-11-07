---
title: Limity usługi Azure łańcucha bloków
description: Omówienie limitów usługi i funkcjonalności w usłudze Azure łańcucha bloków Service
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 20c26db9453220270d17801b74d904384c74cb36
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577215"
---
# <a name="limits-in-azure-blockchain-service"></a>Limity w usłudze Azure łańcucha bloków Service

Usługa Azure łańcucha bloków ma limity usługi i funkcjonalne, takie jak liczba węzłów, które może mieć członek, ograniczenia konsorcjum i kwoty magazynu.

## <a name="pricing-tier"></a>Warstwa cenowa

Maksymalne limity dotyczące transakcji i węzłów modułu sprawdzania poprawności zależą od tego, czy usługa Azure łańcucha bloków jest udostępniana w warstwach cenowych w warstwie Podstawowa czy standardowa.

| Warstwa cenowa | Maksymalna liczba węzłów transakcji | Maksymalna liczba węzłów modułu sprawdzania poprawności |
|:---|:---:|:---:|
| Podstawowa | 10 | 1 |
| Standardowa | 10 | 2 |

Zmiana warstwy cenowej między podstawowa i Standardowa po utworzeniu elementu członkowskiego nie jest obsługiwana.

## <a name="storage-capacity"></a>Pojemność magazynu

Maksymalna ilość miejsca do magazynowania, która może być używana na węzeł dla danych i dzienników księgi, to 1,8 terabajtów.

Zmniejszenie rozmiaru księgi i magazynu dzienników nie jest obsługiwane.

## <a name="consortium-limits"></a>Limity konsorcjum

* **Nazwy konsorcjum i składowe muszą być unikatowe** z innych konsorcjów i nazw członków w usłudze Azure łańcucha bloków.

* **Nie można zmienić nazw członków i konsorcjum**

* **Wszyscy członkowie konsorcjum muszą znajdować się w tej samej warstwie cenowej**

* **Wszyscy członkowie, którzy uczestniczą w konsorcjum, muszą znajdować się w tym samym regionie**

    Pierwszy członek utworzony w ramach konsorcjum wymusza region. Zaproszeni członkowie do konsorcjum muszą znajdować się w tym samym regionie co pierwszy członek. Ograniczenie wszystkich elementów członkowskich do tego samego regionu pomaga zapewnić, że konsensus nie wpłynie negatywnie na sieć.

* **Konsorcjum musi mieć co najmniej jednego administratora**

    Jeśli konsorcjum ma tylko jednego administratora, nie może usunąć siebie z konsorcjum ani usunąć ich członka do momentu dodania lub promocji innego administratora w ramach konsorcjum.

* **Nie można ponownie dodać członków usuniętych z konsorcjum**

    Należy pamiętać, aby dołączyć do konsorcjum i utworzyć nowego członka. Istniejący zasób członkowski nie został usunięty, aby zachować historyczne transakcje.

* **Wszyscy członkowie w konsorcjum muszą używać tej samej wersji księgi**

    Aby uzyskać więcej informacji na temat poprawek, aktualizacji i wersji księgi dostępnych w usłudze Azure łańcucha bloków, zobacz [poprawki, aktualizacje i wersje](ledger-versions.md).

## <a name="next-steps"></a>Następne kroki

* [Poprawki, aktualizacje i wersje](ledger-versions.md)
