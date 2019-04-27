---
title: Dodaj odporności na uszkodzenia w działaniu kopiowania w fabryce danych Azure, pomijania niezgodnych wierszy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać odporność na uszkodzenia w działaniu kopiowania w fabryce danych platformy Azure przez pominięcie niezgodnych wierszy podczas kopiowania
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3a255b21e8bfd7d78954603e9aa6e5ca39cee95b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60566081"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Dodaj odporności na uszkodzenia w działaniu kopiowania, pomijania niezgodnych wierszy

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-copy-activity-fault-tolerance.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [odporność na uszkodzenia w działaniu kopiowania usługi Data Factory](../copy-activity-fault-tolerance.md).

Usługa Azure Data Factory [działania kopiowania](data-factory-data-movement-activities.md) oferuje obsługę niezgodnych wierszy podczas kopiowania danych między magazynami danych źródła i ujścia na dwa sposoby:

- Można przerwać i zakończyć się niepowodzeniem kopii działanie po niezgodnych danych napotkało (zachowanie domyślne).
- Można kontynuować do skopiowania wszystkich danych, dodając odporności na uszkodzenia i pomijanie wiersze danych niezgodne. Ponadto możesz rejestrować niezgodnych wierszy w usłudze Azure Blob storage. Następnie można sprawdzić dziennik, aby dowiedzieć się przyczyną błędu, Usuń dane w źródle danych i ponów próbę wykonania działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Działanie kopiowania obsługuje trzy scenariusze wykrywanie, pomijanie i rejestrowanie danych niezgodne:

- **Niezgodność między typem danych źródła i typ natywny ujścia**

    Na przykład: Kopiowanie danych z pliku CSV w usłudze Blob storage do bazy danych SQL z definicją schematu, która zawiera trzy **INT** kolumny o typie. Wierszy pliku CSV, które zawierają dane liczbowe, takie jak `123,456,789` są została pomyślnie skopiowana do magazynu ujścia. Jednak wiersze zawierające wartości nieliczbowe, takie jak `123,456,abc` są wykrywane jako niezgodna i zostaną pominięte.

- **Niezgodność liczby kolumn między źródła i ujścia**

    Na przykład: Kopiowanie danych z pliku CSV w usłudze Blob storage do bazy danych SQL z definicją schematu, która zawiera sześć kolumn. Wierszy pliku CSV, które zawiera sześć kolumn są została pomyślnie skopiowana do magazynu ujścia. Wierszy pliku CSV, które zawierają więcej lub mniej niż sześć kolumn są wykrywane jako niezgodna i są pomijane.

- **Naruszenie klucza podstawowego podczas zapisywania do usługi SQL Server i Azure SQL Database/Azure Cosmos DB**

    Na przykład: Kopiowanie danych z programu SQL server do usługi SQL database. W bazie danych SQL ujścia jest zdefiniowany klucz podstawowy, ale bez klucza podstawowego jest zdefiniowany w programie SQL server źródła. Zduplikowane wiersze, które istnieją w pliku źródłowym, nie można skopiować do ujścia. Działanie kopiowania kopiuje tylko pierwszy wiersz źródła danych do ujścia. Wiersze kolejne źródła, które zawierają zduplikowane wartości klucza podstawowego, są wykrywane jako niezgodne i zostaną pominięte.

>[!NOTE]
>Ta funkcja nie ma zastosowania w przypadku działania kopiowania jest skonfigurowany do wywoływania zewnętrznych z ładowaniem tym mechanizm [usługi Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) lub [zwolnienie usługi Redshift Amazon](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Dotyczące ładowania danych do usługi SQL Data Warehouse przy użyciu technologii PolyBase, korzystać z pomocy technicznej na uszkodzenia natywnych błędów w programie PolyBase, określając "[usługi](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" w działaniu kopiowania.

## <a name="configuration"></a>Konfigurowanie
W poniższym przykładzie przedstawiono definicję formatu JSON, aby skonfigurować pomijania niezgodnych wierszy w działaniu kopiowania:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Włącz pomijanie niezgodnych wierszy podczas kopiowania, czy nie. | True<br/>FALSE (domyślnie) | Nie |
| **redirectIncompatibleRowSettings** | Grupy właściwości, które można określić, kiedy mają być rejestrowane niezgodnych wierszy. | &nbsp; | Nie |
| **linkedServiceName** | Połączona usługa Azure Storage do przechowywania w dzienniku, który zawiera pominiętych wierszy. | Nazwa [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) połączone usługi, która odnosi się do wystąpienia magazynu, którego chcesz użyć do przechowywania plików dziennika. | Nie |
| **path** | Ścieżka pliku dziennika, który zawiera pominiętych wierszy. | Określ ścieżkę magazynu obiektów Blob, którą chcesz używać do logowania się niezgodne dane. Jeśli ścieżka nie zostanie określona, usługa utworzy kontener. | Nie |

## <a name="monitoring"></a>Monitorowanie
Po zakończeniu uruchomienia działania kopiowania, będzie widoczna Liczba pominiętych wierszy w sekcji monitorowania:

![Monitor pominięte niezgodnych wierszy](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Jeśli skonfigurujesz dziennika niezgodnych wierszy, można znaleźć pliku dziennika w tej ścieżce: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` W pliku dziennika widać, które zostały pominięte wiersze, jak i głównej przyczyny niezgodności.

Oryginalne dane i odpowiedniego błędu są rejestrowane w pliku. Przykład zawartości pliku dziennika jest następująca:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat działania kopiowania w fabryce danych Azure, zobacz [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md).
