---
title: Łączenie programu Excel do platformy Apache Hadoop przy użyciu sterownika Hive ODBC — usługa Azure HDInsight
description: Informacje o sposobie konfigurowania i używania sterownika Hive ODBC firmy Microsoft dla programu Excel, wykonywać zapytania względem danych w klastrach HDInsight z programu Microsoft Excel.
keywords: hadoop programu excel, programu hive w programie excel, hive odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: 01e7ca184f2be755f23cd402e0694ea282e3b85e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441399"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Łączenie programu Excel do platformy Apache Hadoop w usłudze Azure HDInsight przy użyciu sterownika Hive ODBC firmy Microsoft

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Rozwiązania danych Big Data firmy Microsoft integruje składniki Microsoft Business Intelligence (BI) z klastrami Apache Hadoop, które zostały wdrożone w usłudze Azure HDInsight. Przykładem takiej integracji jest możliwość łączenia z programu Excel w magazynie danych Hive w klastrze usługi Hadoop na platformie HDInsight za pomocą sterownika Microsoft Hive Open Database Connectivity (ODBC).

Istnieje również możliwość łączenia danych skojarzonych z klastra usługi HDInsight i innymi źródłami danych, w tym inne klastry usługi Hadoop (inne niż HDInsight), z poziomu programu Excel przy użyciu dodatku Microsoft Power Query dla programu Excel. Aby uzyskać informacje na temat instalowania i za pomocą dodatku Power Query, zobacz [Connect Excel to HDInsight za pomocą dodatku Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania w tym artykule, musi mieć następujące elementy:

* Klaster usługi HDInsight Hadoop. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Stacja robocza z pakietu Office 2010 Professional Plus lub nowszym lub programu Excel 2010 lub nowszej.

## <a name="install-microsoft-hive-odbc-driver"></a>Zainstaluj sterownik Hive ODBC firmy Microsoft
Pobierz i zainstaluj [sterownika Microsoft Hive ODBC](https://go.microsoft.com/fwlink/?LinkID=286698) wersję, która jest zgodna z wersją aplikacji, w której będą używać sterownika ODBC.  W tym artykule sterownik jest używany dla programu Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Utwórz źródło danych Apache Hive ODBC
Poniższe kroki pokazują sposób tworzenia źródła danych ODBC programu Hive.

1. W Windows, przejdź do menu Start > Narzędzia administracyjne Windows > źródła danych ODBC (32-bit)/(64-bit).  Spowoduje to otwarcie **Administrator źródła danych ODBC** okna.

    ![Administrator źródła danych OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "skonfigurować przy użyciu Administratora źródła danych ODBC DSN")

1. Z **DSN użytkownika** zaznacz **Dodaj** otworzyć **Utwórz nowe źródło danych** okna.

1. Wybierz **sterownika Microsoft Hive ODBC**, a następnie wybierz pozycję **Zakończ** otworzyć **Hive ODBC Driver DSN Instalator programu Microsoft** okna.

1. Wpisz lub wybierz poniższe wartości:

   | Właściwość | Opis |
   | --- | --- |
   |  Data Source Name (Nazwa źródła danych) |Nadaj nazwę źródła danych. |
   |  Hostami |Wprowadź polecenie `HDInsightClusterName.azurehdinsight.net`. Na przykład: `myHDICluster.azurehdinsight.net` |
   |  Port |Użyj portu **443**. (Ten port został zmieniony z 563 na 443). |
   |  Database (Baza danych) |Użyj **domyślne**. |
   |  Mechanism (Mechanizm) |Wybierz **Windows, usługa Azure HDInsight** |
   |  Nazwa użytkownika |Wprowadź username użytkownika HTTP klastra HDInsight. Domyślna nazwa użytkownika to **admin**. |
   |  Hasło |Wprowadź hasło użytkownika klastra HDInsight. Zaznacz pole wyboru **Zapisz hasła (zaszyfrowane)** .|

1. Opcjonalnie: Wybierz **opcje zaawansowane...**  

   | Parametr | Opis |
   | --- | --- |
   |  Użyj zapytania natywne |Jeśli wybrano, sterownik ODBC nie podejmuje próby konwertować TSQL HiveQL. Są służy tylko wtedy, gdy są się, że przesyłasz czystego instrukcje HiveQL w 100%. Podczas nawiązywania połączenia programu SQL Server lub usługi Azure SQL Database, należy pozostawić nie zaznaczone. |
   |  Pobrano następującą liczbę na blok wierszy |Podczas pobierania dużą liczbę rekordów, dostrajanie ten parametr może być konieczne zapewnienia optymalnej wydajności. |
   |  Domyślna długość kolumny ciąg, długość kolumny binarne, skala dziesiętną kolumny |Typ danych długości i opisie może mieć wpływ na sposób dane są zwracane. Spowodują niepoprawne informacje, które mają zostać zwrócone ze względu na utratę dokładności i/lub obcięcie. |

    ![Zaawansowane opcje](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN zaawansowane opcje konfiguracji")

1. Wybierz **Test** do testowania źródła danych. Gdy źródło danych jest skonfigurowany poprawnie, wynik testu pokazuje **sukces!** .  

1. Wybierz **OK** zamknięcie okna testów.  

1. Wybierz **OK** zamknąć **Hive ODBC Driver DSN Instalator programu Microsoft** okna.  

1. Wybierz **OK** zamknąć **Administrator źródła danych ODBC** okna.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importowanie danych do programu Excel z usługi HDInsight

W poniższych krokach opisano sposób importowania danych z tabeli programu Hive do skoroszytu programu Excel przy użyciu źródła danych ODBC, który został utworzony w poprzedniej sekcji.

1. Otwórz nowy lub istniejący skoroszyt w programie Excel.

2. Z **danych** kartę, przejdź do folderu **Pobierz dane** > **z innych źródeł** > **z ODBC** do uruchomienia **Z ODBC** okna.

    ![Otwieranie Kreatora połączenia danych](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "otwieranie Kreatora połączenia danych")

3. Z listy rozwijanej wybierz nazwę źródła danych, który został utworzony w ostatniej sekcji, a następnie wybierz **OK**.

4. Do pierwszego użycia **sterownika ODBC** spowoduje to otwarcie okna dialogowego. Wybierz **Windows** menu po lewej stronie. Następnie wybierz pozycję **Connect** otworzyć **Nawigator** okna.

5. Z **Nawigator**, przejdź do **HIVE** > **domyślne** > **hivesampletable**, a następnie wybierz pozycję  **Obciążenia**. Może potrwać kilka minut przed importowanie danych do programu Excel.

    ![Nawigator HDInsight Hive ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "otwieranie Kreatora połączenia danych")

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób użyć sterownika Hive ODBC firmy Microsoft do pobierania danych z usługi HDInsight do programu Excel. Podobnie można pobierać dane z usługi HDInsight do bazy danych SQL. Istnieje również możliwość przekazywania danych do usługi HDInsight. Aby dowiedzieć się więcej, zobacz:

* [Wizualizuj dane Apache Hive z usługą Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel do platformy Apache Hadoop za pomocą dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchomić zapytania usługi Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./../hdinsight-upload-data.md).