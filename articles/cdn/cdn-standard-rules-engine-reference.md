---
title: Odwołanie do aparatu reguł standardowych dla usługi Azure CDN | Dokumenty firmy Microsoft
description: Dokumentacja referencyjna dotycząca warunków dopasowania i akcji w silniku reguł standardowych dla usługi Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 6d4fa4451c3db3d6f2a506eabd5676d18b0219f4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259905"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Dokumentacja aparatu reguł standardowych dla usługi Azure CDN

W [silniku reguł standardowych](cdn-standard-rules-engine.md) dla usługi Azure Content Delivery Network (Azure CDN) reguła składa się z co najmniej jednego dopasowania i akcji. Ten artykuł zawiera szczegółowe opisy warunków dopasowania i funkcji, które są dostępne w silniku reguł standardowych dla usługi Azure CDN.

Aparat reguł jest przeznaczony do ostatecznego urzędu, w jaki sposób określone typy żądań są przetwarzane przez standardową usługę AZURE CDN.

**Typowe zastosowania zasad:**

- Zastądanie lub definiowanie zasad niestandardowej pamięci podręcznej.
- Przekierowanie żądań.
- Modyfikowanie nagłówków żądań i odpowiedzi HTTP.

## <a name="terminology"></a>Terminologia

Aby zdefiniować regułę w silniku reguł, należy ustawić [warunki dopasowania](cdn-standard-rules-engine-match-conditions.md) i [akcje:](cdn-standard-rules-engine-actions.md)

 ![Struktura reguł usługi Azure CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Każda reguła może mieć maksymalnie cztery warunki dopasowania i trzy akcje. Każdy punkt końcowy usługi Azure CDN może mieć maksymalnie pięć reguł. 

Uwzględniony w bieżącym limicie pięciu reguł dla punktu końcowego usługi Azure CDN jest domyślną *regułą globalną*. Reguła globalna nie ma warunków dopasowania, a akcje zdefiniowane w globalnej regule zawsze wyzwalają.

## <a name="syntax"></a>Składnia

Sposób traktowania znaków specjalnych w regule różni się w zależności od tego, jak różne warunki dopasowania i akcje obsługują wartości tekstowe. Warunek dopasowania lub akcja może interpretować tekst w jeden z następujących sposobów:

- [Wartości literału](#literal-values)
- [Wartości symboli wieloznacznych](#wildcard-values)


### <a name="literal-values"></a>Wartości literału

Tekst interpretowany jako wartość literału traktuje wszystkie znaki specjalne *z wyjątkiem symbolu %* jako część wartości, która musi być dopasowana do reguły. Na przykład warunek dopasowania literału ustawiony na `'*'` jest `'*'` spełniony tylko wtedy, gdy zostanie znaleziona dokładna wartość.

Znak procentu służy do wskazywania `%20`kodowania adresów URL (na przykład ).

### <a name="wildcard-values"></a>Wartości symboli wieloznacznych

Tekst interpretowany jako wartość symboli wieloznacznych przypisuje dodatkowe znaczenie znakom specjalnym. W poniższej tabeli opisano sposób interpretowania określonych znaków specjalnych w silniku reguł standardowych:

Znak | Opis
----------|------------
\ | Ukośnik odwrotny jest używany do ucieczki do dowolnego ze znaków określonych w tej tabeli. Ukośnik odwrotny musi być określony bezpośrednio przed znakiem specjalnym, który powinien zostać zmieniony. Na przykład następująca składnia wyuje gwiazdkę:`\*`
% | Znak procentu służy do wskazywania `%20`kodowania adresów URL (na przykład ).
\* | Gwiazdka to symbol wieloznaczny reprezentujący jeden lub więcej znaków.
spacje | Znak spacji wskazuje, że warunek dopasowania może być spełniony przez jedną z określonych wartości lub wzorców.
pojedynczy cudzysłów | Pojedynczy cudzysłów nie ma specjalnego znaczenia. Jednak zestaw pojedynczych cudzysłowów wskazuje, że wartość powinna być traktowana jako wartość literału. Pojedyncze cudzysłowy mogą być używane w następujący sposób:<ul><li>Aby zezwolić na warunek dopasowania, które mają być spełnione, gdy określona wartość pasuje do dowolnej części wartości porównania.  Na przykład `'ma'` można dopasować dowolne z następujących ciągów: <ul><li>/biznes/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/biznes/szablon. **ma**p</li></ul><li>Aby umożliwić określony znak specjalny jako znak literał. Na przykład można określić znak spacji literału, załączając znak spacji w zestawie pojedynczych cudzysłowów (`' '` lub `'<sample value>'`).</li><li>Aby zezwolić na określoną pustą wartość. Określ pustą wartość, określając zestaw pojedynczych cudzysłowów (**''**).</li></ul>**Ważne:**<br /><ul><li>Jeśli określona wartość nie zawiera symbolu wieloznacznego, wartość jest automatycznie uważana za wartość literału. Nie trzeba określać zestawu pojedynczych cudzysłowów dla wartości literału.</li><li>Jeśli ukośnik odwrotny nie jest używany do ucieczki innego znaku w tej tabeli, ukośnik odwrotny jest ignorowany, gdy jest określony w zestawie pojedynczych cudzysłowów.</li><li>Innym sposobem określenia znaku specjalnego jako znaku dosłownego jest ucieczka`\`od niego za pomocą ukośnika odwrotnego ( ).</li></ul>

## <a name="next-steps"></a>Następne kroki

- [Warunki dopasowania w silniku reguł standardowych](cdn-standard-rules-engine-match-conditions.md)
- [Akcje w silniku reguł standardowych](cdn-standard-rules-engine-actions.md)
- [Wymuszanie protokołu HTTPS za pomocą aparatu reguł standardowych](cdn-standard-rules-engine.md)
- [Omówienie usługi Azure CDN](cdn-overview.md)
