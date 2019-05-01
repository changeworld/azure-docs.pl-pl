---
title: Dodaj pakiet języka R do SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W tym artykule wyjaśniono, jak zainstalować pakiet języka R, który nie jest już zainstalowany w SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: 4e7145570cbc906ea540c9d8f95f6c3cbde1c610
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927100"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Dodaj pakiet języka R do SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)

W tym artykule wyjaśniono, jak dodać pakiet języka R do SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj [R](https://www.r-project.org) i [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) na komputerze lokalnym. Język R jest dostępny dla systemów Windows, macOS i Linux. W tym artykule przyjęto założenie, że używasz Windows.

- W tym artykule przedstawiono przykład użycia [usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) do języka R do uruchomienia skryptu w usłudze Azure SQL Database. Można uruchomić skrypty języka R przy użyciu innych Zarządzanie bazą danych lub narzędzi do obsługi zapytań, ale w tym przykładzie przyjęto założenie, Studio danych platformy Azure lub programu SSMS.
   
> [!NOTE]
> Nie można zainstalować pakietu, uruchamiając skrypt języka R za pomocą **sp_execute_external_script** w Studio danych platformy Azure lub programu SSMS. Tylko można zainstalować i usuwanie pakietów przy użyciu wiersza polecenia języka R i programu RStudio zgodnie z opisem w tym artykule. Po zainstalowaniu pakietu można uzyskać dostęp do funkcji pakietu skryptu języka R za pomocą **sp_execute_external_script**.

## <a name="list-r-packages"></a>Pakiety języka R

Firma Microsoft udostępnia kilka pakietów języka R, wstępnie zainstalowane przy użyciu usług Machine Learning w usłudze Azure SQL database.
Można wyświetlić listę zainstalowanych pakietów języka R, uruchamiając następujące polecenie w Studio danych platformy Azure lub programu SSMS.

1. Otwórz Studio danych platformy Azure lub programu SSMS i Połącz z usługi Azure SQL Database.

1. Uruchom następujące polecenie:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

Dane wyjściowe powinny wyglądać podobnie do poniższego.

**Results**

![Zainstalowane pakiety w języku R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Dodaj pakiet z sqlmlutils

Jeśli zachodzi potrzeba użycia pakietu, który nie jest już zainstalowany w usłudze Azure SQL Database, możesz zainstalować go za pomocą [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** pakiet opracowanym w celu ułatwienia użytkownikom interakcję z bazami danych SQL (SQL Server i usługi Azure SQL Database) i wykonać kodem R lub Python w usłudze SQL z klienta R lub Python. Obecnie tylko R wersję **sqlmlutils** jest obsługiwana w usłudze Azure SQL Database.

W poniższym przykładzie nastąpi instalacja **[pośredniczącego](https://cran.r-project.org/web/packages/glue/)** pakiet, który można formatować i interpolacji ciągów. Te kroki instalowania **sqlmlutils** i **RODBCext** (wymagane w przypadku **sqlmlutils**) i Dodaj **pośredniczącego** pakietu.

### <a name="install-sqlmlutils"></a>Zainstaluj **sqlmlutils**

1. Pobierz najnowszy **sqlmlutils** pliku zip z https://github.com/Microsoft/sqlmlutils/tree/master/R/dist na komputerze lokalnym. Nie ma potrzeby Rozpakuj plik.

1. Otwórz **polecenia** i uruchom następujące polecenia, aby zainstalować **RODBCext** i **sqlmlutils** na komputerze lokalnym. Zastąp pełną ścieżkę do **sqlmlutils** pliku zip pobranego (w przykładzie założono, plik znajduje się w folderze Moje dokumenty).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Widoczne dane wyjściowe powinny być podobne do następujących.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Jeśli zostanie wyświetlony błąd "" R"nie jest rozpoznawana jako polecenie wewnętrzne lub zewnętrzne, program wykonywalny lub plik wsadowy", prawdopodobnie oznacza to, że ścieżka do R.exe nie jest uwzględniony w swojej **ścieżki** zmiennej środowiskowej na Windows. Możesz dodać ścieżkę do zmiennej środowiskowej lub przejdź do folderu, w wierszu polecenia (na przykład `cd C:\Program Files\R\R-3.5.3\bin`), a następnie spróbuj ponownie wykonać polecenie.

### <a name="add-the-package"></a>Dodaj pakiet

1. Otwórz program RStudio i Utwórz nowy **skrypt języka R** pliku. 

1. Użyj poniższego kodu języka R do zainstalowania **pośredniczącego** pakietu przy użyciu **sqlmlutils**. Zastąp swoje własne informacje o połączeniu usługi Azure SQL Database.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **Zakres** może być **publicznych** lub **PRYWATNEJ**. Zakres publiczny jest przydatny dla administratora bazy danych w celu zainstalowania pakietów, których mogą używać wszyscy użytkownicy. Zakres prywatnych sprawia, że pakiet jest dostępne tylko dla użytkownika, który instaluje go. Jeśli nie określisz zakresu, domyślnym zakresem będzie zakres **PRYWATNY**.

### <a name="verify-the-package"></a>Sprawdź pakiet

Upewnij się, że **pośredniczącego** pakiet został zainstalowany, uruchamiając poniższy skrypt języka R w programu RStudio. Użyto tych samych **połączenia** zdefiniowane w poprzednim kroku.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Results**

![Zawartość tabeli RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Za pomocą pakietu

Po zainstalowaniu pakietu może używać go w skrypcie języka R za pośrednictwem **sp_execute_external_script**.

1. Otwórz Studio danych platformy Azure lub programu SSMS i Połącz z usługi Azure SQL Database.

1. Uruchom następujące polecenie:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
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

    Zostaną wyświetlone następujące wyniki w **wiadomości** kartę.

    **Results**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Usuń pakiet

Jeśli chcesz usunąć pakiet, należy uruchomić poniższy skrypt języka R w programu RStudio. Użyto tych samych **połączenia** wcześniej zdefiniowaną.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Innym sposobem, aby zainstalować pakiet języka R do usługi Azure SQL database jest do przekazania pakietu języka R z strumień bajtów przy użyciu **CREATE EXTERNAL LIBRARY** instrukcję języka T-SQL. Zobacz [utworzyć bibliotekę ze strumienia bajtów](/sql/t-sql/statements/create-external-library-transact-sql#c-create-a-library-from-a-byte-stream) w [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) dokumentację referencyjną.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usług Azure SQL Database Machine Learning przy użyciu języka R (wersja zapoznawcza) zobacz następujące artykuły.

- [SQL Database usługi Azure Machine Learning przy użyciu języka R (wersja zapoznawcza)](sql-database-machine-learning-services-overview.md)
- [Zapisywanie zaawansowane funkcje języka R w usłudze Azure SQL Database przy użyciu usług Machine Learning (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
- [Praca z danymi SQL i języka R w SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)