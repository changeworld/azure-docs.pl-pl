---
title: Dokumentacja aparatu reguł usługi Azure CDN | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 3163b33f69f4cc2d6cd4127253c7b6fadfddd6b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324054"
---
# <a name="azure-cdn-rules-engine-reference"></a>Dokumentacja aparatu reguł usługi Azure CDN
W tym artykule przedstawiono szczegółowe opisy tych funkcji i warunków dopasowania dostępne dla usługi Azure Content Delivery Network (CDN) [aparat reguł](cdn-rules-engine.md).

Aparat reguł zaprojektowano pod organizacji do końcowego jak określone typy żądań są przetwarzane przez usługę CDN.

**Najczęstsze zastosowania**:

- Zastąp lub Zdefiniuj zasady niestandardowej pamięci podręcznej.
- Zabezpiecz lub odmowę realizacji żądań dla poufnej zawartości.
- Przekieruj żądania.
- Store danych dziennika niestandardowego.

## <a name="terminology"></a>Terminologia
Reguła jest definiowana za pośrednictwem [ **wyrażenia warunkowe**](cdn-rules-engine-reference-conditional-expressions.md), [ **zgodne z warunkami określonymi**](cdn-rules-engine-reference-match-conditions.md), i [ **funkcji**](cdn-rules-engine-reference-features.md). Te elementy zostały wyróżnione na poniższej ilustracji:

 ![Warunek dopasowania sieci CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Składnia

Sposób, w którym znaki specjalne są traktowane zależy od tego, jak warunek dopasowania lub funkcja obsługuje wartości tekstowych. Warunek dopasowania lub funkcji może interpretować tekst w jednym z następujących sposobów:

1. [**Wartości literałów**](#literal-values) 
2. [**Wartości symboli wieloznacznych**](#wildcard-values)
3. [**Wyrażenia regularne**](#regular-expressions)

### <a name="literal-values"></a>Wartości literałów
Tekst, który jest interpretowany jako wartość literału traktuje wszystkie znaki specjalne, z wyjątkiem symbolu % jako część wartości, które muszą być zgodne. Innymi słowy, literał dopasowania warunek równa `\'*'\` tylko jest spełniony, gdy, dokładna wartość (oznacza to, `\'*'\`) zostanie znaleziony.
 
Symbol procentu jest używany w celu wskazania kodowania adresu URL (na przykład `%20`).

### <a name="wildcard-values"></a>Wartości symboli wieloznacznych
Tekst, który jest interpretowany jako wartość symbolu wieloznacznego przypisuje dodatkowe znaczenie znaków specjalnych. W poniższej tabeli opisano, jak interpretować jest przedstawiony poniżej zestaw znaków:

Znak | Opis
----------|------------
\ | Ukośnik odwrotny jest używany jako znak ucieczki znaków określonych w tej tabeli. Ukośnik odwrotny, muszą zostać określone bezpośrednio przed znak specjalny, który powinien być poprzedzone znakiem zmiany znaczenia.<br/>Na przykład następująca składnia specjalne gwiazdki: `\*`
% | Symbol procentu jest używany w celu wskazania kodowania adresu URL (na przykład `%20`).
\* | Gwiazdka jest symbolem wieloznacznym, który reprezentuje co najmniej jeden znak.
Przestrzeń kosmiczna | Znak odstępu wskazuje, że warunek dopasowania muszą zostać spełnione w jednej z określonych wartości lub wzorce.
"wartość" | Pojedynczy cudzysłów nie ma specjalnego znaczenia. Jednak zestaw pojedynczych cudzysłowów służy do wskazania, że wartości powinny być traktowane jako wartości literału. Mogą być używane w następujący sposób:<br><br/>— Umożliwia warunek dopasowania, które należy spełnić, zawsze wtedy, gdy określona wartość pasuje do dowolnej części wartość porównania.  Na przykład `'ma'` będzie zgodny z żadnym z następujących ciągów: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />— Umożliwia znaki specjalne, należy określić jako znak literałowy. Na przykład, można określić znak spacji literału, umieszczając znak odstępu, w ramach zestawu pojedynczych cudzysłowów (czyli `' '` lub `'sample value'`).<br/>— Umożliwia określenia pustej wartości. Określ wartość pusta, określając zestaw pojedynczych cudzysłowów (czyli ").<br /><br/>**Ważne:**<br/>— Jeśli określona wartość nie zawiera symboli wieloznacznych, następnie automatycznie uważa się wartością literałową, co oznacza, że nie jest niezbędne do określenia zestawu pojedynczych cudzysłowów.<br/>— Jeśli ukośnik odwrotny ucieczki nie inny znak w tej tabeli, jest ignorowany, jeśli nie zostanie określony w ramach zestawu apostrofy.<br/>-Innym sposobem określenia znaku specjalnego w jako znak literałowy się przed nimi znak ucieczki ukośnika odwrotnego (czyli `\`).

### <a name="regular-expressions"></a>Wyrażenia regularne

Wyrażenia regularne zdefiniować wzorzec, który jest wyszukiwana w wartości tekstowej. Notacja wyrażeń regularnych definiuje określone znaczenie różnych symboli. Poniższa tabela wskazuje, jak specjalne znaki są traktowane przez warunki dopasowań i funkcje, które obsługują wyrażeń regularnych.

Znak specjalny | Opis
------------------|------------
\ | Ukośnik odwrotny specjalne znak obserwowanych it, co powoduje, że ten znak powinien być traktowany jako wartość literału, a nie na jego znaczenie wyrażenia regularnego. Na przykład następująca składnia specjalne gwiazdki: `\*`
% | Znaczenie symbol procentu jest zależna od jego użycia.<br/><br/> `%{HTTPVariable}`: Ta składnia Określa zmienną HTTP.<br/>`%{HTTPVariable%Pattern}`: Ta składnia używana symbol procentu HTTP, zmiennych i w roli ogranicznika.<br />`\%`: Anulowanie symbol procentu umożliwia ma być używany jako wartość literału lub w celu wskazania kodowania adresu URL (na przykład `\%20`).
\* | Znak gwiazdki umożliwia poprzedni znak można dopasować zero lub więcej razy. 
Przestrzeń kosmiczna | Znak odstępu zwykle jest traktowana jako znak literałowy. 
"wartość" | Apostrofy są traktowane jako znaki literału. Zbiór apostrofy nie ma specjalnego znaczenia.


## <a name="next-steps"></a>Kolejne kroki
* [Warunki dopasowań aparatu reguł](cdn-rules-engine-reference-match-conditions.md)
* [Wyrażenia warunkowe aparatu reguł](cdn-rules-engine-reference-conditional-expressions.md)
* [Funkcje aparatu reguł](cdn-rules-engine-reference-features.md)
* [Zastępowanie zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Omówienie usługi Azure CDN](cdn-overview.md)
