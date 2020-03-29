---
title: Jak używać przetwarzania wsadowego w celu zwiększenia wydajności aplikacji
description: W temacie przedstawiono dowody, że operacje wsadowe bazy danych znacznie zwiększa szybkość i skalowalność aplikacji usługi Azure SQL Database. Mimo że te techniki przetwarzania wsadowego działają dla dowolnej bazy danych programu SQL Server, głównym tematem artykułu jest platforma Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: cacc01151edaf31db938cf8abf3d46e75397758f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545028"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>How to use batching to improve SQL Database application performance (Jak zwiększyć wydajność aplikacji SQL Database za pomocą dzielenia na partie)

Operacje przetwarzania wsadowego do usługi Azure SQL Database znacznie zwiększa wydajność i skalowalność aplikacji. Aby zrozumieć korzyści, pierwsza część tego artykułu obejmuje niektóre przykładowe wyniki badań, które porównują żądania sekwencyjne i wsadowe do bazy danych SQL. W dalszej części artykułu przedstawiono techniki, scenariusze i zagadnienia, które ułatwiają pomyślne korzystanie z przetwarzania wsadowego w aplikacjach platformy Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Dlaczego przetwarzanie wsadowe jest ważne dla bazy danych SQL

Przetwarzanie wsadowe wywołań do usługi zdalnej jest dobrze znaną strategią zwiększania wydajności i skalowalności. Istnieją stałe koszty przetwarzania do wszelkich interakcji z usługą zdalną, takich jak serializacji, transferu sieciowego i deserializacji. Pakowanie wielu oddzielnych transakcji w jedną partię minimalizuje te koszty.

W tym dokumencie chcemy zbadać różne strategie i scenariusze przetwarzania wsadowego bazy danych SQL. Chociaż te strategie są również ważne dla aplikacji lokalnych, które używają programu SQL Server, istnieje kilka przyczyn wyróżniania użycia przetwarzania wsadowego dla bazy danych SQL:

* Istnieje potencjalnie większe opóźnienie sieci w dostępie do bazy danych SQL, zwłaszcza jeśli uzyskujesz dostęp do bazy danych SQL spoza tego samego centrum danych platformy Microsoft Azure.
* Wielodostępne cechy bazy danych SQL oznacza, że wydajność warstwy dostępu do danych koreluje z ogólną skalowalnością bazy danych. Baza danych SQL musi uniemożliwić jednemu dzierżawcy/użytkownikowi monopolizowanie zasobów bazy danych ze szkodą dla innych dzierżawców. W odpowiedzi na użycie przekraczające wstępnie zdefiniowane przydziały baza danych SQL może zmniejszyć przepływność lub odpowiadać za pomocą wyjątków ograniczania przepustowości. Wydajność, takich jak przetwarzanie wsadowe, umożliwiają wykonanie większej ilości pracy w bazie danych SQL przed osiągnięciem tych limitów. 
* Przetwarzanie wsadowe jest również skuteczne dla architektur, które używają wielu baz danych (dzielenia na fragmenty). Wydajność interakcji z każdą jednostką bazy danych jest nadal kluczowym czynnikiem ogólnej skalowalności. 

Jedną z zalet korzystania z bazy danych SQL jest to, że nie trzeba zarządzać serwerami, które obsługują bazę danych. Jednak ta zarządzana infrastruktura oznacza również, że trzeba myśleć inaczej o optymalizacji bazy danych. Nie można już szukać poprawy sprzętu bazy danych lub infrastruktury sieciowej. Platforma Microsoft Azure steruje tymi środowiskami. Głównym obszarem, który można kontrolować jest jak aplikacja współdziała z bazy danych SQL. Przetwarzanie wsadowe jest jedną z tych optymalizacji. 

Pierwsza część papieru analizuje różne techniki przetwarzania wsadowego dla aplikacji platformy .NET, które używają bazy danych SQL. Ostatnie dwie sekcje obejmują wsadowe wytyczne i scenariusze.

## <a name="batching-strategies"></a>Strategie wsadowania

### <a name="note-about-timing-results-in-this-article"></a>Uwaga dotycząca wyników pomiaru czasu w tym artykule

> [!NOTE]
> Wyniki nie są punktami odniesienia, ale mają na celu pokazanie **względnej wydajności.** Chronometrażu są oparte na średnio co najmniej 10 przebiegów testowych. Operacje są wstawiane do pustej tabeli. Te testy zostały zmierzone przed V12 i niekoniecznie odpowiadają przepływności, która może wystąpić w bazie danych V12 przy użyciu nowych [warstw usług DTU](sql-database-service-tiers-dtu.md) lub [warstw usług vCore](sql-database-service-tiers-vcore.md). Względna korzyść z techniki wsadowania powinna być podobna.

