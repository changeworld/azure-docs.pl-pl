---
title: 'Wizualizuj dane z Eksploratora danych platformy Azure z kwerendą importowanych w usłudze Power BI '
description: 'W tym artykule dowiesz się, jak używać jednej z trzech opcji do wizualizacji danych w usłudze Power BI: importowanie zapytania z Eksploratora danych usługi Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ae632f7df34ca342a3dcc12bf5f011df0eb61973
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399319"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Wizualizuj dane przy użyciu zapytań, importowany do usługi Power BI

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Power BI to rozwiązanie do analizy biznesowej, które pozwala wizualizować dane i udostępniać wyniki w organizacji.

Usługa Azure Data Explorer oferuje trzy opcje łączenia się z danymi w usłudze Power BI: za pomocą wbudowanego łącznika, przez zaimportowanie zapytania z usługi Azure Data Explorer lub za pomocą zapytania SQL. W tym artykule pokazano, jak zaimportować zapytanie tak, aby uzyskać dane i wizualizacji w raporcie usługi Power BI.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące polecenie, aby zakończyć w tym artykule:

* Konto e-mail organizacji należące do usługi Azure Active Directory, aby możliwe było łączenie się z [klastrem pomocy usługi Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).

* Program [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (wybierz pozycję **POBIERZ BEZPŁATNIE**)

* [Aplikacja klasyczna Azure Data Explorer](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Pobieranie danych z usługi Azure Data Explorer

Najpierw utwórz zapytanie w aplikacji klasycznej Azure Data Explorer i wyeksportuj je do użycia w usłudze Power BI. Następnie nawiąż połączenie z klastrem pomocy usługi Azure Data Explorer i wprowadź podzestaw danych z tabeli *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. W przeglądarce przejdź do witryny [https://help.kusto.windows.net/](https://help.kusto.windows.net/), aby uruchomić aplikację klasyczną Azure Data Explorer.

1. W aplikacji klasycznej skopiuj poniższe zapytanie do okna zapytania w prawym górnym rogu, a następnie uruchom je.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Kilka pierwszych wierszy zestawu wyników powinno wyglądać podobnie jak na poniższej ilustracji.

    ![Wyniki zapytania](media/power-bi-imported-query/query-results.png)

1. Na karcie **Narzędzia** wybierz pozycję **Zapytanie do usługi Power BI**, a następnie pozycję **OK**.

    ![Eksportowanie zapytania](media/power-bi-imported-query/export-query.png)

1. W programie Power BI Desktop na karcie **Narzędzia główne** wybierz pozycję **Pobierz dane**, a następnie pozycję **Puste zapytanie**.

    ![Pobieranie danych](media/power-bi-imported-query/get-data.png)

1. W edytorze Power Query na karcie **Narzędzia główne** wybierz pozycję **Edytor zaawansowany**.

1. W oknie **Edytor zaawansowany** wklej wyeksportowane zapytanie, a następnie wybierz pozycję **Gotowe**.

    ![Wklejanie zapytania](media/power-bi-imported-query/paste-query.png)

1. W głównym oknie edytora Power Query wybierz pozycję **Edytuj poświadczenia**. Wybierz pozycję **Konto organizacyjne**, zaloguj się, a następnie wybierz pozycję **Połącz**.

    ![Edycja poświadczeń](media/power-bi-imported-query/edit-credentials.png)

1. Na karcie **Narzędzia główne** wybierz pozycję **Zamknij i zastosuj**.

    ![Zamknięcie i zastosowanie](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Wizualizacja danych w raporcie

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już raportu, który został utworzony w tym artykule, usuń plik programu Power BI Desktop (pbix).

## <a name="next-steps"></a>Kolejne kroki

[Wizualizuj dane przy użyciu importowanych zapytania w usłudze Power BI](power-bi-sql-query.md)