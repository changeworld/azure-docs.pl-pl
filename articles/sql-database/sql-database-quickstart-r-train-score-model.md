---
title: Utwórz i wytrenuj model predykcyjny w języku R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Tworzenie prostego modelu predykcyjnego w języku R przy użyciu programu SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza), a następnie przewidywanie wyników, przy użyciu nowych danych.
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
ms.openlocfilehash: 97309a24c0ab12720f968409856a16cab4ff7ac7
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013254"
---
# <a name="create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Utwórz i wytrenuj model predykcyjny w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)

W tym przewodniku Szybki Start możesz będzie Utwórz i wytrenuj model predykcyjny przy użyciu języka R, Zapisz model do tabeli w usłudze SQL database, a następnie używania tego modelu do prognozowania wartości na podstawie nowych danych, przy użyciu publicznej wersji zapoznawczej [usługi Machine Learning (przy użyciu języka R) w usłudze Azure SQL Database ](sql-database-machine-learning-services-overview.md). 

Model który będzie używany w tym przewodniku Szybki Start jest model regresji prostego, który prognozuje odległość zatrzymywanie samochodu na podstawie szybkości. Użyjesz **samochodów** uwzględniony przy użyciu języka R, ponieważ jest mały i łatwa do zrozumienia zestawu danych.

> [!TIP]
> Wiele zestawów danych, małych i dużych, jest dostępnych w środowisku uruchomieniowym języka R. Aby uzyskać listę zestawów danych, instalowane przy użyciu języka R, wpisz `library(help="datasets")` z wiersza polecenia języka R.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

