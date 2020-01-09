---
title: Użyj pozyskiwania jednego kliknięcia do pozyskiwania danych w usłudze Azure Eksplorator danych
description: Dowiedz się, jak pozyskiwanie (ładować) danych do platformy Azure Eksplorator danych po prostu przy użyciu pozyskiwania jednego kliknięcia.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444551"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Użyj pozyskiwania jednego kliknięcia do pozyskiwania danych w usłudze Azure Eksplorator danych

W tym artykule przedstawiono sposób użycia pozyskiwania jednym kliknięciem w celu szybkiego pozyskiwania nowej tabeli w formatach JSON lub CSV. Dane można pozyskać z magazynu lub lokalnego pliku do istniejącej tabeli lub nowej tabeli. Użyj intuicyjnego kreatora jednego kliknięcia, a dane pozyskają się w ciągu kilku minut. Następnie można edytować tabelę i uruchamiać zapytania za pomocą interfejsu użytkownika sieci Web usługi Azure Eksplorator danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Zaloguj się do [aplikacji](https://dataexplorer.azure.com/).
* Utwórz [klaster Eksplorator danych i bazę danych platformy Azure](create-cluster-database-portal.md).
* Zaloguj się do [interfejsu użytkownika sieci Web](https://dataexplorer.azure.com/) i [Dodaj połączenie z klastrem](/azure/data-explorer/web-query-data#add-clusters).

## <a name="ingest-new-data"></a>Pozyskiwanie nowych danych

1. Kliknij prawym przyciskiem myszy *bazę danych* lub wiersz *tabeli* w menu po lewej stronie interfejsu użytkownika sieci Web i wybierz pozycję pozyskiwanie **nowych danych (wersja zapoznawcza)** .

    ![Wybierz opcję pozyskiwania jednego kliknięcia w interfejsie użytkownika sieci Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. W oknie **nowe dane (wersja zapoznawcza)** wybierz kartę **Źródło** i wypełnij **szczegóły projektu**:

    * W polu **tabela**wybierz istniejącą nazwę tabeli z menu rozwijanego lub wybierz pozycję **Utwórz nową** , aby utworzyć nową tabelę.
    * W obszarze **Typ**pozyskiwania wybierz opcję **z magazynu** lub **z pliku**.
      * W przypadku wybrania opcji **Magazyn**wybierz pozycję **Połącz z magazynem** , aby dodać adres URL. Użyj [adresu URL sygnatury dostępu współdzielonego obiektu BLOB](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) dla prywatnych kont magazynu. 
      * W przypadku wybrania **z pliku**wybierz pozycję **Przeglądaj** i przeciągnij plik do pola.
    * Wybierz pozycję **Edytuj schemat** , aby wyświetlić i edytować konfigurację kolumny tabeli.
 
    ![Szczegóły źródła pozyskiwania jednego kliknięcia](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Jeśli wybierzesz pozycję pozyskiwanie **nowych danych (wersja zapoznawcza)** w wierszu *tabeli* , nazwa wybranej tabeli zostanie wyświetlona w obszarze **szczegóły projektu**.

1. W przypadku wybrania istniejącej tabeli zostanie otwarte okno **Mapowanie** kolumn w celu zamapowania kolumn danych źródłowych na kolumny tabeli docelowej. 
    * Użyj **kolumny Pomiń** , aby usunąć kolumnę docelową z tabeli.
    * Użyj **nowej kolumny** , aby dodać nową kolumnę do tabeli.

    ![Okno kolumn mapy](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Na karcie **schemat** :

    * Z menu rozwijanego wybierz opcję **Typ kompresji** , a następnie wybierz opcję **nieskompresowany** lub **gzip**.
    * Z menu rozwijanego wybierz opcję **Format danych** , a następnie wybierz pozycję **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**lub **PSV**. 
        * Po wybraniu formatu **JSON** należy również wybrać **poziomy JSON**, od 1 do 10. Poziomy wpływają na dane kolumn tabeli. 
        * W przypadku wybrania formatu innego niż JSON należy zaznaczyć pole wyboru **Uwzględnij nazwy kolumn** , aby zignorować wiersz nagłówka pliku.
    * **Nazwa mapowania** jest ustawiana automatycznie, ale można ją edytować.
    * W przypadku wybrania istniejącej tabeli możesz wybrać pozycję **Mapuj kolumny** , aby otworzyć okno **Mapowanie kolumn** .

    ![Schemat formatu CSV pozyskiwania jednego kliknięcia](media/ingest-data-one-click/one-click-csv-format.png)

1. Nad okienkiem **edytora** wybierz przycisk **v** , aby otworzyć Edytor. W edytorze można wyświetlać i kopiować automatyczne zapytania generowane na podstawie danych wejściowych. 

1. W tabeli: 
    * Kliknij prawym przyciskiem myszy pozycję nowe nagłówki kolumn, aby **zmienić typ danych**, **Zmień nazwę kolumny**, **Usuń kolumnę**, **Sortuj rosnąco**lub **Sortuj malejąco**. W przypadku istniejących kolumn dostępne jest tylko sortowanie danych. 
    * Kliknij dwukrotnie nazwę nowej kolumny do edycji.

1. Wybierz pozycję **Rozpocznij** pozyskiwanie, aby utworzyć tabelę i mapowanie i rozpocząć pozyskiwanie danych.

    ![Schemat formatu JSON pozyskiwania po jednym kliknięciu](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Zapytania o dane

1. W oknie **ukończono** pozyskiwanie danych wszystkie trzy kroki zostaną oznaczone zielonymi znacznikami wyboru, jeśli pozyskiwanie danych zakończy się pomyślnie.
 
    ![Ukończono pozyskiwanie danych jednym kliknięciem](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Wybierz przycisk **v** , aby otworzyć zapytanie. Skopiuj do interfejsu użytkownika sieci Web, aby edytować zapytanie.

1. Menu po prawej stronie zawiera **szybkie zapytania** i opcje **narzędzi** . 

    * **Szybkie zapytania** obejmują linki do interfejsu użytkownika sieci Web przy użyciu przykładowych zapytań.
    * **Narzędzia** zawierają link do **upuszczania poleceń** w interfejsie użytkownika sieci Web, co pozwala na rozwiązywanie problemów przez uruchomienie odpowiednich poleceń `.drop`.

    > [!TIP]
    > W przypadku używania poleceń `.drop` mogą zostać utracone dane. Korzystaj z nich uważnie.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w interfejsie użytkownika sieci Web usługi Azure Eksplorator danych](web-query-data.md)
* [Zanotuj zapytania dotyczące usługi Azure Eksplorator danych przy użyciu języka zapytań Kusto](write-queries.md)
