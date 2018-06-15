---
title: Łączenie programu Excel do platformy Hadoop za pomocą sterownika ODBC Hive — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania i używania danych zapytania w klastrach HDInsight sterownik Microsoft Hive ODBC dla programu Excel z programu Microsoft Excel.
keywords: hadoop w programie excel, hive w programie excel, hive odbc
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 26234ca17d833fef01ad5a6465824c99d84cc556
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200507"
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Łączenie programu Excel do platformy Hadoop w usłudze Azure HDInsight z sterownik Microsoft Hive ODBC

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Rozwiązania danych Big Data firmy Microsoft składniki Microsoft Business Intelligence (BI) jest zintegrowany z klastrów platformy Apache Hadoop, które zostały wdrożone w usłudze Azure HDInsight. Przykładem takiej integracji jest możliwość łączenia programu Excel w magazynie danych gałęzi klastra usługi Hadoop w usłudze HDInsight za pomocą sterownika Microsoft Hive bazy danych połączenia ODBC (Open).

Istnieje również możliwość łączenia danych związanych z klastrem usługi HDInsight i innych źródeł danych, w tym inne klastry Hadoop (z systemem innym niż HDInsight), w programie Excel przy użyciu dodatku Microsoft Power Query dla programu Excel. Aby uzyskać informacje na temat instalowania i za pomocą dodatku Power Query, zobacz [łączenie programu Excel do usługi HDInsight za pomocą dodatku Power Query][hdinsight-power-query].



**Wymagania wstępne**:

Przed rozpoczęciem tego artykułu, musi mieć następujące elementy:

* **Klaster usługi HDInsight**. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Stacja robocza** z pakietu Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 autonomicznej lub Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Zainstalować sterownik Microsoft Hive ODBC
Pobierz i zainstaluj sterownik Microsoft Hive ODBC z [Centrum pobierania][hive-odbc-driver-download].

W 32-bitowy lub 64-bitowych wersjach systemu Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 i Windows Server 2012 można zainstalować ten sterownik. Sterownik umożliwia połączenie do usługi Azure HDInsight. Jest zainstalowanie wersji zgodnej z wersją aplikacji, w którym korzystać ze sterownika ODBC. W tym samouczku sterownik jest używany z Office Excel.

## <a name="create-hive-odbc-data-source"></a>Tworzenie źródła danych ODBC usługi Hive
Poniższe kroki pokazują, jak utworzyć Hive źródła danych ODBC.

1. Z systemu Windows 8 lub Windows 10, naciśnij klawisz systemu Windows, aby otworzyć ekranu startowego, a następnie wpisz **źródeł danych**.
2. Kliknij przycisk **skonfigurować źródła danych ODBC (32-bitowy)** lub **Konfigurowanie źródeł danych ODBC (64-bitowy)** w zależności od używanej wersji pakietu Office. Jeśli korzystasz z systemu Windows 7, wybierz **źródła danych ODBC (32 bity)** lub **źródła danych ODBC (64 bity)** z **narzędzia administracyjne**. Powinna zostać wyświetlona **Administrator źródła danych ODBC** okna dialogowego.
   
    ![Administrator źródeł danych OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "skonfigurować DSN przy użyciu Administrator źródła danych ODBC")

