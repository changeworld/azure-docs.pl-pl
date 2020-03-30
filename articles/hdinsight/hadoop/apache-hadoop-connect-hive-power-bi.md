---
title: Wizualizuj dane gałęzi Apache za pomocą usługi Power BI — Usługa Azure HDInsight
description: Dowiedz się, jak wizualizować dane hive przetwarzane przez usługę Azure HDInsight za pomocą usługi Microsoft Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/02/2020
ms.openlocfilehash: d9b64785dbd82842479eb3f313b8394f9f25b40b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370002"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Wizualizowanie danych programu Apache Hive przy użyciu usługi Microsoft Power BI za pomocą interfejsu ODBC w usłudze Azure HDInsight

Dowiedz się, jak połączyć program Microsoft Power BI Desktop z usługą Azure HDInsight przy użyciu funkcji ODBC i wizualizować dane gałęzi Apache.

> [!IMPORTANT]
> Sterownik Hive ODBC można wykorzystać do importowania za pośrednictwem ogólnego złącza ODBC w programie Power BI Desktop. Jednak nie jest zalecane dla obciążeń bi, biorąc pod uwagę nieinterakcyjny charakter aparatu zapytań hive. [Złącze interaktywne zapytanie HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) i [złącze HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) są lepszym wyborem dla ich wydajności.

W tym artykule należy załadować dane z tabeli `hivesampletable` gałęzi do usługi Power BI. Tabela Hive zawiera niektóre dane użycia telefonu komórkowego. Następnie wykreślasz dane użycia na mapie świata:

![HdInsight Power BI raport mapy](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Informacje dotyczą również nowego typu klastra [zapytań interaktywnych.](../interactive-query/apache-interactive-query-get-started.md) Aby dowiedzieć się, jak połączyć się z interwymiajna kwerendą HDInsight przy użyciu kwerendy bezpośredniej, zobacz [Wizualizacja danych gałęzi zapytań interaktywnych za pomocą usługi Microsoft Power BI przy użyciu zapytania bezpośredniego w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przejściem przez ten artykuł, musisz mieć następujące elementy:

* Klaster usługi HDInsight. Klaster może być klastrem HDInsight z gałęzią lub nowo wydanym klastrem zapytań interaktywnych. Aby utworzyć klastry, zobacz [Tworzenie klastra](apache-hadoop-linux-tutorial-get-started.md).

* [Program Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). Kopię można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Tworzenie źródła danych ODBC usługi Hive

Zobacz [Tworzenie źródła danych ODBC hive](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z usługi HDInsight

Tabela **hive pouszczalne** pochodzi ze wszystkich klastrów HDInsight.

1. Uruchom program Power BI Desktop.

1. W górnym menu przejdź do **pozycji Strona główna** > **Pobierz dane** > **więcej...**.

    ![HdInsight Excel Power BI otwarte dane](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. W oknie dialogowym **Pobierz dane** wybierz **pozycję Inne** od lewej strony, wybierz pozycję **ODBC** od prawej strony, a następnie wybierz pozycję **Połącz** u dołu.

1. Z okna dialogowego **OdBC** wybierz nazwę źródła danych utworzoną w ostatniej sekcji z listy rozwijanej. Następnie wybierz przycisk **OK**.

1. Przy pierwszym użyciu zostanie otwarte okno dialogowe **sterownika ODBC.** Z lewego menu wybierz **polecenie Domyślne lub Niestandardowe.** Następnie wybierz pozycję **Połącz,** aby otworzyć **nawigator**.

1. W oknie dialogowym **Nawigator** rozwiń rozwiń węzeł **ODBC > > hive domyślnie**, wybierz **pozycję rozmaitość ,** a następnie wybierz pozycję **Załaduj**.

## <a name="visualize-data"></a>Wizualizowanie danych

Kontynuuj od ostatniej procedury.

1. W okienku Wizualizacje wybierz pozycję **Mapa**— ikona kuli ziemskiej.

    ![Usługa HDInsight Power BI dostosowuje raport](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. W okienku **Pola** wybierz **kraj** i **urządzeniezmieje**. Możesz zobaczyć dane wykreślone na mapie.

1. Rozwiń mapę.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak wizualizować dane z usługi HDInsight za pomocą usługi Power BI.  Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Użyj Apache Zeppelin, aby uruchamiać zapytania apache hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Podłącz program Excel do usługi HDInsight za pomocą sterownika ODBC firmy Microsoft Hive](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z apache Hadoop przy użyciu dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Połącz się z usługą Azure HDInsight i uruchom kwerendy hive Apache przy użyciu narzędzi usługi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Użyj narzędzia Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Prześlij dane do usługi HDInsight](./../hdinsight-upload-data.md).
