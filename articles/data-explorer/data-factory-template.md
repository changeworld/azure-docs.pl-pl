---
title: Kopiowanie zbiorczo z bazy danych do Eksploratora danych platformy Azure przy użyciu szablonu usługi Azure Data Factory
description: W tym artykule nauczysz się używać szablonu usługi Azure Data Factory do kopiowania zbiorczego z bazy danych do Eksploratora danych platformy Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293559"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Kopiowanie zbiorczo z bazy danych do Eksploratora danych platformy Azure przy użyciu szablonu usługi Azure Data Factory 

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa analizy danych. Oferuje analizę w czasie rzeczywistym na dużych ilościach danych, które przesyłają strumieniowo z wielu źródeł, takich jak aplikacje, witryny sieci Web i urządzenia IoT. 

Aby skopiować dane z bazy danych w Oracle Server, Netezza, Teradata lub SQL Server do Usługi Azure Data Explorer, należy załadować ogromne ilości danych z wielu tabel. Zazwyczaj dane muszą być podzielone na partycje w każdej tabeli, dzięki czemu można załadować wiersze z wieloma wątkami równolegle z jednej tabeli. W tym artykule opisano szablon do użycia w tych scenariuszach.

[Szablony usługi Azure Data Factory](/azure/data-factory/solution-templates-introduction) są wstępnie zdefiniowanymi potokami fabryki danych. Te szablony mogą pomóc szybko rozpocząć pracę z fabryką danych i skrócić czas opracowywania projektów integracji danych. 

*Tworzenie kopiowania zbiorczego z bazy danych do usługi Azure Data Explorer* szablon przy użyciu funkcji *odnośnik* i *forEach* działań. Aby przyspieszyć kopiowanie danych, można użyć szablonu do utworzenia wielu potoków na bazę danych lub na tabelę. 

