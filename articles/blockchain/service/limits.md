---
title: Limity usługi Azure Blockchain
description: Omówienie limitów usług i funkcjonalności w usłudze Azure Blockchain
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676514"
---
# <a name="limits-in-azure-blockchain-service"></a>Limity w usłudze Blockchain platformy Azure

Usługa Azure Blockchain service ma limity usług i funkcjonalności, takie jak liczba węzłów, które może mieć członek, ograniczenia konsorcjum i kwoty magazynu.

## <a name="pricing-tier"></a>Warstwa cenowa

Maksymalne limity transakcji i węzłów walidatora zależą od tego, czy aprowizujesz usługę Azure Blockchain w podstawowych czy standardowych warstwach cenowych.

| Warstwa cenowa | Maksymalna liczba węzłów transakcji | Maksymalne węzły walidatora |
|:---|:---:|:---:|
| Podstawowy | 10 | 1 |
| Standardowa | 10 | 2 |

Sieć konsorcjum powinna mieć co najmniej dwa węzły warstwy standardowej usługi Azure Blockchain Service. Węzły warstwy standardowej obejmują dwa węzły walidatora. Cztery węzły walidatora są wymagane, aby spełnić [konsensus Stambulskiej Tolerancji Na uszkodzenia.](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)

Użyj podstawowej warstwy jest do tworzenia, testowania i weryfikacji koncepcji. Użyj warstwy standardowej dla wdrożeń klasy produkcyjnej. Należy również użyć *warstwy Standardowa,* jeśli używasz Menedżera danych Blockchain lub wysyłasz dużą liczbę transakcji prywatnych.

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

    Zamiast tego muszą zostać ponownie zaproszeni do przyłączenia się do konsorcjum i utworzenia nowego członka. Ich istniejące zasoby członkowskie nie są usuwane w celu zachowania transakcji historycznych.

* **Wszyscy członkowie konsorcjum muszą używać tej samej wersji księgi**

    Aby uzyskać więcej informacji na temat poprawek, aktualizacji i wersji księgi dostępnych w usłudze Azure Blockchain, zobacz [Łatanie, aktualizacje i wersje](ledger-versions.md).

## <a name="performance"></a>Wydajność

Nie należy używać *funkcji eth.estimate* gazu dla każdego zgłoszenia transakcji. *Eth.estimate* Funkcja jest intensywnie pamięci. Wywołanie funkcji wiele razy zmniejsza transakcje na sekundę drastycznie.

Jeśli to możliwe, użyj konserwatywnej wartości gazu do składania transakcji i zminimalizuj użycie *eth.estimate*.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach dotyczących poprawek i uaktualnień systemów — [łatanie, aktualizacje i wersje](ledger-versions.md).
