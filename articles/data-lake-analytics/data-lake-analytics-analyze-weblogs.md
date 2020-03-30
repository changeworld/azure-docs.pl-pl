---
title: Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics
description: Dowiedz się, jak analizować dzienniki witryn sieci Web przy użyciu usługi Azure Data Lake Analytics w celu uruchamiania funkcji i zapytań U-SQL.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 04c6d4c74a82ccfbcbb0faecb0dca5ec495f6663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672880"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics
Dowiedz się, jak analizować dzienniki witryn za pomocą usługi Data Lake Analytics, zwłaszcza gdy dowiadywać się, które osoby odsyłacze napadały na błędy podczas próby odwiedzenia witryny.

## <a name="prerequisites"></a>Wymagania wstępne
* **Visual Studio 2015 lub Visual Studio 2013**.
* **[Data Lake Tools dla Visual Studio](https://aka.ms/adltoolsvs)**.

    Po zainstalowaniu programu Data Lake Tools for Visual Studio w menu **Narzędzia** w programie Visual Studio zostanie wyświetlony element **usługi Data Lake:**

    ![Menu programu U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Podstawowa znajomość usługi Data Lake Analytics i narzędzia Data Lake Tools for Visual Studio**. Aby rozpocząć, zobacz:

  * [Tworzenie skryptu U-SQL przy użyciu narzędzi usługi Data Lake dla programu Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Konto usługi Data Lake Analytics.**  Zobacz [Tworzenie konta usługi Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
* **Zainstaluj przykładowe dane.** W witrynie Azure Portal otwórz konto Usługi Data Lake Analytics i kliknij **pozycję Przykładowe skrypty** w menu po lewej stronie, a następnie kliknij polecenie **Kopiuj przykładowe dane**. 

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure
Przed utworzeniem i przetestowaniem skryptów U-SQL należy najpierw połączyć się z platformą Azure.

**Aby nawiązać połączenie z usługą Data Lake Analytics**

1. Otwórz program Visual Studio.
2. Kliknij **pozycję Data Lake > Options and Settings**.
3. Kliknij **pozycję Zaloguj się**lub Zmień **użytkownika,** jeśli ktoś się zalogował, i postępuj zgodnie z instrukcjami.
4. Kliknij **przycisk OK,** aby zamknąć okno dialogowe Opcje i Ustawienia.

**Aby przeglądać konta usługi Data Lake Analytics**

1. W programie Visual Studio otwórz **Eksploratora serwera,** naciskając **klawisze CTRL+ALT+S**.
2. W **Eksploratorze serwera** rozwiń węzeł **Azure**, a następnie rozwiń węzeł **Data Lake Analytics**. Zostanie wyświetlona lista kont usługi Data Lake Analytics, o ile jakieś istnieją. Nie można tworzyć kont usługi Data Lake Analytics ze studia. Aby utworzyć konto, zobacz [Wprowadzenie do usługi Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-get-started-portal.md) lub Wprowadzenie do usługi Azure Data Lake Analytics przy użyciu usługi Azure [PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Tworzenie aplikacji U-SQL
Aplikacja U-SQL jest głównie skrypt U-SQL. Aby dowiedzieć się więcej o U-SQL, zobacz [Wprowadzenie do U-SQL](data-lake-analytics-u-sql-get-started.md).

Do aplikacji można dodać operatory zdefiniowane przez użytkownika.  Aby uzyskać więcej informacji, zobacz [Tworzenie operatorów zdefiniowanych przez użytkowników U-SQL dla zadań usługi Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Aby utworzyć i przesłać zadanie usługi Data Lake Analytics**

1. Kliknij **> projektu > pliku**.
2. Wybierz typ U-SQL Project (Projekt U-SQL).

    ![nowy projekt U-SQL programu Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Kliknij przycisk **OK**. Program Visual Studio tworzy rozwiązanie z użyciem pliku Script.usql.
4. Wprowadź następujący skrypt w pliku Script.usql:

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

    Aby zrozumieć U-SQL, zobacz [Wprowadzenie do języka U-SQL usługi Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).    
5. Dodaj nowy skrypt U-SQL do projektu i wprowadź następujące elementy:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Przełącz się z powrotem do pierwszego skryptu U-SQL i obok przycisku **Prześlij** określ swoje konto Analytics.
7. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Build Script** (Kompiluj skrypt). Sprawdź wyniki w okienku Dane wyjściowe.
8. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Submit Script** (Prześlij skrypt).
9. Sprawdź, czy konto Analytics to **konto,** na którym chcesz uruchomić zadanie, a następnie kliknij przycisk **Prześlij**. Po zakończeniu przesyłania wyniki przesyłania i link do zadania są dostępne w oknie wyników narzędzi Data Lake Tools dla programu Visual Studio.
10. Poczekaj, aż zadanie zostanie zakończone pomyślnie.  Jeśli zadanie nie powiodło się, najprawdopodobniej brakuje pliku źródłowego.  Zapoznaj się z sekcją Warunek wstępny tego samouczka. Aby uzyskać dodatkowe informacje dotyczące rozwiązywania problemów, zobacz [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów z jego rozwiązywaniem](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Po zakończeniu zadania zostanie wyświetlony następujący ekran:

    ![data lake analizy analizy weblogs dzienniki stron internetowych](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Teraz powtórz kroki 7- 10 dla **Script1.usql**.

**Aby wyświetlić dane wyjściowe zadania**

1. W **Eksploratorze serwera** rozwiń węzeł **Azure**, rozwiń węzeł **Data Lake Analytics**, rozwiń konto usługi Data Lake Analytics, rozwiń węzeł **Konta magazynu**, kliknij prawym przyciskiem myszy domyślne konto usługi Data Lake Storage, a następnie kliknij przycisk **Explorer**.
2. Kliknij dwukrotnie **pozycję Przykłady,** aby otworzyć folder, a następnie kliknij dwukrotnie pozycję **Wyjścia**.
3. Kliknij dwukrotnie **pozycję UnsuccessfulResponses.log**.
4. Można również dwukrotnie kliknąć plik wyjściowy wewnątrz widoku wykresu zadania, aby przejść bezpośrednio do danych wyjściowych.

## <a name="see-also"></a>Zobacz też
Aby rozpocząć pracę z usługą Data Lake Analytics przy użyciu różnych narzędzi, zobacz:

* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu Portalu Azure](data-lake-analytics-get-started-portal.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu zestawu SDK programu .NET](data-lake-analytics-get-started-net-sdk.md)
