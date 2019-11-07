---
title: Informacje o aparacie Azure CDN standardowych reguł | Microsoft Docs
description: Dokumentacja referencyjna dotycząca warunków i akcji aparatu Azure CDN standardowych reguł.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615938"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Azure CDN z dokumentacji aparatu Microsoft Rules

W tym artykule przedstawiono szczegółowe opisy warunków i postanowień dopasowania dla [aparatu standardowych reguł](cdn-standard-rules-engine.md)usługi Azure Content Delivery Network (CDN).

Aparat reguł został zaprojektowany z myślą o sposobie przetwarzania określonych typów żądań przez sieć CDN.

**Typowe zastosowania**:

- Przesłoń lub Zdefiniuj niestandardowe zasady pamięci podręcznej.
- Przekieruj żądania.
- Modyfikowanie nagłówków żądań i odpowiedzi HTTP

## <a name="terminology"></a>Terminologia

Reguła jest definiowana przy użyciu [**warunków zgodności**](cdn-standard-rules-engine-match-conditions.md)i [**akcji**](cdn-standard-rules-engine-actions.md). Te elementy zostały wyróżnione na poniższej ilustracji:

 ![Struktura reguł sieci CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Każda reguła może mieć maksymalnie 4 warunki dopasowania i 3 akcje. Istnieje maksymalnie 5 reguł na punkt końcowy usługi CDN. Ponadto istnieje reguła w miejscu domyślnie nazywana **regułą globalną**. Jest to reguła bez zgodności, w której akcje zdefiniowane w ramach programu będą zawsze wyzwalane. Ta reguła jest uwzględniona w ograniczeniu do bieżącego 5 reguł.

## <a name="syntax"></a>Składnia

Sposób, w jaki znaki specjalne są traktowane, różni się w zależności od tego, jak warunek dopasowania lub actopm obsługuje wartości tekstowe. Warunek dopasowania lub funkcja może interpretować tekst w jeden z następujących sposobów:

1. [**Wartości literału**](#literal-values)
2. [**Wartości symboli wieloznacznych**](#wildcard-values)


### <a name="literal-values"></a>Wartości literału

Tekst interpretowany jako wartość literału traktuje wszystkie znaki specjalne, z wyjątkiem symbolu%, jako część wartości, która musi być dopasowana. Innymi słowy, warunek dopasowania literału ustawiony na `\'*'\` jest spełniony tylko wtedy, gdy zostanie znaleziona dokładna wartość (czyli `\'*'\`).

Znak procentu jest używany do wskazania kodowania adresu URL (na przykład `%20`).

### <a name="wildcard-values"></a>Wartości symboli wieloznacznych

Tekst, który jest interpretowany jako wartość symbolu wieloznacznego, przypisuje dodatkowe znaczenie do znaków specjalnych. W poniższej tabeli opisano sposób interpretowania następującego zestawu znaków:

Opis | Opis
----------|------------
\ | Ukośnik odwrotny jest używany do ucieczki znaków określonych w tej tabeli. Ukośnik odwrotny musi być określony bezpośrednio przed znakiem specjalnym, który powinien zostać zmieniony.<br/>Na przykład następująca składnia wyprowadza gwiazdkę: `\*`
% | Znak procentu jest używany do wskazania kodowania adresu URL (na przykład `%20`).
\* | Gwiazdka jest symbolem wieloznacznym reprezentującym jeden lub więcej znaków.
Przestrzeń kosmiczna | Znak spacji wskazuje, że warunek dopasowania może być spełniony przez jedną z określonych wartości lub wzorców.
wartościami | Pojedynczy cudzysłów nie ma specjalnego znaczenia. Jednak zestaw pojedynczych cudzysłowów służy do wskazywania, że wartość powinna być traktowana jako wartość literału. Może być używana w następujący sposób:<br><br/>-Pozwala na spełnienie warunku dopasowywania za każdym razem, gdy określona wartość pasuje do dowolnej części wartości porównania.  Na przykład `'ma'` pasuje do żadnego z następujących ciągów: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>**ma**p. gif<br/>/business/template. **ma**p<br /><br />— Umożliwia określenie znaku specjalnego jako znaku literału. Na przykład można określić znak spacji literału, umieszczając znak spacji w zestawie pojedynczych cudzysłowów (czyli `' '` lub `'sample value'`).<br/>-Umożliwia określenie wartości pustej. Określ wartość pustą, określając zestaw pojedynczych cudzysłowów (czyli "").<br /><br/>**Ważne:**<br/>— Jeśli określona wartość nie zawiera symbolu wieloznacznego, zostanie automatycznie uznana za wartość literału, co oznacza, że nie jest konieczne określenie zestawu pojedynczych cudzysłowów.<br/>-Jeśli ukośnik odwrotny nie powoduje ucieczki innego znaku w tej tabeli, jest on ignorowany, gdy jest określony w ramach zestawu pojedynczych cudzysłowów.<br/>Innym sposobem określenia znaku specjalnego jako znaku literału jest wypróbowanie go przy użyciu ukośnika odwrotnego (czyli `\`).

## <a name="next-steps"></a>Następne kroki

- [Warunki dopasowania aparatu reguł standardowych](cdn-standard-rules-engine-match-conditions.md)
- [Standardowe działania aparatu reguł](cdn-standard-rules-engine-actions.md)
- [Wymuszanie protokołu HTTPS przy użyciu standardowego aparatu reguł](cdn-standard-rules-engine.md)
- [Przegląd Azure CDN](cdn-overview.md)
