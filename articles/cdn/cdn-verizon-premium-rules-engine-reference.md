---
title: Odwołanie do aparatu reguł usługi Azure CDN | Dokumenty firmy Microsoft
description: Dokumentacja referencyjna dla reguł usługi Azure CDN reguły odpowiadają warunki i funkcje.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69996753"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Usługa Azure CDN z odwołania do aparatu reguł Verizon Premium

W tym artykule wymieniono szczegółowe opisy dostępnych warunków dopasowania i funkcji [aparatu reguł](cdn-verizon-premium-rules-engine.md)usługi Azure Content Delivery Network (CDN).

Aparat reguł ma być ostatecznym organem w zakresie sposobu przetwarzania określonych typów żądań przez sieć CDN.

**Typowe zastosowania:**

- Zastądanie lub definiowanie zasad niestandardowej pamięci podręcznej.
- Zabezpiecz lub odmów żądań dotyczących poufnych treści.
- Przekierowanie żądań.
- Przechowywanie niestandardowych danych dziennika.

## <a name="terminology"></a>Terminologia

Reguła jest definiowana za pomocą [**wyrażeń warunkowych,**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md) [**warunków dopasowania**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)i [**funkcji**](cdn-verizon-premium-rules-engine-reference-features.md). Elementy te są wyróżnione na poniższej ilustracji:

 ![Warunek dopasowania sieci CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Składnia

Sposób traktowania znaków specjalnych różni się w zależności od tego, jak warunek dopasowania lub funkcja obsługuje wartości tekstowe. Warunek dopasowania lub funkcja może interpretować tekst w jeden z następujących sposobów:

1. [**Wartości literału**](#literal-values)
2. [**Wartości symboli wieloznacznych**](#wildcard-values)
3. [**Wyrażenia regularne**](#regular-expressions)

### <a name="literal-values"></a>Wartości literału

Tekst interpretowany jako wartość literału traktuje wszystkie znaki specjalne, z wyjątkiem symbolu %, jako część wartości, która musi być dopasowana. Innymi słowy warunek dopasowania dosłownego ustawiony na `\'*'\` jest spełniony `\'*'\`tylko wtedy, gdy zostanie znaleziona dokładna wartość (czyli) .

Symbol procentowy służy do wskazywania `%20`kodowania adresów URL (na przykład ).

### <a name="wildcard-values"></a>Wartości symboli wieloznacznych

Tekst interpretowany jako wartość symboli wieloznacznych przypisuje dodatkowe znaczenie znakom specjalnym. W poniższej tabeli opisano sposób interpretowania następującego zestawu znaków:

Znak | Opis
----------|------------
\ | Ukośnik odwrotny jest używany do ucieczki do dowolnego ze znaków określonych w tej tabeli. Ukośnik odwrotny musi być określony bezpośrednio przed znakiem specjalnym, który powinien zostać zmieniony.<br/>Na przykład następująca składnia wyuje gwiazdkę:`\*`
% | Symbol procentowy służy do wskazywania `%20`kodowania adresów URL (na przykład ).
\* | Gwiazdka to symbol wieloznaczny reprezentujący jeden lub więcej znaków.
Miejsce | Znak spacji wskazuje, że warunek dopasowania może być spełniony przez jedną z określonych wartości lub wzorców.
"wartość" | Pojedynczy cytat nie ma specjalnego znaczenia. Jednak zestaw pojedynczych cudzysłowów jest używany do wskazania, że wartość powinna być traktowana jako wartość literału. Może być stosowany w następujący sposób:<br><br/>- Umożliwia warunek dopasowania, które mają być spełnione, gdy określona wartość pasuje do dowolnej części wartości porównania.  Na przykład `'ma'` można dopasować dowolne z następujących ciągów: <br/><br/>/biznes/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/biznes/szablon. **ma**p<br /><br />- Pozwala na to, aby znak specjalny był określony jako znak dosłowny. Na przykład można określić znak spacji literału, załączając znak spacji `' '` w `'sample value'`zestawie pojedynczych cudzysłowów (czyli lub ).<br/>- Umożliwia określoną wartość pustą. Określ pustą wartość, określając zestaw pojedynczych cudzysłowów (czyli '').<br /><br/>**Ważne:**<br/>- Jeśli określona wartość nie zawiera symboli wieloznacznych, to jest automatycznie uważany za wartość literału, co oznacza, że nie jest konieczne, aby określić zestaw pojedynczych cudzysłowów.<br/>- Jeśli ukośnik odwrotny nie uniknie innego znaku w tej tabeli, jest ignorowany, gdy jest określony w zestawie pojedynczych cudzysłowów.<br/>- Innym sposobem określenia znaku specjalnego jako znaku dosłownego jest ucieczka `\`od niego za pomocą ukośnika odwrotnego (czyli ).

### <a name="regular-expressions"></a>Wyrażenia regularne

Wyrażenia regularne definiują wzorzec, który jest wyszukiwany w wartości tekstowej. Notacja wyrażenia regularnego definiuje określone znaczenia dla różnych symboli. W poniższej tabeli przedstawiono sposób traktowania znaków specjalnych według warunków dopasowania i funkcji obsługujących wyrażenia regularne.

Znak specjalny | Opis
------------------|------------
\ | Ukośnik odwrotny unika znaku następującego po nim, co powoduje, że znak ten ma być traktowany jako wartość dosłowna, zamiast nabierać jego znaczenia wyrażenia regularnego. Na przykład następująca składnia wyuje gwiazdkę:`\*`
% | Znaczenie symbolu procentowego zależy od jego użycia.<br/><br/> `%{HTTPVariable}`: Ta składnia identyfikuje zmienną HTTP.<br/>`%{HTTPVariable%Pattern}`: Ta składnia używa symbolu procentowego do identyfikowania zmiennej HTTP i jako ogranicznika.<br />`\%`: Ucieczka symbolu procentowego pozwala na użycie go jako wartości literalnej `\%20`lub wskazanie kodowania adresów URL (na przykład ).
\* | Gwiazdka umożliwia dopasowanie poprzedniego znaku do zera lub więcej razy.
Miejsce | Znak spacji jest zazwyczaj traktowany jako znak dosłowny.
"wartość" | Pojedyncze cudzysłowy są traktowane jako znaki dosłowne. Zestaw pojedynczych cudzysłowów nie ma specjalnego znaczenia.

Dopasuj warunki i funkcje obsługujące wyrażenia regularne akceptują wzorce zdefiniowane przez wyrażenia regularne zgodne z Perl (PCRE).

## <a name="next-steps"></a>Następne kroki

- [Zasady warunków dopasowania silnika](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Reguły wyrażeń warunkowych aparatu](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Reguły funkcji aparatu](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
- [Omówienie usługi Azure CDN](cdn-overview.md)
