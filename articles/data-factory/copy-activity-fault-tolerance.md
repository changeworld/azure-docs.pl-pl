---
title: Odporność na uszkodzenia działanie kopiowania w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu dodawania odporność na uszkodzenia dla działania kopiowania w fabryce danych Azure przez pominięcie niezgodne wierszy.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jingwang
ms.openlocfilehash: 4fef9a9d30adb48f8f68d34e35a7436c04b63125
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Odporność na uszkodzenia działania kopiowania w fabryce danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Wersja 2 — wersja zapoznawcza](copy-activity-fault-tolerance.md)

Działanie kopiowania w fabryce danych Azure oferuje obsługi niezgodne wierszy, gdy kopiowanie danych między źródłowy i odbiorczy magazynów danych na dwa sposoby:

- Możesz przerwać i niepowodzenie kopiowania działanie w przypadku niezgodnych danych napotkało (domyślnie).
- Możesz skopiować wszystkie dane przez dodanie odporność na uszkodzenia i pomijanie niezgodne dane wierszy. Ponadto możesz zalogować się niezgodne wiersze magazynu obiektów Blob platformy Azure lub usługi Azure Data Lake Store. Następnie można sprawdzić dziennik Dowiedz się przyczynę błędu, Usuń dane w źródle danych, a następnie ponów działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [skopiuj działania odporność na uszkodzenia w wersji 1](v1/data-factory-copy-activity-fault-tolerance.md).


 ## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Działanie kopiowania obsługuje trzy scenariusze wykrywanie, pomijanie i rejestrowanie danych niezgodne:

- **Niezgodność między typem źródła danych i typ macierzysty ujścia**. 

    Na przykład: kopiowanie danych z pliku CSV w magazynie obiektów Blob do bazy danych SQL z definicji schematu, która zawiera trzy kolumny typu INT. Wiersze pliku CSV, które zawierają dane liczbowe, takie jak 123,456,789 są została pomyślnie skopiowana do ujścia magazynu. Jednak wiersze zawierające wartości nieliczbowe, takich jak 123,456, abc Wykryto niezgodne i są pomijane.

- **Niezgodność liczby kolumn między serwerem źródłowym a sink**.

    Na przykład: kopiowanie danych z pliku CSV w magazynie obiektów Blob do bazy danych SQL z definicji schematu, który zawiera sześć kolumn. Wiersze pliku CSV, które zawierają sześć kolumn są została pomyślnie skopiowana do ujścia magazynu. Wiersze pliku CSV, które zawierają więcej lub mniej niż sześć kolumn są wykryte jako niezgodne, są pomijane.

- **Naruszenia dotyczącego klucza podstawowego podczas zapisywania do bazy danych programu SQL Server/usługi Azure SQL bazy danych/Azure rozwiązania Cosmos**.

    Na przykład: kopiowanie danych z programu SQL server z bazą danych SQL. W bazie danych SQL zbiornika jest zdefiniowany klucz podstawowy, ale taki klucz podstawowy jest zdefiniowany w programie SQL server źródła. Zduplikowane wiersze, które istnieją w źródle nie można skopiować do ujścia. Działanie kopiowania kopiuje tylko pierwszy wiersz źródła danych do ujścia. Wiersze kolejnych źródła, które zawierają zduplikowane wartości klucza podstawowego są wykrywane niezgodne i są pomijane.

>[!NOTE]
>Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowany do wywołania danych zewnętrznych ładowania, łącznie z mechanizmu [aparat PolyBase magazynu danych SQL Azure](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) lub [zwolnienie Redshift Amazon](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift). Ładowania danych do usługi SQL Data Warehouse przy użyciu programu PolyBase, korzystać z obsługi tolerancji błędów natywnego programu PolyBase w określając "[usługi](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" w przypadku działania kopiowania.

## <a name="configuration"></a>Konfigurowanie
W poniższym przykładzie przedstawiono definicji JSON, aby skonfigurować pomijanie niezgodne wierszy w przypadku działania kopiowania:

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
enableSkipIncompatibleRow | Określa, czy pominąć niezgodne wierszy podczas kopiowania lub nie. | True<br/>Wartość FAŁSZ (ustawienie domyślne) | Nie
redirectIncompatibleRowSettings | Grupy właściwości, które można określić, kiedy mają być rejestrowane niezgodne wierszy. | &nbsp; | Nie
linkedServiceName | Połączonej usługi [usługi Azure Storage](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) do przechowywania dziennika, który zawiera wiersze zostało pominięte. | Nazwa `AzureStorage` lub `AzureDataLakeStore` typu połączonej usługi, która odwołuje się do wystąpienia, które ma być używany do przechowywania plików dziennika. | Nie
ścieżka | Ścieżka pliku dziennika, który zawiera wiersze zostało pominięte. | Określ ścieżkę, którego chcesz używać do logowania się niezgodne dane. Jeśli ścieżka nie zostanie określona, usługa tworzy kontener. | Nie

## <a name="monitor-skipped-rows"></a>Monitorowanie pominiętych wierszy
Po zakończeniu uruchamiania działania kopiowania, można wyświetlić Liczba pominiętych wierszy w danych wyjściowych działania kopiowania:

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
Jeśli konfigurujesz dziennika niezgodne wiersze, można znaleźć pliku dziennika na tej ścieżce: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Pliki dziennika można tylko pliki csv. Oryginalne dane pomijany będą rejestrowane z przecinkami jako ogranicznik kolumny w razie potrzeby. Dodamy dwie kolumny "ErrorCode" i "ErrorMessage" w dodatkowych do oryginalnego źródła danych w pliku dziennika, którym można zobaczyć głównego Przyczyną niezgodności. Kod błędu i komunikat o błędzie będzie podawany w podwójne cudzysłowy. 

Oto przykład zawartości pliku dziennika jest następujący:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne artykuły działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Wydajności działania kopiowania](copy-activity-performance.md)


