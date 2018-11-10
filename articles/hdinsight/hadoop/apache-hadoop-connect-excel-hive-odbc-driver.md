---
title: Łączenie programu Excel z usługą Hadoop przy użyciu sterownika Hive ODBC — usługa Azure HDInsight
description: Informacje o sposobie konfigurowania i używania sterownika Hive ODBC firmy Microsoft dla programu Excel, wykonywać zapytania względem danych w klastrach HDInsight z programu Microsoft Excel.
keywords: hadoop programu excel, programu hive w programie excel, hive odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: ecdde9b5ac4c6c303edf553af3eb33986d4fb199
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005091"
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Łączenie programu Excel do platformy Hadoop w usłudze Azure HDInsight przy użyciu sterownika Hive ODBC firmy Microsoft

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Rozwiązania danych Big Data firmy Microsoft z klastrami Apache Hadoop, które zostały wdrożone przez HDInsight Azure integruje się składniki Microsoft Business Intelligence (BI). Przykładem takiej integracji jest możliwość łączenia z programu Excel w magazynie danych Hive w klastrze usługi Hadoop na platformie HDInsight za pomocą sterownika Microsoft Hive Open Database Connectivity (ODBC).

Istnieje również możliwość łączenia danych skojarzonych z klastra usługi HDInsight i innymi źródłami danych, w tym inne klastry usługi Hadoop (inne niż HDInsight), z poziomu programu Excel przy użyciu dodatku Microsoft Power Query dla programu Excel. Aby uzyskać informacje na temat instalowania i za pomocą dodatku Power Query, zobacz [Connect Excel to HDInsight za pomocą dodatku Power Query][hdinsight-power-query].



**Wymagania wstępne**:

Przed przystąpieniem do wykonywania w tym artykule, musi mieć następujące elementy:

* **Klaster usługi HDInsight**. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Stacja robocza** za pomocą pakietu Office 2013 Professional Plus, Office 365 Pro Plus, programem Excel 2013 Standalone lub Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Zainstaluj sterownik Hive ODBC firmy Microsoft
Pobieranie i instalowanie sterownika Microsoft Hive ODBC z [Centrum pobierania][hive-odbc-driver-download].

Ten sterownik można zainstalować w 32-bitową lub 64-bitowych wersjach systemu Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 i Windows Server 2012. Sterownik umożliwia połączenie usługi Azure HDInsight. Jest zainstalować wersję, która jest zgodna z wersją aplikacji, w których są używane sterownika ODBC. W tym samouczku sterownik jest używany w programie Office Excel.

## <a name="create-hive-odbc-data-source"></a>Tworzenie źródła danych ODBC usługi Hive
Poniższe kroki pokazują sposób tworzenia źródła danych ODBC programu Hive.

1. Z systemu Windows 8 lub Windows 10, naciśnij klawisz Windows, aby otworzyć ekran startowy, a następnie wpisz **źródeł danych**.
2. Kliknij przycisk **Konfigurowanie źródła danych ODBC (32-bitowy)** lub **Konfigurowanie źródła danych ODBC (64-bitowy)** w zależności od używanej wersji pakietu Office. Jeśli używasz Windows 7, wybierz opcję **źródeł danych ODBC (32-bitowa)** lub **źródeł danych ODBC (64-bitowy)** z **narzędzia administracyjne**. Zostanie wyświetlona **Administrator źródła danych ODBC** okna dialogowego.
   
    ![Administrator źródła danych OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "skonfigurować przy użyciu Administratora źródła danych ODBC DSN")

