---
title: Użyj pozyskiwania jednego kliknięcia do pozyskiwania danych w usłudze Azure Eksplorator danych
description: Dowiedz się, jak pozyskiwanie (ładować) danych do platformy Azure Eksplorator danych po prostu przy użyciu pozyskiwania jednego kliknięcia.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 98598a28e14dfd8175cbb019ff1b001c65503580
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73644592"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Użyj pozyskiwania jednego kliknięcia do pozyskiwania danych w usłudze Azure Eksplorator danych

W tym artykule przedstawiono sposób użycia pozyskiwania jednym kliknięciem w celu szybkiego pozyskiwania nowej tabeli w formatach JSON lub CSV z magazynu na platformie Azure Eksplorator danych. Po pobraniu danych możesz edytować tabelę i uruchamiać zapytania przy użyciu interfejsu użytkownika sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Zaloguj się do [aplikacji](https://dataexplorer.azure.com/).
* Tworzenie [klastra usługi Azure Eksplorator danych i bazy danych](create-cluster-database-portal.md)
* Logowanie się do [interfejsu użytkownika sieci Web](https://dataexplorer.azure.com/) i [Dodawanie połączenia do klastra](/azure/data-explorer/web-query-data#add-clusters)
* Źródło danych w usłudze Azure Storage.

## <a name="ingest-new-data"></a>Pozyskiwanie nowych danych

1. Kliknij prawym przyciskiem myszy *bazę danych* lub wiersz *tabeli* w menu po lewej stronie interfejsu użytkownika sieci Web i wybierz pozycję pozyskiwanie **nowych danych (wersja zapoznawcza)**

    ![Wybierz jedną pozycję pozyskiwania w interfejsie użytkownika sieci Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. W oknie **nowe dane (wersja zapoznawcza)** na karcie **Źródło** Uzupełnij **szczegóły projektu**:

    * **Tabela**: wybierz z listy rozwijanej nazwę istniejącej tabeli lub wybierz pozycję **Utwórz nową** , aby utworzyć nową tabelę.
    * Wybierz **Typ** pozyskiwania > **z magazynu** lub **z pliku**.
        * W przypadku wybrania **z magazynu**wybierz pozycję **link do magazynu** , aby dodać adres URL do magazynu. Użyj [adresu URL sygnatury dostępu współdzielonego obiektu BLOB](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) dla prywatnych kont magazynu. 
        * W przypadku wybrania **z pliku**wybierz pozycję **Przeglądaj** i przeciągnij plik do pola.
    * Wybierz pozycję **Edytuj schemat** , aby wyświetlić i edytować konfigurację kolumny tabeli.
 
    ![jedno kliknięcie szczegółów źródła pozyskiwania](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Jeśli wybierzesz pozycję pozyskiwanie **nowych danych (wersja zapoznawcza)** w wierszu *tabeli* , nazwa wybranej tabeli zostanie wyświetlona w obszarze **szczegóły projektu**.

1. W przypadku wybrania istniejącej tabeli zostanie otwarte okno **Mapowanie** kolumn w celu zamapowania kolumn danych źródłowych na kolumny tabeli docelowej. 
    * Użyj **kolumny Pomiń** , aby usunąć kolumnę docelową z tabeli. 
    * Użyj **nowej kolumny** , aby dodać nową kolumnę do tabeli. 

    ![Okno kolumn mapy](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Na karcie **schemat** :

    * Wybierz **Typ kompresji** z listy rozwijanej > **nieskompresowany** lub **gzip**.
    * Wybierz **Format danych** z listy rozwijanej > **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**lub **PSV**. 
        * Po wybraniu formatu **JSON** wybierz pozycję **poziomy JSON**: 1-10. Poziomy wpływają na dane kolumn tabeli. 
        * W przypadku wybrania formatu innego niż JSON: zaznacz pole wyboru **Uwzględnij nazwy kolumn** , aby zignorować wiersz nagłówka pliku.    
    * **Nazwa mapowania** jest ustawiana automatycznie, ale można ją edytować.
    * W przypadku wybrania istniejącej tabeli możesz wybrać przycisk **Mapuj kolumny** , aby otworzyć okno **Mapowanie kolumn** .

    ![jeden kliknięcie formatu CSV pozyskiwania schematu. png](media/ingest-data-one-click/one-click-csv-format.png)

1. W **Edytorze**wybierz pozycję **V** z prawej strony, aby otworzyć Edytor. W edytorze można wyświetlać i kopiować automatyczne zapytania generowane na podstawie danych wejściowych. 

1.  W tabeli: 
    * Kliknij prawym przyciskiem myszy nowe nagłówki kolumn, aby **zmienić typ danych**, **Zmień nazwę kolumny**, **Usuń kolumnę**, **Sortuj rosnąco**lub **Sortuj malejąco**. W przypadku istniejących kolumn dostępne są tylko sortowanie danych. 
    * Kliknij dwukrotnie nazwę nowej kolumny do edycji.

1. Wybierz pozycję **Rozpocznij** pozyskiwanie, aby utworzyć tabelę, utworzyć mapowanie i pozyskiwanie danych.

    ![jeden kliknięcie schematu formatu JSON pozyskiwania](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Zapytania o dane

1. W oknie **ukończono** pozyskiwanie danych wszystkie trzy kroki zostaną oznaczone zielonymi znacznikami wyboru, jeśli pozyskiwanie danych zakończyło się pomyślnie. 
 
    ![ukończono pozyskiwanie danych z jednego kliknięcia](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Wybierz pozycję **V** , aby otworzyć zapytanie. Kopiuj do interfejsu użytkownika sieci Web, aby edytować zapytanie.

1. Menu po prawej stronie zawiera **szybkie zapytania** i **Narzędzia**. 

    * **Szybkie zapytania** obejmują linki do interfejsu użytkownika sieci Web przy użyciu przykładowych zapytań.
    * **Narzędzia** obejmują link do interfejsu użytkownika sieci Web z **poleceniami Drop** , które umożliwiają rozwiązywanie problemów, uruchamiając odpowiednie `.drop` polecenie.

    > [!TIP]
    > Dane mogą zostać utracone przy użyciu poleceń `.drop`. Korzystaj z nich uważnie.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w interfejsie użytkownika sieci Web usługi Azure Eksplorator danych](web-query-data.md)
* [Zanotuj zapytania dotyczące usługi Azure Eksplorator danych przy użyciu języka zapytań Kusto](write-queries.md)
