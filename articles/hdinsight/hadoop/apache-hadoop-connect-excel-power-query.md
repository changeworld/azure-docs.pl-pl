---
title: Łączenie programu Excel do platformy Hadoop za pomocą dodatku Power Query - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skorzystać z składników analizy biznesowej i Użyj dodatku Power Query dla programu Excel w celu dostępu do danych przechowywanych na platformie Hadoop w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: be2b6e27e097ea5206e1faceed75ec212406d330
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>Łączenie programu Excel do platformy Hadoop za pomocą dodatku Power Query
Jedną z kluczowych funkcji rozwiązania danych big data firmy Microsoft jest integracja składniki programu Microsoft business intelligence (BI) z klastrami platformy Hadoop w usłudze Azure HDInsight. Podstawowy przykład jest możliwość łączenia programu Excel z kontem magazynu Azure, które zawiera dane skojarzone z klastrem usługi Hadoop przy użyciu programu Microsoft Power Query dla dodatek programu Excel. W tym artykule przedstawiono sposób konfigurowania i używania dodatku Power Query wykonać zapytania o dane skojarzone z klastrem usługi Hadoop zarządzanych za pomocą usługi HDInsight.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego artykułu, musi mieć następujące elementy:

* **Klaster usługi HDInsight**. Aby skonfigurować jedną, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight] [hdinsight-get-started].
* **Stacja robocza** działa system Windows 7, Windows Server 2008 R2 lub nowszym systemem operacyjnym.
* **Pakiet Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, programu Excel 2013 autonomicznych lub Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Instalowanie dodatku Power Query
Dodatek Power Query można zaimportować danych, który ma zostać produkt wyjściowy lub który został wygenerowany przez zadanie Hadoop uruchomiona w klastrze usługi HDInsight.

W programie Excel 2016 dodatku Power Query zostało zintegrowane na Wstążce danych znajdujących się w sekcji Pobierz & transformacji. Dla starszych wersji programu Excel, Pobierz program Microsoft Power Query dla programu Excel z [Microsoft Download Center] [ powerquery-download] i zainstaluj go.

## <a name="import-hdinsight-data-into-excel"></a>Importowanie danych HDInsight do programu Excel
Dodatek Power Query dla programu Excel można łatwo importować dane z klastrem usługi HDInsight do programu Excel, których narzędzi do analizy Biznesowej, takie jak PowerPivot, a następnie mapować zasilania może służyć do inspekcji, analizowanie i przedstawiają dane.

**Aby zaimportować dane z klastra usługi HDInsight**

1. Otwórz program Excel.
2. Utwórz nowy, pusty skoroszyt.
3. Wykonaj następujące kroki, zależnie od używanej wersji programu Excel:

    - Excel 2016

        - Kliknij przycisk **danych** menu, kliknij przycisk **Pobierz dane** z **Get & Przekształć dane** wstążki, kliknij przycisk **Azure z**, a następnie kliknij przycisk **z HDInsight(HDFS) Azure**.

        ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

    - Excel 2013/2010

        - Kliknij przycisk **dodatku Power Query** menu, kliknij przycisk **Azure z**, a następnie kliknij przycisk **z usługi Microsoft Azure HDInsight**.
   
        ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
        **Uwaga:** Jeśli nie widzisz **dodatku Power Query** menu, przejdź do **pliku** > **opcje** > **Add-Ins**i wybierz **dodatki COM** z listy rozwijanej **Zarządzaj** u dołu strony. Wybierz **Przejdź...**  przycisk i sprawdź, czy zostało zaznaczone pole dodatku Power Query dla dodatek programu Excel.
       
        **Uwaga:** dodatku Power Query umożliwia importowanie danych z systemu plików HDFS, klikając **z innych źródeł**.
4. Aby uzyskać **nazwa konta**, wprowadź nazwę konta magazynu obiektów Blob Azure skojarzonego z klastrem, a następnie kliknij przycisk **OK**. To konto może być [domyślne konto magazynu](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account) lub połączonym koncie magazynu.  Format jest *https://&lt;StorageAccountName >.blob.core.windows.net/*.
5. Dla **klucz konta**wprowadź klucz dla konta magazynu obiektów Blob, a następnie kliknij przycisk **zapisać**. (Należy wprowadzić czas informacje tylko następująca liczba pierwszych konta, możesz uzyskać dostępu do tego magazynu).
6. W **Nawigator** w okienku po lewej stronie edytora zapytań, kliknij dwukrotnie nazwę kontenera magazynu obiektów Blob. Domyślnie nazwa kontenera jest taką samą nazwę jak nazwa klastra.
7. Zlokalizuj **HiveSampleData.txt** w **nazwa** kolumny (ścieżka folderu jest **... / hive/magazynu/hivesampletable/**), a następnie kliknij przycisk **binarne** po lewej stronie HiveSampleData.txt. HiveSampleData.txt zawiera wszystkie klastra. Opcjonalnie można użyć własnego pliku.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Jeśli chcesz, można zmienić nazwy kolumny. Gdy wszystko będzie gotowe, kliknij przycisk **Zamknij & załadować**.  Dane zostały załadowane do skoroszytu:
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób Użyj dodatku Power Query można pobrać danych z usługi HDInsight do programu Excel. Podobnie można pobierać dane z usługi HDInsight do bazy danych SQL Azure. Istnieje również możliwość przekazywania danych do usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wizualizacja gałęzi przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane interakcyjne zapytań Hive z usługą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Umożliwia uruchamianie zapytań Hive w usłudze Azure HDInsight Zeppelin ](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel do usługi HDInsight za pomocą sterownika ODBC firmy Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie do usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