> [!IMPORTANT]
> Pamiętaj, aby użyć narzędzia odpowiedniego dla ilości danych, które chcesz skopiować.
> * Użyj szablonu *Kopiowanie zbiorcze z bazy danych do Eksploratora danych platformy Azure,* aby skopiować duże ilości danych z baz danych, takich jak serwer SQL i Google BigQuery do Eksploratora danych platformy Azure. 
> * Narzędzie [*Kopiowanie danych z fabryki danych*](data-factory-load-data.md) służy do kopiowania kilku tabel z małymi lub umiarkowanymi ilościami danych do Eksploratora danych platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Baza danych i klaster usługi Azure Data Explorer](create-cluster-database-portal.md).
* [Tworzenie fabryki danych](data-factory-load-data.md#create-a-data-factory).
* Źródło danych w bazie danych.

## <a name="create-controltabledataset"></a>Utwórz zestaw danych controltable

*ControlTableDataset* wskazuje, jakie dane zostaną skopiowane ze źródła do miejsca docelowego w potoku. Liczba wierszy wskazuje całkowitą liczbę potoków, które są potrzebne do skopiowania danych. ControlTableDataset należy zdefiniować jako część źródłowej bazy danych.

Przykład formatu tabeli źródłowej programu SQL Server jest wyświetlany w następującym kodzie:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Elementy kodu są opisane w poniższej tabeli:

|Właściwość  |Opis  | Przykład
|---------|---------| ---------|
|Partitionid   |  Kolejność kopiowania | 1  |  
|ŹródłoQuery   |  Kwerenda wskazująca, które dane zostaną skopiowane w czasie wykonywania potoku | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|AdXTableName (Nazwa tabeli ADXTableName)  |  Nazwa tabeli docelowej | Tabela MyAdx       |  

Jeśli zestaw ControlTableDataset jest w innym formacie, utwórz porównywalny zestaw danych ControlTable dla formatu.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Użyj szablonu Kopiowanie zbiorcze z bazy danych do eksploratora danych platformy Azure

1. W okienku **Rozpocznijmy pracę** wybierz pozycję **Utwórz potok z szablonu,** aby otworzyć okienko **Galeria szablonów.**

    ![Okienko Fabryka danych platformy Azure "Rozpocznijmy"](media/data-factory-template/adf-get-started.png)

1. Wybierz szablon **Kopiowanie zbiorcze z bazy danych do usługi Azure Data Explorer.**
 
    ![Szablon "Kopiowanie zbiorcze z bazy danych do Eksploratora danych platformy Azure"](media/data-factory-template/pipeline-from-template.png)

1.  W okienku **Kopiowanie zbiorcze z bazy danych do usługi Azure Data Explorer** w obszarze Dane **wejściowe użytkownika**określ zestawy danych, wykonując następujące czynności: 

    a. Na liście rozwijanej **ControlTableDataset** wybierz połączoną usługę do tabeli kontrolnej, która wskazuje, jakie dane są kopiowane ze źródła do miejsca docelowego i gdzie zostaną umieszczone w miejscu docelowym. 

    b. Z listy rozwijanej **SourceDataset** wybierz usługę połączony z źródłową bazą danych. 

    d. Na liście rozwijanej **AzureDataExplorerTable** wybierz tabelę Eksploratora danych platformy Azure. Jeśli zestaw danych nie istnieje, [utwórz usługę połączony z Eksploratorem danych platformy Azure,](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) aby dodać zestaw danych.

    d. Wybierz pozycję **Użyj tego szablonu**.

    ![Okienko "Kopiowanie zbiorcze z bazy danych do eksploratora danych platformy Azure"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Wybierz obszar na kanwie, poza działaniami, aby uzyskać dostęp do potoku szablonu. Wybierz kartę **Parametry,** aby wprowadzić parametry tabeli, w tym **nazwę** (nazwa tabeli kontrolnej) i **wartość domyślną** (nazwy kolumn).

    ![Parametry potoku](media/data-factory-template/pipeline-parameters.png)

1.  W obszarze **Odnośniki**wybierz **pozycję GetPartitionList,** aby wyświetlić ustawienia domyślne. Kwerenda jest tworzona automatycznie.
1.  Wybierz działanie Polecenia, **ForEachPartition**, wybierz kartę **Ustawienia,** a następnie wykonaj następujące czynności:

    a. W polu **Liczba partii** wprowadź liczbę od 1 do 50. To zaznaczenie określa liczbę potoków, które działają równolegle, aż do osiągnięcia liczby wierszy *ControlTableDataset.* 

    b. Aby upewnić się, że partie potoku działają równolegle, *nie* należy zaznaczać pola wyboru **Sekwencyjne.**

    ![Ustawienia forEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Najlepszym rozwiązaniem jest uruchomienie wielu potoków równolegle, dzięki czemu dane mogą być kopiowane szybciej. Aby zwiększyć wydajność, partycjonuj dane w tabeli źródłowej i przydzielić jedną partycję na potok, zgodnie z datą i tabelą.

1. Wybierz **opcję Sprawdź poprawność wszystkich,** aby sprawdzić poprawność potoku usługi Azure Data Factory, a następnie wyświetlić wynik w okienku **danych wyjściowych sprawdzania poprawności potoku.**

    ![Sprawdzanie poprawności potoków szablonów](media/data-factory-template/validate-template-pipelines.png)

1. W razie potrzeby wybierz opcję **Debugowanie**, a następnie wybierz pozycję **Dodaj wyzwalacz,** aby uruchomić potok.

    ![Przyciski "Debugowanie" i "Uruchom potok"](media/data-factory-template/trigger-run-of-pipeline.png)    

Teraz można użyć szablonu, aby skutecznie kopiować duże ilości danych z baz danych i tabel.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [kopiować dane do Eksploratora danych platformy Azure przy użyciu usługi Azure Data Factory](data-factory-load-data.md).
* Dowiedz się więcej o [łączniku usługi Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) w usłudze Azure Data Factory.
* Dowiedz się więcej o [kwerendach usługi Azure Data Explorer](/azure/data-explorer/web-query-data) dotyczących zapytań dotyczących danych.






