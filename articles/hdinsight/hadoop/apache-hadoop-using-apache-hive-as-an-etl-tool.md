---
title: Za pomocą programu Apache Hive jako narzędzia ETL — usługa Azure HDInsight
description: Używanie programu Apache Hive do wyodrębniania, przekształcania i ładowania (ETL) danych w usłudze Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.openlocfilehash: f8fb036eaca35e41d89b0a9610ebcd68e65f40f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343384"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Użyj Apache Hive jako narzędzia do wyodrębniania, przekształcania i ładowania (ETL)

Zazwyczaj należy czyszczenie i transformowanie przychodzące dane przed załadowaniem ich do miejsca docelowego odpowiednie do analizy. Operacje wyodrębniania, przekształcania i ładowania (ETL) są używane do przygotowania danych i załadowania ich do miejsca docelowego danych.  Apache Hive on HDInsight można odczytać danych bez struktury, przetwarzania danych, zgodnie z potrzebami i następnie załadować dane do magazynu danych relacyjnych, dla systemy podejmowania decyzji. W przypadku tej metody dane są wyodrębnione ze źródła i przechowywane w magazynie skalowalne, takie jak obiekty BLOB usługi Azure Storage lub usługi Azure Data Lake Storage. Dane są następnie przekształcane przy użyciu sekwencji zapytań programu Hive i na koniec przygotowane w gałęzi w ramach przygotowania do zbiorczego ładowania do docelowego magazynu danych.

## <a name="use-case-and-model-overview"></a>Użyj przypadków i modelu — omówienie

Na poniższej ilustracji przedstawiono omówienie przypadków użycia i modelu automatyzacji ETL. Dane wejściowe jest przekształcana Generowanie odpowiednich danych wyjściowych.  Podczas tej transformacji danych można zmienić kształt, typ danych i nawet języku.  Procesów ETL można przekonwertować angielski system miar metryki, zmiana stref czasowych i poprawy dokładności, aby poprawnie wyrównać z istniejącymi danymi w miejscu docelowym.  Procesów ETL można także połączyć nowe dane z istniejącymi danymi zachować raportowania aktualne lub dodatkowo zapewniają wgląd w dane.  Aplikacje, takie jak raportowanie, narzędzia i usługi mogły te dane w wybranym formacie.

![Apache Hive jako ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Usługi Hadoop jest zwykle używana w procesy ETL, które ogromną liczbę plików tekstowych (np. csv) lub mniejszy, ale często Zmienianie liczby plików tekstowych i / lub zaimportować.  Gałąź jest doskonałym narzędziem do użycia, aby przygotować dane przed załadowaniem ich do miejsca docelowego danych.  Gałąź pozwala na tworzenie schematu w woluminie CSV i użyj języka przypominającego SQL, aby wygenerować programów MapReduce, które współdziałają z danymi. 

Typowe kroki, aby przeprowadzić ETL za pomocą programu Hive są następujące:

1. Załaduj dane do usługi Azure Data Lake Storage lub Azure Blob Storage.
2. Tworzenie bazy danych metadanych Store (przy użyciu usługi Azure SQL Database) do użytku przez program Hive swoje Schematy przechowywania.
3. Tworzenie klastra usługi HDInsight i łączenie z magazynem danych.
4. Zdefiniuj schematu do zastosowania podczas odczytu danych w magazynie danych:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs
    
    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT, 
        system BIGINT, 
        systemage BIGINT, 
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    STORED AS TEXTFILE LOCATION 'wasb://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Przekształć dane i ładuje je do miejsca docelowego.  Istnieje kilka sposobów na korzystanie z programu Hive podczas przekształcania i ładowania:

    * Zapytania i przygotowania danych przy użyciu technologii Hive i zapisz go jako elementu CSV w usługi Azure Data Lake Storage lub Azure blob storage.  Następnie należy użyć narzędzia takie jak SQL Server Integration Services (SSIS) do uzyskania tych woluminów CSV i załadować dane do docelowego relacyjnej bazy danych takich jak SQL Server.
    * Wykonywanie zapytań o dane bezpośrednio z programu Excel lub C# za pomocą sterownika ODBC programu Hive.
    * Użyj [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) odczytywanie przygotowane pliki CSV proste i załadować je do docelowej bazy danych relacyjnych.

## <a name="data-sources"></a>Źródła danych

Źródła danych są zwykle zewnętrznych danych, który można dopasować do istniejących danych w magazynie danych, na przykład:

* Mediów społecznościowych, danych, pliki dziennika, czujniki i aplikacje, które generują pliki danych.
* Zestawy danych uzyskany z dostawcy danych, na przykład pogoda statystyk lub dostawcy danych liczbowych sprzedaży.
* Dane przesyłane strumieniowo przechwycone filtrowane i przetwarzane przez odpowiednie narzędzie lub strukturę.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Obiekty docelowe danych wyjściowych

Można użyć programu Hive, dane wyjściowe do różnych celów, w tym:

* Relacyjna baza danych, takich jak SQL Server lub usługi Azure SQL Database.
* Magazyn danych, takich jak Azure SQL Data Warehouse.
* Excel.
* Usługa Azure table i blob storage.
* Aplikacje lub usługi, który wymaga przetworzenia na określone formaty danych lub jako pliki zawierają określonych rodzajów informacji struktury.
* Store dokumentów JSON, takich jak <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Zagadnienia do rozważenia

ETL model zwykle jest używany, gdy chcesz:

* Załaduj przesyłanie strumieniowe danych lub dużych ilości danych z częściową strukturą lub bez struktury z zewnętrznych źródeł do istniejącej bazy danych lub systemu informacji.
* Czyszczenie, przekształcanie i sprawdzania poprawności danych przed załadowaniem, prawdopodobnie za pomocą więcej niż jeden przekształcania przekazać za pośrednictwem klastra.
* Generuj raporty i wizualizacje, które są regularnie aktualizowane.  Na przykład raportu trwa zbyt długo, można wygenerować w ciągu dnia, można zaplanować raport, aby uruchomić w nocy.  Usługa Azure Scheduler, jak i programu PowerShell służy do automatycznego uruchamiania zapytań programu Hive.

Jeśli element docelowy dla danych nie jest bazą danych, można wygenerować plik w odpowiednim formacie zapytania, na przykład woluminu CSV. Ten plik można następnie zaimportować do programu Excel lub Power BI.

Jeśli musisz wykonać kilka operacji na danych w ramach procesu ETL, należy wziąć pod uwagę sposób zarządzania nimi. Jeśli operacje są kontrolowane przez zewnętrzny program, a nie jako przepływ pracy w ramach rozwiązania, należy zdecydować, czy niektóre operacje mogą być wykonywane równolegle, a także do wykrywania po zakończeniu każdego zadania. Przy użyciu mechanizmu przepływu pracy, takich jak Oozie w ramach platformy Hadoop, może być łatwiejsze niż podjęcie próby do organizowania sekwencja operacji przy użyciu skryptów zewnętrznych lub niestandardowych programów. Aby uzyskać więcej informacji na temat programu Oozie, zobacz [przepływu pracy i zadania aranżacji](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Kolejne kroki

* [ETL na dużą skalę](apache-hadoop-etl-at-scale.md)
* [Operacjonalizacja potoku danych](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
