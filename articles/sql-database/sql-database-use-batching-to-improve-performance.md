---
title: Jak używać przetwarzanie wsadowe, aby zwiększyć wydajność aplikacji bazy danych SQL Azure
description: Temat zawiera dowód tym przetwarzanie wsadowe operacji bazy danych znacznie imroves szybkości i skalowalność aplikacji bazy danych SQL Azure. Mimo że te techniki przetwarzanie wsadowe działać dla dowolnej bazy danych programu SQL Server, artykuł koncentruje się na platformie Azure.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: d534e138af7a22b32fbf64e2200016091beac62f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Jak używać przetwarzanie wsadowe, aby zwiększyć wydajność aplikacji bazy danych SQL
Przetwarzanie wsadowe operacji do bazy danych SQL Azure w znacznie poprawia wydajność i skalowalność aplikacji. Aby zrozumieć korzyści, pierwsza część w tym artykule omówiono niektóre przykładowe wyniki testów, pozwalające porównać wsadów i sekwencyjny żądania do bazy danych SQL. W pozostałej części tego artykułu pokazuje techniki, scenariusze i zagadnienia dotyczące pomogą pomyślnie przetwarzanie wsadowe w aplikacjach platformy Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Dlaczego jest przetwarzanie wsadowe ważne dla bazy danych SQL?
Przetwarzanie wsadowe wywołania do zdalnej usługi jest dobrze znane strategię zwiększenie wydajności i skalowalności. Są to stałe koszty przetwarzania do interakcji z usługą zdalnego, takich jak serializacji, transferu sieciowego i deserializacji. Pakowanie wiele oddzielnych transakcji do pojedynczej partii minimalizuje tych kosztów.

W tym dokumencie chcemy zbadania różnych bazy danych SQL, przetwarzanie wsadowe strategii i scenariuszy. Mimo że strategie również są ważne dla aplikacji lokalnych, które używają programu SQL Server, istnieje kilka przyczyn wyróżnianie stosowania przetwarzania wsadowego dla bazy danych SQL:

* Brak potencjalnie większe opóźnienia sieci podczas uzyskiwania dostępu do bazy danych SQL, zwłaszcza, jeśli uzyskują dostęp do bazy danych SQL z poza tym samym centrum danych Microsoft Azure.
* Wielodostępna właściwości bazy danych SQL oznacza, że wydajność odpowiada warstwy dostępu do danych w ogólnej skalowalności bazy danych. Baza danych SQL musi uniemożliwić przejęcie kontroli nad zasobów bazy danych do szkody innych dzierżawców dowolnego pojedynczego dzierżawcy/użytkownika. W odpowiedzi na użycie przekracza wstępnie zdefiniowanych przydziałów bazy danych SQL można zmniejszyć przepustowość lub odpowiadać, podając ograniczania wyjątków. Korzyści, takich jak przetwarzanie wsadowe, umożliwiają kontynuować pracę w bazie danych SQL przed osiągnięciem tych ograniczeń. 
* Tworzenie plików wsadowych jest również dla architektury, które używają wielu baz danych (dzielenia na fragmenty). Wydajność interakcję z każdej jednostki bazy danych nadal jest kluczowym czynnikiem Twojej ogólnej skalowalności. 

Jest jedną z korzyści wynikające ze stosowania bazy danych SQL, że nie trzeba zarządzać serwerami, które zawierają bazy danych. Jednak ta infrastruktura zarządzanych również oznacza, że inaczej myśleć o optymalizacji bazy danych. Nie można sprawdzić do poprawy infrastruktury sprzętowe lub sieciowe bazy danych. Microsoft Azure określa tych środowisk. Można kontrolować obszaru głównego jest współdziałania aplikacji z bazy danych SQL. Tworzenie plików wsadowych jest jednym z tych optymalizacji. 

Pierwsza część papieru sprawdza różnych technik przetwarzanie wsadowe aplikacji .NET, które używają bazy danych SQL. Ostatnich dwóch sekcjach opisano scenariusze i wskazówki dotyczące łączenia we wsady.

## <a name="batching-strategies"></a>Przetwarzanie wsadowe strategie
### <a name="note-about-timing-results-in-this-article"></a>Należy pamiętać o wynikach czasu, w tym artykule
> [!NOTE]
> Wyniki nie są testów porównawczych, ale są przeznaczone do wyświetlenia **względną wydajność**. Czasy są oparte na średnio co najmniej 10 uruchomień testów. Operacje są wstawia do pustej tabeli. Te testy zostały zmierzone pre-V12, a ich nie musi odpowiadać przepływności, które mogą pojawić się w bazie danych w wersji 12 przy użyciu nowego [DTU warstwy usług](sql-database-service-tiers-dtu.md) lub [warstwy usług vCore](sql-database-service-tiers-vcore.md). Względne korzyści przetwarzanie wsadowe techniki powinny być podobne.
> 
> 

