---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: ff7ba04271c150018f2c55b62e40542a686608cf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183741"
---
## <a name="create-client"></a>Tworzenie połączenia klienta
Utwórz połączenie klienta, tworząc obiekt `WindowsAzure.MobileServiceClient`.  Zastąp ciąg `appUrl` adresem URL Twojej aplikacji Mobile App.

```javascript
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="table-reference"></a>Praca z tabelami
Aby uzyskać dostęp do danych lub je zaktualizować, utwórz odwołanie do tabeli zaplecza. Zastąp ciąg `tableName` nazwą tabeli

```javascript
var table = client.getTable(tableName);
```

Po utworzeniu odwołania do tabeli możesz kontynuować pracę z tabelą:

* [Odpytywanie tabeli](#querying)
  * [Filtrowanie danych](#table-filter)
  * [Stronicowanie danych](#table-paging)
  * [Sortowanie danych](#sorting-data)
* [Wstawianie danych](#inserting)
* [Modyfikowanie danych](#modifying)
* [Usuwanie danych](#deleting)

### <a name="querying"></a>Jak: Odpytywanie odwołania do tabeli
Po utworzeniu odwołania do tabeli możesz przy jego użyciu wysłać zapytanie o dane na serwerze.  Zapytania są tworzone w języku przypominającym LINQ.
Aby zwrócić wszystkie dane z tabeli, użyj następującego kodu:

```javascript
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Funkcja success jest wywoływana z użyciem wyników.  Nie umieszczaj w funkcji success pętli `for (var i in results)`, ponieważ będzie ona przechodzić przez informacje zawarte w wynikach w czasie, gdy będą używane inne funkcje zapytań (takie jak `.includeTotalCount()`).

Aby dowiedzieć się więcej o składni zapytań, zobacz [dokumentację obiektu Query].

#### <a name="table-filter"></a>Filtrowanie danych na serwerze
W przypadku odwołania do tabeli możesz użyć klauzuli `where`:

```javascript
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Ponadto możesz użyć funkcji filtrującej obiekt.  W tym przykładzie zmienna `this` jest przypisana do obecnie filtrowanego obiektu.  Poniższy kod jest funkcjonalnie równoważny z poprzednim przykładem:

```javascript
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="table-paging"></a>Stronicowanie danych
Skorzystaj z metod `take()` i `skip()`.  Na przykład jeśli chcesz podzielić tabelę na rekordy składające się ze 100 wierszy:

```javascript
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Metoda `.includeTotalCount()` powoduje dodanie pola totalCount do obiektu results.  Pole totalCount zostanie wypełnione łączną liczbą rekordów, które zostałyby zwrócone w przypadku braku stronicowania.

Następnie możesz udostępnić listę stron za pomocą zmiennej pages i przycisków interfejsu użytkownika. Aby załadować nowe rekordy na każdą stronę, użyj metody `loadPage()`.  Zaimplementuj buforowanie, aby przyspieszyć dostęp do już załadowanych rekordów.

#### <a name="sorting-data"></a>Jak: Zwróć posortowane dane
Użyj metody zapytania `.orderBy()` lub `.orderByDescending()`:

```javascript
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Aby uzyskać informacje o obiekcie Query, zobacz [dokumentację obiektu Query].

### <a name="inserting"></a>Jak: Wstawianie danych
Utwórz obiekt JavaScript z odpowiednimi danymi i asynchronicznie wywołaj metodę `table.insert()`:

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Pomyślnie wstawiony element zostaje zwrócony z dodatkowymi polami, które są wymagane przez operacje synchronizacji.  Zaktualizuj własną pamięć podręczną o te informacje na potrzeby późniejszych aktualizacji.

Zestaw Azure Mobile Apps Node.js Server SDK obsługuje schemat dynamiczny dla celów deweloperskich.  Schemat dynamiczny umożliwia dodawanie kolumn do tabeli przez podanie ich w operacji wstawiania lub aktualizacji.  Zalecamy wyłączenie schematu dynamicznego przed przeniesieniem aplikacji na etap produkcji.

### <a name="modifying"></a>Jak: Modyfikowanie danych
Podobnie jak w przypadku metody `.insert()` należy utworzyć obiekt aktualizacji, a następnie wywołać metodę `.update()`.  Obiekt aktualizacji musi zawierać identyfikator rekordu do zaktualizowania — identyfikator ten uzyskuje się podczas odczytu rekordu bądź wywoływania metody `.insert()`.

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="deleting"></a>Jak: Usuwanie danych
Aby usunąć rekord, wywołaj metodę `.del()`.  Przekaż identyfikator w odwołaniu do obiektu:

```javascript
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
