---
title: Rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu programu Visual Studio
description: Poznaj sposoby instalowania narzędzi Data Lake Tools for Visual Studio oraz tworzenia i testowania skryptów U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/13/2018
ms.openlocfilehash: b463946402eee40d0de0942eeaf37a6f9ea59990
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60510057"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Usługa Azure Data Lake i Stream Analytics Tools zawierają funkcje związane z dwóch usług platformy Azure, Azure Data Lake Analytics i Azure Stream Analytics. Aby uzyskać więcej informacji na temat scenariuszy, Azure Stream Analytics, zobacz [usługi Azure Stream Analytics tools for Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

W tym artykule opisano, jak używać programu Visual Studio do tworzenia kont usługi Azure Data Lake Analytics, definiowania zadań w [U-SQL](data-lake-analytics-u-sql-get-started.md)oraz przesyłania zadań do usługi Data Lake Analytics. Więcej informacji na temat usługi Data Lake Analytics można znaleźć w artykule [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Firma Microsoft zaleca uaktualnienie do narzędzi Azure Data Lake Tools for Visual Studio w wersji 2.3.3000.4 lub nowszej. Poprzednie wersje są niedostępne do pobrania i przestarzałe. 
> 
> **Co muszę zrobić?**
> 
> 1. Sprawdź, czy używasz narzędzi Azure Data Lake Tools for Visual Studio w wersji starszej niż 2.3.3000.4. 
> 
>    ![Sprawdzanie wersji narzędzia](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
> 2. Jeśli używana wersja jest starsza niż 2.3.3000.4, zaktualizuj narzędzia Azure Data Lake Tools for Visual Studio, odwiedzając centrum pobierania: 
>    - [Dla programu Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Dla programu Visual Studio 2013 i 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>Wymagania wstępne

* **Visual Studio**: Obsługiwane są wszystkie wersje, z wyjątkiem Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Zestaw Microsoft Azure SDK dla platformy .NET** w wersji 2.7.1 lub nowszej.  Można go zainstalować przy użyciu [Instalatora platformy internetowej](https://www.microsoft.com/web/downloads/platform.aspx).
* Konto usługi **Data Lake Analytics**. Aby utworzyć konto, zobacz artykuł [Wprowadzenie do pracy z usługą Azure Data Lake Analytics za pomocą witryny Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instalowanie narzędzi Azure Data Lake Tools for Visual Studio

Ten samouczek wymaga zainstalowania narzędzi Data Lake Tools for Visual Studio. Postępuj zgodnie z [instrukcjami instalacji](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Łączenie z kontem usługi Azure Data Lake Analytics

1. Otwórz program Visual Studio.

2. Otwórz Eksplorator serwera, wybierając pozycje **Widok** > **Eksplorator serwera**.

3. Kliknij prawym przyciskiem myszy pozycję **Azure**. Następnie wybierz pozycję **Połącz z subskrypcją platformy Microsoft Azure** i postępuj zgodnie z instrukcjami.

4. W Eksploratorze serwera wybierz pozycje **Azure** > **Data Lake Analytics**. Zobaczysz listę swoich kont usługi Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Pisanie pierwszego skryptu U-SQL

Poniższy tekst to prosty skrypt U-SQL. Definiuje on mały zestaw danych i zapisuje go w domyślnym magazynie Data Lake Store jako plik o nazwie `/data.csv`.

```
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Przesyłanie zadania usługi Data Lake Analytics

1. Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.

2. Wybierz typ **Projekt U-SQL**, a następnie kliknij pozycję **OK**. Program Visual Studio utworzy rozwiązanie z użyciem pliku **Script.usql**.

3. Wklej poprzedni skrypt do okna **Script.usql**.

4. W lewym górnym rogu okna **Script.usql** określ konto usługi Data Lake Analytics.

    ![Przesyłanie projektu U-SQL programu Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. W lewym górnym rogu okna **Script.usql** wybierz pozycję **Prześlij**.

6. Po przesłaniu zadania zostanie otwarta karta **Widok zadania**, na której widoczny będzie postęp zadania. Aby wyświetlić najbardziej aktualny stan zadania i odświeżyć ekran, kliknij pozycję **Odśwież**.

    ![Wykres wydajności zadania skryptu U-SQL programu Visual Studio w usłudze Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * Karta **Podsumowanie zadania** zawiera podsumowanie zadania.   
   * Karta **Graf zadania** wizualizuje postęp zadania.
   * Karta **Operacje dotyczące metadanych** pokazuje wszystkie akcje, które zostały wykonane na wykazie języka U-SQL.
   * Karta **Dane** przedstawia wszystkie dane wejściowe i wyjściowe.
   * Karta **Historia stanu** zawiera szczegóły osi czasu i stanu.
   * Karta **Analiza jednostek AU** zawiera liczbę użytych jednostek analizy w zadaniu oraz informacje dotyczące symulacji innych strategii alokacji jednostek analizy.
   * Karta **Diagnostyka** udostępnia zaawansowaną analizę wykonywania zadania i optymalizacji wydajności.

## <a name="check-job-status"></a>Sprawdzanie stanu zadania

1. W Eksploratorze serwera wybierz pozycje **Azure** > **Data Lake Analytics**.

2. Rozwiń nazwę konta usługi Data Lake Analytics.

3. Kliknij dwukrotnie pozycję **Zadania**.

4. Wybierz wcześniej przesłane zadanie.

## <a name="see-the-job-output"></a>Wyświetlanie danych wyjściowych zadania

1. W Eksploratorze serwera przejdź do przesłanego zadania.

2. Kliknij kartę **Dane**.

3. Na karcie **Dane wyjściowe zadania** wybierz plik `"/data.csv"`.

## <a name="next-steps"></a>Kolejne kroki

* [Uruchamianie skryptów U-SQL na swojej stacji roboczej w celu testowania i debugowania](data-lake-analytics-data-lake-tools-local-run.md)
* [Debugowanie kodu w języku C# w zadaniach U-SQL za pomocą narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
