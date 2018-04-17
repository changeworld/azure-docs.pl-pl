---
title: Używanie Apache Hive jako narzędzie ETL — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Używanie Apache Hive do wyodrębniania, transformacji i ładowania (ETL) danych w usłudze Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 06e06d87abd66c80deb2c8731f68bb8171da574b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Użyj Apache Hive jako narzędzie wyodrębniania, przekształcania i ładowania (ETL)

Zazwyczaj należy wyczyścić i przekształcanie przychodzących danych przed załadowaniem go do miejsca docelowego nadające się do analizy. Operacje wyodrębniania, przekształcania i ładowania (ETL) są używane do przygotowania danych i załaduj go do docelowej danych.  Hive w usłudze HDInsight można odczytać danych bez struktury, przetwarzania danych, zgodnie z potrzebami i następnie załadować dane do magazynu danych relacyjnych dla systemy podejmowania decyzji. W tej metody dane są wyodrębnione ze źródła i przechowywane w magazynie skalowalne, takich jak obiekty BLOB magazynu Azure lub usługi Azure Data Lake Store. Dane jest następnie przekształcone przy użyciu sekwencji zapytań programu Hive i na koniec jest umieszczane w gałęzi w ramach przygotowania do zbiorczego ładowania w magazynie danych docelowym.

## <a name="use-case-and-model-overview"></a>Użyj przypadków i modelu — omówienie

Na poniższej ilustracji przedstawiono omówienie przypadek użycia i model dla ETL automatyzacji. Dane wejściowe jest przekształcana do generowania odpowiednich danych wyjściowych.  Podczas tej transformacji danych można zmienić kształt, typ danych i nawet języka.  Procesów ETL można przekonwertować angielski system miar metryki, zmień stref czasowych i poprawy dokładności prawidłowo uzgodnić z istniejącymi danymi w lokalizacji docelowej.  Procesów ETL można także połączyć nowych danych z istniejącymi danymi zachować raportowania aktualne lub dalsze zapewniają wgląd w istniejących danych.  Aplikacje, takie jak raportowanie, narzędzia i usługi będą mogły używać tych danych w wybranym formacie.

![Apache Hive jako ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop jest zwykle używana w bardzo dużej liczby plików tekstowych (na przykład CSV) lub mniejszej, ale często zmiana liczby plików tekstowych i/lub zaimportować procesów ETL.  Gałąź to doskonałe narzędzie do służy do przygotowywania danych przed załadowaniem go do miejsca docelowego danych.  Gałąź umożliwia tworzenie schematu w woluminie CSV i użyć języka przypominającego SQL w celu wygenerowania programy MapReduce, które współdziałają z danymi. 

Typowe kroki z użyciem gałęzi do wykonania ETL są następujące:

1. Ładowanie danych do usługi Azure Data Lake — magazyn lub magazyn obiektów Blob Azure.
2. Utworzyć bazę danych magazynu metadanych (przy użyciu usługi Azure SQL Database) do użycia przez gałęzi do przechowywania Twojego schematów.
3. Tworzenie klastra usługi HDInsight i Połącz z magazynu danych.
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

5. Przekształcanie danych i załaduj go do miejsca docelowego.  Istnieje kilka sposobów na korzystanie z programu Hive podczas transformacji i ładowania:

    * Zapytania i przygotowania danych przy użyciu Hive i zapisz go jako woluminu CSV w usłudze Azure Data Lake Store lub magazynu obiektów blob platformy Azure.  Następnie należy użyć narzędzia takie jak SQL Server Integration Services (SSIS) do uzyskania tych woluminów CSV i załadować dane do docelowego relacyjnej bazy danych takich jak SQL Server.
    * Zapytanie danych bezpośrednio z programu Excel lub C# za pomocą sterownika ODBC programu Hive.
    * Użyj [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) przygotowane płaskiej plików CSV do odczytu i załadować je do docelowego relacyjnej bazy danych.

## <a name="data-sources"></a>Źródła danych

Źródła danych są zazwyczaj zewnętrznych danych, który można dopasować do istniejących danych w magazynie danych, na przykład:

* Dane społecznościowe, plików dziennika, czujników i aplikacje, które generuje pliki danych.
* Zestawy danych uzyskane z dostawcy danych, takich jak statystyka pogody lub dostawcy sprzedaży.
* Przesyłanie strumieniowe danych przechwycone, filtrowane i przetworzona za pomocą odpowiednich narzędzi lub struktury.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Obiekty docelowe danych wyjściowych

Program Hive dane wyjściowe do różnych celów, w tym:

* Relacyjnej bazy danych, takich jak SQL Server lub baza danych SQL Azure.
* Magazyn danych, takie jak magazyn danych SQL Azure.
* Excel.
* Magazyn Azure tabeli i obiektów blob.
* Aplikacje lub usługi, które wymagają przetwarzanych na określone formaty danych lub jako pliki zawierają określonych rodzajów informacji struktury.
* Magazynu dokumentów JSON, takich jak <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Zagadnienia do rozważenia

ETL model zazwyczaj jest używany, jeśli chcesz:

* Załadować strumienia danych lub dużych ilości danych z częściową strukturą lub bez struktury ze źródeł zewnętrznych do istniejącej bazy danych lub system informacji.
* Czyszczenie, transformacja i sprawdzania poprawności danych przed załadowaniem go, może za pomocą przekształcania więcej niż jeden przechodzą przez klaster.
* Generowanie raportów i wizualizacje, które są regularnie aktualizowane.  Na przykład jeśli raport trwa zbyt długo, aby wygenerować w ciągu dnia, można zaplanować raport ma być uruchamiany w nocy.  Harmonogram systemu Azure i programu PowerShell służy do automatycznego uruchamiania zapytań Hive.

Jeśli element docelowy dla danych nie jest bazą danych, można wygenerować pliku w odpowiednim formacie w ramach zapytania, na przykład woluminów CSV. Następnie można zaimportować ten plik do programu Excel lub Power BI.

Jeśli trzeba wykonać kilka operacji na danych w ramach procesu ETL, należy rozważyć sposób zarządzania nimi. Jeśli działania są kontrolowane przez zewnętrzny program, a nie jako przepływ pracy w ramach rozwiązania, należy zdecydować, czy niektóre operacje mogą być wykonywane równolegle i aby wykryć po zakończeniu każdego zadania. Może być łatwiejsze niż w trakcie organizowania sekwencja operacji przy użyciu zewnętrznych skryptów lub niestandardowe programy przy użyciu mechanizmu przepływu pracy, takich jak Oozie w ramach platformy Hadoop. Aby uzyskać więcej informacji o Oozie, zobacz [przepływu pracy i zadania aranżacji](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Kolejne kroki

* [ETL na dużą skalę](apache-hadoop-etl-at-scale.md)
* [Operacjonalizuj potoku danych](../hdinsight-operationalize-data-pipeline.md)
<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
