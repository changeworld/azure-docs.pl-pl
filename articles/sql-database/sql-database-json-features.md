---
title: Funkcje JSON bazy danych SQL platformy Azure | Dokumentacja firmy Microsoft
description: Baza danych SQL Azure umożliwia analizy, zapytań i formatowanie danych w notacji języka JavaScript Object Notation (JSON).
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.date: 04/01/2018
ms.author: jovanpop
ms.topic: conceptual
ms.openlocfilehash: 687986d108a15973c19ff06f5ca98a468439f30d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649003"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Wprowadzenie do korzystania z funkcji JSON w bazie danych SQL Azure
Azure umożliwia bazy danych SQL, analizy i zapytania dane reprezentowane w notacji obiektu JavaScript [(JSON)](http://www.json.org/) sformatowanie i wyeksportuj dane relacyjne jako tekstu JSON.

JSON to format popularnych danych używane do wymiany danych w nowoczesnych witryn sieci web i aplikacji dla urządzeń przenośnych. JSON jest również używane do przechowywania częściowo ustrukturyzowanych danych w plikach dziennika lub w bazach danych NoSQL, takie jak [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/documentdb/). Wiele usług sieci web REST wyniki zwracane formatować jako tekstu JSON lub akceptować dane w formacie JSON. Azure większość usług takich jak [usługi Azure Search](https://azure.microsoft.com/services/search/), [usługi Azure Storage](https://azure.microsoft.com/services/storage/), i [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/documentdb/) mają punkty końcowe REST, które zwracają lub korzystać z formatu JSON.

Baza danych SQL Azure pozwala łatwo pracować z danych JSON i integrowania bazy danych z usługami nowoczesnych.

## <a name="overview"></a>Przegląd
Baza danych SQL Azure udostępnia następujące funkcje do pracy z danymi JSON:

![Funkcje JSON](./media/sql-database-json-features/image_1.png)

Jeśli masz tekst JSON można wyodrębnić dane z JSON lub sprawdzić, czy JSON jest prawidłowo sformatowane przy użyciu wbudowanych funkcji [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), i [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) funkcja pozwala zaktualizować wartość w tekście JSON. Więcej zaawansowane zapytania i analizy, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funkcji można przekształcić na tablicę obiektów JSON do zestawu wierszy. Każde zapytanie SQL mogą być wykonywane na zestaw wyników zwrócony. Na koniec istnieje [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) klauzuli, które umożliwia formatowanie danych przechowywane w tabelach relacyjne jako tekstu JSON.

## <a name="formatting-relational-data-in-json-format"></a>Formatowanie danych relacyjnych w formacie JSON
Jeśli masz usługi sieci web, że format pobiera dane z bazy danych warstwy i udostępnia odpowiedź w formacie JSON lub struktury języka JavaScript po stronie klienta lub bibliotek, które akceptują dane w formacie JSON zawartości bazy danych można sformatować jako JSON bezpośrednio w zapytaniu SQL. Możesz już trzeba napisać kod aplikacji, które formatuje wyników z bazy danych SQL Azure w formacie JSON lub dodać niektóre Biblioteka serializacji JSON do przekonwertowania wyników zapytań tabelarycznych, a następnie serializacji obiektów do formatu JSON. Zamiast tego można w klauzuli FOR JSON wyników zapytania SQL w formacie JSON w usłudze Azure SQL Database i używać go bezpośrednio w aplikacji.

W poniższym przykładzie wierszy z tabeli Sales.Customer formacie JSON przy użyciu klauzuli FOR JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Klauzula FOR JSON PATH formatuje wyniki zapytania jako tekst w formacie JSON. Nazwy kolumn są używane jako klucze, gdy wartości komórek są generowane jako wartości JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Zestaw wyników jest formatowana jako tablica JSON, w którym każdy wiersz jest formatowana jako oddzielny obiekt JSON.

ŚCIEŻKA wskazuje, że format wyjściowy wyniku JSON można dostosować za pomocą kropkowego w aliasów kolumn. Następujące zapytanie powoduje zmianę klucza "CustomerName" w formacie JSON dane wyjściowe i umieszcza numer telefonu i faksu w obiekcie podrzędne "Skontaktuj się z":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Dane wyjściowe tego zapytania wygląda następująco:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

W tym przykładzie firma Microsoft zwracany pojedynczy obiekt JSON zamiast tablicy, określając [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) opcji. Można użyć tej opcji, jeśli wiadomo, że zwróconego pojedynczy obiekt wyniku zapytania.

Głównym wartość w klauzuli FOR JSON jest, że umożliwia zwracanie złożonych hierarchiczne dane z bazy danych w formacie zagnieżdżonych obiektów JSON lub tablicą. Poniższy przykład przedstawia sposób Uwzględnij zamówienia, które należą do klienta jako tablica zagnieżdżona zleceń:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Zamiast wysyłać oddzielne kwerendy w celu uzyskania danych klienta, a następnie pobrać listę zleceń powiązane, można znaleźć wszystkie niezbędne dane z pojedynczego zapytania, jak pokazano w poniższych przykładowych danych wyjściowych:

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
Złożone obiekty podrzędne, tablic lub danymi hierarchicznymi braku ściśle strukturalnych danych lub rozwijać struktury danych użytkownika w czasie, w formacie JSON może pomóc do reprezentowania żadnej struktury danych złożonych.

JSON to tekstowy format, którego można używać innych typów ciągu w bazie danych SQL Azure. Możesz wysłać lub przechowywać dane JSON jako standardowa NVARCHAR:

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

Dane JSON, w tym przykładzie odpowiada za pomocą typu NVARCHAR(MAX). JSON można wstawiać do tej tabeli lub podana jako wartość argumentu procedury składowanej przy użyciu standardowej składni języka Transact-SQL, jak pokazano w poniższym przykładzie:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Język klienta lub biblioteki, która współdziała z ciągu danych w bazie danych SQL Azure będzie również współpracować z danych JSON. JSON mogą być przechowywane w dowolnej tabeli, która obsługuje typ NVARCHAR, takich jak zoptymalizowanych pod kątem pamięci tabeli lub tabeli z systemową kontrolą wersji. JSON nie zapewnia żadnych ograniczeń w kodzie po stronie klienta lub w warstwie bazy danych.

## <a name="querying-json-data"></a>Wykonywanie zapytania na danych JSON
Jeśli masz dane w formacie JSON przechowywane w tabelach Azure SQL, JSON funkcje umożliwiają używanie tych danych w dowolnym zapytania SQL.

Funkcje JSON, które są dostępne w let bazy danych Azure SQL Traktuj dane w formacie JSON jako inny typ danych SQL. Możesz łatwo Wyodrębnij wartości w tekście JSON i użyć danych JSON w dowolnej kwerendy:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funkcja JSON_VALUE wyodrębnianie wartości w tekście JSON przechowywanych w kolumnie danych. Funkcja ta używa ścieżki notacji języka JavaScript odwoływać się do wartości w tekście JSON do wyodrębnienia. Wyodrębnionego wartość może być używana w dowolnej części zapytania SQL.

Funkcja JSON_QUERY jest podobny do JSON_VALUE. W odróżnieniu od JSON_VALUE ta funkcja wyodrębnia złożonych obiektu podrzędnego, takiego jak tablice lub obiektów, które są umieszczane w tekście JSON.

Funkcja JSON_MODIFY pozwala określić ścieżkę do wartości w tekście JSON, który powinien zostać zaktualizowany, a także nową wartość, która zastąpi stare hasło. Dzięki temu można łatwo aktualizować tekst JSON bez ponownej analizy całego struktury.

Ponieważ JSON są przechowywane w postaci zwykłego tekstu, nie ma żadnych gwarancji, że wartościami przechowywanymi w kolumnach tekstu są prawidłowo sformatowane. Aby sprawdzić, czy tekst w formacie JSON przechowywane jest poprawnie sformatowana przy użyciu standardowych ograniczenia sprawdzania bazy danych SQL Azure i funkcja ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Jeśli tekst wejściowy jest poprawnie sformatowana JSON, funkcja ISJSON zwraca wartość 1. Na każdym insert lub update kolumny JSON to ograniczenie zweryfikuje, że nowe wartości tekstowej nie jest nieprawidłowo sformatowany kod JSON.

## <a name="transforming-json-into-tabular-format"></a>Przekształcania JSON w formacie tabelarycznym
Baza danych SQL Azure umożliwia również przekształcenie kolekcji JSON do danych JSON format i obciążenia lub zapytań tabelarycznych.

OPENJSON to funkcja wartościami przechowywanymi w tabeli, która analizuje tekst JSON, lokalizuje jest Tablica obiektów JSON iterację elementów tablicy i zwraca jeden wiersz w wyniku danych wyjściowych dla każdego elementu tablicy.

![Tabelaryczne JSON](./media/sql-database-json-features/image_2.png)

W powyższym przykładzie można określić gdzie umieścić tablicy JSON, który powinien zostać otwarty (w $. Ścieżka zamówienia), które kolumny ma zostać zwrócony jako wynik i gdzie można znaleźć wartości JSON, które zostaną zwrócone jako komórki.

Firma Microsoft można przekształcać tablica JSON w @orders zmienną do zestawu wierszy, analizować ten zestaw wyników lub wstawić wierszy do tabeli standardowe:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
Kolekcja zamówień formatowane jako tablica JSON i podać jako parametr do procedury składowanej można analizować i wstawione do tabeli zamówienia.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak zintegrować JSON aplikacji, zapoznaj się z tymi zasobami:

* [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
* [Dokumentacji MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Channel 9 wideo](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Informacje na temat różnych scenariuszy integracji JSON do aplikacji, zobacz pokazy w tym [wideo z witryny Channel 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) lub znaleźć scenariusz, który jest zgodny z zastosowaniem w [ogłoszeń blogu JSON](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

