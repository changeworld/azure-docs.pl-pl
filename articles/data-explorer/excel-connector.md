---
title: Wizualizowanie danych za pomocą łącznika Eksplorator danych platformy Azure dla programu Microsoft Excel
description: W tym artykule dowiesz się, jak używać łącznika programu Excel dla Eksplorator danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849058"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Wizualizowanie danych przy użyciu łącznika usługi Azure Eksplorator danych dla programu Excel

Usługa Azure Eksplorator danych oferuje dwie opcje łączenia się z danymi w programie Excel: Użyj łącznika natywnego lub zaimportuj zapytanie z usługi Azure Eksplorator danych. W tym artykule przedstawiono sposób użycia łącznika natywnego w programie Excel i nawiązania połączenia z klastrem usługi Azure Eksplorator danych w celu uzyskania i wizualizacji danych.

Łącznik macierzysty usługi Azure Eksplorator danych Excel oferuje możliwość eksportowania wyników zapytania do programu Excel. Możesz również dodać zapytanie KQL jako źródło danych programu Excel dla dodatkowych obliczeń lub wizualizacji.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Zdefiniuj zapytanie Kusto jako źródło danych programu Excel i Załaduj dane do programu Excel

1. Otwórz **program Microsoft Excel**.
1. Na karcie **dane** wybierz pozycję **Pobierz dane** > **z usługi azure** > **z usługi Azure Eksplorator danych**.

    ![Pobieranie danych z usługi Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. W oknie **Eksplorator danych platformy Azure (Kusto)** wypełnij następujące pola i wybierz pozycję **OK**.

    ![Okno Eksplorator danych platformy Azure (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Pole   |Opis |
    |---------|---------|
    |**Hosta**   |   Nazwa klastra (obowiązkowa)      |    
    |**Baza danych**     |    Nazwa bazy danych      |    
    |**Nazwa tabeli lub zapytanie Eksplorator danych platformy Azure**    |     Nazwa tabeli lub zapytania Eksplorator danych platformy Azure    | 
    
    **Opcje zaawansowane:**

     |Pole   |Opis |
    |---------|---------|
    |**Ogranicz liczbę rekordów wyników zapytania**     |     Ogranicz liczbę rekordów ładowanych do programu Excel  |    
    |**Ogranicz rozmiar danych wyników zapytania (w bajtach)**    |    Ograniczanie rozmiaru danych      |   
    |**Wyłącz obcinanie zestawu wyników**    |         |      
    |**Dodatkowe instrukcje Set (oddzielone średnikami)**    |    Dodaj instrukcje `set`, które mają zostać zastosowane do źródła danych     |   

1.  W okienku **Nawigator** przejdź do odpowiedniej tabeli. W okienku podglądu tabeli wybierz pozycję **Przekształć dane** , aby wprowadzić zmiany w danych, lub wybierz pozycję **Załaduj** , aby załadować ją do programu Excel.

![Okno podglądu tabeli](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Jeśli została już określona nazwa **bazy danych** i/lub **tabeli lub Eksplorator danych kwerendy platformy Azure** , poprawne okienko podglądu tabeli zostanie otwarte automatycznie. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analizowanie i wizualizowanie danych w programie Excel

Po załadowaniu danych do programu Excel i udostępnieniu ich w arkuszu programu Excel można analizować, podsumowywać i wizualizować dane, tworząc relacje i wizualizacje. 

1.  Na karcie **Projektowanie tabeli** wybierz opcję **Podsumowanie z tabelą przestawną**. W oknie **Tworzenie tabeli przestawnej** wybierz odpowiednią tabelę, a następnie wybierz **przycisk OK**.

    ![Utwórz tabelę przestawną](media/excel-connector/create-pivot-table.png)

1. W okienku **pola tabeli przestawnej** wybierz odpowiednie kolumny tabeli, aby utworzyć tabele podsumowujące. W poniższym przykładzie wybrano **EventID** i **stan** .
    
    ![Wybieranie pól tabeli przestawnej](media/excel-connector/pivot-table-pick-fields.png)

1. Na karcie **Analizowanie tabeli przestawnej** wybierz pozycję **Wykres przestawny** , aby utworzyć wizualizacje na podstawie tabeli. 

    ![Wykres przestawny](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. W poniższym przykładzie Użyj **identyfikatora zdarzenia**, **StartTime**i **EventType** , aby wyświetlić dodatkowe informacje o zdarzeniach pogodowych.

    ![Wizualizowanie danych](media/excel-connector/visualize-excel-data.png)

1. Utwórz pełne pulpity nawigacyjne, aby monitorować dane.

## <a name="next-steps"></a>Następne kroki

[Wizualizuj dane przy użyciu zapytania Kusto usługi Azure Eksplorator danych zaimportowanego do programu Microsoft Excel](excel-blank-query.md)