### <a name="transactions"></a>Transakcje

Wydaje się dziwne, aby rozpocząć przegląd partii, omawiając transakcje. Ale użycie transakcji po stronie klienta ma subtelny efekt przetwarzania wsadowego po stronie serwera, który poprawia wydajność. A transakcje można dodać tylko kilka wierszy kodu, więc zapewniają one szybki sposób na poprawę wydajności operacji sekwencyjnych.

Należy wziąć pod uwagę następujący kod Języka C#, który zawiera sekwencję operacji wstawiania i aktualizowania w prostej tabeli.

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

Najlepszym sposobem optymalizacji tego kodu jest zaimplementowanie jakiejś formy przetwarzania wsadowego po stronie klienta tych wywołań. Ale istnieje prosty sposób, aby zwiększyć wydajność tego kodu, po prostu zawijania sekwencji wywołań w transakcji. Oto ten sam kod, który używa transakcji.

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

Transakcje są faktycznie używane w obu tych przykładach. W pierwszym przykładzie każde pojedyncze wywołanie jest transakcją niejawną. W drugim przykładzie jawnej transakcji zawija wszystkie wywołania. Zgodnie z dokumentacją [dziennika transakcji zapisu z wyprzedzeniem](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL)rekordy dziennika są opróżniane na dysk, gdy transakcja zatwierdza. Tak więc, dołączając więcej wywołań w transakcji, zapis do dziennika transakcji może opóźnić, dopóki transakcja nie zostanie zatwierdzona. W efekcie włączasz przetwarzanie wsadowe dla zapisów w dzienniku transakcji serwera.

W poniższej tabeli przedstawiono niektóre wyniki testów ad hoc. Testy wykonywane te same sekwencyjne wstawia z i bez transakcji. Aby uzyskać więcej perspektywy, pierwszy zestaw testów został uruchomiony zdalnie z komputera przenośnego do bazy danych na platformie Microsoft Azure. Drugi zestaw testów został uruchomiony z usługi w chmurze i bazy danych, które znajdują się w tym samym centrum danych platformy Microsoft Azure (Zachodnie stany USA). W poniższej tabeli przedstawiono czas trwania w milisekundach wstawia sekwencyjnych z transakcjami i bez transakcji.

**Lokalnie na platformie Azure:**

