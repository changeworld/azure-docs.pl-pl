---
title: Program Excel & Apache Hadoop z sterownikiem ODBC — Azure HDInsight
description: Dowiedz się, jak skonfigurować i używać sterownika Microsoft Hive ODBC dla programu Excel do wykonywania zapytań dotyczących danych w klastrach usługi HDInsight z programu Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/02/2020
ms.openlocfilehash: f356009197c0446efa2ea2d7f0e90040229df47b
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251067"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Łączenie programu Excel z Apache Hadoop w usłudze Azure HDInsight za pomocą sterownika Microsoft Hive ODBC

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Rozwiązanie firmy Microsoft do obsługi danych Big Data integruje składniki analizy biznesowej firmy Microsoft z klastrami Apache Hadoop, które zostały wdrożone w usłudze Azure HDInsight. Przykładem tej integracji jest możliwość łączenia programu Excel z magazynem danych Hive klastra Hadoop w usłudze HDInsight przy użyciu sterownika Microsoft Hive Open Database Connectivity (ODBC).

Istnieje również możliwość łączenia danych skojarzonych z klastrem usługi HDInsight i innych źródeł danych, w tym innych klastrów usługi Hadoop (innych niż HDInsight), z programu Excel przy użyciu dodatku Microsoft Power Query dla programu Excel. Aby uzyskać informacje na temat instalowania i używania Power Query, zobacz [łączenie programu Excel z usługą HDInsight z Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym artykułem należy dysponować następującymi elementami:

* HDInsight An klastra Hadoop. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Stacja robocza z pakietem Office 2010 Professional Plus lub nowszym albo programem Excel 2010 lub nowszym.

## <a name="install-microsoft-hive-odbc-driver"></a>Zainstaluj sterownik Microsoft Hive ODBC

Pobierz i Zainstaluj wersję [Sterownik Microsoft Hive ODBC](https://www.microsoft.com/download/details.aspx?id=40886) zgodną z wersją aplikacji, w której będziesz używać sterownika ODBC.  W tym artykule sterownik jest używany dla programu Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Tworzenie źródła danych Apache Hive ODBC

Poniższe kroki pokazują, jak utworzyć źródło danych ODBC Hive.

1. W systemie Windows przejdź do menu Start, aby rozpocząć > Narzędzia administracyjne systemu Windows > źródła danych ODBC (32-bitowe)/(64-bitowe).  Spowoduje to otwarcie okna **administratora źródła danych ODBC** .

    ![Administrator źródła danych OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Konfigurowanie DSN przy użyciu administratora źródła danych ODBC")

1. Na karcie **DSN użytkownika** wybierz pozycję **Dodaj** , aby otworzyć okno **Utwórz nowe źródło danych** .

1. Wybierz pozycję **Sterownik Microsoft Hive ODBC**, a następnie wybierz pozycję **Zakończ** , aby otworzyć okno **instalacji sterownik Microsoft Hive ODBC DSN** .

1. Wpisz lub wybierz poniższe wartości:

   | Właściwość | Opis |
   | --- | --- |
   |  Data Source Name (Nazwa źródła danych) |Nadaj nazwę źródła danych. |
   |  Hosty |Wprowadź polecenie `HDInsightClusterName.azurehdinsight.net`. Na przykład `myHDICluster.azurehdinsight.net`. Uwaga: `HDInsightClusterName-int.azurehdinsight.net` jest obsługiwana, dopóki maszyna wirtualna klienta jest połączona z tą samą siecią wirtualną. |
   |  Port |Użyj portu **443**. (Ten port został zmieniony z 563 na 443). |
   |  Baza danych |Użyj **domyślnego**. |
   |  Mechanism (Mechanizm) |Wybieranie **usługi HDInsight systemu Windows Azure** |
   |  User Name (Nazwa użytkownika) |Wprowadź nazwę użytkownika HTTP klastra usługi HDInsight. Domyślna nazwa użytkownika to **admin**. |
   |  Hasło |Wprowadź hasło użytkownika klastra usługi HDInsight. Zaznacz pole wyboru **Zapisz hasło (zaszyfrowane)** .|

1. Opcjonalne: Wybierz **Opcje zaawansowane...**  

   | Parametr | Opis |
   | --- | --- |
   |  Użyj zapytania natywnego |Po wybraniu tego sterownika ODBC nie próbuje skonwertować TSQL na HiveQL. Jest ona używana tylko wtedy, gdy masz 100% pewności, że przesyłasz czyste instrukcje HiveQL. W przypadku nawiązywania połączenia z usługą SQL Server lub Azure SQL Database należy pozostawić zaznaczenie pola wyboru. |
   |  Liczba pobranych wierszy na blok |Podczas pobierania dużej liczby rekordów dostrajanie tego parametru może być wymagane w celu zapewnienia optymalnej wydajności. |
   |  Domyślna długość kolumny ciągu, długość kolumny binarnej, skala kolumn dziesiętnych |Długości i precyzji typu danych mogą mieć wpływ na sposób zwracania danych. Powodują one zwrócenie nieprawidłowych informacji z powodu utraty dokładności i/lub obcięcia. |

    ![Zaawansowane opcje konfiguracji DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Zaawansowane opcje konfiguracji DSN")

1. Wybierz **test** , aby przetestować źródło danych. Gdy źródło danych jest prawidłowo skonfigurowane, wynik testu pokazuje **sukces!** .  

1. Wybierz **przycisk OK** , aby zamknąć okno testowe.  

1. Wybierz **przycisk OK** , aby zamknąć okno **instalacji sterownik Microsoft Hive ODBC DSN** .  

1. Wybierz **przycisk OK** , aby zamknąć okno **Administrator źródła danych ODBC** .  

## <a name="import-data-into-excel-from-hdinsight"></a>Importowanie danych do programu Excel z usługi HDInsight

Poniższe kroki opisują sposób importowania danych z tabeli Hive do skoroszytu programu Excel przy użyciu źródła danych ODBC utworzonego w poprzedniej sekcji.

1. Otwórz nowy lub istniejący skoroszyt w programie Excel.

2. Na karcie **dane** przejdź do okna do **pobierania danych** > **z innych źródeł** > **z ODBC** , aby uruchomić okno **z ODBC** .

    ![Otwórz Kreatora połączenia danych programu Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Otwórz Kreatora połączenia danych programu Excel")

3. Z listy rozwijanej wybierz nazwę źródła danych utworzoną w ostatniej sekcji, a następnie wybierz przycisk **OK**.

4. Do pierwszego użycia zostanie otwarte okno dialogowe **sterownika ODBC** . Z menu po lewej stronie wybierz pozycję **Windows** . Następnie wybierz pozycję **Połącz** , aby otworzyć okno **Nawigator** .

5. W **oknie Nawigator**przejdź do **gałęzi HIVE** > **default** > **hivesampletable**, a następnie wybierz pozycję **Load (ładowanie**). Importowanie danych do programu Excel trwa kilka chwil.

    ![Usługa HDInsight Excel Hive ODBC — Nawigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Usługa HDInsight Excel Hive ODBC — Nawigator")

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób użycia sterownika Microsoft Hive ODBC do pobierania danych z usługi HDInsight do programu Excel. Podobnie można pobrać dane z usługi HDInsight do SQL Database. Możliwe jest również przekazywanie danych do usługi HDInsight. Aby dowiedzieć się więcej, zobacz:

* [Wizualizuj Apache Hive dane za pomocą usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizowanie interakcyjnych zapytań danych Hive za pomocą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Użyj programu Apache Zeppelin do uruchamiania zapytań Apache Hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z Apache Hadoop przy użyciu Power Query](apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia z usługą Azure HDInsight i uruchamianie zapytań Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Użyj narzędzia Azure HDInsight do Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](./../hdinsight-upload-data.md).
