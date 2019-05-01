---
title: Data Lake tools for Visual Studio z Piaskownicą Hortonworks — Azure HDInsight
description: Dowiedz się, jak korzystać z usługi Azure Data Lake tools for Visual Studio z piaskownicą Hortonworks w lokalnej maszyny Wirtualnej. Za pomocą tych narzędzi możesz utworzyć i uruchamiania zadań Hive i Pig w piaskownicy i wyświetlanie danych wyjściowych z zadania i historię.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 8172da3f573f78365cbe76f435a4817e64bc136c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700353"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Użyj usługi Azure Data Lake tools for Visual Studio z Piaskownicą Hortonworks

Usługa Azure Data Lake obejmuje narzędzia do pracy z ogólnych klastry platformy Apache Hadoop. Ten dokument zawiera kroki niezbędne do korzystania z narzędzi Data Lake z Piaskownicą Hortonworks w lokalnej maszyny wirtualnej.

Korzystanie z Piaskownicą Hortonworks umożliwia lokalnie pracować z usługą Hadoop w środowisku deweloperskim. Po opracowaniu rozwiązania, a następnie można wdrożyć je na dużą skalę, można przenieść do klastra usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Piaskownica Hortonworks działający na maszynie wirtualnej w środowisku deweloperskim. Ten dokument został napisany i przetestowane z piaskownicą działające w Oracle VirtualBox. Aby uzyskać informacje na temat konfigurowania piaskownicy, zobacz [Rozpoczynanie pracy z piaskownicą Hortonworks.](hadoop/apache-hadoop-emulator-get-started.md) dokument.

* Visual Studio 2013, Visual Studio 2015 lub Visual Studio 2017 (w każdej wersji).

* [Zestawu Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/) 2.7.1 lub nowszej.

