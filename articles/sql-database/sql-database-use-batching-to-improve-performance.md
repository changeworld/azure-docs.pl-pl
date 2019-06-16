---
title: Jak zwiększyć wydajność aplikacji usługi Azure SQL Database za pomocą adapterów przetwarzania wsadowego
description: W temacie przedstawiono dowód, że przetwarzanie wsadowe operacji bazy danych znacznie zwiększa szybkość i skalowalność aplikacji usługi Azure SQL Database. Mimo że techniki te przetwarzania wsadowego działać dla dowolnej bazy danych programu SQL Server, artykuł koncentruje się na platformie Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: e76b5ecd3d6401c317f6500ec376fc25d3fa55b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331132"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Jak zwiększyć wydajność aplikacji bazy danych SQL za pomocą adapterów przetwarzania wsadowego

Przetwarzanie wsadowe operacji do usługi Azure SQL Database w znacznie zwiększa wydajność i skalowalność aplikacji. Aby zrozumieć korzyści, w pierwszej części tego artykułu opisano niektóre przykładowe wyniki testów są porównywane wsadowej i sekwencyjny żądań do usługi SQL Database. W dalszej części artykułu pokazuje techniki, scenariusze i zagadnienia pomogą pomyślnie partie w aplikacjach systemu Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Dlaczego jest przetwarzanie wsadowe ważne dla bazy danych SQL

Przetwarzanie wsadowe wywołań usługi zdalnej jest dobrze znanych strategii zwiększenia wydajności i skalowalności. Są to stałe koszty przetwarzania do interakcji z usługą zdalną, takie jak serializacji, transfer sieciowy i deserializacji. Pakowanie wielu oddzielnych transakcji w jednej partii minimalizuje te koszty.

W tym dokumencie chcemy sprawdzić różne bazy danych SQL, przetwarzanie wsadowe omówione strategie i scenariusze. Mimo że strategie te są również ważne w przypadku aplikacji lokalnych, korzystających z programu SQL Server, jest kilka powodów wyróżnianie korzystanie z dzielenia na partie dla bazy danych SQL:

* Jest potencjalnie większe opóźnienia sieci podczas uzyskiwania dostępu do bazy danych SQL, szczególnie jeśli uzyskują dostęp do bazy danych SQL z poza tym samym centrum danych Microsoft Azure.
* Wielodostępne właściwości bazy danych SQL oznacza, że wydajność odpowiada warstwy dostępu do danych w ogólnej skalowalności bazy danych. Bazy danych SQL musi zapobiec monopolizowaniu przez zasobów bazy danych szkodę innych dzierżaw dowolnego pojedynczego dzierżawy/użytkownika. W odpowiedzi na użycie przekraczające wstępnie zdefiniowanych limitów przydziału bazy danych SQL można zmniejszyć przepływności lub reagować po zastosowaniu ograniczania wyjątków. Korzyści, takich jak przetwarzanie wsadowe, umożliwiają kontynuować pracę w bazie danych SQL przed osiągnięciem limitów. 
* Przetwarzanie wsadowe jest również obowiązujące dla architektur korzystających z wielu baz danych (fragmentowanie). Wydajność interakcji w taki sposób, w którym każda jednostka bazy danych nadal jest kluczowym czynnikiem w swojej ogólnej skalowalności. 

Jest jedną z korzyści z używania bazy danych SQL, że nie trzeba zarządzać serwerami, które hostują bazy danych. Jednak ta zarządzana infrastruktura również oznacza, że inaczej myśleć o optymalizacji bazy danych. Nie jest już zajrzeć do poprawy infrastruktury sprzętu lub sieci bazy danych. Microsoft Azure kontroluje tych środowisk. Główny obszar, który można kontrolować, jest sposób interakcji aplikacji z usługą SQL Database. Przetwarzanie wsadowe jest jednym z tych optymalizacjach. 

Pierwsza część papieru sprawdza różnych technik przetwarzania wsadowego dla aplikacji platformy .NET, które używają bazy danych SQL. Ostatnie dwie sekcje obejmują, scenariuszy i wytyczne dotyczące przetwarzania wsadowego.

## <a name="batching-strategies"></a>Strategii dzielenia na partie

### <a name="note-about-timing-results-in-this-article"></a>Należy zwrócić uwagę na wyniki chronometrażu w tym artykule

> [!NOTE]
> Wyniki nie są testy porównawcze, ale są przeznaczone do wyświetlenia **względnej wydajności**. Czasy są oparte na średniej liczbie co najmniej 10 przebiegów testów. Operacje są operacje wstawiania do pustej tabeli. Te testy były mierzonego pre-V12, a ich nie muszą odpowiadać na przepływność, które mogą wystąpić w wersji V12 bazą danych, używając nowego [warstwy usługi jednostki DTU](sql-database-service-tiers-dtu.md) lub [warstwy usługi (rdzeń wirtualny)](sql-database-service-tiers-vcore.md). Względnych zalet technika przetwarzania wsadowego powinny być podobne.

