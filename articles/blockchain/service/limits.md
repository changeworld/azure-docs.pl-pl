---
title: Limity usługi Azure Blockchain
description: Omówienie limitów usług i funkcjonalności w usłudze Azure Blockchain
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455647"
---
# <a name="limits-in-azure-blockchain-service"></a>Limity w usłudze Blockchain platformy Azure

Usługa Azure Blockchain service ma limity usług i funkcjonalności, takie jak liczba węzłów, które może mieć członek, ograniczenia konsorcjum i kwoty magazynu.

## <a name="pricing-tier"></a>Warstwa cenowa

Maksymalne limity transakcji i węzłów walidatora zależą od tego, czy aprowizujesz usługę Azure Blockchain w warstwach cenowych podstawowych czy standardowych.

| Warstwa cenowa | Maksymalna liczba węzłów transakcji | Maksymalne węzły walidatora |
|:---|:---:|:---:|
| Podstawowa (Basic) | 10 | 1 |
| Standardowa | 10 | 2 |

Zmiana warstwy cenowej między podstawową a standardową po utworzeniu elementu członkowskiego nie jest obsługiwana.

## <a name="storage-capacity"></a>Pojemność magazynu

Maksymalna ilość miejsca do magazynowania, który może być używany na węzeł dla danych księgi i dzienników wynosi 1,8 terabajtów.

Zmniejszanie rozmiaru magazynu księgi i dziennika nie jest obsługiwane.

## <a name="consortium-limits"></a>Limity konsorcjum

* **Nazwy konsorcjum i członków muszą być unikatowe** od innych nazw konsorcjum i członków w usłudze Azure Blockchain.

* **Nie można zmienić nazw członków i konsorcjum**

* **Wszyscy członkowie konsorcjum muszą być w tym samym poziomie cenowym**

* **Wszyscy członkowie uczestniczący w konsorcjum muszą mieszkać w tym samym regionie**

    Pierwszy element członkowski utworzony w konsorcjum dyktuje region. Zaproszeni członkowie do konsorcjum muszą znajdować się w tym samym regionie co pierwszy element członkowski. Ograniczenie wszystkich członków do tego samego regionu pomaga zapewnić, że konsensus sieciowy nie ma negatywnego wpływu.

* **Konsorcjum musi mieć co najmniej jednego administratora**

    Jeśli w konsorcjum jest tylko jeden administrator, nie może usunąć się z konsorcjum ani usunąć swojego członka, dopóki inny administrator nie zostanie dodany lub promowany w konsorcjum.

* **Członkowie usunięci z konsorcjum nie mogą zostać ponownie dodani**

    Zamiast tego muszą zostać ponownie zaproszeni do przyłączenia się do konsorcjum i utworzenia nowego członka. Ich istniejący zasób elementu członkowskiego nie są usuwane w celu zachowania transakcji historycznych.

* **Wszyscy członkowie konsorcjum muszą używać tej samej wersji księgi**

    Aby uzyskać więcej informacji na temat poprawek, aktualizacji i wersji księgi dostępnych w usłudze Azure Blockchain, zobacz [Łatanie, aktualizacje i wersje](ledger-versions.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach dotyczących poprawek i uaktualnień systemów — [łatanie, aktualizacje i wersje](ledger-versions.md).