* [Azure Data Lake tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurowanie haseł dla obszaru piaskownicy

Upewnij się, że działa Piaskownicą Hortonworks. Następnie postępuj zgodnie z instrukcjami w [rozpocząć pracę z Piaskownicą Hortonworks](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) dokumentu. Te kroki skonfigurować hasło usługi SSH `root` konta i Apache Ambari `admin` konta. Te hasła są używane podczas łączenia z piaskownicy za pomocą programu Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Połącz narzędzia do piaskownicy

1. Otwórz program Visual Studio, wybierz **widoku**, a następnie wybierz pozycję **Eksploratora serwera**.

2. Z **Eksploratora serwera**, kliknij prawym przyciskiem myszy **HDInsight** wpis, a następnie wybierz pozycję **Połącz z emulatorem HDInsight**.

    ![Zrzut ekranu z Eksploratora serwera, za pomocą programu Connect z emulatorem HDInsight wyróżniony](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Z **Połącz z emulatorem HDInsight** okna dialogowego wprowadź hasło, który został skonfigurowany dla systemu Ambari.

    ![Zrzut ekranu przedstawiający okno dialogowe, wyróżnione pola tekstowego hasła](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Kliknij przycisk **Dalej**, aby kontynuować.

4. Użyj **hasło** pola, aby wprowadzić hasło skonfigurowane dla `root` konta. Pozostaw inne pola na wartość domyślną.

    ![Zrzut ekranu przedstawiający okno dialogowe, wyróżnione pola tekstowego hasła](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Kliknij przycisk **Dalej**, aby kontynuować.

5. Poczekaj, aż sprawdzanie poprawności usług, aby zakończyć. W niektórych przypadkach weryfikacja może zakończyć się niepowodzeniem i wyświetlenie monitu o aktualizację konfiguracji. Jeśli weryfikacja zakończy się niepowodzeniem, wybierz opcję **aktualizacji**i zaczekaj do konfiguracji i weryfikacji dla usługi zakończyć.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym przyciskiem aktualizacji](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]  
    > Proces aktualizacji używa Ambari, aby zmodyfikować konfigurację Piaskownicą Hortonworks, do czego oczekuje się przy użyciu usługi Data Lake tools for Visual Studio.

6. Po zakończeniu walidacji wybierz **Zakończ** aby zakończyć konfigurację.
    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym przyciskiem Zakończ](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]  
     > W zależności od szybkości środowiska deweloperskiego i ilość pamięci przydzielonej do maszyny wirtualnej może potrwać kilka minut na konfigurowanie i weryfikowanie usługi.

Po wykonaniu tych kroków, masz teraz **lokalny klaster HDInsight** wpis w oknie Eksploratora serwera w obszarze **HDInsight** sekcji.

## <a name="write-an-apache-hive-query"></a>Napisz zapytanie technologii Hive

Gałąź zawiera język zapytań przypominający SQL (HiveQL) do pracy z danymi strukturalnymi. Wykonaj następujące kroki, aby dowiedzieć się, jak uruchamiać zapytania na żądanie w klastrze lokalnym.

1. W **Eksploratora serwera**, kliknij prawym przyciskiem myszy wpis dla klastra lokalnego, wcześniej dodany, a następnie wybierz **Napisz zapytanie Hive**.

    ![Zrzut ekranu z Eksploratora serwera zapisu wyróżnione zapytania programu Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Zostanie otwarte okno nowego zapytania. W tym miejscu można szybko pisać i przesyłać zapytania do lokalnego klastra.

2. W nowym oknie zapytania wprowadź następujące polecenie:

        select count(*) from sample_08;

    Aby uruchomić zapytanie, wybierz pozycję **przesyłania** w górnej części okna. Pozostałe wartości pozostaw (**partii** i nazwę serwera) na wartości domyślne.

    ![Zrzut ekranu przedstawiający okno zapytania, z wyróżnionym przyciskiem Zatwierdź](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Można również użyć menu rozwijanego obok **przesyłania** wybrać **zaawansowane**. Zaawansowane opcje umożliwiają udostępniają dodatkowe opcje podczas przesyłania zadania.

    ![Zrzut ekranu przedstawia skrypt okno dialogowe](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Po przesłaniu zapytania jest wyświetlany stan zadania. Stan zadania Wyświetla informacje o zadaniu, w trakcie przetwarzania usługi Hadoop. **Stan zadania** zawiera informacje o stanie zadania. Stan jest aktualizowana okresowo po lub ikonę odświeżania można użyć, aby ręcznie odświeżyć stan.

    ![Okno dialogowe zrzut ekranu widoku zadania, stan zadania wyróżniony](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Po **stan zadania** zmieni się na **Zakończono**, przekierowanie acykliczne wykresu (DAG) jest wyświetlana. Ten diagram w tym artykule opisano ścieżki wykonywania, który został określony poprzez Tez, podczas przetwarzania zapytania programu Hive. Tez jest domyślny aparat wykonywania programu Hive w klastrze lokalnym.

    > [!NOTE]  
    > Apache Tez jest również wartością domyślną, korzystając z klastrami HDInsight opartych na systemie Linux. Nie jest domyślny w HDInsight z systemem Windows. Aby go użyć, należy dodać wiersz `set hive.execution.engine = tez;` początek zapytania Hive.

    Użyj **dane wyjściowe zadania** link, aby wyświetlić dane wyjściowe. W tym przypadku jest to 823, liczba wierszy w tabeli sample_08. Informacje diagnostyczne o zadaniu można wyświetlić przy użyciu **dziennik zadań** i **Pobierz dziennik usługi YARN** łącza.

4. Możesz również uruchomić zadania Hive interaktywnie, zmieniając **partii** pole **Interactive**. Następnie wybierz pozycję **Execute**.

    ![Zrzut ekranu interaktywne i wykonywania przyciski wyróżniony](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Zapytanie interakcyjne strumieni dziennika danych wyjściowych wygenerowanych podczas przetwarzania w celu **wyjście usługi HiveServer2** okna.

    > [!NOTE]  
    > Informacje są takie same, dostępny w **dziennik zadań** link po zakończeniu zadania.

    ![Zrzut ekranu przedstawiający dziennika danych wyjściowych](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Tworzenie projektu programu Hive

Można również utworzyć projekt, który zawiera wiele skryptów programu Hive. Gdy pokrewne skrypty lub mają być przechowywane skryptów w systemie kontroli wersji, należy użyć projektu.

1. W programie Visual Studio, wybierz **pliku**, **New**, a następnie **projektu**.

2. Z listy projektów, rozwiń węzeł **szablony**, rozwiń węzeł **usługi Azure Data Lake**, a następnie wybierz pozycję **HIVE (HDInsight)**. Z listy szablonów wybierz **Hive przykładowe**. Wprowadź nazwę i lokalizację, a następnie wybierz pozycję **OK**.

    ![Wyróżnione okna zrzut ekranu nowego projektu z usługi Azure Data Lake, HIVE, Hive próbki i OK](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**Hive przykładowe** projekt zawiera dwa skrypty **WebLogAnalysis.hql** i **SensorDataAnalysis.hql**. Możesz przesłać te skrypty, korzystając z tych samych **przesyłania** znajdujący się u góry okna.

## <a name="create-an-apache-pig-project"></a>Tworzenie projektu Apache Pig

Hive zapewnia języka przypominającego SQL do pracy z danymi strukturalnymi, Pig działa, gdy, wykonując przekształcenia na danych. Pig zawiera język (Pig Latin), który umożliwia tworzenie potoku przekształcenia. Aby korzystanie z języka Pig z lokalnym klastrem, wykonaj następujące kroki:

1. Otwórz program Visual Studio, a następnie wybierz pozycję **pliku**, **New**, a następnie **projektu**. Z listy projektów, rozwiń węzeł **szablony**, rozwiń węzeł **usługi Azure Data Lake**, a następnie wybierz pozycję **Pig (HDInsight)**. Z listy szablonów wybierz **aplikacji Pig**. Wprowadź nazwę i lokalizację, a następnie wybierz **OK**.

    ![Wyróżnione okna zrzut ekranu nowego projektu z usługi Azure Data Lake, Pig, Pig, aplikacji i OK](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Wprowadź następujący tekst jako zawartość **script.pig** pliku, który został utworzony za pomocą tego projektu.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Gdy Pig używa innego języka niż gałąź, sposób uruchamiania zadania jest wspólne dla obu językach za pośrednictwem **przesyłania** przycisku. Wybierając listę rozwijaną obok **przesyłania** Wyświetla okno dialogowe Zaawansowane przesyłania dla technologii Pig.

    ![Zrzut ekranu przedstawia skrypt okno dialogowe](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Wyświetlane są także stan zadania i dane wyjściowe, takie same jak zapytania programu Hive.

    ![Zrzut ekranu przedstawiający ukończonego zadania Pig](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Wyświetl zadania

Data Lake tools umożliwiają również łatwe wyświetlanie informacji o zadaniach, które zostały uruchomione na platformie Hadoop. Wykonaj następujące kroki, aby wyświetlić zadania, które zostały uruchomione w klastrze lokalnym.

1. Z **Eksploratora serwera**, kliknij prawym przyciskiem myszy klaster lokalny, a następnie wybierz **Wyświetl zadania**. Zostanie wyświetlona lista zadań, które zostały przesłane do klastra.

    ![Zrzut ekranu z Eksploratora serwera, za pomocą widoku zadań wyróżniony](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Z listy zadań wybierz jedną, aby wyświetlić szczegóły zadania.

    ![Zrzut ekranu z przeglądarki zadań, przy użyciu jednego z zadań wyróżniony](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Informacje wyświetlane jest podobny do wyświetlanych po uruchomieniu zapytania programu Hive i Pig, wraz z łączami do wyświetlania danych wyjściowych i rejestrowania informacji.

3. Można również zmodyfikować i ponownie prześlij zadanie, w tym miejscu.

## <a name="view-hive-databases"></a>Wyświetl bazy danych programu Hive

1. W **Eksploratora serwera**, rozwiń węzeł **lokalny klaster HDInsight** wpis, a następnie rozwiń węzeł **bazy danych programu Hive**. **Domyślne** i **xademo** są wyświetlane bazy danych w klastrze lokalnym. Rozszerzanie bazy danych zawiera tabele w bazie danych.

    ![Zrzut ekranu Eksploratora serwera z bazami danych rozwinięte](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Rozwijanie tabeli są wyświetlane dla tej tabeli. Aby szybko wyświetlić dane, kliknij prawym przyciskiem myszy tabelę i wybierz **Wyświetl pierwszych 100 wierszy**.

    ![Zrzut ekranu z Eksploratora serwera, z tabeli rozwinięte i Wyświetl pierwszych 100 wierszy zaznaczone](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Właściwości bazy danych i tabeli

Można wyświetlić właściwości bazy danych lub tabeli. Wybieranie **właściwości** wyświetlająca szczegóły dla wybranego elementu w oknie dialogowym właściwości. Na przykład zobacz informacje przedstawione na poniższym zrzucie ekranu:

![Zrzut ekranu właściwości okna](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Tworzenie tabeli

Aby utworzyć tabelę, kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz **Create Table**.

![Zrzut ekranu z Eksploratora serwera, z wyróżnioną pozycją polecenia Create Table](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Następnie można utworzyć tabeli za pomocą formularza. W dolnej części poniższym zrzucie ekranu widać HiveQL raw, który jest używany do tworzenia tabeli.

![Zrzut ekranu przedstawiający formularzu, który został użyty do utworzenia tabeli](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Kolejne kroki

* [Nauka podstaw Piaskownicą Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Samouczek platformy Apache Hadoop — wprowadzenie do HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