### <a name="transactions"></a>Transakcje

Wydaje się dziwna, aby rozpocząć Przegląd dzielenia na partie od Omawiając transakcji. Ale stosowanie transakcji po stronie klienta ma delikatny przetwarzania wsadowego efekt po stronie serwera, który zapewnia lepszą wydajność. I transakcji można dodać za pomocą tylko kilku wierszy kodu, dzięki czemu zapewniają szybki sposób, aby zwiększyć wydajność operacji sekwencyjnego.

Należy wziąć pod uwagę następujące C# kod, który zawiera sekwencję INSERT i operacje aktualizacji w prostej tabeli.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
Poniższy kod ADO.NET sekwencyjnie wykonuje te operacje.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

Najlepszym sposobem, aby zoptymalizować ten kod jest zaimplementowanie pewnego rodzaju po stronie klienta przetwarzanie wsadowe tych wywołań. Ale prosty sposób zwiększyć wydajność ten kod po prostu opakowując sekwencję wywołań w ramach transakcji. Oto ten sam kod, który używa transakcji.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

Transakcje są rzeczywiście używane w obu tych przykładów. W pierwszym przykładzie poszczególnych wywołań jest transakcji niejawnej. W drugim przykładzie transakcji jawnej opakowuje wszystkich połączeń. Na dokumentację [dziennika transakcji zapisu z wyprzedzeniem](https://msdn.microsoft.com/library/ms186259.aspx), rekordy dziennika jest opróżniany do dysku, gdy zatwierdzenia transakcji. Tak, tym więcej wywołań w ramach transakcji zapisu w dzienniku transakcji można opóźnienie, dopóki nie transakcja została zatwierdzona. W efekcie jest włączane, przetwarzanie wsadowe do zapisu do dziennika transakcji serwera.

W poniższej tabeli przedstawiono niektóre ad-hoc wyniki testów. Testy wykonywane tego samego sekwencyjnego wstawiania i bez transakcji. Dla perspektywy więcej pierwszy zestaw testów został uruchomiony zdalnie z laptopa do bazy danych w systemie Microsoft Azure. Drugi zestaw testów został uruchomiony z usługą w chmurze i bazy danych, zarówno znajdowały się w obrębie tego samego centrum danych Microsoft Azure (zachodnie stany USA). W poniższej tabeli przedstawiono czas trwania w milisekundach sekwencyjnego wstawiania i bez transakcji.

**W środowisku lokalnym na platformie Azure**:

| Operacje | Brak transakcji (ms) | W transakcji (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure – Azure (tym samym centrum danych)** :

| Operacje | Brak transakcji (ms) | W transakcji (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach chronometrażu w tym artykule](#note-about-timing-results-in-this-article).

Na podstawie poprzednich wyników testów, zawijanie jednej operacji w transakcji obniża wydajność. Ale miarę zwiększania się liczby operacji w pojedynczą transakcję, poprawę wydajności staje się bardziej oznaczone. Różnicę w wydajności jest lepiej widoczne także w przypadku, gdy wszystkie operacje są wykonywane w ramach centrum danych Microsoft Azure. Większe opóźnienia wykorzystania bazy danych SQL z poza centrum danych Microsoft Azure overshadows przyrost wydajności użycia transakcji.

Mimo że stosowanie transakcji może zwiększyć wydajność, w dalszym ciągu [obserwować najlepsze rozwiązania dotyczące połączenia i transakcje](https://msdn.microsoft.com/library/ms187484.aspx). Zachowanie transakcji możliwie krótkie, a następnie zamknij połączenie z bazą danych, po zakończeniu pracy. Za pomocą instrukcji w poprzednim przykładzie gwarantuje, że połączenie jest zamknięte po zakończeniu blok kolejnych kodu.

Poprzedni przykład pokazuje, że można dodawać transakcji lokalnej do jakiegokolwiek kodu ADO.NET, z dwoma wierszami. Transakcje oferują szybki sposób, aby poprawić wydajność kodu, który sprawia, że sekwencyjnego wstawiania, aktualizowania i usuwania działań. Jednakże aby zapewnić lepszą wydajność, należy rozważyć zmianę kodu dostosowaną skorzystaj z zalet przetwarzanie wsadowe po stronie klienta, takie jak parametry z wartościami przechowywanymi w tabeli.

Aby uzyskać więcej informacji na temat transakcji w ADO.NET, zobacz [lokalnej transakcji w ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parametry z wartościami przechowywanymi w tabeli

Parametry z wartościami przechowywanymi w tabeli obsługuje typach tabel zdefiniowanych przez użytkownika jako parametry w instrukcji Transact-SQL, procedury składowane i funkcje. Ta technika przetwarzania wsadowego po stronie klienta umożliwia wysłanie wielu wierszy danych w obrębie parametr z wartościami przechowywanymi w tabeli. Aby użyć parametrów z wartościami przechowywanymi w tabeli, należy najpierw zdefiniować typ tabeli. Poniższa instrukcja języka Transact-SQL umożliwia utworzenie tabeli o nazwie **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

W kodzie, Utwórz **DataTable** przy użyciu dokładnie tych samych nazw i typy typ tabeli. Przekazać ten **DataTable** w parametrze zapytania tekstowego lub procedury składowanej wywołania. Tej techniki można znaleźć w poniższym przykładzie:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

W poprzednim przykładzie **SqlCommand** obiektu wstawia wierszy z parametru z wartościami przechowywanymi w tabeli  **\@TestTvp**. Utworzony wcześniej **DataTable** obiekt jest przypisany do tego parametru z **SqlCommand.Parameters.Add** metody. Przetwarzanie wsadowe operacji wstawienia w jednym wywołaniu znacznie zwiększa wydajność przez sekwencyjnego wstawiania.

Aby poprawić dalsze w poprzednim przykładzie, należy użyć procedury składowanej zamiast polecenia oparte na tekście. Następujące polecenie języka Transact-SQL tworzy procedurę składowaną, która przyjmuje **SimpleTestTableType** parametr z wartościami przechowywanymi w tabeli.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows] 
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num) 
SELECT mytext, num FROM @TestTvp
END
GO
```

Następnie zmień **SqlCommand** obiektu deklaracji w poprzednim przykładzie kodu do następującego.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

W większości przypadków parametry z wartościami przechowywanymi w tabeli ma równoważną lub lepszą wydajność niż innych technik przetwarzania wsadowego. Parametry z wartościami przechowywanymi w tabeli są preferowane, często w przypadku, ponieważ są one bardziej elastyczny niż inne opcje. Na przykład innych technik, takich jak kopiowanie zbiorcze SQL, umożliwić wstawianie nowych wierszy. Ale za pomocą parametrów z wartościami przechowywanymi w tabeli można użyć logiki w procedurze składowanej, aby określić, które wiersze są aktualizacje i służą do wstawia. Typ tabeli można zmodyfikować w taki sposób, aby zawierała kolumnę "Operacja", która wskazuje, czy określony wiersz powinien zostać wstawiony, zaktualizował lub usunął.

W poniższej tabeli przedstawiono wyniki testów ad hoc do użytku z wartościami przechowywanymi w tabeli parametrów w milisekundach.

| Operacje | W środowisku lokalnym na platformie Azure (ms) | Tym samym centrum danych platformy Azure (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10 000 |23830 |3586 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach chronometrażu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

Przyrost wydajności z dzielenia na partie jest oczywista. W poprzednim teście sekwencyjnych, 1000 wykonanie operacji zajęło 129 sekund poza centrum danych i 21 sekund z w obrębie centrum danych. Jednak parametry z wartościami przechowywanymi w tabeli na 1000 operacji wykorzystanie tylko 2,6 sekund poza centrum danych i 0,4 sekund w centrum danych.

Aby uzyskać więcej informacji na temat parametrów z wartościami przechowywanymi w tabeli, zobacz [parametry Table-Valued](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Kopiowanie masowe programu SQL

Kopiowanie masowe programu SQL jest inny sposób, aby wstawić dużych ilości danych do docelowej bazy danych. Aplikacje .NET mogą używać **SqlBulkCopy** klasy, aby wykonywać zbiorcze operacje wstawiania. **SqlBulkCopy** jest podobny do narzędzia wiersza polecenia, funkcja **Bcp.exe**, albo instrukcji języka Transact-SQL **BULK INSERT**. Poniższy przykład kodu pokazuje, jak wykonać kopiowania masowego wierszy w źródle **DataTable**, tabeli MyTable do tabeli docelowej w programie SQL Server.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Istnieją przypadki, gdzie kopiowania masowego jest preferowana względem parametry z wartościami przechowywanymi w tabeli. Zobacz Tabela porównawcza z wartościami przechowywanymi w tabeli parametrów i operacji WSTAWIANIA ZBIORCZEGO w artykule [parametry Table-Valued](https://msdn.microsoft.com/library/bb510489.aspx).

Następujące wyniki testów ad-hoc Pokaż wydajność przetwarzania wsadowego za pomocą **SqlBulkCopy** w milisekundach.

| Operacje | W środowisku lokalnym na platformie Azure (ms) | Tym samym centrum danych platformy Azure (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10 000 |21605 |2737 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach chronometrażu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

W mniejszych rozmiarach usługi batch, użyj parametrów wartościami przechowywanymi w tabeli pokonał usługę **SqlBulkCopy** klasy. Jednak **SqlBulkCopy** wykonywane 12-31% szybsze niż parametrów z wartościami przechowywanymi w tabeli dla testów 1000 i za 10 000 wierszy. Parametry z wartościami przechowywanymi w tabeli, takich jak **SqlBulkCopy** to dobra opcja w przypadku operacji wsadowej operacji wstawiania, szczególnie w porównaniu do wykonywania operacji-przetwarzanej.

Aby uzyskać więcej informacji na temat kopiowania masowego w ADO.NET, zobacz [operacje kopiowania masowego w programie SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Instrukcje sparametryzowane WSTAWIĆ wiele wierszy

Jeden alternatywą dla małych partiach jest do konstruowania dużych sparametryzowanych instrukcji INSERT, który wstawia wiele wierszy. W poniższym przykładzie kodu pokazano tej techniki.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

W tym przykładzie jest przeznaczona do wyświetlenia podstawowe pojęcia. Zrealizować bardziej realistyczny scenariusz będzie pętli wymaganych jednostek do konstruowania ciągu zapytania oraz parametry polecenia jednocześnie. Są ograniczone do sumy parametrów zapytania 2100, co ogranicza łączną liczbę wierszy, które mogą być przetwarzane w ten sposób.

Następujące wyniki testów ad-hoc Pokaż wydajności tego typu instrukcji insert w milisekundach.

| Operacje | Parametry z wartościami przechowywanymi w tabeli (ms) | Wstaw pojedynczej instrukcji (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach chronometrażu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

Takie podejście może być nieznacznie szybsze w partii, które są mniej niż 100 wierszy. Mimo że poprawy jest mała, ta technika jest inne rozwiązanie, które mogą działać dobrze w danym scenariuszu określonej aplikacji.

### <a name="dataadapter"></a>DataAdapter

**DataAdapter** klas umożliwia modyfikowanie **DataSet** obiektu, a następnie prześlij zmiany jako Operacje INSERT, UPDATE i DELETE. Jeśli używasz **DataAdapter** w ten sposób jest należy pamiętać, że oddzielne wywołań dla każdej operacji distinct. Aby zwiększyć wydajność, należy użyć **UpdateBatchSize** liczbę operacji, które powinny być partii w danym momencie. Aby uzyskać więcej informacji, zobacz [wykonywanie wsadowe operacji za pomocą elementów DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Platformy Entity framework

Entity Framework nie obsługuje obecnie dzielenia na partie. Różnych deweloperów w społeczności podjęto próbę pokaz rozwiązania, takie jak zastąpienie **SaveChanges** metody. Jednak te rozwiązania są zazwyczaj złożone i dostosowane do aplikacji i modelu danych. Projekt codeplex Entity Framework obecnie ma stronę dyskusji na żądanie tej funkcji. Aby wyświetlić tej dyskusji, zobacz [notatek ze spotkań Projekt - 2 sierpień 2012](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML

Aby informacje były kompletne uważamy, że ważne jest, aby porozmawiać na temat XML jako strategii przetwarzania wsadowego. Jednak użycie XML ma nie zalety w porównaniu z innymi metodami i kilka wady. To podejście jest podobne do parametrów z wartościami przechowywanymi w tabeli, ale plik XML lub ciąg jest przekazywany do procedury składowanej zamiast tabeli zdefiniowane przez użytkownika. Procedura składowana analizuje poleceń w procedurze składowanej.

Istnieje kilka wady tego podejścia:

* Praca z XML może być uciążliwe i podatne.
* Analizowanie kodu XML w bazie danych może być użycie Procesora CPU.
* W większości przypadków ta metoda jest mniejsza niż parametrów z wartościami przechowywanymi w tabeli.

Z tego względu nie jest zalecane użycie XML dla zapytań wsadowych.

## <a name="batching-considerations"></a>Zagadnienia dotyczące dzielenia na partie

Poniższe sekcje zawierają więcej wskazówek na korzystanie z dzielenia na partie w aplikacjach baz danych SQL.

### <a name="tradeoffs"></a>Wady i zalety

W zależności od architektury przetwarzanie wsadowe może obejmować zależność między wydajnością i zwiększa odporność. Na przykład Rozważmy scenariusz, w którym Twoja rola nieoczekiwanie ulegnie awarii. Jeśli utracisz jeden wiersz danych, wpływ jest mniejszy niż wpływ utraty dużych partii nieprzesłane wierszy. Istnieje większe ryzyko, gdy bufora wierszy przed wysłaniem ich do bazy danych w określonym przedziale czasu.

Ze względu na to kosztem ocenić typ operacji tego wsadowej. Batch bardziej agresywne (większych partii i dłuższego czasu systemu windows) z danymi, które jest mniej istotny.

### <a name="batch-size"></a>Rozmiar partii

W naszym testy była zwykle nie posiada zalet dzielenia dużych partii na mniejsze części. W rzeczywistości często tę część pola wyniku wolniej niż przesyłanie pojedynczego dużych partii. Na przykład Rozważmy scenariusz, w którym ma zostać wstawiony 1000 wierszy. W poniższej tabeli przedstawiono, jak długo trwa Użyj parametrów z wartościami przechowywanymi w tabeli, aby wstawić 1000 wierszy w przypadku podzielony na mniejsze segmenty.

| Rozmiar partii | Liczba iteracji | Parametry z wartościami przechowywanymi w tabeli (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach chronometrażu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

Aby zobaczyć, że najlepszą wydajność za 1000 wierszy jest się przesłać je wszystkie na raz. W innych testach (nie pokazane tutaj) było małe bardziej wydajne przerwać partii wierszy 10000 dwie partie 5000. Ale schematu tabeli dla tych testów jest stosunkowo proste, więc należy wykonywać testy w szczegółowe dane i usługi batch rozmiarów, aby sprawdzić te wyniki.

Innym czynnikiem do przeanalizowania jest, jeśli łączna liczba partii staje się zbyt duży, bazy danych SQL może ograniczać i odmawiają zatwierdzić partii. Aby uzyskać najlepsze wyniki testu określonego scenariusza, aby ustalić, czy rozmiar partii idealnym rozwiązaniem. Rozmiar partii upewnij można skonfigurować w czasie wykonywania, aby umożliwić szybkie dopasowania na podstawie wydajności lub błędy.

Na koniec równoważyć rozmiar partii z ryzyka związanego z dzielenia na partie. Jeśli występują błędy przejściowe lub roli nie powiedzie się, należy rozważyć konsekwencje ponowieniem próby wykonania operacji lub utraty danych w partii.

### <a name="parallel-processing"></a>Przetwarzanie równoległe

Co zrobić, jeśli trwało podejście zmniejszenie rozmiaru partii, ale umożliwia wykonanie pracy przez wiele wątków? Ponownie Nasze testy wykazały, że kilka mniejszych wielowątkowe instancji najczęściej wykonywane co gorsza niż pojedyncza partia większe. Następujący test próbuje wstawić 1000 wierszy w co najmniej jedna partia równoległych. Ten test pokazuje, jak więcej partie jednoczesnych faktycznie obniżenie wydajności.

| Rozmiar partii [iteracje] | Dwoma wątkami (ms) | Cztery wątki (ms) | Sześć wątków (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach chronometrażu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

Istnieje kilka potencjalnych przyczyn pogorszenie wydajności ze względu na równoległości:

* Istnieje wiele wywołań sieci zamiast jednego.
* Wiele operacji względem pojedynczej tabeli może powodować rywalizację i blokowanie.
* Istnieją koszty ogólne związane z wielowątkowością.
* Koszt otwarcia wielu połączeń przewyższa korzyści przetwarzania równoległego.

Jeśli platformą docelową jest różnych tabelach lub baz danych, istnieje możliwość Zobacz wydajnościowe uzyskać za pomocą tej strategii. Fragmentowanie bazy danych lub Federacji byłoby scenariusz dotyczący tej metody. Dzielenie na fragmenty używa wielu baz danych i kieruje różnych danych do każdej bazy danych. Każda partia małych korzystał z inną bazą danych może być bardziej efektywne następnie wykonanie operacji w sposób równoległy. Przyrost wydajności nie jest jednak istotne wykorzystania jako podstawy do podjęcia decyzji w celu użycia fragmentowania bazy danych w rozwiązaniu.

W niektórych projektach równoległe wykonywanie mniejszych partiach może spowodować ulepszoną przepływność żądań w systemie pod obciążeniem. W tym przypadku mimo że jest to szybsza do przetworzenia pojedynczego większych partii przetwarzania wiele instancji w sposób równoległy może być bardziej wydajne.

Jeśli używasz przetwarzania równoległego, należy wziąć pod uwagę kontrolowanie maksymalną liczbę wątków roboczych. Mniejsza liczba może prowadzić do mniej rywalizacji i skrócony czas wykonywania. Ponadto należy wziąć pod uwagę dodatkowego obciążenia, która umieszcza się to do docelowej bazy danych, zarówno w transakcji i połączeń.

### <a name="related-performance-factors"></a>Czynniki dotyczące wydajności

Typowe wskazówki dotyczące wydajności bazy danych wpływa również na partie. Na przykład, Wstaw wydajność jest ograniczona w przypadku tabel, które mają duże klucza podstawowego lub wiele nieklastrowanych indeksów.

Parametry z wartościami przechowywanymi w tabeli, użyj procedury składowanej, możesz użyć polecenia **SET NOCOUNT ON** na początku procedury. Ta instrukcja pomija zwracana liczba dotyczy wierszy w procedurze. Jednak w naszym testy użytkowania **SET NOCOUNT ON** nie miało wpływu lub obniżenie wydajności. Procedury przechowywane testu był prosty za pomocą jednego **Wstaw** polecenie z wartościami przechowywanymi w tabeli parametru. Istnieje możliwość, bardziej złożonych procedur składowanych używającym tej instrukcji. Ale nie wolno zakładać, że dodanie **SET NOCOUNT ON** do swojej przechowywanej procedurze automatycznie zwiększa wydajność. Aby poznać efekt wywoływany, przetestować procedurę składowaną z lub bez **SET NOCOUNT ON** instrukcji.

## <a name="batching-scenarios"></a>Przetwarzanie wsadowe scenariuszy

Poniżej opisano sposób użycia parametrów z wartościami przechowywanymi w tabeli w trzech scenariuszy aplikacji. Pierwszy scenariusz pokazuje, jak buforowanie i przetwarzanie wsadowe mogą współdziałać ze sobą. Drugi scenariusz zwiększa wydajność, wykonywanie operacji wzorzec / szczegół za pomocą wywołania jednej procedury składowanej. Końcowe scenariusz pokazuje, jak używać parametrów z wartościami przechowywanymi w tabeli w operacji "UPSERT".

### <a name="buffering"></a>buforowanie

Mimo że istnieją pewne scenariusze, które są oczywiste Release candidate dla przetwarzania wsadowego, istnieje wiele scenariuszy, które możesz wykorzystać dzielenia na partie opóźnione przetwarzania. Opóźnione przetwarzania obsługuje także większe ryzyko, że dane zostaną utracone w przypadku nieoczekiwanej awarii. Należy zrozumieć ryzyko i należy rozważyć konsekwencje.

Rozważmy na przykład aplikacja sieci web, który śledzi historię nawigacji każdego użytkownika. Na każdym żądaniu strony aplikacji można utworzyć bazę danych wywołać, aby rejestrować wyświetlenie strony użytkownika. Ale można osiągnąć wyższą wydajność i skalowalność, buforowanie działania nawigacji użytkowników, a następnie wysyłając te dane do bazy danych w partiach. Możesz wyzwolić aktualizacja bazy danych, czas trwania i/lub rozmiar buforu. Reguły można na przykład określić, czy zadanie wsadowe powinny zostać przetworzone po 20 sekund lub gdy buforu osiągnie 1000 elementów.

Poniższy przykład kodu wykorzystuje [Reactive Extensions - Rx](https://msdn.microsoft.com/data/gg577609) na potrzeby przetwarzania buforowanego zdarzeń zgłaszanych przez klasy monitorowania. Wypełnia buforu lub w osiągnięciu limitu czasu, partii danych użytkownika są wysyłane do bazy danych z parametru z wartościami przechowywanymi w tabeli.

Następujące klasy NavHistoryData modeli szczegóły nawigacji użytkownika. Zawiera podstawowe informacje, takie jak identyfikator użytkownika, dostęp do adresu URL i czas dostępu.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

Klasa NavHistoryDataMonitor jest odpowiedzialny za buforowania danych nawigacji użytkownika w bazie danych. Zawiera on metodę RecordUserNavigationEntry, który odpowiada, tworząc **OnAdded** zdarzeń. Poniższy kod przedstawia logikę konstruktora, która używa Rx w celu utworzenia kolekcji zauważalne, na podstawie zdarzeń. Go następnie jest podłączone do tej kolekcji zauważalne przy użyciu metody buforu. Przeciążenie Określa, czy bufor powinny być przesyłane co 20 sekund lub 1000 wpisów.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Program obsługi konwertuje wszystkie buforowane elementy do typu z wartościami przechowywanymi w tabeli, a następnie przekazuje tego typu do procedury składowanej, która przetwarza zadania wsadowego. Poniższy kod pokazuje kompletną definicję zarówno NavHistoryDataEventArgs, jak i klasy NavHistoryDataMonitor.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

Program obsługi konwertuje wszystkie buforowane elementy do typu z wartościami przechowywanymi w tabeli, a następnie przekazuje tego typu do procedury składowanej, która przetwarza zadania wsadowego. Poniższy kod pokazuje kompletną definicję zarówno NavHistoryDataEventArgs, jak i klasy NavHistoryDataMonitor.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Aby użyć tej buforowanie klasy, aplikacja tworzy obiekt NavHistoryDataMonitor statyczne. Każdorazowo, użytkownik uzyskuje dostęp do strony, aplikacja wywołuje metodę NavHistoryDataMonitor.RecordUserNavigationEntry. Buforowanie logiki przechodzą do zajmie się wysłać te wpisy w bazie danych w partiach.

### <a name="master-detail"></a>Wzorzec szczegół

Parametry z wartościami przechowywanymi w tabeli są przydatne w przypadku prostych scenariuszy INSERT. Jednak może być trudniejsze do wstawiania partii, obejmujące więcej niż jedną tabelą. Scenariusz "wzorzec/szczegół" jest dobrym przykładem. W głównej tabeli przedstawiono podstawowej jednostki. Co najmniej jedną tabelę szczegółów przechowywać więcej danych dotyczących jednostki. W tym scenariuszu relacje klucza obcego wymuszają relacji szczegóły, aby unikatowe jednostki głównej. Należy wziąć pod uwagę uproszczoną wersję Tabela PurchaseOrder i jego skojarzonej tabeli OrderDetail. Języka Transact-SQL tworzy tabelę PurchaseOrder z czterech kolumn: OrderID OrderDate, CustomerID i stanu.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Każde zamówienie zawiera co najmniej jeden produkt. Te informacje są przechwytywane w tabeli PurchaseOrderDetail. Języka Transact-SQL tworzy tabelę PurchaseOrderDetail z pięciu kolumn: OrderID OrderDetailID, ProductID, UnitPrice i OrderQty.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
( [OrderID] ASC, [OrderDetailID] ASC ))
```

Z tabeli PurchaseOrder OrderID kolumny w tabeli PurchaseOrderDetail musi odwoływać się zamówienia. Poniższą definicję klucza obcego wymusza to ograniczenie.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Aby można było używać parametrów z wartościami przechowywanymi w tabeli, musi mieć jeden typ tabeli zdefiniowany przez użytkownika dla każdej tabeli docelowej.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE 
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Następnie zdefiniuj procedury przechowywanej, która akceptuje tabele te typy. Ta procedura umożliwia aplikacji lokalnie partii zestaw zamówień i szczegóły zamówienia w jednym wywołaniu. Języka Transact-SQL zawiera deklarację kompletną procedurą składowaną, w tym przykładzie zamówienia zakupu.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE ( 
SubmittedKey int, 
ActualKey int, 
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table   
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table, 
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

W tym przykładzie zdefiniowane lokalnie @IdentityLink tabeli przechowuje rzeczywiste wartości OrderID z nowo wstawione wiersze. Te identyfikatory kolejności różnią się od wartości OrderID tymczasowe w @orders i @details parametry z wartościami przechowywanymi w tabeli. Z tego powodu @IdentityLink tabeli następnie łączy wartości OrderID z @orders parametr rzeczywiste wartości OrderID dla nowych wierszy w tabeli PurchaseOrder. Po wykonaniu tego kroku @IdentityLink mogą ułatwić tabeli, wstawianie szczegóły zamówienia z OrderID rzeczywista, która spełnia ograniczenie klucza obcego.

Tę procedurę składowaną można z poziomu kodu lub z innych wywołań języka Transact-SQL. Zobacz sekcję parametrów z wartościami przechowywanymi w tabeli w tym dokumencie dla przykładu kodu. Języka Transact-SQL przedstawiono sposób wywoływania sp_InsertOrdersBatch.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders 
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Dzięki temu rozwiązaniu każdej partii do korzystania z zestawu wartości OrderID, które zaczynają się od 1. Te wartości tymczasowe OrderID opisywania relacji w zadaniu wsadowym, ale rzeczywiste wartości OrderID są określane w czasie operacji wstawiania. Można uruchomić te same instrukcje w poprzednim przykładzie wielokrotnie i wygenerować unikatowy zamówienia w bazie danych. Z tego powodu należy rozważyć dodanie więcej logikę kodu lub bazy danych, która zapobiega zduplikowane zamówienia, gdy za pomocą tego, przetwarzanie wsadowe techniki.

W tym przykładzie pokazano, że można zebrać jeszcze bardziej złożonych operacji bazy danych, takie jak operacje wzorzec / szczegół za pomocą parametrów z wartościami przechowywanymi w tabeli.

### <a name="upsert"></a>UPSERT

Inny scenariusz przetwarzania wsadowego obejmuje równocześnie aktualizuje istniejące wiersze i wstawianie nowych wierszy. Ta operacja jest czasami określane jako operację "UPSERT" (aktualizacja + insert). Zamiast oddzielnych wywołań do WSTAWIANIA i AKTUALIZOWANIA instrukcji MERGE najlepiej nadaje się dla tego zadania. Instrukcja MERGE można wykonywać zarówno wstawiania i aktualizowania operacji w pojedynczym wywołaniu.

Parametry z wartościami przechowywanymi w tabeli można za pomocą instrukcji MERGE wykonywania aktualizacji i operacje wstawiania. Na przykład rozważmy tabelę pracowników uproszczoną, która zawiera następujące kolumny: EmployeeID FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

W tym przykładzie można użyć fakt, że SocialSecurityNumber jest unikatowy do scalania wielu pracowników. Najpierw utwórz typ tabeli zdefiniowany przez użytkownika:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Następnie utwórz procedurę składowaną lub napisać kod, który używa instrukcji MERGE, aby wykonać aktualizację i wstawiania. W poniższym przykładzie użyto instrukcji MERGE dla parametru z wartościami przechowywanymi w tabeli @employees, typu EmployeeTableType. Zawartość @employees tabeli nie są wyświetlane w tym miejscu.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN 
UPDATE SET
target.FirstName = source.FirstName, 
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Aby uzyskać więcej informacji zobacz dokumentację i przykłady dla instrukcji MERGE. Mimo że można było wykonać to w przechowywane wielu kroku wywołanie procedury przy użyciu oddzielnych INSERT i operacje aktualizacji, instrukcji MERGE jest bardziej wydajne. Kod bazy danych można także skonstruować wywołań języka Transact-SQL, które używają instrukcji MERGE bezpośrednio, bez konieczności dwa wywołania bazy danych dla INSERT i UPDATE.

## <a name="recommendation-summary"></a>Zalecenie podsumowania

Poniższa lista zawiera podsumowanie zaleceń przetwarzania wsadowego omówionych w tym artykule:

* Użyj buforowania i dzielenia na partie, aby zwiększyć wydajność i skalowalność aplikacji usługi SQL Database.
* Dowiedz się, kompromis między dzielenia na partie/buforowanie i zwiększa odporność. Podczas awarii roli ryzyko utraty partii nieprzetworzone dane krytyczne dla prowadzonej działalności może przeważyć korzyści w zakresie wydajności dla przetwarzania wsadowego.
* Należy próbować zachować wszystkie wywołania do bazy danych w jednym centrum danych, aby zmniejszyć opóźnienie.
* Jeśli wybierzesz pojedyncza technika przetwarzania wsadowego, parametry z wartościami przechowywanymi w tabeli oferują najlepszą wydajność i elastyczność.
* Aby zapewnić lepszą wydajność insert przestrzegać następujących wytycznych ogólne, ale Testuj danego scenariusza:
  * < 100 wierszy należy użyć pojedynczego polecenia INSERT sparametryzowanych.
  * W przypadku < 1000 wierszy użyj parametrów z wartościami przechowywanymi w tabeli.
  * Aby uzyskać > = 1000 wierszy, użyj SqlBulkCopy.
* Do aktualizacji i Usuń operacje, parametry z wartościami przechowywanymi w tabeli za pomocą logiki procedurę składowaną, która określa poprawne działanie dla każdego wiersza w tabeli parametru.
* Informacje dotyczące rozmiaru partii:
  * Użyj największych rozmiarów usługi batch, które mają sens dla aplikacji i wymagań biznesowych.
  * Saldo przyrost wydajności dużych partii z ryzykiem tymczasowy lub poważnej awarii. Co to jest skutkiem próby lub utraty danych w partii? 
  * Przetestuj największy rozmiar partii, aby sprawdzić, czy baza danych SQL nie go odrzucić.
  * Utwórz ustawienia konfiguracji tej partii kontrolki, takie jak rozmiar partii lub buforowania w danym przedziale czasu. Te ustawienia zapewniają elastyczność. Można zmienić zachowanie łączenia we wsady w środowisku produkcyjnym, bez konieczności ponownego wdrażania usługi w chmurze.
* Należy unikać równoległe wykonywanie partii zadań, które działają na pojedynczej tabeli w jednej bazie danych. Jeśli chcesz podzielić pojedyncza partia przez wiele wątków roboczych, należy uruchomić testy w celu ustalenia idealna liczba wątków. Po nieokreślona wartość progową wątków będzie obniżyć wydajność, a nie powinna ona być.
* Należy wziąć pod uwagę buforowanie na rozmiar i godziny jako sposób implementowania dzielenia na partie dla dalszych scenariuszach.

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na sposób projektowania baz danych i kodowanie technik związanych z dzielenia na partie może zwiększyć swoje wydajność i skalowalność aplikacji. Ale to tylko jeden składnik w ogólnej strategii. Aby uzyskać więcej sposobów na zwiększenie wydajności i skalowalności, zobacz [wskazówki dotyczące wydajności usługi Azure SQL Database dla pojedynczych baz danych](sql-database-performance-guidance.md) i [zagadnienia dotyczące cen i wydajności dla puli elastycznej](sql-database-elastic-pool-guidance.md).

