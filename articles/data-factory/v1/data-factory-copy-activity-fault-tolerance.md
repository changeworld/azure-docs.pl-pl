---
title: Dodawanie odporności na uszkodzenia w usłudze Azure Data Factory Copy Activity przez pomijanie niezgodnych wierszy
description: Dowiedz się, jak dodać odporność na uszkodzenia w usłudze Azure Data Factory Copy Activity, pomijając niezgodne wiersze podczas kopiowania
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8ffaee75154fd5fe025bdb683c89f16799d6e86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926149"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Dodawanie odporności na uszkodzenia w działaniu kopiowania przez pomijanie niezgodnych wierszy

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-copy-activity-fault-tolerance.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [odporność na uszkodzenia w działaniu kopiowania usługi Data Factory](../copy-activity-fault-tolerance.md).

[Działanie kopiowania](data-factory-data-movement-activities.md) usługi Azure Data Factory oferuje dwa sposoby obsługi niezgodnych wierszy podczas kopiowania danych między magazynami danych źródłowych i magazynów danych ujścia:

- Działanie kopiowania można przerwać i zakończyć niepowodzeniem w przypadku wystąpienia niezgodnych danych (zachowanie domyślne).
- Można kontynuować kopiowanie wszystkich danych, dodając odporność na uszkodzenia i pomijając niezgodne wiersze danych. Ponadto można rejestrować niezgodne wiersze w magazynie obiektów Blob platformy Azure. Następnie można sprawdzić dziennik, aby dowiedzieć się przyczyny błędu, naprawić dane w źródle danych i ponów próbę wykonania działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Działanie kopiowania obsługuje trzy scenariusze wykrywania, pomijania i rejestrowania niezgodnych danych:

- **Niezgodność typu danych źródłowych z natywnym typem ujścia**

    Na przykład: Skopiuj dane z pliku CSV w magazynie obiektów Blob do bazy danych SQL z definicją schematu zawierającą trzy kolumny typu **INT.** Wiersze plików CSV, które zawierają dane `123,456,789` liczbowe, takie jak są kopiowane pomyślnie do magazynu ujścia. Jednak wiersze, które zawierają wartości nienumeryczne, takie jak `123,456,abc` są wykrywane jako niezgodne i są pomijane.

- **Niezgodność liczby kolumn między źródłem i ujściem**

    Na przykład: Skopiuj dane z pliku CSV w magazynie obiektów Blob do bazy danych SQL z definicją schematu zawierającą sześć kolumn. Wiersze plików CSV, które zawierają sześć kolumn są kopiowane pomyślnie do magazynu ujścia. Wiersze pliku CSV, które zawierają więcej lub mniej niż sześć kolumn są wykrywane jako niezgodne i są pomijane.

- **Naruszenie klucza podstawowego podczas zapisywania w usłudze SQL Server/Azure SQL Database/Azure Cosmos DB**

    Na przykład: Kopiowanie danych z serwera SQL do bazy danych SQL. Klucz podstawowy jest zdefiniowany w bazie danych lokacji SQL, ale nie taki klucz podstawowy nie jest zdefiniowany w źródłowym serwerze SQL. Zduplikowanych wierszy, które istnieją w źródle nie można skopiować do ujścia. Kopiuj działanie kopiuje tylko pierwszy wiersz danych źródłowych do ujścia. Kolejne wiersze źródłowe zawierające zduplikowaną wartość klucza podstawowego są wykrywane jako niezgodne i pomijane.

>[!NOTE]
>Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowane do wywoływania zewnętrznego mechanizmu ładowania danych, w tym [usługi Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) lub Amazon [Redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Aby załadować dane do magazynu danych SQL przy użyciu PolyBase, użyj obsługi natywnej tolerancji błędów PolyBase, określając "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" w działaniu kopiowania.

## <a name="configuration"></a>Konfigurowanie
Poniższy przykład zawiera definicję JSON, aby skonfigurować pomijanie niezgodnych wierszy w copy activity:

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

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Włącz pomijanie niezgodnych wierszy podczas kopiowania lub nie. | True<br/>False (domyślnie) | Nie |
| **redirectIncompatibleRowSettings** | Grupa właściwości, które można określić, gdy chcesz zarejestrować niezgodne wiersze. | &nbsp; | Nie |
| **linkedServiceName** | Połączona usługa usługi Azure Storage do przechowywania dziennika, który zawiera pominięte wiersze. | Nazwa usługi [linked AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [AzureStorageSas,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) która odwołuje się do wystąpienia magazynu, którego chcesz użyć do przechowywania pliku dziennika. | Nie |
| **Ścieżka** | Ścieżka pliku dziennika zawierającego pominięte wiersze. | Określ ścieżkę magazynu obiektów Blob, której chcesz użyć do rejestrowania niezgodnych danych. Jeśli nie podasz ścieżkę, usługa tworzy kontener dla Ciebie. | Nie |

## <a name="monitoring"></a>Monitorowanie
Po zakończeniu wykonywania działania kopiowania można zobaczyć liczbę pominiętych wierszy w sekcji monitorowania:

![Monitor pominięty niezgodne wiersze](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Jeśli skonfigurujesz do rejestrowania niezgodnych wierszy, można znaleźć `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` plik dziennika w tej ścieżce: W pliku dziennika, można zobaczyć wiersze, które zostały pominięte i główną przyczyną niezgodności.

Zarówno oryginalne dane, jak i odpowiedni błąd są rejestrowane w pliku. Przykład zawartości pliku dziennika jest następujący:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o aktywności kopiowania w fabryce danych platformy Azure, zobacz [Przenoszenie danych przy użyciu funkcji Kopiowanie](data-factory-data-movement-activities.md).
