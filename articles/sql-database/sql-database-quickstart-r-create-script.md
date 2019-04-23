---
title: Utworzyć i uruchomić proste skrypty języka R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Uruchomić proste skrypty języka R w usłudze Azure SQL Database usług Machine Learning (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: ada09959391c551a9eff4d96b186be29c1e3b7a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013270"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Utworzyć i uruchomić proste skrypty języka R w usłudze Azure SQL Database usług Machine Learning (wersja zapoznawcza)

W tym przewodniku Szybki Start utworzysz i uruchomienia zestawu prostych skryptów języka R za pomocą publicznej wersji zapoznawczej [usługi Machine Learning (przy użyciu języka R) w usłudze Azure SQL Database](sql-database-machine-learning-services-overview.md). Dowiesz się, jak opakowywać sformułowany skrypt języka R w procedurze składowanej [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) i uruchom skrypt w usłudze SQL database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

- Aby uruchomić przykładowy kod w tych ćwiczeń, najpierw musisz mieć usługi Azure SQL database przy użyciu usług Machine Learning (przy użyciu języka R) włączone. W okresie publicznej wersji zapoznawczej firma Microsoft dołączy Cię i włączy usługę Machine Learning dla Twojej istniejącej lub nowej bazy danych. Postępuj zgodnie z instrukcjami w części [Tworzenie konta na potrzeby korzystania z wersji zapoznawczej](sql-database-machine-learning-services-overview.md#signup).

- Upewnij się, że została zainstalowana najnowsza wersja [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Można uruchomić skrypty języka R przy użyciu innych Zarządzanie bazą danych lub narzędzi do obsługi zapytań, ale w tym przewodniku Szybki Start użyjesz programu SSMS.

- Ten przewodnik Szybki Start wymaga, aby skonfigurować regułę zapory na poziomie serwera. Aby uzyskać informacje, jak to zrobić, zobacz [Utwórz regułę zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Uruchom to prosty skrypt

Aby uruchomić skrypt języka R, będzie ona przekazać jako argument — w tym procedury składowane w systemie [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

W poniższych krokach będzie Uruchom ten skrypt przykładowy języka R w usłudze SQL database:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Otwórz program **SQL Server Management Studio** i nawiąż połączenie z usługą SQL Database.

   Jeśli potrzebujesz pomocy przy nawiązywaniu połączenia, zobacz [Szybki Start: Używanie programu SQL Server Management Studio do nawiązywania połączenia i wykonywania zapytań dotyczących danych w bazie danych Azure SQL Database](sql-database-connect-query-ssms.md).

1. Kompletny skrypt języka R w celu przekazania [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedury składowanej.

   Skrypt jest przekazywana `@script` argumentu. Wszystko wewnątrz `@script` argument musi być prawidłowym kodem R.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Jeśli wystąpią błędy, może to być spowodowane tym, że publiczna wersja zapoznawcza usług Machine Learning Services (z językiem R) nie jest włączona dla usługi SQL Database. Zobacz [wymagania wstępne](#prerequisites) powyżej.

   > [!NOTE]
   > Jeśli jesteś administratorem, możesz uruchomić kod zewnętrzny automatycznie. Można udzielić uprawnienia innym użytkownikom przy użyciu polecenia:
   <br>**UDZIEL wykonania dowolnego zewnętrznego skryptu do**  *\<username\>*.

2. Prawidłowego wyniku jest obliczany i R `print` funkcja zwraca wynik, który ma **wiadomości** okna.

   powinno to wyglądać mniej więcej tak.

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Uruchom skrypt Hello World

Skrypt typowym przykładem jest taki, który po prostu wyświetla ciąg "Hello World". Uruchom następujące polecenie.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Dane wejściowe tej procedury składowanej obejmują:

| | |
|-|-|
|*@language* | Określa rozszerzenie języka do wywołania w tym przypadku języka R |
|*@script* | definiuje polecenia przekazywane do środowiska wykonawczego języka R. Cały skrypt języka R muszą być ujęte w tym argumencie jako tekst w formacie Unicode. Można także dodać go do zmiennej typu **nvarchar** , a następnie wywołać zmiennej |
|*@input_data_1* | danych zwróconych przez zapytanie, przekazana do środowiska uruchomieniowego języka R, która zwraca dane do programu SQL Server jako ramkę danych |
|ZA POMOCĄ ZESTAWÓW WYNIKÓW | Klauzula definiuje schemat tabeli zwracanych danych dla programu SQL Server, dodając "Hello World" jako nazwa kolumny **int** dla typu danych |

Polecenie wyświetla następujący tekst:

| Witaj, świecie |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Używaj danych wejściowych i danych wyjściowych

Domyślnie [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) akceptuje jednego zestawu danych jako dane wejściowe, które zazwyczaj podanie w formularzu poprawnym zapytaniem SQL. Następnie zwraca jedną ramkę danych języka R jako dane wyjściowe.

Tylko jeden wejściowy zestaw danych może być przekazywany jako parametr i można zwrócić tylko jeden zestaw danych. Jednak można wywoływać inne zestawy danych z wewnątrz kodu języka R i można zwrócić dane wyjściowe innych typów oprócz zestawu danych. Możesz także dodać słowo kluczowe OUTPUT do dowolnego parametru, aby był zwracany z wynikami.

Na razie Przyjrzyjmy Użyj domyślne dane wejściowe i wyjściowe zmienne [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** i **OutputDataSet**.

1. Utwórz tabelę małych danych testowych.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Użyj `SELECT` instrukcję, aby odpytać tabelę.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Zawartość tabeli RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

1. Uruchom poniższy skrypt języka R. Pobiera dane z tabeli przy użyciu `SELECT` instrukcję, przechodzi środowiskiem uruchomieniowym języka r. i zwraca dane jako ramkę danych. `WITH RESULT SETS` Klauzuli definiuje schemat tabeli zwracanych danych usługi SQL Database, dodanie nazwy kolumny *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Dane wyjściowe skryptu języka R, który zwraca dane z tabeli](./media/sql-database-connect-query-r/r-output-rtestdata.png)

1. Teraz zmienimy nazwy zmiennych wejściowych i wyjściowych. Domyślnie dane wejściowe i dane wyjściowe w nazwach zmiennych jest **InputDataSet** i **OutputDataSet**, ten skrypt umożliwia zmianę nazwy **SQL_in** i **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Należy pamiętać, że R jest rozróżniana wielkość liter. Zmienne wejściowe i wyjściowe, używane w skrypcie języka R (**SQL_out**, **SQL_in**) muszą być zgodne z wartościami zdefiniowanymi za pomocą `@input_data_1_name` i `@output_data_1_name`, w tym przypadku.

   > [!TIP]
   > Tylko jeden wejściowy zestaw danych może być przekazywany jako parametr i można zwrócić tylko jeden zestaw danych. Jednak można wywoływać inne zestawy danych z wewnątrz kodu języka R i można zwrócić dane wyjściowe innych typów oprócz zestawu danych. Możesz także dodać słowo kluczowe OUTPUT do dowolnego parametru, aby był zwracany z wynikami.

1. Możesz również generować wartości tylko przy użyciu skryptu języka R bez danych wejściowych (`@input_data_1` jest ustawiona na wartość pustą).

   Poniższy skrypt generuje tekst "hello" i "world".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Results**

    ![Wyniki zapytania używającego @script jako danych wejściowych](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Sprawdzanie wersji języka R

Jeśli chcesz wyświetlić wersję języka r zainstalowanego w usłudze SQL database, uruchom następujący skrypt.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Funkcja `print` języka R zwraca wersję do okna **Komunikaty**. Na przykład danych wyjściowych poniżej widać, że usługa SQL Database w tym przypadku ma R version 3.4.4 zainstalowane.

**Results**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>Pakiety języka R

Firma Microsoft udostępnia wiele pakietów języka R wstępnie instalowanych z usługami Machine Learning Services w usłudze SQL Database.

Aby wyświetlić listę r, które pakiety są instalowane, łącznie z wersją, zależności, licencji i informacje o ścieżce biblioteki, uruchom następujący skrypt.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

Dane wyjściowe pochodzą z `installed.packages()` w języku R i jest zwracana w wyniku zestawu.

**Results**

![Zainstalowane pakiety w języku R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="next-steps"></a>Kolejne kroki

Aby utworzyć model uczenia maszynowego przy użyciu języka R w usłudze SQL Database, postępuj zgodnie z tego przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Utwórz i wytrenuj model predykcyjny w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-quickstart-r-train-score-model.md)

Aby uzyskać więcej informacji na temat usługi Machine Learning Services, zobacz poniższe artykuły. Mimo że niektóre z tych artykułów dotyczą programu SQL Server, większość informacji ma również zastosowanie do usługi Machine Learning Services (z językiem R) w usłudze Azure SQL Database.

- [Azure SQL Database Machine Learning Services (z językiem R)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Samouczek: Analiza w bazie danych przy użyciu języka R w programie SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Kompleksowy przewodnik po nauce o danych dla języka R i programu SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Samouczek: Używanie funkcji RevoScaleR z danymi programu SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
