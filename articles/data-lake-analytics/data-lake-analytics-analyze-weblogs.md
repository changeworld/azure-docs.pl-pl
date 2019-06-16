---
title: Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics
description: Dowiedz się, jak i analizowanie dzienników witryn sieci Web przy użyciu usługi Data Lake Analytics.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 83742a4f82fb4d67fd258ff0d242847eab634c78
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334116"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics
Dowiedz się, jak i analizowanie dzienników witryn sieci Web przy użyciu usługi Data Lake Analytics, zwłaszcza na sprawdzanie, które przywołujących napotkał błędy podczas próby do odwiedzenia witryny sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne
* **Program Visual Studio 2015 lub Visual Studio 2013**.
* **[Data Lake Tools dla Visual Studio](https://aka.ms/adltoolsvs)** .

    Po zainstalowaniu narzędzi Data Lake Tools for Visual Studio zobaczysz **usługi Data Lake** pozycja **narzędzia** menu w programie Visual Studio:

    ![Menu programu Visual Studio w języku U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Podstawową wiedzę na temat usługi Data Lake Analytics i Data Lake Tools for Visual Studio**. Aby rozpocząć pracę, zobacz:

  * [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Konto usługi Data Lake Analytics.**  Zobacz [Tworzenie konta usługi Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
* **Zainstaluj przykładowych danych.** W witrynie Azure Portal otwórz użytkownik konta usługi Data Lake Analytics, a następnie kliknij przycisk **przykładowe skrypty** w menu po lewej stronie, następnie kliknij polecenie **Copy Sample Data**. 

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure
Zanim można skompilować i przetestować skrypty U-SQL, musisz najpierw połączyć na platformie Azure.

**Aby nawiązać połączenie z usługą Data Lake Analytics**

1. Otwórz program Visual Studio.
2. Kliknij przycisk **usługi Data Lake > Opcje i ustawienia**.
3. Kliknij przycisk **Sign In**, lub **Zmień użytkownika** gdy ktoś zalogował i postępuj zgodnie z instrukcjami.
4. Kliknij przycisk **OK** aby zamknąć okno dialogowe Opcje i ustawienia.

**Aby przeglądać swoje konta usługi Data Lake Analytics**

1. W programie Visual Studio, otwórz **Eksploratora serwera** przez naciśnięcie **CTRL + ALT + S**.
2. W **Eksploratorze serwera** rozwiń węzeł **Azure**, a następnie rozwiń węzeł **Data Lake Analytics**. Zostanie wyświetlona lista kont usługi Data Lake Analytics, o ile jakieś istnieją. Nie można utworzyć konta usługi Data Lake Analytics z programu studio. Aby utworzyć konto, zobacz temat [Rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu Portalu Azure](data-lake-analytics-get-started-portal.md) lub [Rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Tworzenie aplikacji w języku U-SQL
Aplikacji w języku U-SQL to przede wszystkim skrypt U-SQL. Aby dowiedzieć się więcej o języku U-SQL, zobacz [wprowadzenie do języka U-SQL](data-lake-analytics-u-sql-get-started.md).

Dodatkowe operatory zdefiniowane przez użytkownika można dodać do aplikacji.  Aby uzyskać więcej informacji, zobacz [operatorów na potrzeby zadań usługi Data Lake Analytics zdefiniowane przez użytkownika języka U-SQL opracowywanie](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Aby utworzyć i przesłać zadanie usługi Data Lake Analytics**

1. Kliknij przycisk **Plik > Nowy > Projekt**.
2. Wybierz typ projektu U-SQL.

    ![nowy projekt U-SQL programu Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Kliknij przycisk **OK**. Program Visual studio tworzy rozwiązanie z plikiem Script.usql.
4. Wprowadź następujący skrypt do pliku Script.usql:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            DISTRIBUTED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Aby poznać U-SQL, zobacz [wprowadzenie do języka U-SQL usługi Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).    
5. Dodaj nowy skrypt U-SQL do projektu, a następnie wprowadź następujące informacje:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Przejdź wstecz, aby pierwszy skrypt U-SQL i obok **przesyłania** przycisk, określ konto usługi Analytics.
7. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Build Script** (Kompiluj skrypt). Sprawdź wyniki, w okienku danych wyjściowych.
8. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Submit Script** (Prześlij skrypt).
9. Sprawdź **konto usługi Analytics** jest tą, na którym chcesz uruchomić zadanie, a następnie kliknij przycisk **przesyłania**. Po zakończeniu przesyłania wyniki przesyłania i link do zadania są dostępne w oknie wyników narzędzi Data Lake Tools dla programu Visual Studio.
10. Zaczekaj, aż zadanie jest ukończone pomyślnie.  Jeśli zadanie nie powiodło się, prawdopodobnie brakuje pliku źródłowego.  Zobacz sekcję wymagań wstępnych w tym samouczku. Aby uzyskać dodatkowe informacje dotyczące rozwiązywania problemów, zobacz [monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Gdy zadanie jest ukończone, zobaczysz następujący ekran:

    ![Usługa Data lake analytics analizowanie dzienników witryn sieci Web dzienników w sieci Web](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Teraz Powtórz kroki od 7 do 10 dla **Script1.usql**.

**Aby wyświetlić dane wyjściowe zadania**

1. W **Eksploratorze serwera** rozwiń węzeł **Azure**, rozwiń węzeł **Data Lake Analytics**, rozwiń konto usługi Data Lake Analytics, rozwiń węzeł **Konta magazynu**, kliknij prawym przyciskiem myszy domyślne konto usługi Data Lake Storage, a następnie kliknij przycisk **Explorer**.
2. Kliknij dwukrotnie **przykłady** do Otwórz folder, a następnie kliknij dwukrotnie **dane wyjściowe**.
3. Kliknij dwukrotnie **UnsuccessfulResponses.log**.
4. Możesz także dwukrotnie kliknąć plik wyjściowy w widoku wykresu zadania, aby przejść bezpośrednio do wyjścia.

## <a name="see-also"></a>Zobacz także
Aby rozpocząć pracę z usługą Data Lake Analytics przy użyciu różnych narzędzi, zobacz:

* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu portalu Azure](data-lake-analytics-get-started-portal.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu zestawu SDK programu .NET](data-lake-analytics-get-started-net-sdk.md)
