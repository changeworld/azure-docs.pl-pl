---
title: Wizualizuj dane przy użyciu łącznika Usługi Azure Data Explorer dla programu Microsoft Excel
description: W tym artykule dowiesz się, jak używać łącznika programu Excel dla Eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849058"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Wizualizuj dane przy użyciu łącznika Usługi Azure Data Explorer dla programu Excel

Usługa Azure Data Explorer udostępnia dwie opcje łączenia się z danymi w programie Excel: użyj łącznika macierzystego lub zaimportuj kwerendę z Eksploratora danych platformy Azure. W tym artykule pokazano, jak używać łącznika macierzystego w programie Excel i łączyć się z klastrem Usługi Azure Data Explorer w celu uzyskania i wizualizacji danych.

Łącznik natywny programu Azure Data Explorer Excel oferuje możliwość eksportowania wyników kwerend do programu Excel. Można również dodać kwerendę KQL jako źródło danych programu Excel dla dodatkowych obliczeń lub wizualizacji.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Definiowanie zapytania Kusto jako źródła danych programu Excel i ładowanie danych do programu Excel

1. Otwórz **program Microsoft Excel**.
1. Na karcie **Dane** wybierz pozycję **Pobierz dane** > **z platformy Azure** > **z eksploratora danych platformy Azure**.

    ![Pobieranie danych z usługi Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. W oknie **Eksploratora danych platformy Azure (Kusto)** wypełnij następujące pola i wybierz **przycisk OK**.

    ![Okno Eksploratora danych platformy Azure (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Pole   |Opis |
    |---------|---------|
    |**Klastra**   |   Nazwa klastra (obowiązkowa)      |    
    |**baza danych**     |    Nazwa bazy danych      |    
    |**Nazwa tabeli lub kwerenda Eksploratora danych platformy Azure**    |     Nazwa tabeli lub kwerendy Eksploratora danych platformy Azure    | 
    
    **Opcje zaawansowane:**

     |Pole   |Opis |
    |---------|---------|
    |**Ogranicz liczbę rekordów wyników kwerendy**     |     Ograniczanie liczby rekordów załadowanych do programu Excel  |    
    |**Ograniczanie rozmiaru danych wyników kwerendy (bajtów)**    |    Ograniczanie rozmiaru danych      |   
    |**Wyłączanie obcinania zestawu wyników**    |         |      
    |**Instrukcje dodatkowe set (oddzielone średnikami)**    |    Dodawanie `set` instrukcji do zastosowania do źródła danych     |   

1.  W okienku **Nawigator** przejdź do właściwej tabeli. W okienku podglądu tabeli wybierz pozycję **Przekształcaj dane,** aby wprowadzić zmiany w danych, lub wybierz pozycję **Wczytaj,** aby załadować je do programu Excel.

![Okno podglądu tabeli](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Jeśli **nazwa bazy danych** i/lub **tabela lub kwerenda Usługi Azure Data Explorer** są już określone, odpowiednie okienko podglądu tabeli zostanie automatycznie otwarte. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analizowanie i wizualizowanie danych w programie Excel

Gdy dane będą ładowane do programu Excel i są dostępne w arkuszu programu Excel, można analizować, podsumowywać i wizualizować dane, tworząc relacje i wizualizacje. 

1.  Na karcie **Projekt tabeli** wybierz pozycję **Podsumowyj za pomocą tabeli przestawnej**. W oknie **Tworzenie tabeli przestawnej** zaznacz odpowiednią tabelę i wybierz przycisk **OK**.

    ![Tworzenie tabeli przestawnej](media/excel-connector/create-pivot-table.png)

1. W okienku **Pola tabeli przestawnej** zaznacz odpowiednie kolumny tabel, aby utworzyć tabele podsumowania. W poniższym przykładzie wybrane są **identyfikatory zdarzeń** i **stan.**
    
    ![Wybieranie pól tabeli przestawnej](media/excel-connector/pivot-table-pick-fields.png)

1. Na karcie **Analiza tabeli przestawnej** wybierz pozycję **Wykres przestawny,** aby utworzyć wizualizacje na podstawie tabeli. 

    ![Wykres przestawny](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. W poniższym przykładzie użyj **identyfikatora zdarzenia,** **StartTime**i **EventType,** aby wyświetlić dodatkowe informacje o zdarzeniach pogodowych.

    ![Wizualizowanie danych](media/excel-connector/visualize-excel-data.png)

1. Twórz pełne pulpity nawigacyjne, aby monitorować swoje dane.

## <a name="next-steps"></a>Następne kroki

[Wizualizuj dane za pomocą kwerendy Kusto usługi Azure Data Explorer zaimportowane do programu Microsoft Excel](excel-blank-query.md)