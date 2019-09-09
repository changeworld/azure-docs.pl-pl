---
title: Łączenie programu Excel z Apache Hadoop przy użyciu Power Query — usługa Azure HDInsight
description: Dowiedz się, jak korzystać ze składników analizy biznesowej i korzystać z Power Query dla programu Excel, aby uzyskiwać dostęp do danych przechowywanych w usłudze Hadoop w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: c1255296854fd28cca4427b6cac04de7dc7919d8
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810922"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Łączenie programu Excel z Apache Hadoop przy użyciu Power Query
Jedną z kluczowych funkcji rozwiązania Microsoft Big Data jest integracja składników Microsoft Business Intelligence (BI) z klastrami Apache Hadoop w usłudze Azure HDInsight. Podstawowym przykładem jest możliwość łączenia programu Excel z kontem usługi Azure Storage zawierającym dane skojarzone z klastrem Hadoop przy użyciu dodatku Microsoft Power Query dla programu Excel. W tym artykule omówiono sposób konfigurowania i używania Power Query do wykonywania zapytań dotyczących danych skojarzonych z klastrem usługi Hadoop zarządzanym za pomocą usługi HDInsight.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z tym artykułem należy dysponować następującymi elementami:

* **HDInsight an klaster**. Aby je skonfigurować, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight] [HDInsight-Get-Started].
* **Stacja robocza** z systemem operacyjnym Windows 7, windows Server 2008 R2 lub nowszym.
* **Office 2016, office 2013 Professional Plus, office 365 ProPlus, Excel 2013 Standalone lub office 2010 Professional Plus**.

## <a name="install-power-query"></a>Zainstaluj Power Query
Power Query może importować dane wyjściowe lub wygenerowane przez zadanie usługi Hadoop uruchomione w klastrze usługi HDInsight.

W programie Excel 2016 Power Query został zintegrowany z wstążką danych w sekcji Pobieranie & transformacji. W przypadku starszych wersji programu Excel Pobierz Microsoft Power Query dla programu Excel z [Centrum pobierania Microsoft][powerquery-download] i zainstaluj go.

## <a name="import-hdinsight-data-into-excel"></a>Importowanie danych usługi HDInsight do programu Excel
Dodatek Power Query dla programu Excel ułatwia importowanie danych z klastra usługi HDInsight do programu Excel, w którym narzędzia analizy biznesowej, takie jak PowerPivot i Power Map, mogą służyć do sprawdzania, analizowania i prezentowania danych.

**Aby zaimportować dane z klastra usługi HDInsight**

1. Otwórz program Excel.
2. Utwórz nowy pusty skoroszyt.
3. Wykonaj następujące kroki w oparciu o wersję programu Excel:

   - Excel 2016

     - Kliknij menu **dane** , kliknij pozycję **Pobierz dane** na Wstążce **Pobierz & przekształcania danych** , kliknij pozycję **z platformy Azure**, a następnie kliknij pozycję **z usługi Azure HDInsight (HDFS)**.

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - Kliknij menu **Power Query** , kliknij pozycję **z platformy Azure**, a następnie kliknij pozycję **z Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **Uwaga:** Jeśli nie widzisz menu **Power Query** , przejdź do pozycji**Opcje** >  **pliku** > **Dodatki**i wybierz opcję **Dodatki COM** w polu rozwijanym **Zarządzaj** w dolnej części strony. Wybierz przycisk **Przejdź...** i sprawdź, czy pole dla dodatku dla Power Query dla programu Excel zostało zaznaczone.
       
       **Uwaga:** Power Query umożliwia również Importowanie danych z systemu plików HDFS przez kliknięcie **z innych źródeł**.
4. W polu **nazwa konta**wprowadź nazwę konta usługi Azure Blob Storage skojarzonego z klastrem, a następnie kliknij przycisk **OK**. To konto może być domyślnym kontem magazynu lub połączonym kontem magazynu.  Format to *https://&lt;StorageAccountName >. blob. Core. Windows. NET/*.
5. W polu **klucz konta**wprowadź klucz dla konta usługi BLOB Storage, a następnie kliknij przycisk **Zapisz**. (Należy wprowadzić informacje o koncie tylko podczas pierwszego dostępu do tego magazynu).
6. W okienku **Nawigator** po lewej stronie edytora zapytań kliknij dwukrotnie nazwę kontenera magazynu obiektów BLOB. Domyślnie nazwa kontenera jest taka sama jak nazwa klastra.
7. Zlokalizuj **HiveSampleData. txt** w kolumnie **name** (ścieżka folderu to **... /Hive/Warehouse/hivesampletable/**), a następnie kliknij pozycję **Binary** po lewej stronie HiveSampleData. txt. HiveSampleData. txt jest dostarczany ze wszystkimi klastrami. Opcjonalnie możesz użyć własnego pliku.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Jeśli chcesz, możesz zmienić nazwy kolumn. Gdy wszystko będzie gotowe, kliknij przycisk **Zamknij, & obciążenie**.  Dane zostały załadowane do skoroszytu:
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób używania Power Query do pobierania danych z usługi HDInsight do programu Excel. Podobnie można pobrać dane z usługi HDInsight do Azure SQL Database. Możliwe jest również przekazywanie danych do usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wizualizuj Apache Hive dane za pomocą usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizowanie interakcyjnych zapytań danych Hive za pomocą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Użyj programu Apache Zeppelin do uruchamiania zapytań Apache Hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z usługą HDInsight przy użyciu Sterownik Microsoft Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Nawiązywanie połączenia z usługą Azure HDInsight i uruchamianie zapytań Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Użyj narzędzia Azure HDInsight do Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
