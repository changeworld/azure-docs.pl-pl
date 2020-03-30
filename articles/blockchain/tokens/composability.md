---
title: Możliwość komponowania tokenów łańcucha bloków azure
description: Możliwość tworzenia tokenów łańcucha bloków platformy Azure zapewnia elastyczność tworzenia tokenów dla zaawansowanych scenariuszy.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325120"
---
# <a name="azure-blockchain-tokens-composability"></a>Możliwość komponowania tokenów łańcucha bloków azure

[!INCLUDE [Preview note](./includes/preview.md)]

Możliwość komponowania tokenów zapewnia elastyczność tworzenia tokenów dla zaawansowanych scenariuszy. Może istnieć złożony scenariusz, którego nie można zaimplementować przy użyciu [czterech wstępnie utworzonych szablonów tokenów.](templates.md#base-token-types) Możliwości komponowania tokenu umożliwia projektowanie własnych szablonów tokenu przez dodanie lub usunięcie zdefiniowanych zachowań w celu utworzenia własnego szablonu tokenu. Podczas tworzenia nowego szablonu tokenu tokeny łańcucha bloków platformy Azure weryfikuje wszystkie reguły gramatyki tokenu. Skomponowane szablony są zapisywane w usłudze Azure Blockchain Tokens do wystawiania w połączonych sieciach łańcucha bloków.

Zachowania [tokenu](templates.md#token-behaviors) można użyć w poniższych sekcjach, aby zaprojektować szablon tokenu.

## <a name="burnable-b"></a>Palne (b)

Możliwość usunięcia tokenów z dostaw.

Na przykład, gdy wymieniasz punkty karty kredytowej online na kartę upominkową, punkty karty kredytowej są spalane.

## <a name="delegable-g"></a>Delegable (g)

Możliwość delegowania akcji podjętych na token, który jesteś właścicielem.

Pełnomocnik może wykonywać akcje jako właściciel tokenu. Na przykład można użyć tokenu delegable do zaimplementowania głosowania. Delegable token pozwala właściciel tokenu głosowania, aby ktoś inny głosować w ich imieniu.

## <a name="logable-l"></a>Logowanie (l)

Możliwość logowania.

Na przykład można wydać token rejestrowania dla dystrybucji filmów do każdego teatru pokazującego określony film. Aby film miał być odtwarzany, program musi rejestrować transakcję dla każdego pokazu, ponieważ wypłaty tantiem są wyświetlane podczas uruchamiania filmu. Aktorzy budować można użyć tokenów filmu do sprawdzania poprawności wypłat na film pokazano na teatr w dystrybucji.

## <a name="mint-able-m"></a>Mięta (m)

Możliwość ceł dodatkowych żetonów dla klasy tokenu. Rola minter zawiera zachowanie mintable.

Na przykład firma detaliczna, która chce zaimplementować program lojalnościowy, może używać tokenów ekgnacji dla swojego programu lojalnościowego. Mogą one mint dodatkowe punkty lojalnościowe dla swoich klientów, jak ich baza klientów rośnie.  

## <a name="non-subdividable-or-whole-d"></a>Nierozdzielne lub całe (~d)

Ograniczenie, aby zapobiec dzieleniu tokenu na mniejsze części.

Na przykład pojedynczego obrazu artystycznego nie można podzielić na wiele mniejszych części. 

## <a name="non-transferable-t"></a>Niezbywalne (~t)

Ograniczenie, aby zapobiec zmianie własności od początkowego właściciela tokenu.

Na przykład dyplom uniwersytecki jest tokenem niezbywalnym. Po wręczeniu dyplomu absolwentowi nie można go przenieść z absolwenta na inną osobę.

## <a name="roles-r"></a>Role (r)

Możliwość definiowania ról w klasie szablonu tokenu dla określonych zachowań.

Można podać listę nazw ról, które token obsługuje w czasie tworzenia tokenu. Po określeniu ról użytkownik może przypisać role do tych zachowań. Obecnie obsługiwane jest tylko rola minter.

## <a name="singleton-s"></a>Singleton (s)

Ograniczenie, aby zezwolić na dostawę jednego tokenu.

Na przykład artefakt muzeum jest token singleton. Artefakty muzealne są wyjątkowe. Token reprezentujący artefakt ma tylko jeden element w zasilaczu.

## <a name="subdividable-d"></a>Podział (d)

Możliwość podzielenia żetonu na mniejsze części.

Na przykład dolara można podzielić na centy.

## <a name="transferable-t"></a>Zbywalne (t)

Możliwość przeniesienia własności tokenu.

Na przykład tytuł właściwości jest tokenem zbywalnym, który można przenieść z jednej osoby do drugiej, gdy nieruchomość jest sprzedawana.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zarządzaniu kontem tokenów łańcucha bloków platformy Azure](account-management.md).
