---
title: Ponowne przyjmanie za pomocą jednego kliknięcia umożliwia wyłudzenie danych do Eksploratora danych platformy Azure
description: Dowiedz się, jak pozyskiwania (ładowania) danych do usługi Azure Data Explorer po prostu przy użyciu jednego kliknięcia pozyskiwania.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444551"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Ponowne przyjmanie za pomocą jednego kliknięcia umożliwia wyłudzenie danych do Eksploratora danych platformy Azure

W tym artykule pokazano, jak używać pozyskiwania jednym kliknięciem do szybkiego pozyskiwania nowej tabeli w formatach JSON lub CSV. Dane można pozyskiwania z magazynu lub pliku lokalnego do istniejącej tabeli lub nowej tabeli. Użyj intuicyjnego kreatora jednym kliknięciem, a twoje dane połkną w ciągu kilku minut. Następnie można edytować tabelę i uruchamiać kwerendy przy użyciu interfejsu użytkownika usługi Azure Data Explorer w sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Zaloguj się do [aplikacji](https://dataexplorer.azure.com/).
* Tworzenie [klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md).
* Zaloguj się do [interfejsu użytkownika sieci Web](https://dataexplorer.azure.com/) i dodaj połączenie do [klastra](/azure/data-explorer/web-query-data#add-clusters).

## <a name="ingest-new-data"></a>Połknienie nowych danych

1. Kliknij prawym przyciskiem myszy *wiersz bazy danych* lub *tabeli* w lewym menu interfejsu internetowego i wybierz pozycję **Połknieć nowe dane (Wersja zapoznawcza)**.

    ![Wybieranie pozyskiwania jednym kliknięciem w interfejsie użytkownika sieci Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. W oknie **Pozyskiwanie nowych danych (Podgląd)** wybierz kartę **Źródło** i uzupełnij **szczegóły projektu:**

    * W obszarze **Tabela**wybierz istniejącą nazwę tabeli z menu rozwijanego lub wybierz pozycję **Utwórz nową** tabelę.
    * W przypadku **typu pozyskiwania**wybierz opcję **z magazynu** lub z **pliku**.
      * Jeśli wybrano **opcję z magazynu,** wybierz **pozycję Łącze do magazynu,** aby dodać adres URL. Użyj [adresu URL sygnatury dostępu Współdzielonego](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) obiektów Blob dla kont magazynu prywatnego. 
      * Jeśli wybrano **opcję z pliku,** wybierz pozycję **Przeglądaj** i przeciągnij plik do tego pola.
    * Wybierz **pozycję Edytuj schemat,** aby wyświetlić i edytować konfigurację kolumny tabeli.
 
    ![Szczegóły źródła pozyskiwania jednym kliknięciem](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Jeśli wybierzesz opcję **Połknienie nowych danych (Podgląd)** w wierszu *tabeli,* wybrana nazwa tabeli pojawi się w **pliku Szczegóły projektu**.

1. Jeśli zaznaczono istniejącą tabelę, zostanie otwarte okno **Kolumny mapy,** aby zamapować kolumny danych źródłowych na kolumny tabeli docelowej. 
    * Użyj **kolumny Pomiń,** aby usunąć kolumnę docelową z tabeli.
    * Użyj **kolumny Nowy,** aby dodać nową kolumnę do tabeli.

    ![Okno Kolumny mapy](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Na karcie **Schemat:**

    * Z menu rozwijanego wybierz **opcję Typ kompresji,** a następnie wybierz opcję **Nieskompresowany** lub **GZip**.
    * Z menu rozwijanego wybierz **opcję Format danych,** a następnie wybierz polecenie **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**lub **PSV**. 
        * Po wybraniu formatu **JSON** należy również wybrać **poziomy JSON**, od 1 do 10. Poziomy wpływają na obraz danych kolumny tabeli. 
        * W przypadku wybrania formatu innego niż JSON należy zaznaczyć pole wyboru **Uwzględnij nazwy kolumn,** aby zignorować wiersz nagłówka pliku.
    * **Nazwa mapowania** jest ustawiana automatycznie, ale może być edytowana.
    * Jeśli zaznaczono istniejącą tabelę, możesz wybrać **opcję Kolumny Mapy,** aby otworzyć okno **Kolumny Mapy.**

    ![Schemat formatu CSV pozyskiwania jednym kliknięciem](media/ingest-data-one-click/one-click-csv-format.png)

1. Nad okienkiem **Edytor** wybierz przycisk **v,** aby otworzyć edytor. W edytorze można wyświetlać i kopiować automatyczne zapytania generowane na podstawie danych wejściowych. 

1. W tabeli: 
    * Kliknij prawym przyciskiem myszy nowe nagłówki kolumn na **Zmień typ danych,** **Zmień nazwę kolumny**, Usuń **kolumnę,** **Sortuj rosnąco**lub **Sortuj malejąco**. W istniejących kolumnach dostępne jest tylko sortowanie danych. 
    * Kliknij dwukrotnie nową nazwę kolumny, aby edytować.

1. Wybierz **pozycję Rozpocznij pozyskiwania,** aby utworzyć tabelę i mapowanie oraz rozpocząć wprowadzanie danych.

    ![Schemat formatu JSON z jednym kliknięciem](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Zapytania o dane

1. W oknie **Połów danych ukończone,** wszystkie trzy kroki zostaną oznaczone zielonymi znacznikami wyboru, jeśli położenie danych zakończy się pomyślnie.
 
    ![Zakończenie pozyskiwania danych jednym kliknięciem](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Wybierz przycisk **v,** aby otworzyć kwerendę. Skopiuj go do interfejsu użytkownika sieci Web, aby edytować kwerendę.

1. Menu po prawej stronie zawiera **szybkie zapytania** i opcje **Narzędzia.** 

    * **Szybkie zapytania** zawierają łącza do interfejsu użytkownika sieci Web z przykładowymi kwerendami.
    * **Narzędzia** zawierają łącze do **poleceń upuszczania** w interfejsie użytkownika `.drop` sieci Web, które umożliwiają rozwiązywanie problemów przez uruchomienie odpowiednich poleceń.

    > [!TIP]
    > Dane mogą zostać utracine podczas używania `.drop` poleceń. Używaj ich ostrożnie.

## <a name="next-steps"></a>Następne kroki

* [Zapytanie o dane w usłudze Azure Data Explorer web ui](web-query-data.md)
* [Pisanie zapytań dla Eksploratora danych platformy Azure przy użyciu języka zapytań Kusto](write-queries.md)
