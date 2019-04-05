---
title: Przewodnik Szybki start dotyczący korzystania z usług Machine Learning Services (z językiem R) w usłudze Azure SQL Database (wersja zapoznawcza) | Microsoft Docs
description: W tym temacie dowiesz się, jak używać usługi Machine Learning w usłudze Azure SQL Database i jak uruchamiać skrypty języka R w celu dostarczania zaawansowanych funkcji analitycznych na dużą skalę oraz możliwości wykonywania obliczeń i przetwarzania w miejscu, w którym znajdują się dane, eliminując konieczność ściągania danych przez sieć.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 04/04/2019
ms.openlocfilehash: f5d43794ba80e4791c8bf24ad404069a8329518e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051227"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>Szybki start: Korzystanie z usług Machine Learning Services (z językiem R) w usłudze Azure SQL Database (wersja zapoznawcza)

W tym artykule wyjaśniono, jak używać publicznej wersji zapoznawczej usługi [Machine Learning (przy użyciu języka R) w usłudze Azure SQL Database](sql-database-machine-learning-services-overview.md). Przeprowadzi Cię on przez podstawy przenoszenia danych między bazą danych SQL i środowiskiem języka R. Wyjaśnia również sposób opakowywania poprawnie sformułowanego kodu R w procedurze składowanej [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) w celu tworzenia, uczenia i używania modeli uczenia maszynowego w usłudze SQL Database.

Korzystaj z możliwości języka R, aby dostarczać zaawansowaną analizę i uczenie maszynowe w bazie danych. W ten sposób obliczenia i przetwarzanie przenoszone są do miejsca przechowywania danych, co eliminuje konieczność ściągania danych przez sieć. Ponadto wykorzystaj moc korporacyjnych pakietów języka R do dostarczania zaawansowanej analizy na dużą skalę.

Usługa Machine Learning Services obejmuje podstawową dystrybucję języka R z nakładką w postaci korporacyjnych pakietów języka R firmy Microsoft. Funkcje i algorytmy języka R firmy Microsoft zostały opracowane z myślą o skalowaniu i użyteczności, zapewniając analizę predykcyjną, modelowanie statystyczne, wizualizacje danych i najwyższej klasy algorytmy uczenia maszynowego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

