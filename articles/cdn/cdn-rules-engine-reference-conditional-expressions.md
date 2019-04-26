---
title: Wyrażenia warunkowe aparatu reguł usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca usługi Azure CDN zasady warunki dopasowań aparatu i funkcje.
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
ms.openlocfilehash: 73c41b754c0aca5ddb1a49fcd2794aa41b2fa705
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324207"
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Wyrażenia warunkowe aparatu reguł usługi Azure CDN
W tym temacie przedstawiono szczegółowe opisy wyrażeń warunkowych dla usługi Azure Content Delivery Network (CDN) [aparat reguł](cdn-rules-engine.md).

Pierwsza część reguły jest wyrażenia warunkowego.

Wyrażenie warunkowe | Opis
-----------------------|-------------
IF | Jeśli wyrażenie jest zawsze część pierwsza instrukcja w regule. Podobnie jak wszystkie inne wyrażenia warunkowego tej instrukcji IF musi być skojarzony z dopasowania. Jeśli nie dodatkowe wyrażenia warunkowe są zdefiniowane, to dopasowania Określa kryterium, które muszą zostać spełnione przed zestawem funkcji mogą być stosowane do żądania.
JEŚLI | Wyrażenie IF i mogą być dodawane tylko po następujące typy wyrażenia warunkowe: IF, gdy. Oznacza, że istnieje inny warunek, który muszą być spełnione dla początkowego instrukcji IF.
ELSE IF| Wyrażenie ELSE IF określa warunek alternatywny, które muszą zostać spełnione przed rozpoczęciem zbiór funkcji przeznaczonych dla tej instrukcji ELSE IF. Obecność instrukcji ELSE IF wskazuje koniec poprzednią instrukcję. Tylko warunkowe wyrażenie, które mogą być umieszczone po instrukcji ELSE IF jest inna instrukcja ELSE IF. Oznacza to, że instrukcji ELSE IF tylko służy do określenia pojedynczego dodatkowe warunku, który ma zostać spełnione.

**Przykład**: ![Warunek dopasowania sieci CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Kolejne reguły mogą zastąpić akcji określonych przez poprzednią regułę. Przykład: Regułę przechwytującą cały zabezpiecza wszystkie żądania za pośrednictwem uwierzytelniania opartego na tokenach. Można utworzyć inną regułę bezpośrednio poniżej, aby utworzyć wyjątek dla niektórych typów żądań.

### <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Dokumentacja aparatu reguł](cdn-rules-engine-reference.md)
* [Warunki dopasowań aparatu reguł](cdn-rules-engine-reference-match-conditions.md)
* [Funkcje aparatu reguł](cdn-rules-engine-reference-features.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
