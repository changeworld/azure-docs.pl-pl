---
title: Wizualizuj dane przy użyciu zapytania Kusto usługi Azure Eksplorator danych zaimportowanego do programu Microsoft Excel
description: W tym artykule dowiesz się, jak zaimportować zapytanie usługi Azure Eksplorator danych Kusto do programu Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849092"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Wizualizuj dane przy użyciu zapytania Kusto usługi Azure Eksplorator danych zaimportowanego do programu Microsoft Excel

Usługa Azure Eksplorator danych oferuje dwie opcje łączenia się z danymi w programie Excel: Użyj łącznika natywnego lub zaimportuj zapytanie z usługi Azure Eksplorator danych. W tym artykule opisano sposób importowania zapytania z usługi Azure Eksplorator danych do programu Excel w celu wizualizacji danych. Dodaj zapytanie Kusto jako źródło danych programu Excel, aby wykonać dodatkowe obliczenia lub wizualizacje danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Organizacyjne konto e-mail, które jest członkiem usługi Azure Active Directory, dzięki czemu można nawiązać połączenie z [klastrem pomocy usługi azure Eksplorator danych](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>lub</br>
* Utwórz [klaster testowy i bazę danych](create-cluster-database-portal.md) , a następnie zaloguj się do [aplikacji interfejsu użytkownika sieci Web usługi Azure Eksplorator danych](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definiowanie zapytania Kusto jako źródła danych programu Excel

1. W [interfejsie użytkownika sieci Web usługi Azure Eksplorator danych](https://dataexplorer.azure.com/clusters/help/databases/Samples)uruchom zapytanie i sprawdź wyniki.

1. Wybierz kartę **udostępnianie** i wybierz pozycję **zapytanie do Power BI**.

    ![Kwerenda interfejsu użytkownika sieci Web do Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Zostanie wyświetlone okno z następującym powiadomieniem:

    ![Eksportuj zapytanie do schowka](media/excel-blank-query/query-exported-to-clipboard.png)

1. Otwórz **program Microsoft Excel**.

1. Na karcie **dane** wybierz pozycję **Pobierz dane** > **z innych źródeł** > **pustego zapytania**.

    ![Pobierz dane i wybierz puste zapytanie](media/excel-blank-query/get-data-blank-query.png)

1. Zostanie otwarte okno **edytora Power Query** . W oknie Wybierz **Edytor zaawansowany**.

    ![Okno edytora dodatku PowerShell](media/excel-blank-query/power-query-editor.png)

1. W oknie **Edytor zaawansowany** wklej wyeksportowane zapytanie do schowka, a następnie wybierz pozycję **gotowe**.

    ![Zaawansowane zapytanie edytora](media/excel-blank-query/advanced-editor-query.png)    

1. Aby przeprowadzić uwierzytelnianie, wybierz pozycję **Edytuj poświadczenia**.

    ![Edycja poświadczeń](media/excel-blank-query/edit-credentials.png)

1. Wybierz pozycję **konto organizacyjne** i **Zaloguj się**. Ukończ proces logowania, a następnie wybierz pozycję **Połącz**.

    ![Ukończ logowanie](media/excel-blank-query/complete-sign-in.png)

    Powtórz poprzednie kroki, aby dodać więcej zapytań. Można zmienić nazwy zapytań na bardziej zrozumiałe nazwy.

1. Wybierz przycisk **zamknij & Załaduj** , aby pobrać dane do programu Excel.

    ![Wybierz pozycję Zamknij i Załaduj](media/excel-blank-query/close-and-load.png)

1. Teraz Twoje dane są w programie Excel. Wybierz przycisk **Odśwież** , aby odświeżyć zapytanie.

    ![Wyświetlanie danych w programie Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Następne kroki

[Wizualizowanie danych przy użyciu łącznika usługi Azure Eksplorator danych dla programu Excel](excel-connector.md)