### <a name="transactions"></a>Transakcje
Wydaje się dziwne, aby rozpocząć Przegląd przetwarzania wsadowego przez dyskutować transakcji. Ale stosowanie transakcji po stronie klienta ma Delikatny efekt przetwarzanie wsadowe po stronie serwera, który zapewnia lepszą wydajność. I transakcji można dodać przy tylko kilku wierszy kodu, więc zapewniają szybki sposób poprawić wydajność kolejnych operacji.

Należy wziąć pod uwagę następujące C# kod, który zawiera sekwencję INSERT i aktualizowanie operacji na prostej tabeli.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

Poniższy kod ADO.NET sekwencyjnie wykonuje te operacje.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();

        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

Najlepszy sposób, aby zoptymalizować ten kod jest wdrożenie jakiegoś typu po stronie klienta przetwarzanie wsadowe tych wywołań. Występuje prosty sposób, aby zwiększyć wydajność ten kod po prostu zawijania sekwencję wywołań w transakcji. W tym miejscu jest ten sam kod, który używa transakcji.

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

Transakcje są rzeczywiście używane w obu tych przykładów. W pierwszym przykładzie każdego wywołania poszczególnych jest transakcji niejawnej. W drugim przykładzie transakcji jawnej opakowuje wszystkie wywołania. Na dokumentację [dziennika transakcji zapisu z wyprzedzeniem](https://msdn.microsoft.com/library/ms186259.aspx), rekordów dziennika są opróżniane po zatwierdzeniu transakcji. Tak dołączając kolejnych wywołań w transakcji, zapisu w dzienniku transakcji można opóźnić dopóki transakcja została przekazana. W efekcie włączasz przetwarzania wsadowego dla operacji zapisu do dziennika transakcji serwera.

W poniższej tabeli przedstawiono niektóre wyniki testowania ad hoc. Testy wykonywane tego samego sekwencyjnego wstawiania z włączonymi i wyłączonymi transakcji. Więcej perspektywy pierwszy zestaw testów był uruchamiany zdalnie z komputera przenośnego z bazą danych programu Microsoft Azure. Drugi zestaw testów został uruchomiony z usługi w chmurze i że oba znajdowały się w tym samym centrum danych Microsoft Azure (zachodnie stany USA) bazy danych. W poniższej tabeli przedstawiono czas w milisekundach sekwencyjnego wstawiania z włączonymi i wyłączonymi transakcji.

**Dane lokalne na platformie Azure**:

| Operacje | Brak transakcji (ms) | W transakcji (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure do platformy Azure (tym samym centrum danych)**:

| Operacje | Brak transakcji (ms) | W transakcji (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach czasu, w tym temacie](#note-about-timing-results-in-this-topic).
> 
> 

Oparte na poprzednie wyniki testu, zawijania jednej operacji w transakcji obniża wydajność. Ale zwiększania liczby operacji w ramach jednej transakcji zwiększenie wydajności staje się bardziej oznaczone. Różnica w wydajności jest bardziej widoczne również w przypadku, gdy wszystkie operacje są wykonywane w ramach centrum danych Microsoft Azure. Zwiększona opóźnień poza centrum danych Microsoft Azure przy użyciu bazy danych SQL z overshadows wzmocnienie wydajności przy użyciu transakcji.

Mimo że użycie transakcji można zwiększyć wydajność, w dalszym ciągu [obserwować najlepsze rozwiązania dotyczące połączenia i transakcje](https://msdn.microsoft.com/library/ms187484.aspx). Zachowanie transakcji możliwie krótki i zamknąć połączenie z bazą danych po zakończeniu pracy. Przy użyciu instrukcji w poprzednim przykładzie gwarantuje, że połączenie jest zamknięte po zakończeniu blok kodu kolejne.

W poprzednim przykładzie pokazano, kodowi ADO.NET o dwa wiersze dodaniem transakcji lokalnej. Transakcje oferują możliwość szybkiego zwiększenia wydajności kodu, który sprawia, że sekwencyjnego wstawiania, aktualizowania i usuwania działań. Jednak dla największą wydajność, należy rozważyć zmianę kodu po Wykorzystaj po stronie klienta przetwarzanie wsadowe, takie jak parametry przechowywanymi w tabeli.

Aby uzyskać więcej informacji o transakcji w ADO.NET, zobacz [lokalne transakcje w ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parametry przechowywanymi w tabeli
Parametry przechowywanymi w tabeli obsługuje typach tabel zdefiniowanych przez użytkownika jako parametry w instrukcji języka Transact-SQL, procedury składowane i funkcje. Ta technika przetwarzanie wsadowe po stronie klienta umożliwia wysyłanie wiele wierszy danych w ramach parametru przechowywanymi w tabeli. Aby użyć parametrów przechowywanymi w tabeli, należy najpierw zdefiniować typu tabeli. Następującą instrukcję Transact-SQL tworzy typ tabeli o nazwie **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );


W kodzie, możesz utworzyć **DataTable** o dokładnie tych samych nazw i typy typ tabeli. Przekazany **DataTable** w parametrze tekstu zapytania lub procedury składowanej wywołania. W poniższym przykładzie przedstawiono tej techniki:

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

W poprzednim przykładzie **SqlCommand** obiektu wstawia wiersze z parametrem przechowywanymi w tabeli **@TestTvp**. Utworzonej wcześniej **DataTable** obiekt jest przypisany do tego parametru z **SqlCommand.Parameters.Add** metody. Przetwarzanie wsadowe operacji wstawienia w jednym wywołaniu znacznie zwiększa wydajność za pośrednictwem sekwencyjnego wstawiania.

Aby poprawić dalsze w poprzednim przykładzie, użyj procedury składowanej zamiast polecenia opartego na tekście. Następujące polecenie języka Transact-SQL tworzy procedury przechowywanej, która przyjmuje **SimpleTestTableType** parametru przechowywanymi w tabeli.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Następnie zmienić **SqlCommand** deklaracji w poprzednim przykładzie kodu dla następujących obiektów.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

W większości przypadków przechowywanymi w tabeli Parametry mają równoważną lub lepszą wydajność niż innych technik, przetwarzanie wsadowe. Parametry przechowywanymi w tabeli są często zalecane, ponieważ są one bardziej elastyczne niż inne opcje. Na przykład innych technik, takich jak kopiowania zbiorczego SQL, tylko umożliwienia wstawiania nowych wierszy. Ale z parametrami przechowywanymi w tabeli, można użyć logikę w procedurze składowanej, aby określić wiersze, które są aktualizacje oraz, które są wstawia. Typ tabeli można zmodyfikować w taki sposób, aby zawiera kolumny "Operacji", która wskazuje, czy określony wiersz powinien można wstawiać, zaktualizować lub usunąć.

W poniższej tabeli przedstawiono wyniki testu ad hoc do użycia parametrów przechowywanymi w tabeli w milisekundach.

| Operacje | Dane lokalne na platformie Azure (ms) | Tym samym centrum danych Azure (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10 000 |23830 |3586 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach czasu, w tym temacie](#note-about-timing-results-in-this-topic).
> 
> 

Bardziej wydajne z tworzenie plików wsadowych jest oczywista. W poprzednim teście sekwencyjnych 1000 operacji zajęło 129 sekund poza centrum danych i 21 sekund od w centrum danych. Jednak parametrami przechowywanymi w tabeli operacje 1000 wykorzystania tylko 2.6 sekund poza centrum danych i 0,4 sekund w centrum danych.

Aby uzyskać więcej informacji o parametrach przechowywanymi w tabeli, zobacz [zwracającej tabelę parametrów](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Kopiowania zbiorczego SQL
Kopiowania zbiorczego SQL jest inny sposób, aby wstawić dużych ilości danych do docelowej bazy danych. Aplikacje .NET mogą używać **SqlBulkCopy** klasy do wykonania zbiorczego wstawiania operacji. **SqlBulkCopy** jest podobny do narzędzia wiersza polecenia, funkcja **Bcp.exe**, lub instrukcji języka Transact-SQL **BULK INSERT**. Poniższy przykładowy kod przedstawia sposób kopiowania zbiorczego wierszy w źródle **DataTable**, do tabeli docelowej w programie SQL Server tabeli MyTable.

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

Istnieją przypadki, gdzie kopiowania zbiorczego jest preferowana przez parametry przechowywanymi w tabeli. Zobacz Tabela porównawcza przechowywanymi w tabeli parametrów lub BULK INSERT operacji w artykule [zwracającej tabelę parametrów](https://msdn.microsoft.com/library/bb510489.aspx).

Następujące wyniki testu ad hoc Pokaż wydajności przetwarzania wsadowego z **SqlBulkCopy** w milisekundach.

| Operacje | Dane lokalne na platformie Azure (ms) | Tym samym centrum danych Azure (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10 000 |21605 |2737 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach czasu, w tym temacie](#note-about-timing-results-in-this-topic).
> 
> 

W mniejszych rozmiarach partii, korzystanie z parametrów przechowywanymi w tabeli outperformed **SqlBulkCopy** klasy. Jednak **SqlBulkCopy** wykonano szybciej niż przechowywanymi w tabeli parametrów % 12-31 testów 1000 i 10 000 wierszy. Przechowywanymi w tabeli parametrów, takich jak **SqlBulkCopy** jest dobra opcja w przypadku operacji wsadowej operacji wstawienia, szczególnie w porównaniu do wykonywania operacji niewsadowego.

Aby uzyskać więcej informacji dotyczących kopiowania zbiorczego w ADO.NET, zobacz [operacje kopiowania masowego w programie SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Instrukcje sparametryzowana WSTAWIĆ wiele wierszy
Jeden alternatywą dla małych partii jest skonstruowanie duże sparametryzowanych instrukcji INSERT, która wstawia wiele wierszy. Poniższy przykład kodu pokazuje tej metody.

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


W tym przykładzie jest przeznaczone do wyświetlenia podstawowe pojęcia. Scenariusz bardziej realistyczne czy pętli jednostek wymagane do utworzenia ciąg zapytania i parametry polecenia jednocześnie. Jest ograniczona do całkowitej liczbie parametrów zapytania 2100, co ogranicza całkowitą liczbę wierszy, które mogą być przetwarzane w ten sposób.

Następujące wyniki testu ad hoc Pokaż wydajności tego typu instrukcji insert w milisekundach.

| Operacje | Parametry z wartościami przechowywanymi w tabeli (ms) | Wstaw jednej instrukcji (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach czasu, w tym temacie](#note-about-timing-results-in-this-topic).
> 
> 

Ta metoda może być nieco większą partii, które są mniej niż 100 wierszy. Mimo że poprawy jest mały, Metoda ta jest inną opcją, która może działać również w danym scenariuszu określonej aplikacji.

### <a name="dataadapter"></a>Element DataAdapter
**Element DataAdapter** klasa umożliwia modyfikowanie **DataSet** obiekt, a następnie prześlij zmiany jako operacji INSERT, UPDATE i DELETE. Jeśli używasz **element DataAdapter** w ten sposób jest należy pamiętać, że oddzielne wywołań dla każdej operacji distinct. Aby zwiększyć wydajność, należy użyć **UpdateBatchSize** właściwości Liczba operacji, które należy umieścić w zadaniu wsadowym, w czasie. Aby uzyskać więcej informacji, zobacz [wykonywania wsadowego operacje przy użyciu obiektów DataAdapter](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Programu Entity framework
Entity Framework nie obsługuje obecnie przetwarzanie wsadowe. Różnych deweloperów w społeczności podjęto próbę rozwiązania, takie jak zastąpienie prezentacja **SaveChanges** metody. Ale rozwiązania są zwykle złożone i dostosowane do aplikacji i danych modelu. Projektu programu Entity Framework w witrynie codeplex ma obecnie strony dyskusji na żądanie tej funkcji. Aby wyświetlić te informacje, zobacz [spotkania uwagami - 2 sierpień 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Aby informacje były kompletne uważamy, że ważne jest, aby porozmawiać o XML jako strategii przetwarzanie wsadowe. Jednak użycie XML ma nie zalet w porównaniu z innych metod i kilka wady. Podejście jest podobny do parametrów przechowywanymi w tabeli, ale plik XML lub ciąg jest przekazany do procedury składowanej zamiast tabeli zdefiniowane przez użytkownika. Procedura składowana analizuje poleceń w procedurze składowanej.

Istnieje kilka wadą tego podejścia:

* Praca z danymi XML może być skomplikowane i błąd podatnych na błędy.
* Analiza kodu XML w bazie danych może być użycie Procesora CPU.
* W większości przypadków ta metoda jest mniejsza niż parametry przechowywanymi w tabeli.

Z tego względu nie jest zalecane używanie XML dla partii zapytań.

## <a name="batching-considerations"></a>Przetwarzanie wsadowe uwagi
Poniższe sekcje zawierają więcej wskazówek dotyczących stosowania przetwarzania wsadowego w aplikacjach baz danych SQL.

### <a name="tradeoffs"></a>Wady i zalety
W zależności od architektury przetwarzanie wsadowe może obejmować zależności między wydajność i odporność. Na przykład Rozważmy scenariusz, w którym rola użytkownika nieoczekiwanie ulegnie awarii. W przypadku utraty jednego wiersza danych, wpływ jest mniejszy niż wpływ utraty duży wsadu nieprzesłane wierszy. Istnieje większe ryzyko, gdy bufor wierszy przed wysłaniem ich do bazy danych w oknie określonego czasu.

Ze względu na to zależnościami jaki typ operacji tej partii należy. Bardziej agresywnie partii (większe partie i dłuższy czas systemu windows) przy użyciu danych jest mniej istotny.

### <a name="batch-size"></a>Rozmiar partii
Nasze testy nie było zwykle żadnych dodatkowych zalet fundamentalne dużych partie na mniejsze fragmenty. W rzeczywistości często tej części pola spowodowała wolniej niż przesyłanie dużych pojedyncza partia. Na przykład Rozważmy scenariusz, w którym chcesz wstawić 1000 wierszy. W poniższej tabeli przedstawiono, jak długo trwa używania parametrów przechowywanymi w tabeli, aby wstawić 1000 wierszy, gdy podzielona na mniejsze partie.

| Rozmiar partii | Liczba iteracji | Parametry z wartościami przechowywanymi w tabeli (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach czasu, w tym temacie](#note-about-timing-results-in-this-topic).
> 
> 

Widać najlepszą wydajność 1000 wierszy jest przesyłanie ich jednocześnie. W innych testach (nie jest tutaj widoczny) było bardziej wydajne małych Podziel partii 10000 wiersza na dwie partie 5000. Ale schemat tabeli dla tych testów jest stosunkowo proste, dlatego należy wykonać na określonych danych i rozmiary partii, aby sprawdzić te wyniki testów.

Innym czynnikiem do przeanalizowania jest Jeśli całkowita liczba partii stanie się zbyt duży, baza danych SQL może ograniczyć oraz odmówić można przekazać partii. Aby uzyskać najlepsze wyniki należy przetestować konkretnego scenariusza do określenia, czy rozmiar partii idealne. Rozmiar partii należy można skonfigurować w czasie wykonywania, aby włączyć szybkie dopasowania na podstawie wydajności lub błędy.

Ponadto równoważenie rozmiar partii z ryzyko związane z przetwarzanie wsadowe. Jeśli ma błędów przejściowych lub roli nie powiedzie się, należy wziąć pod uwagę skutków wykonania operacji lub utraty danych w partii.

### <a name="parallel-processing"></a>Przetwarzanie równoległe
Co zrobić, jeśli trwało podejście zmniejszania rozmiaru partii, ale używany do wykonywania pracy wiele wątków? Ponownie Nasze testy wykazały, czy partie mniejszych wielowątkowe najczęściej wykonywane gorsza niż pojedyncza partia większy. Następującego testu podejmuje próbę wstawienia 1000 wierszy w partii równoległych co najmniej jeden. Ten test pokazuje, jak więcej jednoczesnych partie faktycznie obniżenie wydajności.

| Rozmiar partii [iteracji] | Dwoma wątkami (ms) | Cztery wątków (ms) | Sześć wątków (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Wyniki nie są testy. Zobacz [należy pamiętać o wynikach czasu, w tym temacie](#note-about-timing-results-in-this-topic).
> 
> 

Istnieje kilka potencjalnych przyczyn spadek wydajności z powodu równoległości:

* Istnieje wiele wywołań awarią sieci, zamiast jednego.
* Wiele operacji na jednej tabeli może spowodować rywalizację i blokowania.
* Występują ogólne koszty związane z wielowątkowości.
* Wydatków otwarcia wielu połączeń przeważa nad korzyści przetwarzania równoległego.

W przypadku skierowania różnych tabelach lub baz danych, istnieje możliwość uzyskania z tej strategii wydajności w temacie. Dzielenia na fragmenty bazy danych lub federacje byłoby scenariusz dla tej metody. Dzielenia na fragmenty używa wielu baz danych i kieruje inne dane dla każdej bazy danych. Jeśli w każdej partii małych przechodzi do innej bazy danych, może być skuteczniejsza następnie operacji równolegle. Jednak bardziej wydajne nie jest istotne, do użycia jako podstawy do podjęcia decyzji do użycia w rozwiązaniu dzielenia na fragmenty bazy danych.

W niektórych projektach wykonywanie równoległe partii mniejszych może spowodować lepszą przepustowość żądań w systemie pod obciążeniem. W takim przypadku mimo że jest szybsze do przetworzenia pojedyncza partia większy, wiele instancji równoległego przetwarzania może być bardziej wydajne.

Jeśli używasz wykonywanie równoległe, należy wziąć pod uwagę kontrolowanie maksymalną liczbę wątków roboczych. Mniejsza liczba może spowodować mniej rywalizacji i skrócić czas wykonywania. Należy również rozważyć dodatkowe obciążenia, które ten zostaje umieszczony na docelowej bazy danych, zarówno w połączeń i transakcji.

### <a name="related-performance-factors"></a>Czynniki wydajności związanych z
Typowe wskazówki dotyczące wydajności bazy danych ma wpływ również na przetwarzanie wsadowe. Na przykład, Wstaw wydajność jest ograniczona do tabel, które mają duże kluczem podstawowym lub wiele nieklastrowanych indeksów.

Użycie procedury składowanej, parametry przechowywanymi w tabeli można użyć polecenia **SET NOCOUNT ON** na początku procedury. Ta instrukcja pomija zwracany liczby wierszy wykorzystywanych w procedurze. Jednak w przypadku stosowania testów **SET NOCOUNT ON** nie miała wpływu albo obniżenie wydajności. Procedury przechowywane testu została proste za pomocą jednej **Wstaw** polecenia z parametru przechowywanymi w tabeli. Istnieje możliwość, że bardziej złożonych procedur składowanych będzie korzystać z tej instrukcji. Ale nie zakłada się, że dodawanie **SET NOCOUNT ON** Twojego procedury składowanej automatycznie poprawia wydajność. Aby zrozumieć wpływ, należy przetestować procedurę składowaną z włączonymi i wyłączonymi **SET NOCOUNT ON** instrukcji.

## <a name="batching-scenarios"></a>Przetwarzanie wsadowe scenariuszy
W poniższych sekcjach opisano sposób używania parametrów przechowywanymi w tabeli w trzech scenariuszy aplikacji. Pierwszy scenariusz pokazuje, jak buforowanie i przetwarzanie wsadowe mogą współdziałać ze sobą. Drugi scenariusz umożliwia zwiększenie wydajności, wykonując operacje główny szczegółowy w wywołaniu jednej procedury składowanej. Końcowe scenariuszu pokazano, jak używać parametrów przechowywanymi w tabeli w operacji "UPSERT".

### <a name="buffering"></a>Buforowanie
Istnieje kilka scenariuszy, które są oczywiste candidate dla przetwarzania wsadowego, istnieje wiele scenariuszy, które można wykorzystać przetwarzania wsadowego przez przetwarzanie opóźnione. Opóźnione przetwarzania niesie również większe ryzyko, że dane zostaną utracone w przypadku nieoczekiwanego błędu. Należy zrozumieć to ryzyko i należy wziąć pod uwagę skutki.

Rozważmy na przykład aplikacji sieci web służący do śledzenia historii nawigacji dla każdego użytkownika. Na każdym żądaniu strony aplikacji można utworzyć bazy danych wywołania do rejestrowania widoku strony użytkownika. Jednak buforowanie działania nawigacji użytkowników, a następnie wysyła te dane do bazy danych w partiach można osiągnąć wyższą wydajność i skalowalność. Możesz wyzwolić aktualizacji bazy danych przez czas, który upłynął i/lub rozmiaru buforu. Reguły można na przykład określić, że partii powinny być przetwarzane po 20 sekund lub gdy bufor osiągnie 1000 elementów.

Poniższy przykład kodu wykorzystuje [rozszerzenia reaktywne - Rx](https://msdn.microsoft.com/data/gg577609) przetworzyć buforowanych zdarzeń zgłaszanych przez klasy monitorowania. Wypełnia buforu lub w osiągnięciu limitu czasu, partii danych użytkownika są wysyłane do bazy danych z parametrem przechowywanymi w tabeli.

Następujące klasy NavHistoryData modele szczegóły użytkownika nawigacji. Zawiera podstawowe informacje, takie jak identyfikator użytkownika, dostęp do adresu URL i czas dostępu.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

Klasa NavHistoryDataMonitor jest odpowiedzialny za buforowania danych nawigacji użytkownika do bazy danych. Zawiera metodę RecordUserNavigationEntry, która odpowiada za wywoływanie **OnAdded** zdarzeń. Poniższy kod przedstawia logikę konstruktora, używaną do tworzenia kolekcji zauważalne oparte na zdarzeniu odbierania. Następnie subskrybuje to zauważalne kolekcji z metodą buforu. Przeciążenie Określa, czy bufor mają być wysyłane co 20 sekund lub 1000 wpisów.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

Program obsługi konwertuje wszystkie buforowane elementy na typ przechowywanymi w tabeli, a następnie przekazuje tego typu do procedury składowanej, która przetwarza partii. Poniższy kod przedstawia pełnej definicji dla klasy NavHistoryDataMonitor i NavHistoryDataEventArgs.

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

        public void RecordUserNavigationEntry(NavHistoryData data)
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

Aby korzystać z tej klasy buforowania, aplikacja tworzy obiekt NavHistoryDataMonitor statycznych. Zawsze użytkownik uzyskuje dostęp do strony, aplikacja wywołuje metodę NavHistoryDataMonitor.RecordUserNavigationEntry. Przebieg logiki buforowania obsługę wysyłania te wpisy w bazie danych w partiach.

### <a name="master-detail"></a>Wzorzec szczegół
Parametry przechowywanymi w tabeli są przydatne w scenariuszach INSERT proste. Jednak może być trudniejsze do wstawiania wsadowego, obejmujące więcej niż jedna tabela. Scenariusz "wzorzec/szczegół" jest dobrym przykładem. Główny tabeli identyfikuje podstawowej jednostki. Co najmniej jedna tabela szczegółów przechowywać więcej danych dotyczących jednostki. W tym scenariuszu relacje klucza obcego wymusić relacji szczegóły, aby unikatowe jednostki wzorca. Należy wziąć pod uwagę uproszczonej wersji tabeli PurchaseOrder i jego skojarzonej tabeli OrderDetail. Języka Transact-SQL tworzy tabelę PurchaseOrder z czterech kolumn: OrderID, OrderDate CustomerID i stanu.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Każdej kolejności zawiera co najmniej jeden produkt. Te informacje są przechwytywane w tabeli PurchaseOrderDetail. Języka Transact-SQL tworzy PurchaseOrderDetail tabeli z kolumnami pięć: OrderID, OrderDetailID ProductID, UnitPrice i OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

Z tabeli PurchaseOrder OrderID kolumny w tabeli PurchaseOrderDetail musi odwoływać się zamówienia. Następujący definicji klucza obcego wymusza to ograniczenie.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Aby użyć parametrów przechowywanymi w tabeli, musi mieć jeden typ tabeli zdefiniowany przez użytkownika dla każdej tabeli docelowej.

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

Następnie zdefiniuj procedury przechowywanej, która akceptuje tabele tych typów. Ta procedura umożliwia aplikacji lokalnie partii zestaw zleceń i szczegółów zamówienia w pojedynczym wywołaniu. Języka Transact-SQL zawiera deklarację całą procedurę składowaną w ramach tego przykładu zamówienia zakupu.

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

W tym przykładzie zdefiniowane lokalnie @IdentityLink w tabeli są przechowywane na rzeczywiste wartości OrderID z nowo wstawionych wierszy. Te identyfikatory kolejności różnią się od wartości OrderID tymczasowe w @orders i @details parametry przechowywanymi w tabeli. Z tego powodu @IdentityLink połączy OrderID wartości z tabeli @orders parametru, do rzeczywistych wartości OrderID dla nowych wierszy w tabeli PurchaseOrder. Po wykonaniu tego kroku @IdentityLink tabeli może ułatwić wstawianie szczegółów zamówienia z OrderID rzeczywista, która spełnia ograniczenie klucza obcego.

Tę procedurę składowaną można z kodu lub inne wywołania języka Transact-SQL. Zobacz sekcję przechowywanymi w tabeli parametrów tego dokumentu, na przykład kodu. Języka Transact-SQL pokazuje sposób wywoływania sp_InsertOrdersBatch.

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

To rozwiązanie umożliwia każdej partii użyć zestawu OrderID wartości, które rozpoczynają się do 1. Te wartości tymczasowe OrderID opisano relacje w partii, ale na rzeczywiste wartości OrderID są określane w czasie operacji insert. Można cyklicznie uruchamiany tych samych instrukcji w poprzednim przykładzie i wygenerować unikatowy zamówienia w bazie danych. Z tego powodu należy rozważyć dodanie więcej logiki kodu lub bazy danych, która zapobiega zduplikowane zleceń za pomocą tej techniki partie.

W tym przykładzie pokazano, że można zebrać bardziej złożone operacje bazy danych, takimi jak operacje wzorzec szczegół za pomocą parametrów przechowywanymi w tabeli.

### <a name="upsert"></a>UPSERT
Inny przetwarzanie wsadowe scenariusz obejmuje jednoczesne aktualizowanie istniejących wierszy i wstawianie nowych wierszy. Ta operacja jest czasami nazywany operacji "UPSERT" (aktualizacja + insert). Zamiast oddzielnego wywołania do WSTAWIANIA i AKTUALIZOWANIA instrukcji MERGE jest najlepiej nadaje się do tego zadania. Instrukcji MERGE można wykonywać zarówno wstawiania i aktualizowania operacji w pojedynczym wywołaniu.

Przeprowadzić aktualizacje i wstawienia w instrukcji MERGE można użyć parametrów przechowywanymi w tabeli. Rozważmy na przykład uproszczony tabeli pracowników, który zawiera następujące kolumny: identyfikator pracownika, FirstName, LastName, SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))

W tym przykładzie można użyć fakt, że SocialSecurityNumber jest unikatowy do scalania wielu pracowników. Najpierw utwórz typu tabeli zdefiniowanego przez użytkownika:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Następnie utwórz procedurę składowaną lub pisania kodu, który używa instrukcji MERGE do aktualizowania i wstawiania. W poniższym przykładzie użyto instrukcji MERGE dla parametru przechowywanymi w tabeli, @employees, typu EmployeeTableType. Zawartość @employees tabeli nie są wyświetlane tutaj.

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

Aby uzyskać więcej informacji zawiera dokumentacja i przykłady dla instrukcji MERGE. Mimo że taką samą pracę może być wykonana w przechowywane wielu kroku wywołania procedury z oddzielne INSERT, a operacje aktualizacji, instrukcji MERGE jest bardziej wydajny. Kod bazy danych można także konstruować wywołania języka Transact-SQL, które bezpośrednio, bez konieczności dwóch wywołania bazy danych dla INSERT i UPDATE za pomocą instrukcji MERGE.

## <a name="recommendation-summary"></a>Zalecenie podsumowania
Poniższa lista zawiera podsumowanie zaleceń przetwarzanie wsadowe omówione w tym artykule:

* Użyj buforowania i przetwarzanie wsadowe, aby zwiększyć wydajność i skalowalność aplikacji bazy danych SQL.
* Zrozumienie skutków ubocznych między przetwarzanie wsadowe buforowania i odporność. Podczas awarii roli ryzyko utraty nieprzetworzone partii strategicznych danych biznesowych mogą przeważają korzyści wydajności przetwarzania wsadowego.
* Próbować zachować wszystkie wywołania do bazy danych w ramach jednego centrum danych do zmniejszenia opóźnienia.
* Jeśli wybierzesz pojedyncza technika przetwarzanie wsadowe parametry przechowywanymi w tabeli oferują najlepszą wydajność i elastyczność.
* Dla największą wydajność insert wykonaj następujące ogólne wytyczne, ale test danego scenariusza:
  * < 100 wierszy użyj jednego sparametryzowane polecenia INSERT.
  * < 1000 wierszy użyj parametrów przechowywanymi w tabeli.
  * Aby uzyskać > = 1000 wierszy, użyj SqlBulkCopy.
* Dla aktualizacji i operacji usunięcia, parametry przechowywanymi w tabeli za pomocą procedury składowanej logikę, która określa poprawne działanie na każdego wiersza w tabeli parametru.
* Informacje dotyczące rozmiaru partii:
  * Największy rozmiar partii, odpowiednich dla aplikacji i wymaganiami biznesowymi używany.
  * Równoważenie bardziej wydajne dużych partii z ryzyko tymczasowych lub poważnej awarii. Co to jest konsekwencją ponownych prób i utraty danych w partii? 
  * Przetestuj największy rozmiar partii, aby sprawdzić, czy baza danych SQL nie go odrzucić.
  * Utwórz ustawienia konfiguracji tej partii kontroli, takich jak rozmiar partii lub przedział czasu buforowania. Te ustawienia zapewniają elastyczność. Przetwarzanie wsadowe zachowanie w środowisku produkcyjnym można zmienić bez ponownego wdrożenia usługi w chmurze.
* Unikaj wykonywanie równoległe w partii, które pracują na pojedynczej tabeli w jednej bazie danych. Jeśli chcesz podzielić pojedyncza partia przez wiele wątków roboczych, należy uruchomić testy w celu ustalenia idealne liczba wątków. Po nieokreślone wartości progowej więcej wątków będzie obniżyć wydajność, a nie powinna ona być.
* Należy wziąć pod uwagę buforowanie włączone rozmiaru i czasu sposób stosowania przetwarzania wsadowego dla scenariuszy.

## <a name="next-steps"></a>Kolejne kroki
Ten artykuł koncentruje się na sposób projektowania baz danych i kodowania technik związanych z przetwarzanie wsadowe może poprawić Twojej aplikacji wydajności i skalowalności. Ale tylko jeden składnik w ogólnej strategii. Aby uzyskać więcej sposobów poprawy wydajności i skalowalności, zobacz [wytyczne dotyczące wydajności bazy danych SQL Azure dla pojedynczej bazy danych](sql-database-performance-guidance.md) i [zagadnienia dotyczące cen i wydajności dla elastycznej puli](sql-database-elastic-pool-guidance.md).

