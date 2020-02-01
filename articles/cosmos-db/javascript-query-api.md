---
title: Współpraca z interfejsem API zintegrowanej kwerendy języka JavaScript w Azure Cosmos DB procedury składowane i wyzwalacze
description: W tym artykule przedstawiono koncepcje dotyczące interfejsu API zapytań zintegrowanych z językiem JavaScript do tworzenia procedur składowanych i wyzwalaczy w Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901837"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Interfejs API zapytań JavaScript w Azure Cosmos DB

Oprócz wydawania zapytań przy użyciu interfejsu API SQL w Azure Cosmos DB, [zestaw SDK po stronie serwera Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/) udostępnia interfejs JavaScript do wykonywania zoptymalizowanych zapytań w Cosmos DB procedurach składowanych i wyzwalaczach. Nie trzeba znać języka SQL, aby używać tego interfejsu JavaScript. Interfejs API zapytań języka JavaScript umożliwia Programistyczne tworzenie zapytań przez przekazywanie funkcji predykatów do sekwencji wywołań funkcji, z składnią ECMAScript5's wbudowane i popularne biblioteki języka JavaScript, takie jak Lodash. Zapytania są analizowane przez środowisko uruchomieniowe JavaScript i efektywnie wykonywane przy użyciu Azure Cosmos DB indeksów.

## <a name="supported-javascript-functions"></a>Obsługiwane funkcje języka JavaScript

| **Function** | **Opis** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Łańcuchowe wywołanie, które musi być zakończona zaczyna się od value().|
|`filter(predicateFunction [, options] [, callback])`|Służy do przefiltrowania przy użyciu funkcji predykatu, który zwraca wartość PRAWDA/FAŁSZ, aby przefiltrować wchodzącym/wychodzącym dokumentów wejściowych do Wynikowy zestaw danych wejściowych. Ta funkcja działa podobnie do klauzuli WHERE w języku SQL.|
|`flatten([isShallow] [, options] [, callback])`|Łączy i spłaszcza tablice z każdego elementu wejściowego w jedną tablicę. Ta funkcja działa podobnie jak SelectMany w składniku LINQ.|
|`map(transformationFunction [, options] [, callback])`|Stosuje projekcji, biorąc pod uwagę funkcja transformacji, która mapuje każdego elementu wejściowego obiektu JavaScript lub wartość. Ta funkcja działa podobnie do klauzuli SELECT w tabeli SQL.|
|`pluck([propertyName] [, options] [, callback])`|Ta funkcja jest skrót mapę, która wyodrębnia wartość właściwości jednego z każdego elementu wejściowego.|
|`sortBy([predicate] [, options] [, callback])`|Tworzy nowy zestaw dokumentów, sortując dokumenty w strumieniu dokumentu wejściowego w kolejności rosnącej przy użyciu danego predykatu. Ta funkcja działa podobnie do klauzuli ORDER BY w języku SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Tworzy nowy zestaw dokumentów, sortując dokumenty w strumieniu dokumentu wejściowego w kolejności malejącej przy użyciu podanego predykatu. Ta funkcja działa podobnie do klauzuli ORDER BY x DESC w języku SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Wykonuje samosprzężenie z tablicą wewnętrzną i dodaje wyniki z obu stron jako krotki do rzutowania wyników. Na przykład przyłączenie do dokumentu osoby. zwierzęta domowe spowodują utworzenie krotek [Person, PET]. Jest to podobne do SelectMany w ŁĄCZu .NET.|

Gdy się wewnątrz predykatu lub selektora funkcje, następujące konstrukcje JavaScript Pobierz automatycznie zoptymalizowane pod kątem uruchamiania bezpośrednio na indeksów usługi Azure Cosmos DB:

- Operatory proste: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literały, literał obiektu w tym: {}
- var zwrotu

Następujące elementy języka JavaScript nie uzyskać zoptymalizowane pod kątem indeksów usługi Azure Cosmos DB:

- Przepływ sterowania (na przykład, jeśli, podczas gdy)
- Wywołania funkcji

Aby uzyskać więcej informacji, zobacz [dokumentację języka JavaScript po stronie serwera Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>Arkusz Ściągawka SQL do JavaScript

W poniższej tabeli przedstawiono różne zapytania SQL i odpowiednie zapytania języka JavaScript. Podobnie jak w przypadku zapytań SQL, właściwości (na przykład item.id) uwzględniają wielkość liter.

> [!NOTE]
> `__` (podwójne podkreślenie) jest aliasem funkcji `getContext().getCollection()` podczas korzystania z interfejsu API zapytań języka JavaScript.

|**SQL**|**Interfejs API zapytań JavaScript**|**Opis**|
|---|---|---|
|WYBIERZ POZYCJĘ *<br>DOKUMENTACH| __.map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc;<br>});|To powoduje wszystkie dokumenty (z podziałem na strony za pomocą tokenu kontynuacji) jako.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message jako komunikat<br>&nbsp;&nbsp;&nbsp;docs. Actions <br>DOKUMENTACH|__.map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Zwróć {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;komunikat: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projekty identyfikator, komunikat (alias do msg) i akcji z wszystkich dokumentów.|
|WYBIERZ POZYCJĘ *<br>DOKUMENTACH<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|Zapytania dotyczące dokumentów za pomocą predykat: identyfikator = "X998_Y998".|
|WYBIERZ POZYCJĘ *<br>DOKUMENTACH<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Tagi, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|Zapytania dotyczące dokumentów, które mają tagi i właściwość Tags jest tablicą, zawierającego wartość 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message jako komunikat<br>DOKUMENTACH<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;komunikat: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|Zapytania dotyczące dokumentów z predykatem, id = "X998_Y998", a następnie projekty identyfikatora i komunikatu (alias do komunikatu).|
|Wybierz wartość tagu<br>DOKUMENTACH<br>Dołącz do dokumentów w tagu. Tagi<br>Klauzula ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć dokumentu. Tagi & & Array.isArray (doc. Znaczniki);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtry dla dokumentów, których właściwości tablicy, tagi, i sortuje wynikowy dokumenty _ts sygnatura czasowa systemu właściwości, a następnie projekty + spłaszcza tablica tagów.|

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o pojęciach i sposobie pisania i używania procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w programie Azure Cosmos DB:

- [Jak napisać procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka JavaScript](how-to-write-javascript-query-api.md)
- [Praca z Azure Cosmos DB procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika](stored-procedures-triggers-udfs.md)
- [Jak używać procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Dokumentacja interfejsu API po stronie serwera w usłudze Azure Cosmos DB JavaScript](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