> [!IMPORTANT]
> SQL Database usługi Azure Machine Learning jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Zarejestruj się, aby wypróbować wersję zapoznawczą](sql-database-machine-learning-services-overview.md#signup).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić przykładowy kod w tych ćwiczeniach, najpierw musisz mieć usługę SQL Database z włączonymi usługami Machine Learning Services (z językiem R). W okresie publicznej wersji zapoznawczej firma Microsoft dołączy Cię i włączy usługę Machine Learning dla Twojej istniejącej lub nowej bazy danych. Postępuj zgodnie z instrukcjami w części [Tworzenie konta na potrzeby korzystania z wersji zapoznawczej](sql-database-machine-learning-services-overview.md#signup).

Możesz nawiązać połączenie z usługą SQL Database i uruchamiać skrypty języka R za pomocą dowolnego narzędzia do zarządzania bazą danych lub obsługi zapytań, o ile może ono nawiązać połączenie z usługą SQL Database i uruchomić zapytanie lub procedurę składowaną T-SQL. W tym przewodniku Szybki start używany jest program [SQL Server Management Studio](sql-database-connect-query-ssms.md).

Na potrzeby ćwiczenia [dodawanie pakietu](#add-package) należy również zainstalować język [R](https://www.r-project.org/) i program [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) na komputerze lokalnym.

Ten przewodnik Szybki start wymaga również skonfigurowania reguły zapory na poziomie serwera. W celu zapoznania się z samouczkiem przedstawiający sposób wykonywania tej czynności zobacz [Create server-level firewall rule](sql-database-server-level-firewall-rule.md) (Tworzenie reguły zapory na poziomie serwera).

## <a name="verify-r-exists"></a>Sprawdzanie, czy środowisko języka R istnieje

Możesz sprawdzić, czy usługi Machine Learning Services (z językiem R) są włączone dla Twojej usługi SQL Database. Wykonaj poniższe kroki.

1. Otwórz program SQL Server Management Studio i nawiąż połączenie z usługą SQL Database. Aby uzyskać więcej informacji na temat nawiązywania połączenia, zobacz [Szybki start: Używanie programu SQL Server Management Studio do nawiązywania połączenia i wykonywania zapytań dotyczących danych w bazie danych SQL Azure](sql-database-connect-query-ssms.md).

1. Uruchom poniższy kod. 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    Jeśli wszystko jest dobrze, powinien zostać wyświetlony wynik podobny do poniższego.

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. Jeśli wystąpią błędy, może to być spowodowane tym, że publiczna wersja zapoznawcza usług Machine Learning Services (z językiem R) nie jest włączona dla usługi SQL Database. Zobacz powyżej, jak utworzyć konto na potrzeby korzystania z publicznej wersji zapoznawczej.

## <a name="grant-permissions"></a>Udzielenie uprawnień

Jeśli jesteś administratorem, automatycznie możesz uruchamiać kod zewnętrzny. Wszystkim innym użytkownikom musi zostać nadane uprawnienie.

Przed uruchomieniem polecenia zastąp zmienną `<username>` prawidłowym identyfikatorem logowania użytkownika w bazie danych.

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>Podstawowe interakcje języka R

Istnieją dwa sposoby uruchomienia kodu R w usłudze SQL Database:

+ Dodaj skrypt języka R jako argument systemowej procedury składowanej [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).
+ Ze [zdalnego klienta języka R](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client) nawiąż połączenie z bazą danych SQL Database, używając usługi SQL Database jako kontekstu obliczeniowego.

Następujące ćwiczenie koncentruje się na pierwszym modelu interakcji: sposobie przekazania kodu języka R do procedury składowanej.

1. Uruchom prosty skrypt, aby zobaczyć, jak skrypt języka R jest wykonywany w usłudze SQL Database.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

2. Przy założeniu, że wszystko jest skonfigurowane prawidłowo, obliczany jest poprawny wynik, a funkcja `print` języka R zwraca wynik do okna **Komunikaty**.

    **Wyniki**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    Pobieranie komunikatów **stdout** jest przydatne podczas testowania kodu, ale częściej musisz zwracać wyniki w formacie tabelarycznym, tak aby można ich było użyć w aplikacji lub zapisać w tabeli. Zobacz sekcję danych wejściowych i wyjściowych poniżej, aby uzyskać więcej informacji.

Należy pamiętać, że wszystko wewnątrz argumentu `@script` musi być prawidłowym kodem R.

## <a name="inputs-and-outputs"></a>Dane wejściowe i wyjściowe

Domyślnie procedura [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) akceptuje pojedynczy wejściowy zestaw danych, który zazwyczaj jest dostarczany w formie poprawnego zapytania SQL. Inne typy danych wejściowych mogą być przekazywane jako zmienne SQL.

Procedura składowana zwraca jako dane wyjściowe jedną ramkę danych języka R, ale mona również przesyłać w danych wyjściowych wartości skalarne i modele jako zmienne. Możesz na przykład przesłać dane wyjściowe uczonego modelu jako zmienną binarną i przekazać ją do instrukcji INSERT języka T-SQL, aby zapisać ten model w tabeli. Można również wygenerować wykresy (w formacie binarnym) lub wartości skalarne (pojedyncze wartości, takie jak data i godzina, czas, jaki upłynął do nauczenia modelu i tak dalej).

Na razie przyjrzyjmy się tylko domyślnym zmiennym wejściowym i wyjściowym procedury sp_execute_external_script: `InputDataSet` i `OutputDataSet`.

1. Utwórz małą tabelę danych testowych, uruchamiając następującą instrukcję języka T-SQL:

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    Po utworzeniu tabeli użyj następującej instrukcji, aby wysłać zapytanie do tabeli:
  
    ```sql
    SELECT * FROM RTestData
    ```

    **Wyniki**

    ![Zawartość tabeli RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

2. Możesz pobrać dane z tabeli jako dane wejściowe do skryptu języka R. Uruchom instrukcję poniżej. Pobiera ona dane z tabeli, wykonuje komunikację dwustronną za pośrednictwem środowiska uruchomieniowego języka R i zwraca wartości z nazwą kolumny *NewColName*.

    Dane zwrócone przez zapytanie są przekazywane do środowiska uruchomieniowego języka R, które zwraca dane do usługi SQL Database jako ramkę danych. Klauzula WITH RESULT SETS definiuje schemat zwracanych danych tabeli dla usługi SQL Database.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **Wyniki**

    ![Dane wyjściowe skryptu języka R, który zwraca dane z tabeli](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. Zmieńmy nazwy zmiennych wejściowych lub wyjściowych. Skrypt powyżej używał domyślnych nazw zmiennych danych wejściowych i wyjściowych _InputDataSet_ i _OutputDataSet_. Aby zdefiniować dane wejściowe skojarzone z _InputDatSet_, możesz użyć  *\@input_data_1* zmiennej.

    W tym skrypcie nazwy zmiennych danych wyjściowych i wejściowych dla procedury składowanej zostały zmienione na *SQL_out* i *SQL_in*:

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    Należy pamiętać, że w języku R jest rozróżniana wielkość liter, więc wielkość liter zmiennych wejściowych i wyjściowych w `@input_data_1_name` i `@output_data_1_name` musi być zgodna z tą w kodzie R w `@script`. 

    Ważne jest również kolejność parametrów. Podaj wymagane parametry  *\@input_data_1* i  *\@output_data_1* pierwsza, aby można było używać następujące parametry opcjonalne  *\@ input_data_1_name* i  *\@output_data_1_name*.

    Tylko jeden wejściowy zestaw danych może być przekazywany jako parametr i można zwrócić tylko jeden zestaw danych. Jednak można wywoływać inne zestawy danych z wewnątrz kodu języka R i można zwrócić dane wyjściowe innych typów oprócz zestawu danych. Możesz także dodać słowo kluczowe OUTPUT do dowolnego parametru, aby był zwracany z wynikami. 

    Instrukcja `WITH RESULT SETS` definiuje schemat dla danych, który jest używany w usłudze SQL Database. Należy podać typy danych kompatybilne z usługą SQL dla każdej kolumny zwracanej z języka R. Można również użyć definicji schematu, aby podać nowe nazwy kolumn, ponieważ nie trzeba używać nazw kolumn z ramki danych języka R.

4. Możesz również wygenerować wartości przy użyciu skryptu języka R i pozostawić pusty wejściowy ciąg zapytania w elemencie _@input_data_1_.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **Wyniki**

    ![Wyniki zapytania używającego @script jako danych wejściowych](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Sprawdzanie wersji języka R

Jeśli chcesz zobaczyć, która wersja języka R jest zainstalowana w usłudze SQL Database, wykonaj następujące czynności:

1. Uruchom poniższy skrypt w usłudze SQL Database.

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. Funkcja `print` języka R zwraca wersję do okna **Komunikaty**. W przykładowych danych wyjściowych poniżej możesz przekonać się, że usługa SQL Database w tym przypadku ma zainstalowaną wersję 3.4.4 języka R.

    **Wyniki**

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

Firma Microsoft udostępnia wiele pakietów języka R wstępnie instalowanych z usługami Machine Learning Services w usłudze SQL Database. Aby wyświetlić listę zainstalowanych pakietów języka R, w tym ich wersję, zależności, licencję i informacje o ścieżce biblioteki, wykonaj poniższe kroki. Aby dodać pakiety, zobacz sekcję [dodawanie pakietu](#add-package).

1. Uruchom poniższy skrypt w usłudze SQL Database.

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. Dane wyjściowe pochodzą z `installed.packages()` w języku R i są zwracane jako zestaw wyników.

    **Wyniki**

    ![Zainstalowane pakiety w języku R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="create-a-predictive-model"></a>Tworzenie modelu predykcyjnego

Możesz wyszkolić model przy użyciu języka R i zapisać model w tabeli w usłudze SQL Database. W tym ćwiczeniu wyszkolisz prosty model regresji, który przewiduje dystans potrzebny na zatrzymanie samochodu na podstawie prędkości. Użyjesz zestawu danych `cars` dołączonego do środowiska języka R, ponieważ jest mały i łatwy do zrozumienia.

1. Najpierw utwórz tabelę, aby zapisać dane uczenia.

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    Wiele zestawów danych, małych i dużych, jest dostępnych w środowisku uruchomieniowym języka R. Aby uzyskać listę zestawów danych zainstalowanych przy użyciu języka R, wpisz polecenie `library(help="datasets")` w wierszu polecenia języka R.

2. Tworzenie modelu regresji. Dane prędkości samochodu zawierają dwie kolumny, obie liczbowe: `dist` i `speed`. Istnieje wiele obserwacji niektórych prędkości. Na podstawie tych danych utworzysz model regresji liniowej, opisujący pewne relacje między prędkością samochodu a odległością wymaganą do zatrzymania samochodu.

    Wymagania modelu liniowego są proste:

   - Zdefiniuj formułę, która opisuje relację między zmienną zależną `speed` a zmienną niezależną `distance`.

   - Podaj dane wejściowe do użycia w uczenia modelu.

     > [!TIP]
     > Jeśli potrzebujesz przypomnienia informacji na temat modeli liniowych, zalecamy skorzystanie z tego samouczka, w którym opisano proces dopasowywania modelu przy użyciu funkcji rxLinMod: [Dopasowanie modeli liniowych](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

     Aby utworzyć model, definiujesz formułę wewnątrz kodu języka R i przekazujesz dane jako parametr wejściowy.

     ```sql
     DROP PROCEDURE IF EXISTS generate_linear_model;
     GO
     CREATE PROCEDURE generate_linear_model
     AS
     BEGIN
       EXEC sp_execute_external_script
       @language = N'R'
       , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
           trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
       , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
       , @input_data_1_name = N'CarsData'
       , @output_data_1_name = N'trained_model'
       WITH RESULT SETS ((model VARBINARY(max)));
     END;
     GO
     ```

     Pierwszy argument funkcji rxLinMod to parametr *formula*, który definiuje odległość jako zależną od prędkości. Dane wejściowe są przechowywane w zmiennej `CarsData`, która jest wypełniana przez zapytanie SQL. Jeśli nie przypiszesz konkretnej nazwy do swoich danych wejściowych, domyślną nazwą zmiennej będzie _InputDataSet_.

2. Następnie utworzysz tabelę, w której będzie przechowywany model, aby można go było ponownie szkolić lub użyć do prognozowania. Dane wyjściowe pakietu języka R, który tworzy model, są zazwyczaj **obiektem binarnym**. W związku z tym tabela musi udostępniać kolumnę typu **VARBINARY(max)**.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. Aby zapisać model, uruchom następującą instrukcję Transact-SQL w celu wywołania procedury składowanej, wygenerowania modelu i zapisania go w tabeli.

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    Należy pamiętać, że jeśli uruchomisz ten kod po raz drugi, otrzymasz następujący błąd:

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    Jedną z opcji pozwalających uniknąć tego błędu jest aktualizowanie nazwy dla każdego nowego modelu. Możesz na przykład zmienić nazwę na bardziej opisową i uwzględnić typ modelu, datę jego utworzenia i tak dalej.

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. Ogólnie rzecz biorąc, dane wyjściowe języka R z procedury składowanej [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) są ograniczone do jednej ramki danych.

    Jednak można zwracać dane wyjściowe innych typów, na przykład wartości skalarne, oprócz ramki danych.

    Załóżmy na przykład, że chcesz szkolić model, ale natychmiast wyświetlić tabelę współczynników z modelu. Możesz utworzyć tabelę współczynników jako główny zestaw wyników i zwrócić dane wyjściowe uczonego modelu w zmiennej SQL. Możesz od razu ponownie użyć modelu przez wywołanie zmiennej lub zapisać model do tabeli, jak pokazano poniżej.

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **Wyniki**

    ![Przeszkolony model z dodatkowymi danymi wyjściowymi](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>Przewidywanie

Użyj modelu utworzonego w poprzedniej sekcji, aby oceniać prognozy w odniesieniu do nowych danych. Aby wykonać _ocenianie_ przy użyciu nowych danych, pobierz jeden z przeszkolonych modeli z tabeli, a następnie wywołaj nowy zestaw danych, na którym mają być oparte przewidywania. Ocenianie to termin czasami używany w nauce o danych, oznaczający generowanie przewidywań, prawdopodobieństw lub innych wartości na podstawie nowych danych wprowadzanych do uczonego modelu.

1. Najpierw utwórz tabelę z nowymi danymi prędkości. Zwróć uwagę, że oryginalne dane szkoleniowe zatrzymują się na prędkości 25 mil na godzinę. To dlatego, że oryginalne dane były oparte na doświadczeniu z 1920 roku! Być może zastanawiasz się, jak długa byłaby droga zatrzymania samochodu z lat 1920, gdyby założyć, że mógłby jechać z prędkością 60 mil na godzinę lub nawet 100 mil na godzinę? Aby odpowiedzieć na to pytanie, należy podać kilka nowych wartości prędkości.

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    W tym przykładzie, ponieważ model jest oparty na algorytmie **rxLinMod** dostarczanego jako część pakietu **RevoScaleR**, należy wywołać funkcję [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict), a nie ogólną funkcję `predict` języka R.

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
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
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    Powyższy skrypt wykonuje następujące czynności:

   + Używa instrukcji SELECT, aby uzyskać pojedynczy model z tabeli, a następnie przekazuje go jako parametr wejściowy.

   + Po pobraniu modelu z tabeli wywołuje funkcję `unserialize` na modelu.

       > [!TIP] 
       > Zapoznaj się także z nowymi [funkcjami serializacji](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) dostarczanymi przez kolekcję funkcji RevoScaleR, które obsługują oceniania w czasie rzeczywistym.
   + Stosuje funkcję `rxPredict` z odpowiednimi argumentami do modelu i podaje nowe dane wejściowe.

   + W przykładzie funkcja `str` jest dodawana podczas fazy testowania, aby sprawdzić schemat danych zwracanych z języka R. Później można usunąć tę instrukcję.

   + Nazwy kolumn użytych w skrypcie języka R niekoniecznie są przekazywane do danych wyjściowych procedury składowanej. W tym miejscu po użyliśmy klauzuli WITH RESULTS, aby zdefiniować kilka nowych nazw kolumn.

     **Wyniki**

     ![Zestaw wyników dla przewidywania drogi zatrzymania](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

     Istnieje również możliwość użycia klauzuli [PREDICT w języku Transact-SQL](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) do wygenerowania przewidywanej wartości lub oceny na podstawie przechowywanego modelu.

<a name="add-package"></a>

## <a name="add-a-package"></a>Dodawanie pakietu

Jeśli zachodzi potrzeba użycia pakietu, który nie jest jeszcze zainstalowany w usłudze SQL Database, możesz zainstalować go za pomocą narzędzi [sqlmlutils](https://github.com/Microsoft/sqlmlutils). Wykonaj poniższe kroki, aby zainstalować pakiet.

1. Pobierz najnowszy plik zip **sqlmlutils** ze strony [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) na komputer lokalny. Nie musisz rozpakowywać pliku.

1. Jeśli nie masz zainstalowanego języka R, pobierz język R z witryny [www.r-project.org](https://www.r-project.org/) i zainstaluj go na komputerze lokalnym. Język R jest dostępny dla systemów Windows, macOS i Linux. W tym przykładzie używamy systemu Windows.

1. Najpierw zainstaluj pakiet **RODBCext**, który jest wstępnie wymaganym składnikiem dla narzędzia **sqlmlutils**. **RODBCext** instaluje również zależność pakietu **RODBC**. Otwórz **wiersz polecenia** i uruchom następujące polecenie:

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    Jeśli zostanie wyświetlony następujący błąd: „»R« nie jest rozpoznawany jako polecenie wewnętrzne lub zewnętrzne, program wykonywalny lub plik wsadowy”, prawdopodobnie oznacza to, że ścieżka do pliku R.exe nie jest uwzględniona w zmiennej środowiskowej **PATH** w systemie Windows. Przed uruchomieniem polecenia możesz dodać katalog do zmiennej środowiskowej lub przejść do katalogu w wierszu polecenia (na przykład `cd C:\Program Files\R\R-3.5.1\bin`).

1. Użyj polecenia **R CMD INSTALL**, aby zainstalować narzędzie **sqlmlutils**. Określ ścieżkę do katalogu, do którego został pobrany plik zip, oraz nazwę pliku zip. Na przykład:

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    Uzyskane dane wyjściowe będą podobne do następujących:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. W tym przykładzie użyjesz programu RStudio Desktop jako zintegrowanego środowiska projektowego (IDE). Jeśli wolisz, możesz użyć innego środowiska IDE. Pobierz i zainstaluj program RStudio Desktop ze strony [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/), jeśli nie masz jeszcze zainstalowanego programu RStudio.

1. Otwórz program **RStudio** i utwórz nowy plik **skryptu języka R**. Użyj poniższego kodu języka R, aby zainstalować pakiet przy użyciu sqlmlutils. W poniższym przykładzie zainstalujesz pakiet [pośredniczący](https://cran.r-project.org/web/packages/glue/), który można sformatować i interpolować ciąg.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > **Zakres** może być **PUBLICZNY** lub **PRYWATNY**. Zakres publiczny jest przydatny dla administratora bazy danych w celu zainstalowania pakietów, których mogą używać wszyscy użytkownicy. W przypadku zakresu prywatnego pakiet będzie dostępny tylko dla użytkownika, który go zainstalował. Jeśli nie określisz zakresu, domyślnym zakresem będzie zakres **PRYWATNY**.

1. Teraz upewnij się, że pakiet **pośredniczący** został zainstalowany.

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **Wyniki**

    ![Zawartość tabeli RTestData](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. Po zainstalowaniu pakietu można używać go w skrypcie języka R za pośrednictwem procedury **sp_execute_external_script**. Otwórz program **SQL Server Management Studio** i nawiąż połączenie z usługą SQL Database. Uruchom następujący skrypt:

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    Zobaczysz następujący wynik w karcie Komunikaty.

    **Wyniki**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. Jeśli chcesz usunąć pakiet, uruchom następujący skrypt języka R w programie **RStudio** na komputerze lokalnym.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> Innym sposobem zainstalowania pakietów języka R w usłudze SQL Database jest przekazanie pakietu języka R do strumienia bajtów za pomocą instrukcji [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Machine Learning Services, zobacz poniższe artykuły. Mimo że niektóre z tych artykułów dotyczą programu SQL Server, większość informacji ma również zastosowanie do usługi Machine Learning Services (z językiem R) w usłudze Azure SQL Database.

- [Usługi Azure SQL Database usługi Machine Learning (przy użyciu języka R)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Samouczek: Dowiedz się, analiza w bazie danych przy użyciu języka R w programie SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Wskazówki do nauki o danych end-to-end dla języków R i programu SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Samouczek: Używanie funkcji RevoScaleR R z danych programu SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
