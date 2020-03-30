---
title: Korzystanie z gałęzi Apache jako narzędzia ETL — Azure HDInsight
description: Użyj apache hive do wyodrębniania, przekształcania i ładowania danych (ETL) w usłudze Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be331f36a6305b05ce83a2b2d5fdfb73a154ce3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623117"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Użyj narzędzia Apache Hive jako narzędzia Wyodrębnij, Przekształcania i Ładowania (ETL)

Zazwyczaj należy wyczyścić i przekształcić przychodzące dane przed załadowaniem go do miejsca docelowego odpowiedniego do analizy. Operacje wyodrębniania, przekształcania i ładowania (ETL) są używane do przygotowywania danych i ładowania ich do miejsca docelowego danych.  Apache Hive on HDInsight może odczytywać dane bez struktury, przetwarzać dane w razie potrzeby, a następnie ładować dane do relacyjnego magazynu danych dla systemów obsługi decyzji. W tym podejściu dane są wyodrębniane ze źródła i przechowywane w skalowalnej pamięci masowej, takiej jak obiekty blob usługi Azure Storage lub usługi Azure Data Lake Storage. Dane są następnie przekształcane przy użyciu sekwencji zapytań hive i jest ostatecznie przemieszczane w ramach gałęzi w ramach przygotowań do zbiorczego ładowania do magazynu danych docelowych.

## <a name="use-case-and-model-overview"></a>Omówienie przypadków użycia i modelu

Na poniższej ilustracji przedstawiono przegląd przypadku użycia i modelu automatyzacji ETL. Dane wejściowe są przekształcane w celu wygenerowania odpowiedniego wyjścia.  Podczas tej transformacji dane mogą zmieniać kształt, typ danych, a nawet język.  Procesy ETL mogą konwertować imperialne na metryczne, zmieniać strefy czasowe i poprawiać precyzję, aby prawidłowo wyrównać z istniejącymi danymi w miejscu docelowym.  Procesy ETL mogą również łączyć nowe dane z istniejącymi danymi, aby aktualizować raportowanie lub zapewniać dalszy wgląd w istniejące dane.  Aplikacje, takie jak narzędzia raportowania i usługi mogą następnie korzystać z tych danych w żądanym formacie.

![Apache Hive jako architektura ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop jest zwykle używany w procesach ETL, które importują ogromną liczbę plików tekstowych (takich jak CSV) lub mniejszą, ale często zmieniającą się liczbę plików tekstowych lub oba.  Gałąź jest doskonałym narzędziem do przygotowania danych przed załadowaniem ich do miejsca docelowego danych.  Gałąź umożliwia utworzenie schematu za pośrednictwem pliku CSV i użycie języka podobnego do języka SQL do generowania programów MapReduce, które współdziałają z danymi.

Typowe kroki do korzystania z hive do wykonywania ETL są następujące:

1. Załaduj dane do usługi Azure Data Lake Storage lub usługi Azure Blob Storage.
2. Utwórz bazę danych magazynu metadanych (przy użyciu usługi Azure SQL Database) do użycia przez hive podczas przechowywania schematów.
3. Utwórz klaster HDInsight i połącz magazyn danych.
4. Zdefiniuj schemat do zastosowania w czasie odczytu nad danymi w magazynie danych:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Przekształć dane i załaduj je do miejsca docelowego.  Istnieje kilka sposobów używania gałęzi podczas przekształcania i ładowania:

    * Zapytaj i przygotuj dane przy użyciu gałęzi i zapisz go jako plik CSV w usłudze Azure Data Lake Storage lub magazynie obiektów blob platformy Azure.  Następnie użyj narzędzia, takiego jak SQL Server Integration Services (SSIS), aby uzyskać te csv i załadować dane do docelowej relacyjnej bazy danych, takiej jak SQL Server.
    * Kwerenda danych bezpośrednio z programu Excel lub C# przy użyciu sterownika ODBC hive.
    * Użyj [Apache Sqoop,](apache-hadoop-use-sqoop-mac-linux.md) aby odczytać przygotowane płaskie pliki CSV i załadować je do docelowej relacyjnej bazy danych.

## <a name="data-sources"></a>Źródła danych

Źródła danych są zazwyczaj danymi zewnętrznymi, które można dopasować do istniejących danych w magazynie danych, na przykład:

* Dane z mediów społecznościowych, pliki dziennika, czujniki i aplikacje generujące pliki danych.
* Zestawy danych uzyskane od dostawców danych, takie jak statystyki pogody lub numery sprzedaży dostawców.
* Przesyłanie strumieniowe danych przechwyconych, filtrowanych i przetwarzanych za pomocą odpowiedniego narzędzia lub struktury.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Cele dotyczące produkcji

Za pomocą gałęzi można przesyłać dane do różnych obiektów docelowych, w tym:

* Relacyjnej bazy danych, takich jak SQL Server lub Azure SQL Database.
* Magazyn danych, taki jak usługa Azure SQL Data Warehouse.
* Programu excel.
* Magazyn tabel i obiektów blob platformy Azure.
* Aplikacje lub usługi, które wymagają przetwarzania danych w określonych formatach lub jako pliki zawierające określone typy struktury informacji.
* Magazyn dokumentów JSON, taki jak [usługa Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

## <a name="considerations"></a>Zagadnienia do rozważenia

Model ETL jest zwykle używany, gdy chcesz:

* Załaduj dane strumienia lub duże ilości danych półstrukturalnych lub nieustrukturyzowanych ze źródeł zewnętrznych do istniejącej bazy danych lub systemu informacyjnego.
* Czyść, przekształcanie i sprawdzanie poprawności danych przed załadowaniem, być może przy użyciu więcej niż jednego przejścia transformacji za pośrednictwem klastra.
* Generuj raporty i wizualizacje, które są regularnie aktualizowane. Na przykład jeśli raport trwa zbyt długo, aby wygenerować w ciągu dnia, można zaplanować raport do uruchomienia w nocy. Aby automatycznie uruchomić kwerendę hive, można użyć [usługi Azure Logic Apps](../../logic-apps/logic-apps-overview.md) i programu PowerShell.

Jeśli obiekt docelowy danych nie jest bazą danych, można wygenerować plik w odpowiednim formacie w ramach kwerendy, na przykład CSV. Ten plik można następnie zaimportować do programu Excel lub usługi Power BI.

Jeśli musisz wykonać kilka operacji na danych w ramach procesu ETL, należy wziąć pod uwagę, jak nimi zarządzać. Jeśli operacje są kontrolowane przez program zewnętrzny, a nie jako przepływ pracy w rozwiązaniu, należy zdecydować, czy niektóre operacje mogą być wykonywane równolegle i wykryć po zakończeniu każdego zadania. Korzystanie z mechanizmu przepływu pracy, takiego jak Oozie w hadoop może być łatwiejsze niż próba zorganizowania sekwencji operacji przy użyciu zewnętrznych skryptów lub programów niestandardowych. Aby uzyskać więcej informacji na temat usługi Oozie, zobacz [Przepływ pracy i aranżacja zadań](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Następne kroki

* [ETL na dużą skalę](apache-hadoop-etl-at-scale.md)
* [Operacjonalizacja potoku danych](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
