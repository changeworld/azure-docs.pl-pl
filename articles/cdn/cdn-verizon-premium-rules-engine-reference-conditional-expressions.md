---
title: Wyrażenia warunkowe dla usługi Azure CDN — aparat reguł Verizon Premium
description: Dokumentacja referencyjna dla usługi Azure CDN z reguł Verizon Premium reguły zgodne warunki i funkcje.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082968"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Usługa Azure CDN z wyrażeń warunkowych aparatu Verizon Premium

W tym artykule wymieniono szczegółowe opisy aparatu reguł warunkowego dostarczania zawartości (CDN) wyrażeń [warunkowych](cdn-verizon-premium-rules-engine.md).

Pierwszą częścią reguły jest wyrażenie warunkowe.

Wyrażenie warunkowe | Opis
-----------------------|-------------
IF | Wyrażenie IF jest zawsze częścią pierwszej instrukcji w regule. Podobnie jak wszystkie inne wyrażenia warunkowe, ta instrukcja IF musi być skojarzona z dopasowaniem. Jeśli nie zdefiniowano żadnych dodatkowych wyrażeń warunkowych, to dopasowanie określa kryterium, które musi być spełnione, zanim zestaw funkcji może być stosowany do żądania.
A JEŚLI | Wyrażenie I IF może być dodawane tylko po następujących typach wyrażeń warunkowych:JEŻELI I IF. Wskazuje, że istnieje inny warunek, który musi być spełniony dla początkowego oświadczenia IF.
W PRZECIWNYM RAZIE, JEŚLI| Wyrażenie ELSE IF określa warunek alternatywny, który musi być spełniony przed zestawem funkcji specyficznych dla niniejszej instrukcji ELSE IF. Obecność instrukcji ELSE IF wskazuje koniec poprzedniej instrukcji. Jedynym wyrażeniem warunkowym, które może zostać umieszczone po instrukcji ELSE IF, jest inna instrukcja ELSE IF. Oznacza to, że instrukcja ELSE IF może być używana tylko do określenia jednego dodatkowego warunku, który musi być spełniony.

**Przykład:** ![Warunek dopasowania cdn](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Kolejna reguła może zastąpić akcje określone przez poprzednią regułę.
   > Przykład: Reguła catch-all zabezpiecza wszystkie żądania za pomocą uwierzytelniania opartego na tokenie. Inna reguła może zostać utworzona bezpośrednio pod nim, aby wprowadzić wyjątek dla niektórych typów żądań.

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure CDN](cdn-overview.md)
- [Odwołanie do aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Zasady warunków dopasowania silnika](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Reguły funkcji aparatu](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)