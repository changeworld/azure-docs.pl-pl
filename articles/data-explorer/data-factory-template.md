---
title: Użyj szablonu Azure Data Factory do kopiowania zbiorczego z bazy danych na platformę Azure Eksplorator danych
description: W tym temacie dowiesz się, jak używać szablonu Azure Data Factory do kopiowania zbiorczego z bazy danych na platformę Azure Eksplorator danych
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 5a6aebd276ef8658da9ca763be7da5c38a9c772a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873434"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>Użyj szablonu Azure Data Factory do kopiowania zbiorczego z bazy danych na platformę Azure Eksplorator danych

Azure Eksplorator danych to szybka i w pełni zarządzana usługa analizy danych do analizy w czasie rzeczywistym w przypadku dużych ilości danych przesyłanych strumieniowo z wielu źródeł, takich jak aplikacje, witryny sieci Web i urządzenia IoT. Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze. Możesz użyć usługi Azure Data Factory, aby wypełnić bazę danych platformy Azure Eksplorator danych danymi z istniejącego systemu i zaoszczędzić czas podczas kompilowania rozwiązań analitycznych. 

[Szablony Azure Data Factory](/azure/data-factory/solution-templates-introduction) są wstępnie zdefiniowanych Azure Data Factory potoków, które pozwalają szybko rozpocząć pracę z Data Factory i skrócić czas opracowywania projektów integracji danych. **Kopiowanie masowe z bazy danych do platformy Azure Eksplorator danych** szablon jest tworzony przy użyciu działań **wyszukiwania** i **foreach** . Możesz użyć szablonu, aby utworzyć wiele potoków dla bazy danych lub tabeli w celu przyspieszenia kopiowania danych. 

> [!IMPORTANT]
> * Aby skopiować duże ilości danych z baz danych, takich jak SQL Server i Google BigQuery do usługi Azure Eksplorator danych, użyj szablonu **kopiowania zbiorczego z bazy danych do usługi azure Eksplorator danych** . 
> * Za pomocą [Narzędzia do kopiowania](data-factory-load-data.md) Skopiuj kilka tabel z małymi lub średnimi ilościami danych na platformę Azure Eksplorator danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Klaster Eksplorator danych i baza danych platformy Azure](create-cluster-database-portal.md)
* [Tworzenie fabryki danych](data-factory-load-data.md#create-a-data-factory)
* Źródło danych w bazie danych

## <a name="create-controltabledataset"></a>Utwórz ControlTableDataset

**ControlTableDataset** wskazuje, jakie dane zostaną skopiowane z lokalizacji źródłowej do docelowej w potoku. Liczba wierszy wskazuje całkowitą liczbę potoków wymaganych do skopiowania danych. **ControlTableDataset** należy zdefiniować jako część źródłowej bazy danych.

Przykład formatu tabeli źródłowej SQL Server:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```
    
|Właściwość  |Opis  | Przykład
|---------|---------| ---------|
|PartitionId   |   Kopiuj kolejność | 1  |  
|SourceQuery   |   zapytanie wskazujące, które dane zostaną skopiowane podczas wykonywania potoku | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  Nazwa tabeli docelowej | MyAdxTable       |  

Jeśli **ControlTableDataset** jest w innym formacie, Utwórz porównywalny **ControlTableDataset** dla formatu.

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>Używanie kopiowania zbiorczego z bazy danych do szablonu Eksplorator danych platformy Azure

1. **Na stronie Wprowadzenie** wybierz kafelek **Utwórz potok na podstawie szablonu** lub wybierz ikonę ołówka (karta**autor** ) po prawej stronie > **+**  >  **potoku z szablonu** , aby otworzyć galerię szablonów.

    ![Azure Data Factory Zacznijmy](media/data-factory-template/adf-get-started.png)

1. Wybierz pozycję Szablon **Kopiuj zbiorczo z bazy danych na platformę Azure Eksplorator danych**.
 
    ![Wybierz potok z szablonu](media/data-factory-template/pipeline-from-template.png)

1.  W oknie **Kopiowanie zbiorcze z bazy danych do platformy Azure Eksplorator danych** wybierz z listy rozwijanej pozycję istniejące zestawy. 

    * **ControlTableDataset** — połączona usługa w celu kontrolowania tabeli, która wskazuje, jakie dane są kopiowane z lokalizacji źródłowej do docelowej i gdzie zostaną umieszczone w miejscu docelowym. 
    * **SourceDataset** — połączona usługa do źródłowej bazy danych. 
    * **AzureDataExplorerTable** — tabela Eksplorator danych platformy Azure. Jeśli zestaw danych nie istnieje, [Utwórz połączoną usługę Azure Eksplorator danych](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) , aby dodać zestaw danych.
    * Wybierz pozycję **Użyj tego szablonu**.

    ![Skonfiguruj szablon Eksplorator danych kopiowania zbiorczego na platformie Azure](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Wybierz obszar na kanwie poza działaniami, aby uzyskać dostęp do potoku szablonu. Wybierz **Parametry** , aby wprowadzić parametry dla tabeli, w tym **nazwę** (nazwę tabeli formantów) i **wartość domyślną** (nazwy kolumn).

    ![Parametry potoku](media/data-factory-template/pipeline-parameters.png)

1.  Wybierz działanie Lookup (wyszukiwanie), **GetPartitionList**, aby wyświetlić ustawienia domyślne. Zapytanie jest tworzone automatycznie.
1.  Wybierz pozycję działanie polecenia **ForEachPartition**, wybierz pozycję **Ustawienia**
    * **Liczba partii**: Wybierz liczbę z prze1-50. Ten wybór określa liczbę potoków, które są uruchamiane równolegle do momentu osiągnięcia liczby **ControlTableDataset** wierszy. 
    * NIE zaznaczaj pola wyboru **sekwencyjnego** , aby wsady potoku były uruchamiane równolegle.

    ![Ustawienia ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Najlepszym rozwiązaniem jest równoległe uruchamianie wielu potoków, aby dane mogły być kopiowane szybciej. Podziel dane w tabeli źródłowej i Przydziel partycję na potok, zgodnie z datą i tabelą, aby zwiększyć wydajność.

1. Wybierz pozycję **Weryfikuj wszystkie** , aby zweryfikować potok ADF. Zobacz **dane wyjściowe walidacji potoku**.

    ![Weryfikuj potoki szablonów](media/data-factory-template/validate-template-pipelines.png)

1. Wybierz opcję **Debuguj**, w razie potrzeby, a następnie **Dodaj wyzwalacz** , aby uruchomić potok.

    ![Uruchom potok](media/data-factory-template/trigger-run-of-pipeline.png)    


Teraz można użyć szablonu **kopiowania zbiorczego z bazy danych do platformy Azure Eksplorator danych** do wydajnego kopiowania dużych ilości danych z baz danych i tabel.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat procedury [kopiowania danych do platformy Azure Eksplorator danych przy użyciu Azure Data Factory](data-factory-load-data.md).

* Więcej informacji na temat [łącznika usługi Azure Eksplorator danych](/azure/data-factory/connector-azure-data-explorer) w programie Azure Data Factory.

* Dowiedz się więcej na temat [zapytań dotyczących usługi Azure Eksplorator danych](/azure/data-explorer/web-query-data) na potrzeby zapytań dotyczących danych.