| Operacje | Brak transakcji (ms) | Transakcja (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Platforma Azure na platformę Azure (to samo centrum danych):**

| Operacje | Brak transakcji (ms) | Transakcja (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Wyniki nie są punktami odniesienia. Zobacz [uwagę dotyczącą wyników pomiaru czasu w tym artykule](#note-about-timing-results-in-this-article).

Na podstawie poprzednich wyników testu zawijania pojedynczej operacji w transakcji faktycznie zmniejsza wydajność. Ale jak zwiększyć liczbę operacji w ramach jednej transakcji, poprawa wydajności staje się bardziej widoczne. Różnica w wydajności jest również bardziej zauważalne, gdy wszystkie operacje występują w centrum danych platformy Microsoft Azure. Zwiększone opóźnienie korzystania z bazy danych SQL spoza centrum danych platformy Microsoft Azure przyćmiewa przyrost wydajności przy użyciu transakcji.

Chociaż korzystanie z transakcji może zwiększyć wydajność, należy nadal [przestrzegać najlepszych praktyk dotyczących transakcji i połączeń](https://msdn.microsoft.com/library/ms187484.aspx). Zachowaj transakcję tak krótki, jak to możliwe i zamknij połączenie z bazą danych po zakończeniu pracy. Using instrukcji w poprzednim przykładzie zapewnia, że połączenie jest zamknięty po zakończeniu kolejnych blok kodu.

W poprzednim przykładzie pokazano, że można dodać transakcję lokalną do dowolnego kodu ADO.NET z dwoma wierszami. Transakcje oferują szybki sposób, aby poprawić wydajność kodu, który sprawia, że sekwencyjne wstawiania, aktualizacji i usuwania operacji. Jednak dla najszybszej wydajności należy rozważyć zmianę kodu dalej, aby skorzystać z przetwarzania wsadowego po stronie klienta, takich jak parametry wycenione tabeli.

Aby uzyskać więcej informacji o transakcjach w ADO.NET, zobacz [Transakcje lokalne w ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parametry wycenione w tabeli

Parametry wyceniane w tabeli obsługują typy tabel zdefiniowane przez użytkownika jako parametry w instrukcjach Transact-SQL, procedurach przechowywanych i funkcjach. Ta technika przetwarzania wsadowego po stronie klienta umożliwia wysyłanie wielu wierszy danych w parametrze wycenianym przez tabelę. Aby użyć parametrów wycenionych w tabeli, należy najpierw zdefiniować typ tabeli. Następująca instrukcja Transact-SQL tworzy typ tabeli o nazwie **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

W kodzie należy utworzyć **DataTable** z dokładnie tymi samymi nazwami i typami typu tabeli. Przekaż tę **tabelę data w** parametrze w kwerendzie tekstowej lub wywołaniu procedury składowanej. W poniższym przykładzie przedstawiono następującą technikę:

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

W poprzednim przykładzie **sqlcommand** obiekt wstawia wiersze z tabeli wyceniane parametru ** \@TestTvp**. Poprzednio utworzony obiekt **DataTable** jest przypisywany do tego parametru za pomocą metody **SqlCommand.Parameters.Add.** Przetwarzanie wstawia w jednym wywołaniu znacznie zwiększa wydajność przez kolejne wstawia.

Aby poprawić poprzedni przykład dalej, należy użyć procedury składowanej zamiast polecenia tekstowego. Następujące polecenie Transact-SQL tworzy procedurę składowaną, która przyjmuje parametr **SimpleTestTableType** tabeli wartości.

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

Następnie zmień deklarację obiektu **SqlCommand** w poprzednim przykładzie kodu na następujące.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

W większości przypadków parametry wyceniane w tabeli mają równoważną lub lepszą wydajność niż inne techniki przetwarzania wsadowego. Parametry wyceniane w tabeli są często preferowane, ponieważ są bardziej elastyczne niż inne opcje. Na przykład inne techniki, takie jak kopiowanie zbiorcze SQL, zezwalają tylko na wstawianie nowych wierszy. Jednak w przypadku parametrów wycenianych w tabeli można użyć logiki w procedurze składowanej, aby określić, które wiersze są aktualizacjami, a które wstawia. Typ tabeli można również zmodyfikować tak, aby zawierał kolumnę "Operacja", która wskazuje, czy określony wiersz powinien zostać wstawiony, zaktualizowany lub usunięty.

W poniższej tabeli przedstawiono wyniki testów ad hoc dotyczące stosowania parametrów wycenionych w tabeli w milisekundach.

| Operacje | Lokalnie na platformie Azure (ms) | Azure tego samego centrum danych (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10 000 |23830 |3586 |

> [!NOTE]
> Wyniki nie są punktami odniesienia. Zobacz [uwagę dotyczącą wyników pomiaru czasu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

Przyrost wydajności z partii jest natychmiast widoczne. W poprzednim teście sekwencyjnym 1000 operacji trwało 129 sekund poza centrum danych i 21 sekund z poziomu centrum danych. Jednak w parametrach wycenionych w tabeli 1000 operacji zajmuje tylko 2,6 sekundy poza centrum danych i 0,4 sekundy w centrum danych.

Aby uzyskać więcej informacji na temat parametrów wycenianych w [tabeli, zobacz Parametry wyceniane w tabeli](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Kopia zbiorcza SQL

Kopia zbiorcza SQL to kolejny sposób wstawiania dużych ilości danych do docelowej bazy danych. Aplikacje .NET mogą używać klasy **SqlBulkCopy** do wykonywania operacji wstawiania zbiorczego. **SqlBulkCopy** jest podobny w funkcji do narzędzia wiersza polecenia, **Bcp.exe**lub Instrukcji Transact-SQL, **BULK INSERT**. Poniższy przykład kodu pokazuje, jak zbiorczo kopiować wiersze w źródle **DataTable**, tabela, do tabeli docelowej w programie SQL Server, MyTable.

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

Istnieją przypadki, w których kopiowanie zbiorcze jest preferowane niż parametry wyceniane w tabeli. Zobacz tabelę porównawczą parametrów wycenionych w tabeli w porównaniu z operacjami wstawiania luzem w artykule [Parametry wyceniane w tabeli](https://msdn.microsoft.com/library/bb510489.aspx).

Następujące wyniki testów ad hoc pokazują wydajność przetwarzania wsadowego z **SqlBulkCopy** w milisekundach.

| Operacje | Lokalnie na platformie Azure (ms) | Azure tego samego centrum danych (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10 000 |21605 |2737 |

> [!NOTE]
> Wyniki nie są punktami odniesienia. Zobacz [uwagę dotyczącą wyników pomiaru czasu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

W mniejszych rozmiarach partii parametry wyceniania tabeli przewyższyły klasę **SqlBulkCopy.** Jednak **SqlBulkCopy** wykonywane 12-31% szybciej niż parametry tabeli wyceniane dla testów 1000 i 10000 wierszy. Podobnie jak parametry wyceniane w tabeli, **SqlBulkCopy** jest dobrym rozwiązaniem dla wstawia wsadowych, szczególnie w porównaniu do wydajności operacji nieparzysto-wsadowych.

Aby uzyskać więcej informacji na temat kopiowania zbiorczego w ADO.NET, zobacz [Operacje kopiowania zbiorczego w programie SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Wielowierszowe parametryzowane instrukcje INSERT

Jedną z alternatyw dla małych partii jest skonstruowanie dużej sparametryzowanej instrukcji INSERT, która wstawia wiele wierszy. Poniższy przykład kodu pokazuje tę technikę.

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

Ten przykład ma na celu pokazanie podstawowej koncepcji. Bardziej realistyczny scenariusz będzie pętli przez wymagane jednostki do konstruowania ciągu kwerendy i parametry polecenia jednocześnie. Są ograniczone do łącznie 2100 parametrów kwerendy, więc ogranicza całkowitą liczbę wierszy, które mogą być przetwarzane w ten sposób.

Poniższe wyniki testu ad hoc pokazują wydajność tego typu wstawiania instrukcji w milisekundach.

| Operacje | Parametry wycenione w tabeli (ms) | Jednowstawowe INSERT (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Wyniki nie są punktami odniesienia. Zobacz [uwagę dotyczącą wyników pomiaru czasu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

Takie podejście może być nieco szybsze dla partii, które są mniejsze niż 100 wierszy. Chociaż poprawa jest mała, ta technika jest inną opcją, która może działać dobrze w konkretnym scenariuszu aplikacji.

### <a name="dataadapter"></a>Dataadapter

**Klasa DataAdapter** umożliwia modyfikowanie obiektu **DataSet,** a następnie przesyłanie zmian jako operacje WSTAW, AKTUALIZUJ i USUŃ. Jeśli używasz **DataAdapter** w ten sposób, należy pamiętać, że oddzielne wywołania są wykonane dla każdej operacji różnych. Aby zwiększyć wydajność, należy użyć **UpdateBatchSize** właściwość do liczby operacji, które powinny być wsadowe w czasie. Aby uzyskać więcej informacji, zobacz [Wykonywanie operacji wsadowych przy użyciu programów DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Struktura jednostek

Entity Framework obecnie nie obsługuje przetwarzania wsadowego. Różni deweloperzy w społeczności próbowali zademonstrować obejścia, takie jak zastąpienie **SaveChanges** metody. Ale rozwiązania są zazwyczaj złożone i dostosowane do aplikacji i modelu danych. Projekt codeplex entity framework ma obecnie stronę dyskusji na temat tego żądania funkcji. Aby wyświetlić tę dyskusję, zobacz [Informacje o spotkaniu projektu — 2 sierpnia 2012](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012)r. .

### <a name="xml"></a>XML

Dla kompletności uważamy, że ważne jest, aby mówić o XML jako strategii przetwarzania wsadowego. Jednak użycie XML nie ma żadnych zalet w stosunku do innych metod i kilka wad. Podejście jest podobne do parametrów wycenianych w tabeli, ale plik lub ciąg XML jest przekazywany do procedury składowanej zamiast tabeli zdefiniowanej przez użytkownika. Procedura składowana analizuje polecenia w procedurze składowanej.

Istnieje kilka wad tego podejścia:

* Praca z XML może być uciążliwa i podatna na błędy.
* Analizowanie XML w bazie danych może być intensywnie korzystające z procesora CPU.
* W większości przypadków ta metoda jest wolniejsza niż parametry wyceniane w tabeli.

Z tych powodów użycie pliku XML w kwerendach wsadowych nie jest zalecane.

## <a name="batching-considerations"></a>Zagadnienia dotyczące przetwarzania wsadowego

W poniższych sekcjach przedstawiono więcej wskazówek dotyczących korzystania z przetwarzania wsadowego w aplikacjach bazy danych SQL.

### <a name="tradeoffs"></a>Kompromisów

W zależności od architektury przetwarzania wsadowego może obejmować kompromis między wydajnością i odpornością. Rozważmy na przykład scenariusz, w którym twoja rola nieoczekiwanie ustępuje. Jeśli utracisz jeden wiersz danych, wpływ jest mniejszy niż wpływ utraty dużej partii niezaudłomowanych wierszy. Istnieje większe ryzyko podczas buforowania wierszy przed wysłaniem ich do bazy danych w określonym przedziale czasu.

Z powodu tego kompromisu należy ocenić typ operacji, które można partii. Partia bardziej agresywnie (większe partie i dłuższe okna czasowe) z danymi, które są mniej krytyczne.

### <a name="batch-size"></a>Rozmiar partii

W naszych testach zazwyczaj nie było żadnych korzyści z podziału dużych partii na mniejsze kawałki. W rzeczywistości ten podpodział często powodował mniejszą wydajność niż przesyłanie pojedynczej dużej partii. Rozważmy na przykład scenariusz, w którym chcesz wstawić 1000 wierszy. W poniższej tabeli pokazano, jak długo trwa używanie parametrów wycenionych w tabeli, aby wstawić 1000 wierszy po podzieleniu na mniejsze partie.

| Rozmiar partii | Iteracji | Parametry wycenione w tabeli (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Wyniki nie są punktami odniesienia. Zobacz [uwagę dotyczącą wyników pomiaru czasu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

Widać, że najlepszą wydajnością dla 1000 wierszy jest przesłanie ich wszystkich naraz. W innych testach (nie pokazano tutaj), nie było niewielki przyrost wydajności, aby podzielić partię 10000 wiersz na dwie partie 5000. Ale schemat tabeli dla tych testów jest stosunkowo proste, więc należy wykonać testy na określonych danych i wielkości partii, aby zweryfikować te wyniki.

Innym czynnikiem do rozważenia jest to, że jeśli całkowita partia staje się zbyt duża, bazy danych SQL może ograniczyć i odmówić zatwierdzenia partii. Aby uzyskać najlepsze wyniki, należy przetestować określony scenariusz, aby ustalić, czy istnieje idealny rozmiar partii. Upewnij się, że rozmiar partii można skonfigurować w czasie wykonywania, aby włączyć szybkie dostosowanie na podstawie wydajności lub błędów.

Na koniec należy zrównoważyć rozmiar partii z ryzykiem związanym z przetwarzaniem wsadowym. Jeśli występują błędy przejściowe lub rola nie powiedzie się, należy wziąć pod uwagę konsekwencje ponowienia próby wykonania operacji lub utraty danych w partii.

### <a name="parallel-processing"></a>Przetwarzanie równoległe

Co zrobić, jeśli przyjął podejście zmniejszenia rozmiaru partii, ale używane wiele wątków do wykonania pracy? Ponownie, nasze testy wykazały, że kilka mniejszych partii wielowątkowych zazwyczaj wykonywane gorzej niż pojedyncza większa partia. Następujący test próbuje wstawić 1000 wierszy w jednej lub więcej równoległych partii. Ten test pokazuje, jak więcej jednoczesnych partii faktycznie zmniejszyła wydajność.

| Rozmiar partii [Iteracje] | Dwa wątki (ms) | Cztery wątki (ms) | Sześć wątków (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Wyniki nie są punktami odniesienia. Zobacz [uwagę dotyczącą wyników pomiaru czasu w tym artykule](#note-about-timing-results-in-this-article).
> 
> 

Istnieje kilka potencjalnych przyczyn pogorszenia wydajności z powodu równoległości:

* Istnieje wiele jednoczesnych wywołań sieciowych zamiast jednego.
* Wiele operacji względem jednej tabeli może spowodować rywalizację i blokowanie.
* Istnieją koszty ogólne związane z wielowątkowym.
* Koszt otwarcia wielu połączeń przewyższają korzyści z przetwarzania równoległego.

Jeśli kierujesz reklamy na różne tabele lub bazy danych, można zobaczyć pewne przyrost wydajności dzięki tej strategii. Dzielenie na fragmenty bazy danych lub federacje będzie scenariusz dla tego podejścia. Dzielenie na fragmenty używa wielu baz danych i trasy różnych danych do każdej bazy danych. Jeśli każda mała partia przechodzi do innej bazy danych, a następnie wykonywanie operacji równolegle może być bardziej wydajne. Jednak przyrost wydajności nie jest wystarczająco istotne, aby użyć jako podstawa decyzji o użyciu dzielenia na fragmenty bazy danych w rozwiązaniu.

W niektórych projektach równoległe wykonywanie mniejszych partii może spowodować lepszą przepływność żądań w systemie pod obciążeniem. W takim przypadku, mimo że jest szybsze do przetwarzania pojedynczej partii większe, przetwarzania wielu partii równolegle może być bardziej wydajne.

Jeśli używasz wykonywania równoległego, należy rozważyć kontrolowanie maksymalnej liczby wątków roboczych. Mniejsza liczba może spowodować mniej rywalizacji i krótszy czas wykonywania. Należy również wziąć pod uwagę dodatkowe obciążenie, które to umieszcza w docelowej bazie danych zarówno w połączeniach i transakcjach.

### <a name="related-performance-factors"></a>Powiązane czynniki wydajności

Typowe wskazówki dotyczące wydajności bazy danych wpływa również na przetwarzanie wsadowe. Na przykład wydajność wstawiania jest zmniejszona dla tabel, które mają duży klucz podstawowy lub wiele indeksów nieklastrowanych.

Jeśli parametry wycenione w tabeli używają procedury składowanej, można użyć polecenia **SET NOCOUNT ON** na początku procedury. Ta instrukcja pomija zwrot liczby wierszy, których dotyczy problem w procedurze. Jednak w naszych testach użycie **SET NOCOUNT ON** albo nie miało wpływu, albo nie miało wpływu na wydajność. Procedura składowana testu była prosta za pomocą pojedynczego polecenia **INSERT** z parametru wycenionego przez tabelę. Możliwe jest, że bardziej złożone procedury składowane skorzystałyby z tego oświadczenia. Ale nie zakładaj, że dodanie **SET NOCOUNT ON** do procedury składowanej automatycznie zwiększa wydajność. Aby zrozumieć efekt, przetestuj procedurę składowaną z instrukcją **SET NOCOUNT ON** i bez niej.

## <a name="batching-scenarios"></a>Scenariusze przetwarzania wsadowego

W poniższych sekcjach opisano sposób używania parametrów wycenionych w tabeli w trzech scenariuszach aplikacji. Pierwszy scenariusz pokazuje, jak buforowanie i przetwarzanie wsadowe może współpracować. Drugi scenariusz zwiększa wydajność, wykonując operacje szczegółów wzorca w jednym wywołaniu procedury składowanej. Ostateczny scenariusz pokazuje, jak używać parametrów wycenionych w tabeli w operacji "UPSERT".

### <a name="buffering"></a>Buforowanie

Chociaż istnieje kilka scenariuszy, które są oczywiste kandydata do przetwarzania wsadowego, istnieje wiele scenariuszy, które mogą korzystać z przetwarzania wsadowego przez opóźnione przetwarzanie. Jednak opóźnione przetwarzanie niesie ze sobą większe ryzyko utraty danych w przypadku nieoczekiwanego błędu. Ważne jest, aby zrozumieć to ryzyko i rozważyć konsekwencje.

Rozważmy na przykład aplikację sieci web, która śledzi historię nawigacji każdego użytkownika. Na każdym żądaniu strony aplikacja może nawiązać wywołanie bazy danych w celu zarejestrowania widoku strony użytkownika. Jednak wyższą wydajność i skalowalność można osiągnąć, buforując działania nawigacyjne użytkowników, a następnie wysyłając te dane do bazy danych w partiach. Aktualizację bazy danych można wyzwolić według upływającego czasu i/lub rozmiaru buforu. Na przykład reguła może określić, że partia powinna być przetwarzana po 20 sekundach lub gdy bufor osiągnie 1000 elementów.

Poniższy przykład kodu używa [rozszerzeń reaktywnych — Rx](https://msdn.microsoft.com/data/gg577609) do przetwarzania buforowanych zdarzeń wywoływanych przez klasę monitorowania. Po wypełnieniu buforu lub osiągnięciu limitu czasu partia danych użytkownika jest wysyłana do bazy danych z parametrem wycenianym w tabeli.

Następująca klasa NavHistoryData modeluje szczegóły nawigacji użytkownika. Zawiera podstawowe informacje, takie jak identyfikator użytkownika, dostęp do adresu URL i czas dostępu.

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

Klasa NavHistoryDataMonitor jest odpowiedzialna za buforowanie danych nawigacji użytkownika do bazy danych. Zawiera metodę RecordUserNavigationEntry, która odpowiada przez wywoływanie **onadded** zdarzenia. Poniższy kod przedstawia logikę konstruktora, która używa Rx do tworzenia obserwowalnej kolekcji na podstawie zdarzenia. Następnie subskrybuje tę obserwowalną kolekcję za pomocą Buffer metody. Przeciążenie określa, że bufor powinien być wysyłany co 20 sekund lub 1000 wpisów.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Program obsługi konwertuje wszystkie buforowane elementy na typ wyceniony w tabeli, a następnie przekazuje ten typ do procedury składowanej, która przetwarza partię. Poniższy kod przedstawia pełną definicję dla klas NavHistoryDataEventArgs i NavHistoryDataMonitor.

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

Program obsługi konwertuje wszystkie buforowane elementy na typ wyceniony w tabeli, a następnie przekazuje ten typ do procedury składowanej, która przetwarza partię. Poniższy kod przedstawia pełną definicję dla klas NavHistoryDataEventArgs i NavHistoryDataMonitor.

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

Aby użyć tej klasy buforowania, aplikacja tworzy statyczny Obiekt NavHistoryDataMonitor. Za każdym razem, gdy użytkownik uzyskuje dostęp do strony, aplikacja wywołuje metodę NavHistoryDataMonitor.RecordUserNavigationEntry. Logika buforowania przechodzi do obsługi wysyłania tych wpisów do bazy danych w partiach.

### <a name="master-detail"></a>Szczegóły wzorca

Parametry wyceniane w tabeli są przydatne w przypadku prostych scenariuszy INSERT. Jednak może to być trudniejsze do wstawienia partii, które obejmują więcej niż jedną tabelę. Dobrym przykładem jest scenariusz "master/detail". Tabela główna identyfikuje jednostkę podstawową. Co najmniej jedna tabela szczegółów przechowuje więcej danych o jednostce. W tym scenariuszu relacje klucza obcego wymuszają relację szczegółów z unikatową jednostką główną. Należy wziąć pod uwagę uproszczoną wersję tabeli PurchaseOrder i skojarzonej z nią tabeli OrderDetail. Następujące transact-SQL tworzy tabelę PurchaseOrder z czterema kolumnami: OrderID, OrderDate, CustomerID i Status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Każde zamówienie zawiera jeden lub więcej zakupów produktów. Te informacje są przechwytywane w tabeli PurchaseOrderDetail. Następujące Transact-SQL tworzy PurchaseOrderDetail tabeli z pięciu kolumn: OrderID, OrderDetailID, ProductID, UnitPrice i OrderQty.

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

Kolumna OrderID w tabeli PurchaseOrderDetail musi odwoływać się do zamówienia z tabeli Zamówienie. Następująca definicja klucza obcego wymusza to ograniczenie.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Aby używać parametrów wycenionych w tabeli, dla każdej tabeli docelowej musi być używany jeden typ tabeli zdefiniowany przez użytkownika.

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

Następnie zdefiniuj procedurę składowaną, która akceptuje tabele tych typów. Ta procedura umożliwia aplikacji lokalnie partii zestaw zamówień i szczegóły zamówienia w jednym wywołaniu. Poniższy transact-SQL zawiera pełną deklarację procedury składowanej dla tego przykładu zamówienia zakupu.

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

W tym przykładzie tabela zdefiniowana @IdentityLink lokalnie przechowuje rzeczywiste wartości OrderID z nowo wstawionych wierszy. Te identyfikatory zamówień różnią się od tymczasowych wartości OrderID w parametrach @orders i @details wartościach tabeli. Z tego powodu @IdentityLink tabela łączy następnie wartości @orders OrderID z parametru z rzeczywistymi wartościami OrderID dla nowych wierszy w tabeli PurchaseOrder. Po tym kroku @IdentityLink tabela może ułatwić wstawienie szczegółów zamówienia z rzeczywistym OrderID, który spełnia ograniczenie klucza obcego.

Tej procedury składowanej można używać z kodu lub innych wywołań Transact-SQL. Zobacz sekcję parametrów wycenionych w tabeli w tym dokumencie, aby zapoznać się z przykładem kodu. Poniżej transact-SQL pokazuje, jak wywołać sp_InsertOrdersBatch.

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

To rozwiązanie umożliwia każdej partii użycie zestawu wartości OrderID, które zaczynają się od 1. Te tymczasowe wartości OrderID opisują relacje w partii, ale rzeczywiste wartości OrderID są określane w czasie operacji wstawiania. Można uruchomić te same instrukcje w poprzednim przykładzie wielokrotnie i generować unikatowe zamówienia w bazie danych. Z tego powodu należy rozważyć dodanie więcej kodu lub logiki bazy danych, która uniemożliwia zduplikowane zamówienia podczas korzystania z tej techniki przetwarzania wsadowego.

W tym przykładzie pokazano, że jeszcze bardziej złożone operacje bazy danych, takie jak operacje szczegółów wzorca, mogą być wsadowe przy użyciu parametrów wycenianych w tabeli.

### <a name="upsert"></a>Upsert (upsert)

Inny scenariusz przetwarzania wsadowego obejmuje jednoczesne aktualizowanie istniejących wierszy i wstawianie nowych wierszy. Ta operacja jest czasami określana jako operacja "UPSERT" (aktualizacja + wstawianie). Zamiast wykonywać oddzielne wywołania WSTAWIANIA I AKTUALIZACJI, instrukcja MERGE najlepiej nadaje się do tego zadania. Instrukcja MERGE może wykonywać operacje wstawiania i aktualizowania w jednym wywołaniu.

Parametry wyceniane w tabeli mogą być używane z instrukcją MERGE do wykonywania aktualizacji i wstawiania. Rozważmy na przykład uproszczoną tabelę Employee zawierającą następujące kolumny: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

W tym przykładzie można użyć faktu, że SocialSecurityNumber jest unikatowy do wykonywania scalania wielu pracowników. Najpierw utwórz typ tabeli zdefiniowany przez użytkownika:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Następnie utwórz procedurę składowaną lub napisz kod, który używa instrukcji MERGE do wykonania aktualizacji i wstawiania. W poniższym przykładzie użyto instrukcji MERGE @employeesw parametrze wycenionym w tabeli, typu EmployeeTableType. Zawartość tabeli @employees nie jest wyświetlana w tym miejscu.

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

Aby uzyskać więcej informacji, zobacz dokumentację i przykłady instrukcji MERGE. Chociaż ta sama praca może być wykonana w wieloetapowym wywołaniu procedury składowanej z oddzielnymi operacjami INSERT i UPDATE, instrukcja MERGE jest bardziej wydajna. Kod bazy danych można również konstruować wywołania Transact-SQL, które używają instrukcji MERGE bezpośrednio bez konieczności dwóch wywołań bazy danych dla WSTAWiania i AKTUALIZACJI.

## <a name="recommendation-summary"></a>Podsumowanie rekomendacji

Poniższa lista zawiera podsumowanie zaleceń dotyczących przetwarzania wsadowego omówionych w tym artykule:

* Użyj buforowania i przetwarzania wsadowego, aby zwiększyć wydajność i skalowalność aplikacji bazy danych SQL.
* Zrozumienie kompromisów między przetwarzaniem wsadowym/buforowania i odporności. Podczas niepowodzenia roli ryzyko utraty nieprzetwochanej partii danych krytycznych dla firmy może przeważać nad korzyściami płynącymi z wydajności przetwarzania wsadowego.
* Spróbuj zachować wszystkie wywołania do bazy danych w jednym centrum danych, aby zmniejszyć opóźnienia.
* Jeśli wybierzesz jedną technikę wsadowania, parametry wyceniane w tabeli oferują najlepszą wydajność i elastyczność.
* Aby uzyskać najszybszą wydajność wstawiania, postępuj zgodnie z tymi ogólnymi wytycznymi, ale przetestuj scenariusz:
  * W przypadku < 100 wierszy należy użyć pojedynczego sparametryzowanego polecenia INSERT.
  * W przypadku < 1000 wierszy należy użyć parametrów wycenionych w tabeli.
  * Dla >= 1000 wierszy, użyj SqlBulkCopy.
* W przypadku operacji aktualizacji i usuwania należy użyć parametrów wycenionych w tabeli z logiką procedury składowanej, która określa prawidłową operację w każdym wierszu w parametrze tabeli.
* Wytyczne dotyczące rozmiaru partii:
  * Użyj największych rozmiarów partii, które mają sens dla aplikacji i wymagań biznesowych.
  * Zrównoważyć przyrost wydajności dużych partii z ryzykiem tymczasowych lub katastrofalnych awarii. Co jest konsekwencją ponownych prób lub utraty danych w partii? 
  * Przetestuj największy rozmiar partii, aby sprawdzić, czy baza danych SQL nie odrzuca go.
  * Tworzenie ustawień konfiguracji, które kontrolują przetwarzanie wsadowe, takie jak rozmiar partii lub okno czasu buforowania. Te ustawienia zapewniają elastyczność. Można zmienić zachowanie przetwarzania wsadowego w produkcji bez ponownego rozmieszczania usługi w chmurze.
* Unikaj równoległego wykonywania partii, które działają na jednej tabeli w jednej bazie danych. Jeśli zdecydujesz się podzielić pojedynczą partię na wiele wątków roboczych, uruchom testy, aby określić idealną liczbę wątków. Po nieokreślony próg więcej wątków zmniejszy wydajność, a nie zwiększy ją.
* Należy wziąć pod uwagę buforowanie na rozmiar i czas jako sposób implementowania przetwarzania wsadowego dla większej liczby scenariuszy.

## <a name="next-steps"></a>Następne kroki

W tym artykule skupiono się na tym, jak projektowanie baz danych i techniki kodowania związane z przetwarzaniem wsadowym mogą poprawić wydajność i skalowalność aplikacji. Ale to tylko jeden z czynników w ogólnej strategii. Aby uzyskać więcej sposobów zwiększenia wydajności i skalowalności, zobacz [wskazówki dotyczące wydajności usługi Azure SQL Database dla pojedynczych baz danych](sql-database-performance-guidance.md) oraz [zagadnienia dotyczące ceny i wydajności dla puli elastycznej.](sql-database-elastic-pool-guidance.md)

