---
title: 'Szybki start: Wizualizuj dane z Eksploratora danych Azure przy użyciu zapytania SQL w usłudze Power BI'
description: 'Z tego przewodnika Szybki start nauczysz się, jak używać jednej z trzech opcji wizualizacji danych w usłudze Power BI: zapytania SQL względem klastra usługi Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: 4a3a688adaae8fe66c336617cdd0a4807f16ec68
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045511"
---
# <a name="quickstart-visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Szybki start: Wizualizuj dane przy użyciu łącznika Eksplorator danych platformy Azure dla usługi Power BI

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Power BI to rozwiązanie do analizy biznesowej, które pozwala wizualizować dane i udostępniać wyniki w organizacji.

Usługa Azure Data Explorer oferuje trzy opcje łączenia się z danymi w usłudze Power BI: za pomocą wbudowanego łącznika, przez zaimportowanie zapytania z usługi Azure Data Explorer lub za pomocą zapytania SQL. W tym przewodniku Szybki start pokazano, jak za pomocą zapytania SQL uzyskać dane i zwizualizować je w raporcie usługi Power BI.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego przewodnika Szybki start potrzebne są następujące elementy:

* Konto e-mail organizacji należące do usługi Azure Active Directory, aby możliwe było łączenie się z [klastrem pomocy usługi Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).

* Program [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (wybierz pozycję **POBIERZ BEZPŁATNIE**)

## <a name="get-data-from-azure-data-explorer"></a>Pobieranie danych z usługi Azure Data Explorer

Najpierw nawiąż połączenie z klastrem pomocy usługi Azure Data Explorer, a następnie wprowadź podzestaw danych z tabeli *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Zazwyczaj w usłudze Azure Data Explorer używasz natywnego języka zapytań, ale ta usługa obsługuje również zapytania SQL, których użyjemy tutaj. Usługa Azure Data Explorer automatycznie tłumaczy zapytanie SQL na zapytanie natywne.

1. W programie Power BI Desktop na karcie **Narzędzia główne** wybierz pozycję **Pobierz dane**, a następnie pozycję **Więcej**.

    ![Pobieranie danych](media/power-bi-sql-query/get-data-more.png)

1. Wyszukaj ciąg *Azure SQL Database*, wybierz usługę **Azure SQL Database**, a następnie przycisk **Połącz**.

    ![Wyszukiwanie i pobieranie danych](media/power-bi-sql-query/search-get-data.png)

1. Na ekranie **Baza danych programu SQL Server** wypełnij formularz, używając poniższych informacji.

    ![Opcje bazy danych, tabeli i zapytania](media/power-bi-sql-query/database-table-query.png)

    **Ustawienie** | **Wartość** | **Opis pola**
    |---|---|---|
    | Serwer | *help.kusto.windows.net* | Adres URL klastra pomocy (bez *https://*). W przypadku innych klastrów adres URL ma postać *\<Nazwa_klastra\>.\<Region\>.kusto.windows.net*. |
    | Database (Baza danych) | *Przykłady* | Przykładowa baza danych hostowana w klastrze, z którą nawiązujesz połączenie. |
    | Tryb łączności danych | *Import* | Określa, czy usługa Power BI importuje dane, czy łączy się bezpośrednio ze źródłem danych. Korzystając z tego łącznika, możesz wybrać dowolną z tych opcji. |
    | Limit czasu polecenia | Pozostaw puste | Czas działania zapytania, po jakim zgłaszany jest błąd przekroczenia limitu czasu. |
    | Instrukcja SQL | Skopiuj zapytanie znajdujące się pod tą tabelą | Instrukcja SQL, którą usługa Azure Data Explorer tłumaczy na zapytanie natywne. |
    | Inne opcje | Pozostaw wartości domyślne | Te opcje nie mają zastosowania w klastrach usługi Azure Data Explorer. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Jeśli jeszcze nie masz połączenia z klastrem pomocy, zaloguj się. Zaloguj się przy użyciu konta Microsoft, a następnie wybierz przycisk **Połącz**.

    ![Logowanie](media/power-bi-sql-query/sign-in.png)

1. Na **help.kusto.windows.net: Przykłady** ekranu, wybierz opcję **obciążenia**.

    ![Ładowanie danych](media/power-bi-sql-query/load-data.png)

    Tabela otwiera się w głównym oknie usługi Power BI w widoku raportu, w którym można tworzyć raporty na podstawie przykładowych danych.

## <a name="visualize-data-in-a-report"></a>Wizualizacja danych w raporcie

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już raportu, który został utworzony na potrzeby tego przewodnika Szybki start, usuń plik programu Power BI Desktop (pbix).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Szybki start: Wizualizuj dane przy użyciu importowanych zapytania w usłudze Power BI](power-bi-connector.md)