3. Przy użyciu serwera DNS użytkownika, kliknij przycisk **Dodaj** otworzyć **Utwórz nowe źródło danych** kreatora.
4. Wybierz **sterownik Microsoft Hive ODBC**, a następnie kliknij przycisk **Zakończ**. Powinna zostać wyświetlona **Instalator programu Microsoft Hive ODBC sterownika DNS** okna dialogowego.
5. Wpisz lub wybierz poniższe wartości:
   
   | Właściwość | Opis |
   | --- | --- |
   |  Data Source Name (Nazwa źródła danych) |Nadaj nazwę źródła danych. |
   |  Host |Wprowadź ciąg &lt;nazwa_klastra_usługi_HDInsight>.azurehdinsight.net, np. myHDICluster.azurehdinsight.net. |
   |  Port |Użyj portu <strong>443</strong>. (Ten port został zmieniony z 563 na 443). |
   |  Database (Baza danych) |Użyj wartości <strong>Default</strong> (Domyślna). |
   |  Mechanism (Mechanizm) |Wybierz wartość <strong>Azure HDInsight Service</strong> (Usługa Azure HDInsight). |
   |  Nazwa użytkownika |Wprowadź username użytkownika HTTP klastra usługi HDInsight. Domyślna nazwa użytkownika to <strong>admin</strong>. |
   |  Hasło |Wprowadź hasło użytkownika klastra usługi HDInsight. |
   
    </table>
   
    Istnieją pewne ważne parametry znać po kliknięciu **zaawansowane opcje**:
   
   | Parametr | Opis |
   | --- | --- |
   |  Użyj natywnego zapytania |Gdy jest wybrana, sterownik ODBC nie próbuje przekonwertować TSQL HiveQL. Są go używać tylko wtedy, gdy 100% się, że przesyłasz czysty instrukcje HiveQL. Podczas nawiązywania połączenia z serwerem SQL lub bazy danych SQL Azure, należy pozostawić niezaznaczone. |
   |  Pobranych na blok wierszy |Podczas pobierania dużej liczby rekordów, dostrajanie ten parametr może zajść konieczność zapewnienia optymalnej wydajności. |
   |  Domyślna długość kolumny ciąg, długość kolumny binarnej, skala kolumny dziesiętnej |Typ danych długości i opisie może mieć wpływ na sposób dane są zwracane. Spowodują one nieprawidłowe informacje będą zwracane z powodu utraty dokładność i/lub obcięcie. |

    ![Zaawansowane opcje](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN zaawansowane opcje konfiguracji")

1. Kliknij przycisk **Test** do testowania źródła danych. Gdy źródło danych jest skonfigurowane poprawnie, pokazuje *testy ZAKOŃCZYŁO się pomyślnie!*.
2. Kliknij przycisk **OK** aby zamknąć okno dialogowe testu. Nowe źródło danych musi zostać umieszczony w **Administrator źródła danych ODBC**.
3. Kliknij przycisk **OK** aby zakończyć pracę kreatora.

## <a name="import-data-into-excel-from-hdinsight"></a>Importowanie danych do programu Excel z usługi HDInsight
W poniższych krokach opisano sposób importowania danych z tabeli programu Hive do skoroszytu programu Excel przy użyciu źródła danych ODBC, który został utworzony w poprzedniej sekcji.

1. Otwórz nowy lub istniejący skoroszyt w programie Excel.
2. Z **danych** , kliknij pozycję **Pobierz dane**, kliknij przycisk **z innych źródeł**, a następnie kliknij przycisk **z ODBC** można uruchomić **danych Kreator połączenia**.
   
    ![Kreator połączenia danych otwartych](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Kreator połączenia danych otwartych")
4. Wybierz nazwę źródła danych, który został utworzony w ostatniej sekcji, a następnie kliknij przycisk **OK**.
5. Wprowadź nazwę użytkownika Hadoop (domyślną nazwą jest admin) i hasło, a następnie kliknij przycisk **Connect**.
6. W Nawigatorze, rozwiń węzeł **HIVE**, rozwiń węzeł **domyślne**, kliknij przycisk **hivesampletable**, a następnie kliknij przycisk **obciążenia**. Importowanie danych do programu Excel może potrwać kilka sekund.

    ![Nawigator ODBC programu Hive HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Kreator połączenia danych otwartych")


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób użycia sterownik Microsoft Hive ODBC do pobierania danych z usługi HDInsight do programu Excel. Podobnie można pobierać dane z usługi HDInsight do bazy danych SQL. Istnieje również możliwość do przekazania danych do usługi HDInsight. Aby dowiedzieć się więcej, zobacz:

* [Wizualizacja gałęzi przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane interakcyjne zapytań Hive z usługą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Umożliwia uruchamianie zapytań Hive w usłudze Azure HDInsight Zeppelin ](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel do platformy Hadoop za pomocą dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Łączenie do usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


