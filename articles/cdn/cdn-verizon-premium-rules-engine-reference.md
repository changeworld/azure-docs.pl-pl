---
title: Dokumentacja aparatu reguł usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca usługi Azure CDN zasady warunki dopasowań aparatu i funkcje.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593151"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Usługa Azure CDN from dokumentacja aparatu reguł Premium firmy Verizon

W tym artykule przedstawiono szczegółowe opisy tych funkcji i warunków dopasowania dostępne dla usługi Azure Content Delivery Network (CDN) [aparat reguł](cdn-verizon-premium-rules-engine.md).

Aparat reguł zaprojektowano pod organizacji do końcowego jak określone typy żądań są przetwarzane przez usługę CDN.

**Najczęstsze zastosowania**:

- Zastąp lub Zdefiniuj zasady niestandardowej pamięci podręcznej.
- Zabezpiecz lub odmowę realizacji żądań dla poufnej zawartości.
- Przekieruj żądania.
- Store danych dziennika niestandardowego.

## <a name="terminology"></a>Terminologia

Reguła jest definiowana za pośrednictwem [ **wyrażenia warunkowe**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [ **zgodne z warunkami określonymi**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), i [ **funkcji**](cdn-verizon-premium-rules-engine-reference-features.md). Te elementy zostały wyróżnione na poniższej ilustracji:

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
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.mdZnaczenie symbol procentu jest zależna od jego użycia. Docs
descr`%{HTTPVariable}`: Ta składnia Określa zmienną HTTP.match`%{HTTPVariable%Pattern}`: Ta składnia używana symbol procentu HTTP, zmiennych i w roli ogranicznika.1/2019`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`20`).f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | Znak gwiazdki umożliwia poprzedni znak można dopasować zero lub więcej razy.
Przestrzeń kosmiczna | Znak odstępu zwykle jest traktowana jako znak literałowy.
"wartość" | Apostrofy są traktowane jako znaki literału. Zbiór apostrofy nie ma specjalnego znaczenia.

## <a name="next-steps"></a>Następne kroki

- [Warunki dopasowań aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
- [Omówienie usługi Azure CDN](cdn-overview.md)