---
title: Praca ze zintegrowanym interfejsem API zapytań JavaScript w procedurach przechowywanych i wyzwalaczach usługi Azure Cosmos DB
description: W tym artykule przedstawiono pojęcia interfejsu API zapytań zintegrowane z językiem JavaScript w celu utworzenia procedur przechowywanych i wyzwalaczy w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76901837"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Interfejs API zapytań JavaScript w usłudze Azure Cosmos DB

Oprócz wystawiania zapytań przy użyciu interfejsu API SQL w usłudze Azure Cosmos DB zestaw [SDK po stronie serwera usługi Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/) udostępnia interfejs JavaScript do wykonywania zoptymalizowanych zapytań w procedurach przechowywanych i wyzwalaczach usługi Cosmos DB. Nie musisz znać języka SQL, aby używać tego interfejsu JavaScript. Interfejs API zapytań JavaScript umożliwia programowe tworzenie zapytań przez przekazywanie funkcji predykatu do sekwencji wywołań funkcji, ze składnią znaną wbudowanym tablicom ECMAScript5 i popularnymi bibliotekami JavaScript, takimi jak Lodash. Zapytania są analizowane przez środowisko wykonawcze JavaScript i wydajnie wykonywane przy użyciu indeksów usługi Azure Cosmos DB.

## <a name="supported-javascript-functions"></a>Obsługiwane funkcje JavaScript

| **Funkcja** | **Opis** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Rozpoczyna wywołanie łańcuchowe, które musi zostać zakończone z value().|
|`filter(predicateFunction [, options] [, callback])`|Filtruje dane wejściowe przy użyciu funkcji predykatu, która zwraca wartość true/false w celu filtrowania dokumentów wejściowych do wynikowego zestawu. Ta funkcja zachowuje się podobnie do klauzuli WHERE w języku SQL.|
|`flatten([isShallow] [, options] [, callback])`|Łączy i spłaszcza tablice z każdego elementu wejściowego w jedną tablicę. Ta funkcja zachowuje się podobnie do SelectMany w LINQ.|
|`map(transformationFunction [, options] [, callback])`|Stosuje projekcję, biorąc pod uwagę funkcję przekształcania, która mapuje każdy element wejściowy do obiektu lub wartości JavaScript. Ta funkcja zachowuje się podobnie do select klauzuli w języku SQL.|
|`pluck([propertyName] [, options] [, callback])`|Ta funkcja jest skrótem dla mapy, która wyodrębnia wartość pojedynczej właściwości z każdego elementu wejściowego.|
|`sortBy([predicate] [, options] [, callback])`|Tworzy nowy zestaw dokumentów, sortując dokumenty w strumieniu dokumentu wejściowego w porządku rosnącym przy użyciu danego predykatu. Ta funkcja zachowuje się podobnie do klauzuli ORDER BY w języku SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Tworzy nowy zestaw dokumentów, sortując dokumenty w strumieniu dokumentu wejściowego w kolejności malejącej przy użyciu danego predykatu. Ta funkcja zachowuje się podobnie do klauzuli ORDER BY x DESC w języku SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Wykonuje samospołączenie z tablicy wewnętrznej i dodaje wyniki z obu stron jako krotek do projekcji wynikowej. Na przykład, łączący się z person document with person.pets would produce [person, pet] tuples. Jest to podobne do SelectMany w .NET LINK.|

Po uwzględnieniu w funkcjach predykatu i/lub selektora następujące konstrukcje JavaScript są automatycznie optymalizowane do uruchamiania bezpośrednio w indeksach usługi Azure Cosmos DB:

- Proste `=` `+` `-` `*` `/` `%` `|` operatory: `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Literały, w tym dosłowny obiekt:{}
- var, powrót

Następujące konstrukcje JavaScript nie są optymalizowane pod kątem indeksów usługi Azure Cosmos DB:

- Przepływ sterowania (na przykład, jeśli, na, podczas)
- Wywołania funkcji

Aby uzyskać więcej informacji, zobacz [dokumentację JavaScript po stronie serwera usługi Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>Arkusz kodu SQL do JavaScript

W poniższej tabeli przedstawiono różne zapytania SQL i odpowiadające im zapytania JavaScript. Podobnie jak w przypadku zapytań SQL, właściwości (na przykład item.id) są rozróżniane.

> [!NOTE]
> `__` (podwójne podkreślenie) jest aliasem funkcji `getContext().getCollection()` podczas korzystania z interfejsu API zapytań języka JavaScript.

|**SQL**|**Interfejs API zapytań JavaScript**|**Opis**|
|---|---|---|
|WYBIERZ *<br>Z dokumentów| __.map(funkcja(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;powrót doc;<br>});|Wyniki we wszystkich dokumentach (na poszczegieł token kontynuacji), jak jest.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.actions <br>Z dokumentów|__.map(funkcja(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;powrót {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;akcje:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Wyświetla identyfikator, wiadomość (alias do msg) i akcję ze wszystkich dokumentów.|
|WYBIERZ *<br>Z dokumentów<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(funkcja(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;zwraca doc.id ==="X998_Y998";<br>});|Zapytania dotyczące dokumentów z predykatem: id = "X998_Y998".|
|WYBIERZ *<br>Z dokumentów<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(dokumenty. Tagi, 123)|__.filter(funkcja(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;zwraca x.Tagi && x.Tags.indexOf(123) > -1;<br>});|Kwerendy dla dokumentów, które mają Właściwości Znaczniki i Tagi jest tablica zawierająca wartość 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>Z dokumentów<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(funkcja(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;zwraca doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(funkcja(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;powrót {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Kwerendy dla dokumentów z predykatem, id = "X998_Y998", a następnie projekty id i message (aliased do msg).|
|WYBIERZ ZNACZNIK WARTOŚĆ<br>Z dokumentów<br>Dołącz do dokumentów tagu. Tagi<br>ZAMÓWIENIE WEDŁUG DOCS._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(funkcja(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;powrót doc. Tagi && Array.isArray(doc. Tagi);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(funkcja(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;powrót doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tagi")<br>&nbsp;&nbsp;&nbsp;&nbsp;.spłaszczyć()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtry dla dokumentów, które mają właściwość tablicy, Znaczniki i sortuje wynikowe dokumenty przez właściwość systemu sygnatury czasowej _ts, a następnie projekty + spłaszcza tablicę Znaczniki.|

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej pojęć i jak pisać i używać procedur przechowywanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB:

- [Jak pisać procedury przechowywane i wyzwalacze przy użyciu interfejsu API zapytań Javascript](how-to-write-javascript-query-api.md)
- [Praca z procedurami przechowywanymi, wyzwalaczami i funkcjami zdefiniowanymi przez usługę Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Jak korzystać z procedur przechowywanych, wyzwalaczy, funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Odwołanie do interfejsu API po stronie serwera usługi Azure Cosmos DB JavaScript](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
