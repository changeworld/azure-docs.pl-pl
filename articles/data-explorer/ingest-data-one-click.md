---
title: Użyj pozyskiwania jednego kliknięcia do pozyskiwania danych w usłudze Azure Eksplorator danych
description: Dowiedz się, jak pozyskiwanie (ładować) danych do platformy Azure Eksplorator danych po prostu przy użyciu pozyskiwania jednego kliknięcia.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520051"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Użyj pozyskiwania jednego kliknięcia do pozyskiwania danych w usłudze Azure Eksplorator danych

W tym artykule przedstawiono sposób użycia pozyskiwania jednym kliknięciem w celu szybkiego pozyskiwania nowej tabeli w formatach JSON lub CSV z magazynu na platformie Azure Eksplorator danych. Po pobraniu danych możesz edytować tabelę i uruchamiać zapytania przy użyciu interfejsu użytkownika sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Zaloguj się do [aplikacji](https://dataexplorer.azure.com/).
* Tworzenie [klastra usługi Azure Eksplorator danych i bazy danych](create-cluster-database-portal.md)
* Źródło danych w usłudze Azure Storage.

## <a name="ingest-new-data"></a>Pozyskiwanie nowych danych

1. Kliknij prawym przyciskiem myszy *nazwę bazy danych* i wybierz pozycję pozyskiwanie **nowych danych (wersja zapoznawcza)**

    ![Wybierz jedną pozycję pozyskiwania w interfejsie użytkownika sieci Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. W oknie pozyskiwanie **danych (wersja zapoznawcza)** na karcie **Źródło** Uzupełnij **szczegóły projektu**:

    * Wprowadź nową **nazwę tabeli**. 
    * Wybierz **Typ** > pozyskiwania**z magazynu**.
    * Wprowadź **link do magazynu** Dodaj adres URL do magazynu. Użyj adresu URL sygnatury dostępu współdzielonego obiektu BLOB dla prywatnych kont magazynu. 
    * Wybierz pozycję **Edytuj schemat**
 
    ![jedno kliknięcie szczegółów źródła pozyskiwania](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. Na karcie **schemat** wybierz pozycję **Format danych** z listy rozwijanej > **JSON** lub **CSV**. 
   
   W przypadku wybrania opcji **CSV**:
    * Zaznacz pole wyboru **Ignoruj nagłówek** , aby zignorować wiersz nagłówka pliku CSV.    
    * **Nazwa mapowania** jest ustawiana automatycznie, ale można ją edytować.

    ![jeden kliknięcie formatu CSV pozyskiwania schematu. png](media/ingest-data-one-click/one-click-csv-format.png)

   Jeśli wybierzesz pozycję **JSON**:
    * Wybierz **poziomy JSON**: 1-10 z listy rozwijanej. Poziomy w pliku JSON są wyświetlane w tabeli w prawym dolnym rogu. 
    * **Nazwa mapowania** jest ustawiana automatycznie, ale można ją edytować.

    ![jeden kliknięcie schematu formatu JSON pozyskiwania](media/ingest-data-one-click/one-click-json-format.png)  

1. W **Edytorze**wybierz pozycję **V** po prawej stronie, aby otworzyć Edytor. W edytorze można wyświetlać i kopiować automatyczne zapytania generowane na podstawie danych wejściowych. 

1.  W tabeli w prawym dolnym rogu: 
    * Wybierz pozycję **V** po prawej stronie kolumny, aby **zmienić nazwę kolumny**, **Usuń kolumnę**, **Sortuj rosnąco**lub **Sortuj malejąco**
    * Kliknij dwukrotnie nazwę kolumny, aby edytować.
    * Wybierz ikonę z lewej strony nazwy kolumny, aby zmienić typ danych. 

1. Wybierz pozycję **Rozpocznij** pozyskiwanie, aby utworzyć tabelę, utworzyć mapowanie i pozyskiwanie danych.
 
## <a name="query-data"></a>Zapytania o dane

1. W oknie **ukończono** pozyskiwanie danych wszystkie trzy kroki zostaną oznaczone zielonymi znacznikami wyboru, jeśli pozyskiwanie danych zakończyło się pomyślnie. 
 
    ![ukończono pozyskiwanie danych z jednego kliknięcia](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Wybierz pozycję **V** , aby otworzyć zapytanie. Kopiuj do interfejsu użytkownika sieci Web, aby edytować zapytanie.

1. Menu po prawej stronie zawiera **szybkie zapytania** i **Narzędzia**. 

    * **Szybkie zapytania** obejmują linki do interfejsu użytkownika sieci Web przy użyciu przykładowych zapytań.
    * **Narzędzia** obejmują link do interfejsu użytkownika sieci Web z **poleceniami Drop** , które umożliwiają rozwiązywanie problemów, `.drop` uruchamiając odpowiednie polecenie.

    > [!TIP]
    > Dane mogą zostać utracone przy `.drop` użyciu poleceń. Korzystaj z nich uważnie.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w interfejsie użytkownika sieci Web usługi Azure Eksplorator danych](web-query-data.md)
* [Zanotuj zapytania dotyczące usługi Azure Eksplorator danych przy użyciu języka zapytań Kusto](write-queries.md)
