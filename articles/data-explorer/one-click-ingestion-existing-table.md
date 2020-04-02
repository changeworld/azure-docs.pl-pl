---
title: Ponowne przyjmowanie jednym kliknięciem umożliwia wyłudzenie danych do istniejącej tabeli Eksploratora danych platformy Azure
description: Połknąć (załadowanie) danych do istniejącej tabeli Usługi Azure Data Explorer po prostu przy użyciu jednego kliknięcia pozyskiwania.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: a7fdab66394c132bcd9134669b841d178c559f28
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546206"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-an-existing-table-in-azure-data-explorer"></a>Przykuwanie jednym kliknięciem umożliwia wyłudzenie danych do istniejącej tabeli w Eksploratorze danych platformy Azure

Połknienie jednym kliknięciem umożliwia szybkie wyłowienie danych w formacie JSON, CSV i innych formatach do tabeli. Za pomocą interfejsu użytkownika sieci Web usługi Azure Data Explorer można pozyskiwania danych z magazynu, z pliku lokalnego lub z kontenera. 

W tym dokumencie opisano użycie intuicyjnego kreatora jednym kliknięciem w celu pozyskiwania danych JSON z pliku do istniejącej tabeli. Następnie można edytować tabelę i uruchamiać kwerendy za pomocą interfejsu internetowego usługi Azure Data Explorer.

Łk 1 kliknięcia jest szczególnie przydatne podczas pozyskiwania danych po raz pierwszy lub gdy schemat danych jest nieznany. 

Aby zapoznać się z omówieniem pozyskiwania jednym kliknięciem i listą wymagań wstępnych, zobacz [Połkanie jednym kliknięciem](ingest-data-one-click.md).
Aby uzyskać informacje dotyczące pozyskiwania danych do nowej tabeli w Eksploratorze danych platformy Azure, zobacz [Łk 1 kliknięcia do nowej tabeli](one-click-ingestion-new-table.md)

## <a name="ingest-new-data"></a>Połknienie nowych danych

1. W lewym menu interfejsu użytkownika sieci Web kliknij prawym przyciskiem myszy *bazę danych* lub *tabelę* i wybierz pozycję **Połknieć nowe dane (Wersja zapoznawcza).**

    ![Wybieranie pozyskiwania jednym kliknięciem w interfejsie użytkownika sieci Web](media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png)   
 
1. W oknie **Pozyskiwanie nowych danych (Podgląd)** automatycznie jest wybierana jest karta **Źródło.**

1. Jeśli pole **Tabela** nie jest wypełniane automatycznie, wybierz istniejącą nazwę tabeli z menu rozwijanego.
    > [!TIP]
    > Jeśli wybierzesz opcję **Połknienie nowych danych (Podgląd)** w wierszu *tabeli,* wybrana nazwa tabeli pojawi się w **pliku Szczegóły projektu**.

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]
    
Wybierz **pozycję Edytuj schemat,** aby wyświetlić i edytować konfigurację kolumny tabeli.

## <a name="edit-the-schema"></a>Edytowanie schematu

1. Zostanie otwarte okno dialogowe **Kolumny Mapy** i można mapować kolumny danych źródłowych do kolumn tabeli docelowej. 
    * W **polach Kolumny źródłowe** wprowadź nazwy kolumn do mapowania za pomocą **kolumn docelowych**.
    * Aby usunąć mapowanie, wybierz ikonę kosza.

    ![Okno Kolumny mapy](media/one-click-ingestion-existing-table/map-columns.png)

1. Wybierz pozycję **Update** (Aktualizuj).
1. Na karcie **Schemat:**
    1. Wybierz **typ kompresji**, a następnie wybierz opcję **Nieskompresowany** lub **GZip**.

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]
        
    1. W przypadku wybrania opcji **JSON**należy również wybrać **poziomy JSON**, od 1 do 10. Poziomy wpływają na obraz danych kolumny tabeli.

    ![Wybieranie poziomów JSON](media/one-click-ingestion-existing-table/json-levels.png)

    * W przypadku wybrania formatu innego niż JSON można zaznaczyć pole wyboru **Uwzględnij nazwy kolumn,** aby zignorować wiersz nagłówka pliku.
        
    ![Wybierz pozycję Dołącz nazwy kolumn](media/one-click-ingestion-existing-table/non-json-format.png)

    > [!Note]
    > Formaty tabelaryczne mogą pozyskiwania danych kolumn tylko do jednej kolumny w tabeli Usługi Azure Data Explorer. 

    * Nowe mapowania są ustawiane automatycznie, ale można je zmienić, aby użyć istniejącego. 
    * Można wybrać **opcję Kolumny Mapy,** aby otworzyć okno **Kolumny mapy.**

## <a name="copy-and-paste-queries"></a>Kopiowanie i wklejanie zapytań

1. Nad okienkiem **Edytor** wybierz przycisk **v,** aby otworzyć edytor. W edytorze można wyświetlać i kopiować polecenia automatyczne generowane na podstawie danych wejściowych. 
1. W tabeli: 
    * Zaznacz nowe nagłówki kolumn, aby dodać **nową kolumnę,** **Usuń kolumnę,** **Sortuj rosnąco**lub **Posortuj malejąco**. W istniejących kolumnach dostępne jest tylko sortowanie danych.

    > [!Note]
    > * Nie można zaktualizować nazwy i typu danych dla istniejących tabel.
    > * Polecenia upuszczania tylko przywrócić zmiany, które zostały wprowadzone przez jego przepływ pozyskiwania (nowe zakresy i kolumny). Nic innego nie zostanie upuszczone.

[![](media/one-click-ingestion-existing-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-existing-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Rozpocznij połkwanie

Wybierz **pozycję Rozpocznij pozyskiwania,** aby utworzyć tabelę i mapowanie oraz rozpocząć wprowadzanie danych.

![Rozpocznij połkwanie](media/one-click-ingestion-existing-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Połów danych zakończony

W oknie **Połów danych ukończone,** wszystkie trzy kroki zostaną oznaczone zielonymi znacznikami wyboru, jeśli położenie danych zakończy się pomyślnie.
 
![Zakończenie pozyskiwania danych jednym kliknięciem](media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Następne kroki

* [Zapytanie o dane w usłudze Azure Data Explorer web ui](/azure/data-explorer/web-query-data)
* [Pisanie zapytań dla Eksploratora danych platformy Azure przy użyciu języka zapytań Kusto](/azure/data-explorer/write-queries)
