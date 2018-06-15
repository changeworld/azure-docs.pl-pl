---
title: Zasady usługi Azure CDN aparat odwołania | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 602b4303dd1940791c11b8b71ac6a27f0474a6d5
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29733683"
---
# <a name="azure-cdn-rules-engine-reference"></a>Zasady usługi Azure CDN aparat odwołania
W tym artykule przedstawiono szczegółowe opisy funkcji i warunki dopasowania dostępne dla usługi Azure sieci dostarczania zawartości (CDN) [aparatu reguł](cdn-rules-engine.md).

Aparat reguł została zaprojektowana jako urząd końcowego dla określonych typów żądań są przetwarzane przez usługę CDN.

**Najczęstsze zastosowania**:

- Zastąpienie lub zdefiniowania zasad niestandardowych pamięci podręcznej.
- Zabezpieczanie lub odrzucanie żądań dla poufnej zawartości.
- Przekieruj żądania.
- Przechowywanie danych dziennika niestandardowego.

## <a name="terminology"></a>Terminologia
Reguła jest definiowana za pośrednictwem [ **wyrażenia warunkowe**](cdn-rules-engine-reference-conditional-expressions.md), [ **zgodne z warunkami określonymi**](cdn-rules-engine-reference-match-conditions.md), i [ **funkcji**](cdn-rules-engine-reference-features.md). Te elementy zostały wyróżnione na poniższej ilustracji:

 ![Warunek dopasowania CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Składnia

Sposób, w którym znaki specjalne są traktowane zależy od tego, jak dopasowania warunku lub funkcji obsługuje wartości tekstowe. Dopasowanie warunku lub funkcji mogą interpretować tekst w jednym z następujących sposobów:

1. [**Wartości literałów**](#literal-values) 
2. [**Wartości symboli wieloznacznych**](#wildcard-values)
3. [**Wyrażenia regularne**](#regular-expressions)

### <a name="literal-values"></a>Wartości literałów
Tekst, który jest interpretowana jako wartość literału traktuje wszystkie znaki specjalne, z wyjątkiem % symbol jako część wartości, które muszą być zgodne. Innymi słowy, literałem odpowiada warunku ustawioną `\'*'\` tylko jest spełniony, gdy który dokładnie wartość (oznacza to, `\'*'\`) został znaleziony.
 
Symbol procentu jest używany do określania, kodowania adresu URL (na przykład `%20`).

### <a name="wildcard-values"></a>Wartości symboli wieloznacznych
Tekst, który jest interpretowany jako wartość symbolu wieloznacznego przypisuje dodatkowych znaczeń znaki specjalne. W poniższej tabeli opisano, jak jest interpretowana następujący zestaw znaków:

Znak | Opis
----------|------------
\ | Ukośnik odwrotny służy do zmiany znaczenia znaków określonych w tej tabeli. Należy określić ukośnik odwrotny bezpośrednio przed znak specjalny, który powinien być zmienione znaczenie.<br/>Na przykład następującej składni specjalne gwiazdkę: `\*`
% | Symbol procentu jest używany do określania, kodowania adresu URL (na przykład `%20`).
* | Znak gwiazdki jest symbol wieloznaczny, który reprezentuje co najmniej jeden znak.
Spacja | Znak odstępu wskazuje, czy warunek dopasowania muszą zostać spełnione w jednym z określonymi wartościami lub wzorce.
"wartość" | Pojedynczy cudzysłów nie ma specjalnego znaczenia. Jednak zestaw apostrofy umożliwia wskazują, że wartość powinien być traktowany jako wartość literału. Mogą być używane w następujący sposób:<br><br/>— Umożliwia dopasowanie warunek należy spełnić, jeśli określona wartość dopasowuje dowolną część wartości porównania.  Na przykład `'ma'` czy pasuje do żadnego z następujących ciągów: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/ firm/szablonu. **ma**p<br /><br />— Umożliwia znak specjalny, można określić jako literał znaków. Na przykład można określić znak spacji literału umieszczając znak odstępu, w ramach zestawu pojedynczych cudzysłowów (to znaczy `' '` lub `'sample value'`).<br/>— Umożliwia wartość pusta, należy określić. Określ wartość pusta, określając zestaw pojedynczych cudzysłowów (to znaczy ").<br /><br/>**Ważne:**<br/>— Jeśli określona wartość nie zawiera symboli wieloznacznych, następnie automatycznie uważa się wartość literału, co oznacza, że nie jest niezbędne do określenia zestawu apostrofy.<br/>— Jeśli ukośnik odwrotny ucieczki nie innego znaku w tej tabeli, zostanie zignorowany, gdy jest określony w ramach zestawu apostrofy.<br/>-Inny sposób, aby określić znak specjalny w jako znak ucieczki za pomocą ukośnik odwrotny (czyli `\`).

### <a name="regular-expressions"></a>Wyrażenia regularne

Wyrażenia regularne Zdefiniuj wzorzec, który jest wyszukiwany w wartości tekstowej. Notacja wyrażeń regularnych definiuje określone znaczenie wielu symboli. W poniższej tabeli przedstawiono sposób specjalne znaki są traktowane przez dopasowanie warunków i funkcje, które obsługują wyrażeń regularnych.

Znaki specjalne | Opis
------------------|------------
\ | Ukośnik odwrotny specjalne znak następujące it, co powoduje, że ten znak powinien być traktowany jako wartość literału zamiast przyjmowania na jego znaczenie wyrażenia regularnego. Na przykład następującej składni specjalne gwiazdkę: `\*`
% | Znaczenie symbol procentu zależy od jego użycia.<br/><br/> `%{HTTPVariable}`: Ta składnia identyfikuje Zmienna HTTP.<br/>`%{HTTPVariable%Pattern}`: Ta składnia używana symbol procentu HTTP, zmiennych i jako ogranicznik.<br />`\%`: Anulowanie symbol procentu pozwala ma być używany jako wartość literału lub w celu wskazania kodowania adresu URL (na przykład `\%20`).
* | Znak gwiazdki umożliwia poprzedni znak można dopasować zero lub więcej razy. 
Spacja | Znak odstępu zwykle jest traktowana jako literał znaków. 
"wartość" | Apostrofy są traktowane jako literały. Zestaw apostrofy nie ma specjalnego znaczenia.


## <a name="next-steps"></a>Kolejne kroki
* [Warunki uzgadniania aparatu reguł](cdn-rules-engine-reference-match-conditions.md)
* [Wyrażenia warunkowe aparatu reguł](cdn-rules-engine-reference-conditional-expressions.md)
* [Funkcje aparatu reguł](cdn-rules-engine-reference-features.md)
* [Zastąpienie zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Omówienie usługi Azure CDN](cdn-overview.md)
