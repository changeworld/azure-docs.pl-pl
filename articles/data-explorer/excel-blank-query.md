---
title: Wizualizuj dane za pomocą kwerendy Kusto usługi Azure Data Explorer zaimportowane do programu Microsoft Excel
description: W tym artykule dowiesz się, jak zaimportować zapytanie Kusto eksploratora danych platformy Azure do programu Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849092"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Wizualizuj dane za pomocą kwerendy Kusto usługi Azure Data Explorer zaimportowane do programu Microsoft Excel

Usługa Azure Data Explorer udostępnia dwie opcje łączenia się z danymi w programie Excel: użyj łącznika macierzystego lub zaimportuj kwerendę z Eksploratora danych platformy Azure. W tym artykule pokazano, jak zaimportować kwerendę z Eksploratora danych platformy Azure do programu Excel w celu wizualizacji danych. Dodaj kwerendę Kusto jako źródło danych programu Excel, aby wykonać dodatkowe obliczenia lub wizualizacje danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Organizacyjne konto e-mail, które jest członkiem usługi Azure Active Directory, dzięki czemu można połączyć się z [klastrem pomocy Usługi Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>lub</br>
* Utwórz [klaster testowy i bazę danych](create-cluster-database-portal.md) oraz zaloguj się do [aplikacji interfejsu użytkownika sieci Web usługi Azure Data Explorer](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definiowanie zapytania Kusto jako źródła danych programu Excel

1. W [usłudze Azure Data Explorer web ui](https://dataexplorer.azure.com/clusters/help/databases/Samples)uruchom kwerendę i sprawdź wyniki.

1. Wybierz kartę **Udostępnianie** i wybierz pozycję **Kwerenda w usłudze Power BI**.

    ![Zapytanie interfejsu użytkownika sieci Web do usługi Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Pojawi się okno z następującym powiadomieniem:

    ![eksportowanie kwerendy do schowka](media/excel-blank-query/query-exported-to-clipboard.png)

1. Otwórz **program Microsoft Excel**.

1. Na karcie **Dane** wybierz pozycję **Pobierz puste zapytanie o dane** > **z innych źródeł** > **Blank Query**.

    ![Pobierz dane i wybierz pustą kwerendę](media/excel-blank-query/get-data-blank-query.png)

1. Zostanie otwarte okno **Edytora dodatków Power** Query. W oknie wybierz pozycję **Edytor zaawansowany**.

    ![Okno edytora dodatku power query](media/excel-blank-query/power-query-editor.png)

1. W oknie **Edytor zaawansowany** wklej kwerendę wyeksportowaną do schowka i wybierz pozycję **Gotowe**.

    ![Zaawansowane zapytanie edytora](media/excel-blank-query/advanced-editor-query.png)    

1. Aby uwierzytelnić, wybierz pozycję **Edytuj poświadczenia**.

    ![Edycja poświadczeń](media/excel-blank-query/edit-credentials.png)

1. Wybierz **konto instytucji** i zaloguj **się**. Ukończ proces logowania, a następnie wybierz pozycję **Połącz**.

    ![Pełne logowanie](media/excel-blank-query/complete-sign-in.png)

    Powtórz poprzednie kroki, aby dodać więcej zapytań. Można zmienić nazwę kwerendy na bardziej znaczące nazwy.

1. Wybierz przycisk **Zamknij & Załaduj,** aby uzyskać dane do programu Excel.

    ![Wybierz zamknij i załaduj](media/excel-blank-query/close-and-load.png)

1. Teraz twoje dane są w programie Excel. Wybierz przycisk **Odśwież,** aby odświeżyć kwerendę.

    ![Wyświetlanie danych w programie Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Następne kroki

[Wizualizuj dane przy użyciu łącznika Usługi Azure Data Explorer dla programu Excel](excel-connector.md)