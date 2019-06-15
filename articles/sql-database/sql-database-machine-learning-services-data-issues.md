---
title: Praca z języków R i SQL typów danych i obiektów
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dowiedz się, jak pracować z typów danych i obiektów danych w języku R w usłudze Azure SQL Database przy użyciu usług Machine Learning (wersja zapoznawcza), łącznie z typowych problemów, które można napotkać.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 01d3af14963e92393d34a952bddc8097b7b08f18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232613"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Praca z danymi SQL i języka R w SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)

W tym artykule omówiono niektóre typowe problemy, które można napotkać podczas przenoszenia danych między języków R i bazy danych SQL w [usługi Machine Learning (przy użyciu języka R) w usłudze Azure SQL Database](sql-database-machine-learning-services-overview.md). Środowisko, w których można uzyskiwać za pośrednictwem tego ćwiczenia zapewnia podstawowe tła podczas pracy z danymi w własnego skryptu.

Typowe problemy, które możesz napotkać obejmują:

- Typy danych czasami nie są zgodne
- Niejawne konwersje może mieć miejsce
- Operacje rzutowania i przekonwertować czasami są wymagane
- Języki R i SQL za pomocą różnych danych obiektów

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

- Aby uruchomić przykładowy kod w tych ćwiczeń, najpierw musisz mieć usługi Azure SQL database przy użyciu usług Machine Learning (przy użyciu języka R) włączone. W okresie publicznej wersji zapoznawczej firma Microsoft dołączy Cię i włączy usługę Machine Learning dla Twojej istniejącej lub nowej bazy danych. Postępuj zgodnie z instrukcjami w części [Tworzenie konta na potrzeby korzystania z wersji zapoznawczej](sql-database-machine-learning-services-overview.md#signup).

- Upewnij się, że została zainstalowana najnowsza wersja [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Można uruchomić skrypty języka R przy użyciu innych Zarządzanie bazą danych lub narzędzi do obsługi zapytań, ale w tym przewodniku Szybki Start użyjesz programu SSMS.

## <a name="working-with-a-data-frame"></a>Praca z danym

Gdy skrypt zwraca wyniki z języka R do bazy danych SQL, aplikacja musi zwracać dane jako **data.frame**. Inny rodzaj obiektu, generowany w skrypcie — czy, listy, współczynnika, vector lub dane binarne - muszą zostać skonwertowane do ramki danych, aby wyprowadzić dane jako część wyników procedury składowanej. Na szczęście są wiele funkcji języka R do obsługi zmiany innych obiektów do ramki danych. Możesz nawet serializowanie modelu binarnego umożliwiający i przywrócić go w ramce danych odbywa się w dalszej części tego artykułu.

Najpierw Przyjrzyjmy eksperymentować z niektórych podstawowych obiektów R - wektorów, macierzach i listy — i zobacz, jak konwersja na ramce danych zmienia się dane wyjściowe przekazane do bazy danych SQL.

Porównanie tych dwóch skryptów "Hello World" w języku R. Skrypty Szukaj niemal identyczne, ale pierwsza zwraca jednokolumnową z trzech wartości, natomiast druga zwraca trzy kolumny za pomocą pojedynczej wartości każdego.

**Przykład 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Przykład 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Wyniki są więc różne

Zwykle można znaleźć odpowiedzi przy użyciu języka R `str()` polecenia. Dodaj funkcję `str(object_name)` dowolne miejsce w skrypcie języka R, danych schematu określony obiekt R zwracane jako komunikat informacyjny. Można wyświetlić komunikaty w **wiadomości** kartę w programie SSMS.

Aby ustalić, dlaczego przykład 1 i 2 przykład mają takie różne wyniki, Wstaw wiersz `str(OutputDataSet)` na końcu `@script` definicji zmiennej w każdej instrukcji w następujący sposób:

**Przykład 1 za pomocą funkcji str dodane**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Przykład 2 za pomocą funkcji str dodane**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Teraz, Przejrzyj tekst w **wiadomości** aby zobaczyć, dlaczego dane wyjściowe są różne.

**Wyniki — przykład 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Wyniki — przykład 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Jak widać, to niewielkie zmiany w składni języka R ma duży wpływ na schemacie wyniki. Wszystkie szczegółowe informacje, różnice w typów danych języka R są wyjaśnione w szczegółowe informacje w *struktur danych* sekcji ["Zaawansowane R" przez Hadley Wickham](http://adv-r.had.co.nz).

Teraz po prostu należy pamiętać, trzeba sprawdzić oczekiwanych wyników, gdy coercing — obiekty języka R do ramki danych.

> [!TIP]
> Pozwala też R tożsamości funkcje, takie jak `is.matrix`, `is.vector`, aby zostały zwrócone informacje na temat struktury danych wewnętrznych.

## <a name="implicit-conversion-of-data-objects"></a>Niejawna konwersja obiektów danych

Każdy obiekt danych języka R ma własne reguły jak wartości są obsługiwane w połączeniu z innymi obiektami danych, jeśli obiekty dwóch danych mają taką samą liczbę wymiarów, lub jeśli dowolny obiekt danych zawiera typy danych heterogenicznych.

Na przykład załóżmy, że chcesz wykonać mnożenie macierzy za pomocą języka R. Chcesz mnożenie macierzy jedna kolumna z trzech wartości przez tablicę przy użyciu czterech wartości i w rezultacie oczekiwać macierzy 4 x 3.

Najpierw utwórz małej tabeli danych testowych.

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

Teraz uruchom poniższy skrypt.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

Dzieje się w tle kolumny z trzech wartości jest konwertowana na pojedynczej kolumny macierzy. Ponieważ macierz jest szczególnym przypadkiem tablicy w języku R, tablicy `y` jest niejawnie to tego zmusić macierzy jednokolumnową się dwa argumenty, które są zgodne.

**Results**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Pamiętaj jednak, co się stanie, gdy zmieniasz rozmiar tablicy `y`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Teraz R zwraca pojedynczą wartość jako wynik.

**Results**
    
|Col1|
|---|
|1542|

Dlaczego? W tym przypadku ponieważ dwa argumenty mogą być obsługiwane jako wektory tę samą długość, R Zwraca iloczyn wewnętrzny jako macierzy.  Jest to oczekiwane zachowanie zgodnie z regułami algebry liniowej. Jednak może spowodować problemy, jeśli Twoja aplikacja oczekuje schemat danych wyjściowych, które nigdy nie zmieni się!

## <a name="merge-or-multiply-columns-of-different-length"></a>Scal lub mnożenia kolumn o innej długości

R zapewnia dużą elastyczność w stosowaniu wektorów, o różnych rozmiarach i łącząc te struktury jak kolumny w ramki danych. Listami kierunków może wyglądać w tabeli, ale ich nie są zgodne z wszystkich reguł, które określają sposób tabel bazy danych.

Na przykład, poniższy skrypt definiuje tablicy liczbowej o długości 6 i zapisuje ją w zmiennej R `df1`. Tablicy liczbowej są następnie łączone w liczbach całkowitych tabeli RTestData (utworzonego powyżej) zawierający trzy (3) wartości, można wprowadzić nową ramkę danych `df2`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Aby wypełnić ramki danych, języka R jest powtarzany elementy pobierane RTestData tyle razy, zgodnie z potrzebami odpowiadać liczbie elementów w tablicy `df1`.

**Results**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Należy pamiętać, ramkę danych tylko przypomina tabelę, że jest faktycznie lista wektorów.

## <a name="cast-or-convert-sql-data"></a>Polecenie CAST lub convert danych SQL

SQL i języka R nie należy używać tych samych typów danych, więc po uruchomieniu zapytania w języku SQL, aby pobrać dane, a następnie przekaż go do środowiska wykonawczego języka R, niektóre typu niejawna konwersja zwykle odbywa się. Inny zbiór konwersje odbywa się po powrocie z języka R do bazy danych SQL.

- SQL wypycha dane z zapytania do procesu języka R i konwertuje je do wewnętrznej reprezentacji zapewnia im większą wydajność.
- Środowisko uruchomieniowe języka R powoduje załadowanie danych do zmiennej data.frame i wykonuje operacjach na danych.
- Aparat bazy danych zwraca dane do bazy danych SQL przy użyciu bezpiecznego połączenia wewnętrzne i przedstawia dane pod kątem typy danych SQL.
- Można pobrać danych przez podłączenie do bazy danych SQL przy użyciu biblioteki klienta lub sieci, który może wysyłać zapytania SQL i obsługi zestawów danych tabelarycznych. Ta aplikacja kliencka może wpłynąć na dane w inny sposób.

Aby zobaczyć, jak to działa, należy uruchomić zapytania, taką jak ta na [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) hurtowni danych. Ten widok zwraca dane sprzedaży, używanego podczas tworzenia prognoz.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Można użyć dowolnej wersji AdventureWorks, lub utworzyć inne zapytanie, za pomocą własną bazę danych. Punkt jest próbować obsłużyć części danych, które zawiera tekst, daty i godziny oraz wartości liczbowych.

Teraz spróbuj przy użyciu tego zapytania jako dane wejściowe dla procedury składowanej.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Jeśli wystąpi błąd, prawdopodobnie musisz wprowadzić pewne zmiany tekstu zapytania. Na przykład predykat ciągu w klauzuli WHERE muszą być ujęte w dwóch zestawach znaki pojedynczego cudzysłowu.

Po otrzymaniu kwerendy Praca Przejrzyj wyniki `str` funkcji, aby sprawdzić, jak R traktuje dane wejściowe.

**Results**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Kolumna daty i godziny został przetworzony przy użyciu języka R typu danych, **POSIXct**.
- Kolumny tekstowej "ProductSeries" została zidentyfikowana jako **współczynnik**, co oznacza zmienną podzielonych na kategorie. Wartości ciągu są obsługiwane jako czynniki domyślnie. W przypadku przekazania parametrów do języka R, jest konwertowany na liczbę całkowitą do użytku wewnętrznego i następnie mapowane z powrotem na ciąg w danych wyjściowych.

## <a name="summary"></a>Podsumowanie

W tych krótkie przykłady znajdują się trzeba sprawdzić skutków Konwersja danych podczas przekazywania SQL zapytania jako dane wejściowe. Ponieważ niektóre typy danych SQL nie są obsługiwane przez języka R, należy wziąć pod uwagę te metody, aby uniknąć błędów:

- Twoje dane testowe z wyprzedzeniem i sprawdź kolumn lub wartości w schemacie, może to być problem podczas przekazywania do kodu R.
- Określ kolumny w źródle danych wejściowych pojedynczo, zamiast używania `SELECT *`i sposób obsługi poszczególnych kolumn.
- Wykonaj jawnych rzutowań, zgodnie z potrzebami, przygotowując swoje dane wejściowe, aby uniknąć niespodzianek.
- Unikaj kolumn przekazywanie danych (np. identyfikatory GUID lub identyfikatorami ROWGUID), które powodują błędy i nie są przydatne w przypadku modelowania.

Aby uzyskać więcej informacji na temat obsługiwanych i nieobsługiwanych typów danych języka R, zobacz [R bibliotek i typy danych](/sql/advanced-analytics/r/r-libraries-and-data-types).