3. Przy użyciu serwera DNS użytkownika, kliknij przycisk **Dodaj** otworzyć **Utwórz nowe źródło danych** kreatora.
4. Wybierz **sterownika Microsoft Hive ODBC**, a następnie kliknij przycisk **Zakończ**. Zostanie wyświetlona **Hive ODBC Driver DNS Instalator programu Microsoft** okna dialogowego.
5. Wpisz lub wybierz poniższe wartości:
   
   | Właściwość | Opis |
   | --- | --- |
   |  Data Source Name (Nazwa źródła danych) |Nadaj nazwę źródła danych. |
   |  Host |Wprowadź ciąg &lt;nazwa_klastra_usługi_HDInsight>.azurehdinsight.net, np. myHDICluster.azurehdinsight.net. |
   |  Port |Użyj portu <strong>443</strong>. (Ten port został zmieniony z 563 na 443). |
   |  Database (Baza danych) |Użyj wartości <strong>Default</strong> (Domyślna). |
   |  Mechanism (Mechanizm) |Wybierz wartość <strong>Azure HDInsight Service</strong> (Usługa Azure HDInsight). |
   |  Nazwa użytkownika |Wprowadź username użytkownika HTTP klastra HDInsight. Domyślna nazwa użytkownika to <strong>admin</strong>. |
   |  Hasło |Wprowadź hasło użytkownika klastra HDInsight. |
   
    </table>
   
    Istnieją pewne ważne parametry Pamiętaj o kliknięciu **zaawansowane opcje**:
   
   | Parametr | Opis |
   | --- | --- |
   |  Użyj zapytania natywne |Jeśli wybrano, sterownik ODBC nie podejmuje próby konwertować TSQL HiveQL. Są służy tylko wtedy, gdy są się, że przesyłasz czystego instrukcje HiveQL w 100%. Podczas nawiązywania połączenia programu SQL Server lub usługi Azure SQL Database, należy pozostawić nie zaznaczone. |
   |  Pobrano następującą liczbę na blok wierszy |Podczas pobierania dużą liczbę rekordów, dostrajanie ten parametr może być konieczne zapewnienia optymalnej wydajności. |
   |  Domyślna długość kolumny ciąg, długość kolumny binarne, skala dziesiętną kolumny |Typ danych długości i opisie może mieć wpływ na sposób dane są zwracane. Spowodują niepoprawne informacje, które mają zostać zwrócone ze względu na utratę dokładności i/lub obcięcie. |

    ![Zaawansowane opcje](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN zaawansowane opcje konfiguracji")

1. Kliknij przycisk **Test** do testowania źródła danych. Gdy źródło danych jest skonfigurowany poprawnie, pokazuje *testów ZAKOŃCZYŁO się pomyślnie!*.
2. Kliknij przycisk **OK** aby zamknąć okno dialogowe testu. Nowe źródło danych, które są wymienione na **Administrator źródła danych ODBC**.
3. Kliknij przycisk **OK** aby zakończyć pracę kreatora.

## <a name="import-data-into-excel-from-hdinsight"></a>Importowanie danych do programu Excel z usługi HDInsight
W poniższych krokach opisano sposób importowania danych z tabeli programu Hive do skoroszytu programu Excel przy użyciu źródła danych ODBC, który został utworzony w poprzedniej sekcji.

1. Otwórz nowy lub istniejący skoroszyt w programie Excel.
2. Z **danych** kliknij pozycję **Pobierz dane**, kliknij przycisk **z innych źródeł**, a następnie kliknij przycisk **z ODBC** można uruchomić **danych Kreator połączenia**.
   
    ![Otwieranie Kreatora połączenia danych](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "otwieranie Kreatora połączenia danych")
4. Wybierz nazwę źródła danych, który został utworzony w ostatniej sekcji, a następnie kliknij przycisk **OK**.
5. Wprowadź nazwę użytkownika usługi Hadoop (nazwa domyślna to administrator) i hasło, a następnie kliknij przycisk **Connect**.
6. W oknie Nawigator rozwiń **HIVE**, rozwiń **domyślne**, kliknij przycisk **hivesampletable**, a następnie kliknij przycisk **obciążenia**. Importowanie danych do programu Excel może potrwać kilka sekund.

    ![Nawigator HDInsight Hive ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "otwieranie Kreatora połączenia danych")


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób użyć sterownika Hive ODBC firmy Microsoft do pobierania danych z usługi HDInsight do programu Excel. Podobnie można pobierać dane z usługi HDInsight do bazy danych SQL. Istnieje również możliwość przekazywania danych do usługi HDInsight. Aby dowiedzieć się więcej, zobacz:

* [Wizualizuj dane programu Hive z usługą Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Uruchamianie zapytań programu Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin ](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z usługą Hadoop przy użyciu dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


