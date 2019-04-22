---
title: Odporność na uszkodzenia działania kopiowania w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu dodawania odporności na uszkodzenia do działania kopiowania w usłudze Azure Data Factory przez pominięcie niezgodnych wierszy.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: ef0bb3716a32a0f25b90e74bc44d7291c146b431
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267467"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Odporność na uszkodzenia w działaniu kopiowania w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Bieżąca wersja](copy-activity-fault-tolerance.md)

Działanie kopiowania w usłudze Azure Data Factory oferuje obsługę niezgodnych wierszy podczas kopiowania danych między magazynami danych źródła i ujścia na dwa sposoby:

- Można przerwać i zakończyć się niepowodzeniem kopii działanie po niezgodnych danych napotkało (zachowanie domyślne).
- Można kontynuować do skopiowania wszystkich danych, dodając odporności na uszkodzenia i pomijanie wiersze danych niezgodne. Ponadto możesz rejestrować niezgodnych wierszy w usłudze Azure Blob storage lub Azure Data Lake Store. Następnie można sprawdzić dziennik, aby dowiedzieć się przyczyną błędu, Usuń dane w źródle danych i ponów próbę wykonania działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Działanie kopiowania obsługuje trzy scenariusze wykrywanie, pomijanie i rejestrowanie danych niezgodne:

- **Niezgodności między typ danych źródła i ujścia typ macierzysty**. 

    Na przykład: Kopiowanie danych z pliku CSV w usłudze Blob storage do bazy danych SQL z definicją schematu, która zawiera trzy kolumny typu INT. Wierszy pliku CSV, które zawierają dane liczbowe, takie jak 123,456,789 są została pomyślnie skopiowana do magazynu ujścia. Jednak wiersze, które zawierają wartości nieliczbowe, takich jak 123,456, abc, są wykrywane jako niezgodne i zostaną pominięte.

- **Niezgodność liczby kolumn między źródła i ujścia**.

    Na przykład: Kopiowanie danych z pliku CSV w usłudze Blob storage do bazy danych SQL z definicją schematu, która zawiera sześć kolumn. Wierszy pliku CSV, które zawiera sześć kolumn są została pomyślnie skopiowana do magazynu ujścia. Wierszy pliku CSV, które zawierają więcej lub mniej niż sześć kolumn są wykrywane jako niezgodna i są pomijane.

- **Naruszenie klucza podstawowego podczas zapisywania do usługi SQL Server i Azure SQL Database/Azure Cosmos DB**.

    Na przykład: Kopiowanie danych z programu SQL server do usługi SQL database. W bazie danych SQL ujścia jest zdefiniowany klucz podstawowy, ale bez klucza podstawowego jest zdefiniowany w programie SQL server źródła. Zduplikowane wiersze, które istnieją w pliku źródłowym, nie można skopiować do ujścia. Działanie kopiowania kopiuje tylko pierwszy wiersz źródła danych do ujścia. Wiersze kolejne źródła, które zawierają zduplikowane wartości klucza podstawowego, są wykrywane jako niezgodne i zostaną pominięte.

>[!NOTE]
>- Ładowanie danych do usługi SQL Data Warehouse przy użyciu technologii PolyBase, skonfiguruj ustawienia tolerancji błędów natywnego programu PolyBase w, określając Odrzuć zasad za pomocą "[usługi](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" w działaniu kopiowania. Można włączyć przekierowującego niezgodnych wierszy programu PolyBase do obiektu Blob lub Azure Data Lake Store w zwykły sposób, jak pokazano poniżej.
>- Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowany do wywołania [zwolnienie usługi Redshift Amazon](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).


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
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Określa, czy pominąć niezgodnych wierszy podczas kopiowania, czy nie. | True<br/>FALSE (domyślnie) | Nie
redirectIncompatibleRowSettings | Grupy właściwości, które można określić, kiedy mają być rejestrowane niezgodnych wierszy. | &nbsp; | Nie
linkedServiceName | Połączona usługa [usługi Azure Storage](connector-azure-blob-storage.md#linked-service-properties) lub [usługi Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) do przechowywania w dzienniku, który zawiera pominiętych wierszy. | Nazwa `AzureStorage` lub `AzureDataLakeStore` typu połączonej usługi, która odwołuje się do wystąpienia, które chcesz użyć do przechowywania plików dziennika. | Nie
ścieżka | Ścieżka pliku dziennika, który zawiera pominiętych wierszy. | Wpisz ścieżkę, którą chcesz używać do logowania się niezgodne dane. Jeśli ścieżka nie zostanie określona, usługa utworzy kontener. | Nie

## <a name="monitor-skipped-rows"></a>Monitorowanie pominiętych wierszy
Po zakończeniu uruchomienia działania kopiowania, będzie widoczna Liczba pominiętych wierszy w danych wyjściowych działania kopiowania:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Jeśli skonfigurujesz dziennika niezgodnych wierszy, można znaleźć pliku dziennika w tej ścieżce: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Pliki dziennika można tylko pliki csv. Oryginalne dane pomijany będą rejestrowane z przecinkami jako ogranicznik kolumny w razie potrzeby. Możemy dodać dwie kolumny "ErrorCode" i "Komunikat o błędzie" się z dodatkowymi oryginalnych danych źródłowych w pliku dziennika, tam, gdzie zobaczysz głównego Przyczyna niezgodności. Kod błędu oraz komunikat o błędzie zostaną podane w cudzysłów. 

Przykład zawartości pliku dziennika jest następująca:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne artykuły dotyczące działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Wydajności działania kopiowania](copy-activity-performance.md)