- Aby uruchomić przykładowy kod w tych ćwiczeń, najpierw musisz mieć usługi Azure SQL database przy użyciu usług Machine Learning (przy użyciu języka R) włączone. W okresie publicznej wersji zapoznawczej firma Microsoft dołączy Cię i włączy usługę Machine Learning dla Twojej istniejącej lub nowej bazy danych. Postępuj zgodnie z instrukcjami w części [Tworzenie konta na potrzeby korzystania z wersji zapoznawczej](sql-database-machine-learning-services-overview.md#signup).

- Upewnij się, że została zainstalowana najnowsza wersja [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Można uruchamiać skrypty języka R przy użyciu innych Zarządzanie bazą danych lub narzędzi do obsługi zapytań, ale w tym przewodniku Szybki Start użyjesz programu SSMS.

- Ten przewodnik Szybki Start wymaga, aby skonfigurować regułę zapory na poziomie serwera. Aby uzyskać informacje, jak to zrobić, zobacz [Utwórz regułę zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).

## <a name="create-and-train-a-predictive-model"></a>Utwórz i wytrenuj model predykcyjny

Dane szybkość samochodów w **samochodów** zestaw danych zawiera dwie kolumny, zarówno liczbowe: **dist** i **szybkość**. Dane obejmują wiele uwagi zatrzymywanie przy różnych prędkościach. Z tych danych utworzymy model regresji liniowej, który opisuje relację między szybkość car i odległość wymagany do zatrzymania samochód.

Wymagania modelu liniowego są proste:
- Zdefiniuj formułę, która opisuje relację między zmiennej zależnych od ustawień lokalnych *szybkość* i zmiennych niezależnych *odległość*.
- Podaj dane wejściowe do użycia w uczenia modelu.

> [!TIP]
> Jeśli potrzebujesz przypomnienia informacji na temat modeli liniowych, wypróbuj ten samouczek, w której opisano proces dopasowywania model przy użyciu rxLinMod: [Dopasowanie modeli liniowych](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

W poniższych krokach, które należy skonfigurować dane szkoleniowe utworzyć model regresji, uczyć je przy użyciu danych szkoleniowych, a następnie Zapisz model do tabeli SQL.

1. Otwórz program **SQL Server Management Studio** i nawiąż połączenie z usługą SQL Database.

   Jeśli potrzebujesz pomocy przy nawiązywaniu połączenia, zobacz [Szybki Start: Używanie programu SQL Server Management Studio do nawiązywania połączenia i wykonywania zapytań dotyczących danych w bazie danych Azure SQL Database](sql-database-connect-query-ssms.md).

1. Tworzenie **CarSpeed** tabeli, aby zapisać dane szkoleniowe.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Tworzenie modelu regresji przy użyciu `rxLinMod`. 

   Do tworzenia modelu Definiowanie formuły wewnątrz kodu języka R i następnie przekaż dane szkoleniowe **CarSpeed** jako parametr wejściowy.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     Pierwszy argument funkcji rxLinMod to parametr *formula*, który definiuje odległość jako zależną od prędkości. Dane wejściowe są przechowywane w zmiennej `CarsData`, która jest wypełniana przez zapytanie SQL.

1. Utwórz tabelę przechowywania modelu, można użyć w dalszej części do przewidywania. 

   Dane wyjściowe pakietu języka R, który tworzy model jest zazwyczaj **obiektu binarnego**, więc tabela musi zawierać kolumnę **VARBINARY(max)** typu.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Teraz wywołaniu procedury składowanej, Generowanie modelu i zapisz go na tabelę.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Należy pamiętać, że jeśli uruchomisz ten kod po raz drugi, otrzymasz następujący błąd:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Jedną opcję, aby uniknąć tego błędu jest aktualizacja nazwy dla każdego nowego modelu. Możesz na przykład zmienić nazwę na bardziej opisową i uwzględnić typ modelu, datę jego utworzenia i tak dalej.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Widok tabeli współczynników

Ogólnie rzecz biorąc, dane wyjściowe języka R z procedury składowanej [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) są ograniczone do jednej ramki danych. Jednak można zwracać dane wyjściowe innych typów, na przykład wartości skalarne, oprócz ramki danych.

Na przykład załóżmy, że chcesz do nauczenia modelu, ale natychmiast wyświetlić tabelę programu współczynnikach z modelu. Aby to zrobić, tworzysz tabelę programu współczynnikach główny wynik ustaw i danych wyjściowych uczonego modelu w zmiennej SQL. Można od razu ponownie użyć modelu przez wywołanie metody zmiennej lub modelu można zapisać do tabeli, jak pokazano poniżej.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Results**

![Przeszkolony model z dodatkowymi danymi wyjściowymi](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Oceniać nowe dane za pomocą uczonego modelu

*Ocenianie* to termin używany w zakresie analizy danych w sposób generowania przewidywań, prawdopodobieństwa lub innych wartości na podstawie nowych danych do uczonego modelu. Użyjemy modelu, który został utworzony w poprzedniej sekcji, aby oceniać prognozy w odniesieniu do nowych danych.

Zwróć uwagę, że oryginalne dane szkoleniowe zatrzymują się na prędkości 25 mil na godzinę. To dlatego, że oryginalne dane były oparte na doświadczeniu z 1920 roku! Być może zastanawiasz się, jak długo może upłynąć samochodów z 1920s zatrzymywane, jeśli można rozpocząć pracę tak szybko, jak 60 mil na godzinę lub nawet 100 mil na godzinę? Odpowiedzi na to pytanie, możesz podać niektóre nowe wartości szybkość do modelu.

1. Utwórz tabelę z nowymi danymi szybkości.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Przewidywanie zatrzymywanie odległości od tych nowych wartości szybkości.

   Ponieważ model jest oparta na **rxLinMod** algorytm w ramach **kolekcję funkcji RevoScaleR** pakietu, należy wywołać [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) funkcji zamiast ogólnych R `predict` funkcji.

   Ten przykładowy skrypt:
   - Używa instrukcji SELECT w celu uzyskania jednolitego modelu z tabeli
   - Przekazuje ją jako parametr wejściowy
   - Wywołania `unserialize` funkcji na podstawie modelu
   - Stosuje `rxPredict` funkcji z argumentami odpowiedniego do modelu
   - Udostępnia nowe dane wejściowe

   > [!TIP]
   > Ocenianie w czasie rzeczywistym dla [funkcji serializacji](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) dostarczone przez kolekcję funkcji RevoScaleR.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Results**

   ![Zestaw wyników dla przewidywania drogi zatrzymania](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> W tym skrypcie przykładowym `str` funkcji zostanie dodany podczas fazy testowania, aby sprawdzić schemat danych zwracanych z językiem R. Można usunąć, aby wykonać zapytanie później.
>
> Nazwy kolumn użytych w skrypcie języka R niekoniecznie są przekazywane do danych wyjściowych procedury składowanej. W tym miejscu klauzula wyników z definiuje kilka nowych nazw kolumn.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Machine Learning Services, zobacz poniższe artykuły. Mimo że niektóre z tych artykułów dotyczą programu SQL Server, większość informacji ma również zastosowanie do usługi Machine Learning Services (z językiem R) w usłudze Azure SQL Database.

- [Azure SQL Database Machine Learning Services (z językiem R)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Samouczek: Analiza w bazie danych przy użyciu języka R w programie SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Kompleksowy przewodnik po nauce o danych dla języka R i programu SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Samouczek: Używanie funkcji RevoScaleR z danymi programu SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
