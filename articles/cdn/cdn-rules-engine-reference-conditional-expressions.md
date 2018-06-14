---
title: Zasady usługi Azure CDN aparat wyrażeń warunkowych | Dokumentacja firmy Microsoft
description: Dokumentacja referencyjna dla usługi Azure CDN zasady warunków dopasowania aparatu i funkcje.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842930"
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Zasady usługi Azure CDN aparat wyrażeń warunkowych
Ten temat zawiera szczegółowe opisy wyrażeń warunkowych dla usługi Azure sieci dostarczania zawartości (CDN) [aparatu reguł](cdn-rules-engine.md).

Pierwsza część reguły jest wyrażenia warunkowego.

Wyrażenie warunkowe | Opis
-----------------------|-------------
JEŚLI | Jeśli wyrażenie jest zawsze część pierwszą instrukcją w regule. Podobnie jak wszystkie inne wyrażenia warunkowego tej instrukcji IF musi być skojarzony z dopasowania. Jeśli są zdefiniowane nie dodatkowe wyrażenia warunkowego, tego dopasowania Określa kryterium, które muszą zostać spełnione przed zestaw funkcji można stosować na żądanie.
A JEŚLI | Wyrażenie IF i mogą być dodane tylko po następujących typów wyrażenia warunkowe: IF, i jeśli. Wskazuje on, istnieje inny warunek, które muszą zostać spełnione dla początkowego instrukcji IF.
ELSE IF| Wyrażenia ELSE IF określa warunek alternatywną, na którym muszą zostać spełnione przed dokonaniem zestaw funkcji specyficznych dla tej instrukcji ELSE IF. Obecność instrukcji ELSE IF wskazuje koniec poprzednią instrukcję. Wyrażenie warunkowe tylko może być umieszczany po instrukcji ELSE IF innej instrukcji ELSE IF. Oznacza to, że instrukcji ELSE IF tylko można użyć do określenia jednego warunku dodatkowe, który ma zostać spełnione.

**Przykład**: ![CDN zgodne z warunkiem](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Kolejne reguły mogą zastąpić akcje określone przez poprzednie reguły. Przykład: Reguła wychwytywania chroni wszystkie żądania przy użyciu uwierzytelniania opartego na tokenie. Inną regułę można tworzyć bezpośrednio poniżej, aby utworzyć wyjątek dla niektórych typów żądań.

### <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Odwołanie do aparatu reguł](cdn-rules-engine-reference.md)
* [Warunki uzgadniania aparatu reguł](cdn-rules-engine-reference-match-conditions.md)
* [Funkcje aparatu reguł](cdn-rules-engine-reference-features.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
