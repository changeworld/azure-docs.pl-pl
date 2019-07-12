---
title: Wyrażenia warunkowe aparatu reguł usługi Azure CDN from Verizon — Premium | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca usługi Azure CDN from Verizon — Premium zasady warunki dopasowań aparatu i funkcje.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: f790e37ae876c0640d55ebfb51abb43c6a705f04
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593219"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Usługa Azure CDN w warstwie Premium firmy Verizon wyrażenia warunkowe aparatu reguł

W tym artykule przedstawiono szczegółowe opisy wyrażeń warunkowych dla usługi Azure Content Delivery Network (CDN) [aparat reguł](cdn-verizon-premium-rules-engine.md).

Pierwsza część reguły jest wyrażenia warunkowego.

Wyrażenie warunkowe | Opis
-----------------------|-------------
IF | Jeśli wyrażenie jest zawsze część pierwsza instrukcja w regule. Podobnie jak wszystkie inne wyrażenia warunkowego tej instrukcji IF musi być skojarzony z dopasowania. Jeśli nie dodatkowe wyrażenia warunkowe są zdefiniowane, to dopasowania Określa kryterium, które muszą zostać spełnione przed zestawem funkcji mogą być stosowane do żądania.
JEŚLI | Wyrażenie IF i mogą być dodawane tylko po następujące typy wyrażenia warunkowe: IF, gdy. Oznacza, że istnieje inny warunek, który muszą być spełnione dla początkowego instrukcji IF.
ELSE IF| Wyrażenie ELSE IF określa warunek alternatywny, które muszą zostać spełnione przed rozpoczęciem zbiór funkcji przeznaczonych dla tej instrukcji ELSE IF. Obecność instrukcji ELSE IF wskazuje koniec poprzednią instrukcję. Tylko warunkowe wyrażenie, które mogą być umieszczone po instrukcji ELSE IF jest inna instrukcja ELSE IF. Oznacza to, że instrukcji ELSE IF tylko służy do określenia pojedynczego dodatkowe warunku, który ma zostać spełnione.

**Przykład**: ![Warunek dopasowania sieci CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Kolejne reguły mogą zastąpić akcji określonych przez poprzednią regułę.
   > Przykład: Regułę przechwytującą cały zabezpiecza wszystkie żądania za pośrednictwem uwierzytelniania opartego na tokenach. Można utworzyć inną regułę bezpośrednio poniżej, aby utworzyć wyjątek dla niektórych typów żądań.

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Warunki dopasowań aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)