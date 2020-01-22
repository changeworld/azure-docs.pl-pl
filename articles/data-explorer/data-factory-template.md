---
title: Kopiuj zbiorczo z bazy danych na platformę Azure Eksplorator danych przy użyciu szablonu Azure Data Factory
description: W tym artykule dowiesz się, jak używać szablonu Azure Data Factory do zbiorczego kopiowania z bazy danych na platformę Azure Eksplorator danych
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293559"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Kopiuj zbiorczo z bazy danych na platformę Azure Eksplorator danych przy użyciu szablonu Azure Data Factory 

Azure Eksplorator danych to szybka i w pełni zarządzana usługa analizy danych. Oferuje ona analizę w czasie rzeczywistym w przypadku dużych ilości danych przesyłanych strumieniowo z wielu źródeł, takich jak aplikacje, witryny sieci Web i urządzenia IoT. 

Aby skopiować dane z bazy danych programu Oracle Server, Netezza, Teradata lub SQL Server do usługi Azure Eksplorator danych, należy załadować ogromne ilości danych z wielu tabel. Zwykle dane należy podzielić na partycje w każdej tabeli, aby można było załadować wiersze z wieloma wątkami równolegle z pojedynczej tabeli. W tym artykule opisano szablon, który ma być używany w tych scenariuszach.

[Szablony Azure Data Factory](/azure/data-factory/solution-templates-introduction) są wstępnie zdefiniowane Data Factory potoków. Te szablony mogą pomóc szybko rozpocząć pracę z Data Factory i skrócić czas opracowywania projektów integracji danych. 

*Kopię zbiorczą z bazy danych można utworzyć do szablonu Eksplorator danych platformy Azure* przy użyciu działań *wyszukiwania* i *foreach* . Aby przyspieszyć kopiowanie danych, można użyć szablonu do tworzenia wielu potoków na bazę danych lub na tabelę. 

