---
title: Ponowne przyjmowanie jednym kliknięciem umożliwia wyłudzenie danych do nowej tabeli Eksploratora danych platformy Azure
description: Połknąć (załadowanie) danych do nowej tabeli Usługi Azure Data Explorer po prostu przy użyciu jednego kliknięcia pozyskiwania.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 1e337a46d1d8ddda1b07bde6f12d4c1f7feda42f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549214"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-a-new-table-in-azure-data-explorer"></a>Przykuwanie jednym kliknięciem umożliwia pochłonienie danych do nowej tabeli w Eksploratorze danych platformy Azure

Połknienie jednym kliknięciem umożliwia szybkie wyłowienie danych w formacie JSON, CSV i innych formatach do tabeli. Za pomocą interfejsu użytkownika sieci Web usługi Azure Data Explorer można pozyskiwania danych z magazynu, z pliku lokalnego lub z kontenera. 

W tym dokumencie opisano użycie intuicyjnego kreatora jednym kliknięciem w celu pozyskiwania danych CSV z kontenera do nowej tabeli. Następnie można edytować tabelę i uruchamiać kwerendy za pomocą interfejsu internetowego usługi Azure Data Explorer. Można również ustawić ciągłe pozyskiwanie, aby automatycznie pozyskiwać dane do tabeli po aktualizacji danych źródłowych.

Łk 1 kliknięcia jest szczególnie przydatne podczas pozyskiwania danych po raz pierwszy lub gdy schemat danych jest nieznany. 

Aby zapoznać się z omówieniem pozyskiwania jednym kliknięciem i listą wymagań wstępnych, zobacz [Połkanie jednym kliknięciem](ingest-data-one-click.md).
Aby uzyskać informacje dotyczące pozyskiwania danych do istniejącej tabeli w Eksploratorze danych platformy Azure, zobacz [Łk 1. kliknięcie do istniejącej tabeli](one-click-ingestion-existing-table.md)

## <a name="ingest-new-data"></a>Połknienie nowych danych

1. W lewym menu interfejsu użytkownika sieci Web kliknij prawym przyciskiem myszy *bazę danych* i wybierz pozycję **Połknieć nowe dane (Wersja zapoznawcza)**.

    ![Wybieranie pozyskiwania jednym kliknięciem w interfejsie użytkownika sieci Web](media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png)   
 
1. W oknie **Pozyskiwanie nowych danych (Podgląd)** automatycznie jest wybierana jest karta **Źródło.** 

1. Wybierz **pozycję Utwórz nową tabelę** i wprowadź nazwę nowej tabeli. Można użyć alfanumeryczne, łączniki i podkreślenia. Znaki specjalne nie są obsługiwane.

![Tworzenie nowej tabeli](media/one-click-ingestion-new-table/create-new-table.png) 

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]

Wybierz **pozycję Edytuj schemat,** aby wyświetlić i edytować konfigurację kolumny tabeli. System wybierze jeden z obiektów blob losowo i schemat zostanie wygenerowany na podstawie tego obiektu blob. Patrząc na nazwę źródła, usługa automatycznie identyfikuje, czy jest skompresowany, czy nie.

## <a name="edit-the-schema"></a>Edytowanie schematu

1. Na karcie **Schemat:**

    1. Wybierz **format danych:**

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]

    1. W przypadku wybrania opcji **JSON**należy również wybrać **poziomy JSON**, od 1 do 10. Poziomy wpływają na obraz danych kolumny tabeli. 

    ![Wybieranie poziomów JSON](media/one-click-ingestion-new-table/json-levels.png)

    * W przypadku wybrania formatu innego niż JSON można zaznaczyć pole wyboru **Uwzględnij nazwy kolumn,** aby zignorować wiersz nagłówka pliku.

        ![Wybierz pozycję Dołącz nazwy kolumn](media/one-click-ingestion-new-table/non-json-format.png)
        
1. W polu **Nazwa mapowania** wprowadź nazwę mapowania. Można użyć znaków alfanumeryczne i podkreśleń. Spacje, znaki specjalne i łączniki nie są obsługiwane.
    
    ![Nazwa mapowania tabeli](media/one-click-ingestion-new-table/table-mapping.png)

## <a name="copy-and-paste-queries"></a>Kopiowanie i wklejanie zapytań

1. Nad okienkiem **Edytor** wybierz przycisk **v,** aby otworzyć edytor. W edytorze można wyświetlać i kopiować automatyczne zapytania generowane na podstawie danych wejściowych. 
1. W tabeli: 
    * Kliknij dwukrotnie nową nazwę kolumny, aby edytować.
    * Zaznacz nowe nagłówki kolumn i wykonaj dowolną z następujących czynności:
    
|Akcja         |Opis                                  |
|-----------------|-------------------------------------------|
|Zmiana typu danych |Zmienianie typu danych z automatycznie wybranego przez usługę na jeden z innych [obsługiwanych typów danych](#edit-the-schema)|
|Zmień nazwę kolumny    |Zmienianie nazwy kolumny |
|Nowa kolumna       |Dodawanie nowej kolumny|
|Usuń kolumnę    |Usuwanie zaznaczonej kolumny|
|Sortuj rosnąco   |Sortowanie tabeli według zaznaczonej kolumny w porządku rosnącym (tylko istniejące kolumny)|
|Sortuj malejąco  |Sortowanie tabeli według zaznaczonej kolumny w porządku malejącym (tylko istniejące kolumny) |

> [!Note]
> W przypadku formatów tabelarów każdej kolumny można przybyć do jednej kolumny w Eksploratorze danych platformy Azure.
> Można tworzyć nowe kolumny z różnych poziomów JSON.

[![](media/one-click-ingestion-new-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-new-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Rozpocznij połkwanie

Wybierz **pozycję Rozpocznij pozyskiwania,** aby utworzyć tabelę i mapowanie oraz rozpocząć wprowadzanie danych.
![Rozpocznij połkwanie](media/one-click-ingestion-new-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Połów danych zakończony

W oknie **Połów danych ukończone,** wszystkie trzy kroki zostaną oznaczone zielonymi znacznikami wyboru, jeśli położenie danych zakończy się pomyślnie.
 
![Zakończenie pozyskiwania danych jednym kliknięciem](media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

**Ciągłe połknienie**

Ciągłe pozyskiwanie umożliwia utworzenie siatki zdarzeń, która nasłuchuje kontenera źródłowego. Każdy nowy obiekt blob, który spełnia kryteria wstępnie zdefiniowanych parametrów (prefiks, sufiks itd.) zostanie automatycznie wynajęty do tabeli docelowej.

> [!Note]
> Ciągłe połknienie jest istotne tylko podczas połkwania z pojemnika.

1. Wybierz **ciągłe pozyskiwania,** aby otworzyć witrynę Azure portal. Strona połączenia danych zostanie otwarta z otwartym łącznikiem danych siatki zdarzeń z wprowadzonymi już parametrami źródłowymi i docelowymi (kontener źródłowy, tabele i mapowania).

1. Wybierz **utwórz,** aby utworzyć połączenie danych, które będzie nasłuchiwać wszelkich zmian w tym kontenerze kontenera. 

## <a name="next-steps"></a>Następne kroki

* [Zapytanie o dane w usłudze Azure Data Explorer web ui](/azure/data-explorer/web-query-data)
* [Pisanie zapytań dla Eksploratora danych platformy Azure przy użyciu języka zapytań Kusto](/azure/data-explorer/write-queries)
