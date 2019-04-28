---
title: Łączenie programu Excel do platformy Apache Hadoop za pomocą dodatku Power Query — usługa Azure HDInsight
description: Dowiedz się, jak korzystać z zalet składników analizy biznesowej i Użyj dodatku Power Query dla programu Excel w celu dostępu do danych przechowywanych na platformie Hadoop w HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: df7bb39120dfe4c45a4749065f77649bc51d0356
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122151"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Łączenie programu Excel do platformy Apache Hadoop za pomocą dodatku Power Query
Kluczowa funkcja klasy rozwiązania danych big data firmy Microsoft jest integracja składniki programu Microsoft business intelligence (BI) z klastrami Apache Hadoop w usłudze Azure HDInsight. Podstawowy przykład jest możliwość łączenia programu Excel z kontem usługi Azure Storage, która zawiera dane skojarzone z klastrem usługi Hadoop przy użyciu programu Microsoft Power Query dla dodatku programu Excel. W tym artykule przedstawiono sposób konfigurowania i używania dodatku Power Query przesyłać zapytania dotyczące danych skojarzonych z klastrem usługi Hadoop, zarządzane przy użyciu HDInsight.

### <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania w tym artykule, musi mieć następujące elementy:

* **Klaster usługi HDInsight**. Aby skonfigurować jeden, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight] [hdinsight-get-started].
* **Stacja robocza** uruchomionym, Windows 7, Windows Server 2008 R2 lub nowszej wersji systemu operacyjnego.
* **Pakiety Office 2016, Office 2013 Professional Plus, usługi Office 365 ProPlus, programu Excel 2013 Standalone lub Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Instalowanie dodatku Power Query
Dodatek Power Query można zaimportować danych, który ma zostać produkt wyjściowy lub który został wygenerowany przez zadania usługi Hadoop, uruchomiony w klastrze usługi HDInsight.

W programie Excel 2016 dodatku Power Query zostało zintegrowane wstążki danych w obszarze Pobierz i Transformuj. W przypadku starszych wersji programu Excel, Pobierz Microsoft Power Query dla programu Excel z [Microsoft Download Center] [ powerquery-download] i zainstaluj go.

## <a name="import-hdinsight-data-into-excel"></a>Importowanie danych HDInsight do programu Excel
Dodatek Power Query dla programu Excel można łatwo importować dane z klastra usługi HDInsight do programu Excel, w których narzędzi do analizy Biznesowej, takie jak PowerPivot i Power Map można sprawdzić, analizowanie i prezentowanie danych.

**Aby zaimportować dane z klastra usługi HDInsight**

1. Otwórz program Excel.
2. Utwórz nowy pusty skoroszyt.
3. Wykonaj następujące czynności, w zależności od używanej wersji programu Excel:

   - Excel 2016

     - Kliknij przycisk **danych** menu, kliknij przycisk **Pobierz dane** z **Pobierz i Transformuj dane** wstążki, kliknij przycisk **z platformy Azure**, a następnie kliknij przycisk **z platformy Azure HDInsight(HDFS)**.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - Kliknij przycisk **dodatku Power Query** menu, kliknij przycisk **z platformy Azure**, a następnie kliknij przycisk **z Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **Uwaga:** Jeśli nie widzisz **dodatku Power Query** przejdź do menu **pliku** > **opcje** > **Add-ins**i wybierz  **Dodatki COM** z listy rozwijanej **Zarządzaj** u dołu strony. Wybierz **Przejdź...**  znajdujący się i sprawdź, czy zostało zaznaczone pole dla dodatku Power Query dla dodatku programu Excel.
       
       **Uwaga:** Dodatku Power Query pozwala również na importowanie danych z systemu plików HDFS, klikając **z innych źródeł**.
4. Aby uzyskać **nazwa konta**, wprowadź nazwę konta magazynu obiektów Blob platformy Azure skojarzony z klastrem, a następnie kliknij przycisk **OK**. To konto może być domyślne konto magazynu lub połączone konto magazynu.  Format jest *https://&lt;StorageAccountName >.blob.core.windows.net/*.
5. Aby uzyskać **klucz konta**, wprowadź klucz dla konta usługi Blob storage, a następnie kliknij przycisk **Zapisz**. (Należy wprowadzić czas informacji tylko pierwszy konta dostępu tego magazynu.)
6. W **Nawigator** w okienku po lewej stronie edytora zapytań, kliknij dwukrotnie nazwę kontenera magazynu obiektów Blob. Domyślnie nazwa kontenera jest taką samą nazwę jak nazwa klastra.
7. Znajdź **HiveSampleData.txt** w **nazwa** kolumny (ścieżka folderu jest **... / hive/warehouse/hivesampletable/**), a następnie kliknij przycisk **binarne** w lewej części HiveSampleData.txt. HiveSampleData.txt jest dostarczany z klastrem usługi. Opcjonalnie można użyć własnego pliku.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Jeśli chcesz, można zmienić nazwy kolumny. Gdy wszystko będzie gotowe, kliknij przycisk **Zamknij i załaduj**.  Dane zostały załadowane do skoroszytu:
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób pobierania danych z HDInsight do programu Excel za pomocą dodatku Power Query. Podobnie można pobrać danych z HDInsight do usługi Azure SQL Database. Istnieje również możliwość przekazywania danych do HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wizualizuj dane Apache Hive z usługą Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchomić zapytania usługi Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
