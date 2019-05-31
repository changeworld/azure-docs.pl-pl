---
title: Praca z JavaScript zapytanie o języku zintegrowanym interfejsu API w usłudze Azure Cosmos DB
description: Ten artykuł wprowadza pojęcia dla języka JavaScript zapytanie o języku zintegrowanym interfejsu API do tworzenia procedur składowanych i wyzwalaczy w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: cc1815ce4a7a9ed40848e4a67a7fd9e032c1daa1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226194"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Zapytanie JavaScript API w usłudze Azure Cosmos DB

Oprócz wysyłania zapytań przy użyciu interfejsu API SQL w usłudze Azure Cosmos DB, [SDK po stronie serwera usługi Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/) umożliwia wykonywanie zoptymalizowane zapytania przy użyciu interfejsu języka JavaScript. Nie trzeba znać języka SQL, aby użyć tego interfejsu języka JavaScript. Zapytanie języka JavaScript, które interfejs API umożliwia programowe tworzenie zapytań, przekazując funkcji predykatu sekwencję funkcja wywołuje składnią znane elementy tablicy wbudowane i popularne biblioteki JavaScript, takich jak Lodash ECMAScript5 firmy. Zapytania są analizowane przez środowisko uruchomieniowe JavaScript i efektywnie posługując się indeksów usługi Azure Cosmos DB.

## <a name="supported-javascript-functions"></a>Obsługiwane funkcje języka JavaScript

| **Function** | **Opis** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Łańcuchowe wywołanie, które musi być zakończona zaczyna się od value().|
|`filter(predicateFunction [, options] [, callback])`|Służy do przefiltrowania przy użyciu funkcji predykatu, który zwraca wartość PRAWDA/FAŁSZ, aby przefiltrować wchodzącym/wychodzącym dokumentów wejściowych do Wynikowy zestaw danych wejściowych. Ta funkcja działa podobnie do klauzuli WHERE w języku SQL.|
|`flatten([isShallow] [, options] [, callback])`|Łączy i spłaszcza magazynowymi pochodzącymi od każdego elementu wejściowego w jedną. Ta funkcja działa podobnie jak SelectMany w składniku LINQ.|
|`map(transformationFunction [, options] [, callback])`|Stosuje projekcji, biorąc pod uwagę funkcja transformacji, która mapuje każdego elementu wejściowego obiektu JavaScript lub wartość. Ta funkcja działa podobnie do klauzuli SELECT w tabeli SQL.|
|`pluck([propertyName] [, options] [, callback])`|Ta funkcja jest skrót mapę, która wyodrębnia wartość właściwości jednego z każdego elementu wejściowego.|
|`sortBy([predicate] [, options] [, callback])`|Tworzy nowy zestaw dokumentów, sortując dokumentów w usłudze stream dokument wejściowy w kolejności rosnącej, korzystając z określonym predykatem. Ta funkcja działa podobnie do klauzuli ORDER BY w języku SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Tworzy nowy zestaw dokumentów, sortując dokumentów w usłudze stream dokument wejściowy w porządku malejącym przy użyciu podanego predykatu. Ta funkcja działa podobnie do klauzuli ORDER BY x DESC w języku SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Wykonuje samosprzężenie wewnętrzny tablicę i dodaje wyniki z obu stron jako krotki do projekcji wynik. Na przykład dołączenie dokumentu osoby z person.pets dałby w efekcie [osoby pet] krotek. Jest to podobne do SelectMany linku .NET.|

Gdy się wewnątrz predykatu lub selektora funkcje, następujące konstrukcje JavaScript Pobierz automatycznie zoptymalizowane pod kątem uruchamiania bezpośrednio na indeksów usługi Azure Cosmos DB:

- Proste operatory: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literały, literał obiektu w tym: {}
- var zwrotu

Następujące elementy języka JavaScript nie uzyskać zoptymalizowane pod kątem indeksów usługi Azure Cosmos DB:

- Przepływ sterowania (na przykład, jeśli, podczas gdy)
- Wywołania funkcji

Aby uzyskać więcej informacji, zobacz [dokumentacja po stronie serwera usługi Cosmos DB w JavaScript](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL do ściągawka dotycząca języka JavaScript

W poniższej tabeli przedstawiono różne zapytania SQL i odpowiednie zapytania języka JavaScript. Podobnie jak w przypadku zapytań SQL właściwości (na przykład item.id) jest rozróżniana wielkość liter.

> [!NOTE]
> `__` (podwójne podkreślenie) jest aliasem funkcji `getContext().getCollection()` podczas korzystania z interfejsu API zapytań języka JavaScript.

|**SQL**|**Zapytanie JavaScript API**|**Opis**|
|---|---|---|
|WYBIERZ POZYCJĘ *<br>DOKUMENTACH| __.map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc;<br>});|To powoduje wszystkie dokumenty (z podziałem na strony za pomocą tokenu kontynuacji) jako.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.ID,<br>&nbsp;&nbsp;&nbsp;docs.Message jako msg,<br>&nbsp;&nbsp;&nbsp;docs.Actions <br>DOKUMENTACH|__.map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Zwróć {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;komunikat: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projekty identyfikator, komunikat (alias do msg) i akcji z wszystkich dokumentów.|
|WYBIERZ POZYCJĘ *<br>DOKUMENTACH<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|Zapytania dotyczące dokumentów za pomocą predykat: identyfikator = "X998_Y998".|
|WYBIERZ POZYCJĘ *<br>DOKUMENTACH<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|Zapytania dotyczące dokumentów, które mają tagi i właściwość Tags jest tablicą, zawierającego wartość 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.ID,<br>&nbsp;&nbsp;&nbsp;docs.Message jako msg<br>DOKUMENTACH<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;komunikat: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|Zapytania dotyczące dokumentów z predykatem, id = "X998_Y998", a następnie projekty identyfikatora i komunikatu (alias do komunikatu).|
|Wybierz wartość tagu<br>DOKUMENTACH<br>Dołącz do dokumentów w tagu. Tagi<br>Klauzula ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć dokumentu. Tagi & & Array.isArray (doc. Znaczniki);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtry dla dokumentów, których właściwości tablicy, tagi, i sortuje wynikowy dokumenty _ts sygnatura czasowa systemu właściwości, a następnie projekty + spłaszcza tablica tagów.|

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej pojęcia i porad zapisu, a następnie użyj procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB:

- [Jak napisać procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka Javascript](how-to-write-javascript-query-api.md)
- [Pracę z usługą Azure Cosmos DB procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika](stored-procedures-triggers-udfs.md)
- [Sposób użycia procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Dokumentacja interfejsu API po stronie serwera w usłudze Azure Cosmos DB JavaScript](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
