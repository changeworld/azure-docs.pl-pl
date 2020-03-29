---
title: Łączenie programu Excel z apache Hadoop za pomocą dodatku Power Query — Usługa Azure HDInsight
description: Dowiedz się, jak korzystać ze składników analizy biznesowej i używać dodatku Power Query dla programu Excel do uzyskiwania dostępu do danych przechowywanych w programie Hadoop w umiaśniarce HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435802"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Łączenie programu Excel z apache hadoop przy użyciu dodatku Power Query

Jedną z kluczowych cech rozwiązania microsoft big-data jest integracja składników analizy biznesowej firmy Microsoft (BI) z klastrami Apache Hadoop w usłudze Azure HDInsight. Podstawowym przykładem jest możliwość połączenia programu Excel z kontem usługi Azure Storage zawierającym dane skojarzone z klastrem Hadoop przy użyciu dodatku Microsoft Power Query dla programu Excel. W tym artykule opisano, jak skonfigurować i używać dodatku Power Query do wykonywania zapytań o dane skojarzone z klastrem Hadoop zarządzanym za pomocą usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w programie HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Stacja robocza z systemem Windows 10, 7, Windows Server 2008 R2 lub nowszym systemem operacyjnym.
* Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone lub Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Instalowanie dodatku Microsoft Power Query

Dodatek Power Query może importować dane, które zostały wygenerowane przez zadanie Hadoop uruchomione w klastrze HDInsight.

W programie Excel 2016 dodatek Power Query został zintegrowany ze wstążką Dane w sekcji Pobierz & przekształcanie. W przypadku starszych wersji programu Excel pobierz program Microsoft Power Query dla programu Excel z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?LinkID=286689) i zainstaluj go.

## <a name="import-hdinsight-data-into-excel"></a>Importowanie danych usługi HDInsight do programu Excel

Dodatek Power Query dla programu Excel ułatwia importowanie danych z klastra HDInsight do programu Excel, gdzie narzędzia analizy biznesowej, takie jak PowerPivot i Power Map, mogą być używane do sprawdzania, analizowania i prezentowania danych.

1. Uruchom program Excel.

1. Utwórz nowy pusty skoroszyt.

1. Wykonaj następujące kroki na podstawie wersji programu Excel:

   * Excel 2016

     * Wybierz > **data** > **Get Data** > From**Azure** > **HDInsight(HDFS)**

       ![Hdi. PowerQuery.SelectHdiSource.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Program Excel 2013/2010

     * Wybierz **dodatek Power Query** > **z platformy Azure** > **z usługi Microsoft Azure HDInsight**.

       ![Hdi. Źródło powerquery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Uwaga:** Jeśli nie widzisz menu **Dodatku Power Query,** przejdź do opcji Dodatki**opcji** > **Add-ins** **plików** > i wybierz pozycję **Dodatki COM** z pola rozwijanego **Zarządzaj** u dołu strony. Wybierz przycisk **Przejdź...** i sprawdź, czy pole dodatku Power Query dla programu Excel zostało zaznaczone.

       **Uwaga:** Dodatek Power Query umożliwia również importowanie danych z usługi HDFS, wybierając opcję **Z innych źródeł**.

1. W oknie dialogowym **Usługi Azure HDInsight(HDFS)** w polu **tekstowym Nazwa konta lub adres URL** wprowadź nazwę konta magazynu obiektów Blob platformy Azure skojarzonego z klastrem. Następnie wybierz przycisk **OK**. To konto może być domyślnym kontem magazynu lub połączonym kontem magazynu.  Format to `https://StorageAccountName.blob.core.windows.net/`.

1. W **obszarze Klucz konta**wprowadź klucz dla konta magazynu obiektów Blob, a następnie wybierz pozycję **Połącz**. (Informacje o koncie należy wprowadzić tylko przy pierwszym wejściu do tego sklepu).

1. W okienku **Nawigator** po lewej stronie Edytora zapytań kliknij dwukrotnie nazwę kontenera magazynu obiektów Blob skojarzonego z klastrem. Domyślnie nazwa kontenera jest taka sama jak nazwa klastra.

1. Znajdź **plik HiveSampleData.txt** w kolumnie **Nazwa** (ścieżka folderu to **.. /hive/warehouse/hivesampletable/**), a następnie wybierz **opcję Binary** po lewej stronie pliku HiveSampleData.txt. HiveSampleData.txt jest dostarczany z całym klastrem. Opcjonalnie można użyć własnego pliku.

    ![Dane importu zapytania zasilania programu HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Jeśli chcesz, możesz zmienić nazwę nazw kolumn. Gdy będziesz gotowy, wybierz **pozycję Zamknij & załaduj**.  Dane zostały załadowane do skoroszytu:

    ![Zaimportowana tabela zapytania zasilania hdi excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak pobierać dane z programu HDInsight do programu Excel za pomocą dodatku Power Query. Podobnie można pobrać dane z usługi HDInsight do usługi Azure SQL Database. Możliwe jest również przesyłanie danych do usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wizualizuj dane gałęzi Apache za pomocą usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane gałęzi zapytań interaktywnych za pomocą usługi Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Użyj Apache Zeppelin, aby uruchamiać zapytania apache hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Podłącz program Excel do usługi HDInsight za pomocą sterownika ODBC firmy Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Połącz się z usługą Azure HDInsight i uruchom kwerendy hive Apache przy użyciu narzędzi usługi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Użyj narzędzia Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Prześlij dane do usługi HDInsight](./../hdinsight-upload-data.md).
