---
title: Praca z danymi JSON w usłudze Azure SQL Database | Dokumentacja firmy Microsoft
description: Usługa Azure SQL Database umożliwia analizy, zapytań i formatowanie danych w notacji języka JavaScript Object Notation (JSON).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 77f6125980c43817230b8a8d4beb32757f23e6c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702969"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Wprowadzenie do funkcji JSON w usłudze Azure SQL Database
Azure umożliwia bazy danych SQL, analizy i kwerend danych reprezentowanych w JavaScript Object Notation [(JSON)](https://www.json.org/) formatowania i eksportowanie danych relacyjnych jako tekst w formacie JSON. Dostępne w usłudze Azure SQL Database są następujące scenariusze JSON:
- [Formatowanie danych relacyjnych w formacie JSON](#formatting-relational-data-in-json-format) przy użyciu `FOR JSON` klauzuli.
- [Praca z danymi w formacie JSON](#working-with-json-data)
- [Wykonywanie zapytania o dane JSON](#querying-json-data) przy użyciu funkcji skalarnych w formacie JSON.
- [Przekształcania JSON w formacie tabelarycznym](#transforming-json-into-tabular-format) przy użyciu `OPENJSON` funkcji.

## <a name="formatting-relational-data-in-json-format"></a>Formatowanie danych relacyjnych w formacie JSON
Jeśli masz usługę sieci web, że formatowanie pobiera dane z bazy danych w warstwie i udostępnia odpowiedź w formacie JSON lub platformy JavaScript po stronie klienta lub bibliotek, które akceptują dane w formacie JSON, możesz sformatować zawartości bazy danych jako dane JSON bezpośrednio w zapytaniu SQL. Nie jest już konieczne pisanie kodu aplikacji, która formatuje wyniki z usługi Azure SQL Database jako plik JSON lub zawierają niektóre biblioteki serializacji JSON do przekonwertowania wyników zapytania tabelaryczne, a następnie wykonywania serializacji obiektów do formatu JSON. Zamiast tego można użyć w klauzuli FOR JSON wyniki zapytania SQL w formacie JSON w usłudze Azure SQL Database i użyć go bezpośrednio w aplikacji.

W poniższym przykładzie wiersze z tabeli Sales.Customer są w formacie JSON za pomocą klauzuli FOR JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Klauzula FOR JSON PATH formatuje wyniki zapytania jako tekst w formacie JSON. Nazwy kolumn są używane jako klucze, podczas gdy wartości komórek są generowane jako wartości JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Zestaw wyników jest formatowana jako tablicę JSON, gdzie każdy wiersz jest formatowana jako oddzielny obiekt JSON.

ŚCIEŻKA wskazuje, że można dostosować format danych wyjściowych wyników JSON przy użyciu notacji z kropką w aliasów kolumn. Następujące zapytanie powoduje zmianę klucza "CustomerName" w formacie JSON dane wyjściowe i umieszcza numer telefonu i faksu w podobiekcie "Contact":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Dane wyjściowe to zapytanie wygląda następująco:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

W tym przykładzie firma Microsoft zwracany pojedynczy obiekt JSON zamiast tablicy, określając [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) opcji. Można użyć tej opcji, jeśli wiesz, że zwracasz pojedynczy obiekt wyniku zapytania.

Główne wartość w klauzuli FOR JSON jest, że dzięki temu można zwrócić złożonych hierarchiczne dane z bazy danych w formacie zagnieżdżonych obiektów JSON lub tablic. Poniższy przykład pokazuje, jak dołączyć wiersze z `Orders` tabeli, które należą do `Customer` jako zagnieżdżonych tablic z `Orders`:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Zamiast wysyłać oddzielić kwerendy w celu uzyskania danych klienta, a następnie Fetch lista zamówień powiązanych możesz uzyskać wszystkie niezbędne dane za pomocą jednego zapytania, jak pokazano w poniższym przykładzie danych wyjściowych:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Praca z danymi w formacie JSON
Jeśli masz ściśle ze strukturą danych, w przypadku złożonych obiektów podrzędnych, tablic lub dane hierarchiczne lub z czasem ewoluować struktury danych użytkownika, w formacie JSON może pomóc do reprezentowania Każda struktura danych złożonych.

JSON jest w formacie tekstowym, które mogą być używane jak innego typu ciągu w usłudze Azure SQL Database. Możesz wysyłać lub przechowywać dane JSON jako standardowa NVARCHAR:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Dane JSON użytych w tym przykładzie jest reprezentowane za pomocą typu NVARCHAR(MAX). JSON mogą być wstawiane do tej tabeli lub dostarczana jako argument procedury składowanej przy użyciu standardowej składni języka Transact-SQL, jak pokazano w poniższym przykładzie:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Język klienta lub biblioteki, które również sprawdzi współdziałanie z danymi na ciąg w usłudze Azure SQL Database z danych JSON. JSON mogą być przechowywane w dowolnej tabeli, która obsługuje typ NVARCHAR, takich jak zoptymalizowane pod kątem pamięci tabeli lub tabeli wersjonowanego przez System. JSON nie wprowadza żadnych ograniczeń w kodzie po stronie klienta lub w warstwie bazy danych.

## <a name="querying-json-data"></a>Wykonywanie zapytania o dane JSON
Jeśli masz dane w formacie JSON przechowywane w tabelach Azure SQL, funkcje JSON pozwalają używać tych danych w każdym zapytaniu SQL.

Funkcje JSON, które są dostępne w Azure SQL database umożliwiają traktować dane w formacie JSON jako inny typ danych SQL. Możesz łatwo Wyodrębnij wartości z pliku tekstowego w formacie JSON i używać danych JSON w każdym zapytaniu:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funkcja JSON_VALUE wyodrębniania wartości w tekście JSON przechowywane w tej kolumnie danych. Ta funkcja używa ścieżki notacji języka JavaScript można odwołać się do wartości w tekście JSON do wyodrębnienia. Wyodrębnione wartości można w dowolnej części zapytania SQL.

Funkcja JSON_QUERY jest podobny do JSON_VALUE. W odróżnieniu od JSON_VALUE ta funkcja wyodrębnia obiektu złożonego podrzędnych, np. tablic lub obiektów, które są umieszczane w tekście JSON.

Funkcja JSON_MODIFY pozwala określić ścieżkę do wartości w tekście JSON, który powinien zostać zaktualizowany, a także nową wartość, która zastąpi starą. Dzięki temu można łatwo zaktualizować tekstu JSON, bez ponownej analizy całą strukturę.

Ponieważ JSON jest przechowywany w postaci zwykłego tekstu, nie ma żadnej gwarancji, że wartości przechowywane w kolumnach tekstu są poprawnie sformatowane. Można sprawdzić, czy tekst w formacie JSON przechowywane jest poprawnie sformatowany przy użyciu standardowych ograniczeń check usługi Azure SQL Database i funkcja ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Jeśli tekst wejściowy jest poprawnie sformatowana JSON, funkcja ISJSON zwraca wartość 1. Na każdym wstawienie lub aktualizacja kolumny JSON to ograniczenie zweryfikuje, że nowe wartości tekstowej nie jest nieprawidłowo sformatowany kod JSON.

## <a name="transforming-json-into-tabular-format"></a>Przekształcania JSON w formacie tabelarycznym
Usługa Azure SQL Database umożliwia także przekształcić kolekcje JSON w danych JSON format i obciążenia lub zapytań tabelarycznych.

OPENJSON jest funkcją wartość tabeli, która analizuje tekst w formacie JSON, lokalizuje Tablica obiektów JSON, wykonuje iterację przez elementy tablicy i zwraca jeden wiersz w wyniku dane wyjściowe dla każdego elementu tablicy.

![Tabelaryczne JSON](./media/sql-database-json-features/image_2.png)

W powyższym przykładzie można określić lokalizacji tablicę JSON, który powinien zostać otwarty (w $. Ścieżka zamówienia), które kolumny ma zostać zwrócony jako wynik i gdzie można znaleźć wartości JSON, które zostaną zwrócone jako komórki.

Firma Microsoft może zmienić tablicę JSON w @orders zmienną do zestawu wierszy, analizowanie tego zestawu wyników lub wstawianie wierszy do tabeli standardowe:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
Kolekcji zamówień formatowane jako tablica JSON, a podana jako parametr do procedury składowanej można przeanalizować i wstawione do tabeli zamówienia.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak zintegrować JSON w aplikacji, zapoznaj się z tymi zasobami:

* [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [Dokumentacja MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Wideo Channel 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Aby dowiedzieć się więcej o różnych scenariuszy integracji JSON w aplikacji, zobacz pokazy, w tym [wideo Channel 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) lub znaleźć scenariusz, który pasuje do danego przypadku użycia w [wpisy na blogu JSON](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