> [!IMPORTANT]
> Upewnij się, że korzystasz z narzędzia, które jest odpowiednie dla ilości danych, które chcesz skopiować.
> * Aby skopiować duże ilości danych z baz danych, takich jak SQL Server i Google BigQuery do usługi Azure Eksplorator danych, użyj szablonu *kopiowania zbiorczego z bazy danych do usługi azure Eksplorator danych* . 
> * Za pomocą [*narzędzia Kopiowanie danych Data Factory*](data-factory-load-data.md) Skopiuj kilka tabel z małymi lub średnimi ilościami danych na platformę Azure Eksplorator danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Baza danych i klaster usługi Azure Data Explorer](create-cluster-database-portal.md).
* [Utwórz fabrykę danych](data-factory-load-data.md#create-a-data-factory).
* Źródło danych w bazie danych.

## <a name="create-controltabledataset"></a>Utwórz ControlTableDataset

*ControlTableDataset* wskazuje, jakie dane zostaną skopiowane ze źródła do miejsca docelowego w potoku. Liczba wierszy wskazuje całkowitą liczbę potoków, które są konieczne do kopiowania danych. Należy zdefiniować ControlTableDataset jako część źródłowej bazy danych.

Przykład formatu tabeli źródłowej SQL Server przedstawiono w poniższym kodzie:
    
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
|PartitionId   |  Kolejność kopiowania | 1  |  
|SourceQuery   |  Zapytanie wskazujące, które dane zostaną skopiowane podczas wykonywania potoku | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  Nazwa tabeli docelowej | MyAdxTable       |  

Jeśli ControlTableDataset jest w innym formacie, Utwórz porównywalny ControlTableDataset dla formatu.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Używanie kopiowania zbiorczego z bazy danych do szablonu Eksplorator danych platformy Azure

1. W okienku **wprowadzenie** wybierz pozycję **Utwórz potok na podstawie szablonu** , aby otworzyć okienko **galerii szablonów** .

    ![Okienko Azure Data Factory "wprowadzenie"](media/data-factory-template/adf-get-started.png)

1. Wybierz szablon **Eksplorator danych kopiowania zbiorczego z bazy danych do platformy Azure** .
 
    ![Szablon "Zbiorcza kopia z bazy danych do platformy Azure Eksplorator danych"](media/data-factory-template/pipeline-from-template.png)

1.  W okienku **Kopiuj zbiorczo z bazy danych do platformy Azure Eksplorator danych** w obszarze **dane wejściowe użytkownika**Określ zestawy danych, wykonując następujące czynności: 

    a. Z listy rozwijanej **ControlTableDataset** wybierz połączoną usługę do tabeli sterowanie, która wskazuje, jakie dane są kopiowane ze źródła do miejsca docelowego i gdzie zostanie umieszczone w miejscu docelowym. 

    b. Z listy rozwijanej **SourceDataset** wybierz połączoną usługę do źródłowej bazy danych. 

    d. Z listy rozwijanej **AzureDataExplorerTable** wybierz tabelę Eksplorator danych platformy Azure. Jeśli zestaw danych nie istnieje, [Utwórz połączoną usługę Azure Eksplorator danych](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) , aby dodać zestaw danych.

    d. Wybierz przycisk **Użyj tego szablonu**.

    ![Okienko "kopiowanie masowe z bazy danych do platformy Azure Eksplorator danych"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Wybierz obszar na kanwie poza działaniami, aby uzyskać dostęp do potoku szablonu. Wybierz kartę **Parametry** , aby wprowadzić parametry dla tabeli, w tym **nazwę** (nazwę tabeli formantów) i **wartość domyślną** (nazwy kolumn).

    ![Parametry potoku](media/data-factory-template/pipeline-parameters.png)

1.  W obszarze **odnośnik**wybierz pozycję **GetPartitionList** , aby wyświetlić ustawienia domyślne. Zapytanie jest tworzone automatycznie.
1.  Wybierz działanie polecenie, **ForEachPartition**, wybierz kartę **Ustawienia** , a następnie wykonaj następujące czynności:

    a. W polu **Liczba partii** wprowadź liczbę z przestawu od 1 do 50. Ten wybór określa liczbę potoków, które są uruchamiane równolegle do momentu osiągnięcia liczby wierszy *ControlTableDataset* . 

    b. Aby upewnić się, że wsady potoku *są* uruchamiane równolegle, nie zaznaczaj pola wyboru **sekwencyjnego** .

    ![Ustawienia ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Najlepszym rozwiązaniem jest uruchomienie wielu potoków równolegle, aby dane mogły być kopiowane szybciej. Aby zwiększyć wydajność, Podziel dane w tabeli źródłowej i Przydziel jedną partycję na potok, zgodnie z datą i tabelą.

1. Wybierz pozycję **Weryfikuj wszystkie** , aby sprawdzić potoku Azure Data Factory, a następnie Wyświetl wyniki w okienku **danych wyjściowych walidacji potoku** .

    ![Weryfikuj potoki szablonów](media/data-factory-template/validate-template-pipelines.png)

1. W razie potrzeby wybierz pozycję **Debuguj**, a następnie wybierz pozycję **Dodaj wyzwalacz** , aby uruchomić potok.

    ![Przyciski "Debugowanie" i "przebieg potoku"](media/data-factory-template/trigger-run-of-pipeline.png)    

Teraz można używać szablonu do wydajnego kopiowania dużych ilości danych z baz danych i tabel.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak kopiować dane do usługi Azure Eksplorator danych przy użyciu Azure Data Factory](data-factory-load-data.md).
* Więcej informacji na temat [łącznika usługi Azure Eksplorator danych](/azure/data-factory/connector-azure-data-explorer) w programie Azure Data Factory.
* Dowiedz się więcej na temat [zapytań dotyczących usługi Azure Eksplorator danych](/azure/data-explorer/web-query-data) na potrzeby zapytań dotyczących danych.






