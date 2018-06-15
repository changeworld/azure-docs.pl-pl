---
title: Data Lake tools dla programu Visual Studio z Hortonworks piaskownicy - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą usługi Azure Data Lake tools dla programu Visual Studio piaskownicy Hortonworks uruchomiony na lokalnej maszynie wirtualnej. Z tych narzędzi można tworzyć i uruchamiania zadań Hive i Pig na piaskownicy, a dane wyjściowe zadania w widoku i historii.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: larryfr
ms.openlocfilehash: a4c1f5a8100d5d4017e56ef129aa4f4826746868
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886735"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Użyj usługi Azure Data Lake tools dla programu Visual Studio z piaskownicy Hortonworks

Usługa Azure Data Lake zawiera narzędzia do pracy z ogólnym klastrów platformy Hadoop. Ten dokument zawiera kroki niezbędne do korzystania z narzędzi Data Lake z piaskownicy Hortonworks uruchomiony na lokalnej maszynie wirtualnej.

Przy użyciu izolowanego Hortonworks umożliwia pracę z platformą Hadoop lokalnie na środowiska deweloperskiego. Po opracowaniu rozwiązania, można wdrożyć ją na dużą skalę, następnie można przenieść do klastra usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Piaskownica Hortonworks działający na maszynie wirtualnej na środowiska deweloperskiego. Ten dokument został zapisany i przetestowana piaskownicy uruchomionych w Oracle VirtualBox. Aby uzyskać informacje na temat konfigurowania piaskownicy, zobacz [wprowadzenie Hortonworks piaskownicy.](hadoop/apache-hadoop-emulator-get-started.md) dokument.

* Visual Studio 2013, Visual Studio 2015 lub Visual Studio 2017 (dowolna wersja).

* [Zestawu Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/) 2.7.1 lub nowszej.

* [Azure Data Lake tools dla Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurowanie haseł piaskownicy

Upewnij się, że piaskownicy Hortonworks jest uruchomiona. Następnie postępuj zgodnie z instrukcjami [wprowadzenie w piaskownicy Hortonworks](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) dokumentu. Te kroki skonfigurować hasło SSH `root` konto i Ambari `admin` konta. Te hasła są używane, gdy połączysz się piaskownicy w programie Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Połączenie narzędzi do piaskownicy

1. Otwórz program Visual Studio, wybierz **widoku**, a następnie wybierz **Eksploratora serwera**.

2. Z **Eksploratora serwera**, kliknij prawym przyciskiem myszy **HDInsight** wpis, a następnie wybierz **Połącz z emulatora HDInsight**.

    ![Zrzut ekranu z Eksploratora serwera, z Połącz z emulatora HDInsight wyróżnione](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Z **Połącz z emulatora HDInsight** okna dialogowego wprowadź hasło, które są skonfigurowane dla narzędzia Ambari.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnioną pozycją pole tekstowe hasła](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Kliknij przycisk **Dalej**, aby kontynuować.

4. Użyj **hasło** pole hasło skonfigurowane dla `root` konta. Pozostaw pola wartość domyślną.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnioną pozycją pole tekstowe hasła](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Kliknij przycisk **Dalej**, aby kontynuować.

5. Poczekaj, aż weryfikacji usług, aby zakończyć. W niektórych przypadkach sprawdzanie poprawności może zakończyć się niepowodzeniem i monit o zaktualizowanie konfiguracji. Jeśli weryfikacja zakończy się niepowodzeniem, wybierz **aktualizacji**i zaczekaj, aż konfiguracji i weryfikacji dla usługi zakończyć.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym przycisku Aktualizuj](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > Proces aktualizacji używa narzędzia Ambari, aby zmodyfikować konfigurację piaskownicy Hortonworks do czego oczekuje się przy użyciu usługi Data Lake tools dla programu Visual Studio.

6. Po zakończeniu sprawdzania poprawności, wybierz **Zakończ** aby zakończyć konfigurację.
    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnioną pozycją przycisk Zakończ](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > W zależności od szybkości środowiska projektowego i ilość pamięci przydzielonej do maszyny wirtualnej może upłynąć kilka minut, aby skonfigurować i zweryfikować usług.

Po wykonaniu tych kroków, masz teraz **lokalnego klastra usługi HDInsight** wpis w Eksploratorze serwera w obszarze **HDInsight** sekcji.

## <a name="write-a-hive-query"></a>Napisz zapytanie Hive

Gałąź zapewnia języka przypominającego SQL kwerendy (HiveQL) do pracy z danych strukturalnych. Wykonaj następujące kroki, aby dowiedzieć się, jak do wykonywania kwerend na żądanie do klastra lokalnego.

1. W **Eksploratora serwera**, kliknij prawym przyciskiem myszy wpis dla klastra lokalnego dodanego wcześniej, a następnie wybierz **Napisz zapytanie Hive**.

    ![Zrzut ekranu z Eksploratora serwera zapisu wyróżnione zapytanie Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Zostanie otwarte okno nowego zapytania. W tym miejscu można szybko zapisu i przesłać zapytanie z lokalnym klastrem.

2. W nowym oknie zapytania wprowadź następujące polecenie:

        select count(*) from sample_08;

    Aby uruchomić zapytanie, zaznacz **przesyłania** w górnej części okna. Pozostaw inne wartości (**partii** i nazwa serwera) na wartości domyślne.

    ![Zrzut ekranu okna zapytań, przycisk Prześlij wyróżnione](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Umożliwia także menu rozwijanym obok pozycji **przesyłania** wybierz **zaawansowane**. Zaawansowane opcje umożliwiają udostępniają dodatkowe opcje podczas przesyłania zadania.

    ![Zrzut ekranu przedstawia skryptu, okno dialogowe](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Po przesłaniu zapytania, zostanie wyświetlony stan zadania. Stan zadania Wyświetla informacje o zadaniu jest przetwarzany przez Hadoop. **Stan zadania** zawiera informacje o stanie zadania. Stan jest okresowo aktualizowana, lub można ręcznie odświeżyć stan ikony odświeżania.

    ![Okno dialogowe zrzut ekranu widoku zadania, z wyróżnioną pozycją stan zadania](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Po **stan zadania** zmienia się na **zakończone**, zostanie wyświetlony skierowany acykliczne Graph (DAG). Ten schemat przedstawia ścieżka wykonywania określonej przez Tez podczas przetwarzania zapytania Hive. Tez jest domyślny aparat wykonywania gałęzi w klastrze lokalnym.

    > [!NOTE]
    > Tez jest to wartość domyślna, korzystając z klastrów usługi HDInsight opartych na systemie Linux. Nie jest domyślnie w usłudze HDInsight z systemu Windows. Aby go użyć, należy dodać wiersz `set hive.execution.engine = tez;` na początku zapytania Hive.

    Użyj **dane wyjściowe zadania** łącze, aby wyświetlić dane wyjściowe. W takim przypadku jest 823, liczbę wierszy w tabeli sample_08. Można wyświetlić informacje diagnostyczne o zadaniu przy użyciu **dziennik zadań** i **Pobierz dziennik YARN** łącza.

4. Można również uruchamiać zadania Hive interaktywnego, zmieniając **partii** do **Interactive**. Następnie wybierz **Execute**.

    ![Zrzut ekranu interakcyjne i wykonywanie przycisków wyróżnione](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Interakcyjne zapytania strumieni dziennika dane wyjściowe, generowane podczas przetwarzania na **danych wyjściowych serwera HiveServer2** okna.

    > [!NOTE]
    > Informacje są takie same, który jest dostępny z **dziennik zadań** link po zakończeniu zadania.

    ![Zrzut ekranu przedstawiający pliku dziennika](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Utwórz projekt Hive

Można również utworzyć projekt, który zawiera wiele skryptów Hive. Użyj projektu, jeśli pokrewne skrypty lub przechowywania skryptów w systemie kontroli wersji.

1. W programie Visual Studio, wybierz **pliku**, **nowy**, a następnie **projektu**.

2. Z listy projektów, rozwiń węzeł **szablony**, rozwiń węzeł **usługi Azure Data Lake**, a następnie wybierz **gałąź (HDInsight)**. Wybierz z listy szablonów **Hive próbki**. Wprowadź nazwę i lokalizację, a następnie wybierz **OK**.

    ![Wyróżnione okno zrzut ekranu nowego projektu, przy użyciu usługi Azure Data Lake, HIVE, przykładowe Hive i OK](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**Hive próbki** projekt zawiera dwa skrypty **WebLogAnalysis.hql** i **SensorDataAnalysis.hql**. Skrypty te można przesłać za pomocą takie same **przesyłania** u góry okna.

## <a name="create-a-pig-project"></a>Utwórz projekt usługi Pig

Gałąź zawiera języka przypominającego SQL do pracy z danych strukturalnych, natomiast Pig działanie polega na przekształcenia danych. Pig zapewnia umożliwia tworzenie potoku transformacji języka (Pig Latin). Aby korzystanie z języka Pig z lokalnym klastrem, wykonaj następujące kroki:

1. Otwórz program Visual Studio i wybierz **pliku**, **nowy**, a następnie **projektu**. Z listy projektów, rozwiń węzeł **szablony**, rozwiń węzeł **usługi Azure Data Lake**, a następnie wybierz **Pig (HDInsight)**. Wybierz z listy szablonów **aplikacji Pig**. Wprowadź nazwę lokalizacji, a następnie wybierz **OK**.

    ![Okno zrzut ekranu nowego projektu, przy użyciu usługi Azure Data Lake, Pig, Pig, aplikacji i OK, wyróżniony](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Wprowadź następujący tekst jako zawartość **script.pig** pliku, który został utworzony z tego projektu.

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

    Pig używa innego języka niż Hive, sposób uruchamiania zadania jest jednolita obu językach za pośrednictwem **przesyłania** przycisku. Wybieranie listy rozwijanej obok **przesyłania** Wyświetla okno dialogowe Zaawansowane przesyłania programu Pig.

    ![Zrzut ekranu przedstawia skryptu, okno dialogowe](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Stan zadania i dane wyjściowe będzie również wyświetlana, taka sama jak zapytań programu Hive.

    ![Zrzut ekranu przedstawiający zakończonych zadań Pig](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Wyświetl zadania

Narzędzia Data Lake umożliwiają również łatwe wyświetlanie informacji o zadaniach, które zostały uruchomione na platformie Hadoop. Wykonaj następujące kroki, aby wyświetlić zadania, które zostały uruchomione w klastrze lokalnym.

1. Z **Eksploratora serwera**, kliknij prawym przyciskiem myszy klaster lokalny, a następnie wybierz **Wyświetl zadania**. Zostanie wyświetlona lista zadań, które zostały przesłane do klastra.

    ![Zrzut ekranu z Eksploratora serwera, z wyróżnioną pozycją zadaniami widoku](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Z listy zadań wybierz jedną, aby wyświetlić szczegóły zadania.

    ![Zrzut ekranu z zadania przeglądarki, z jednym z wyróżnionym zadania](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Informacje wyświetlane jest podobne do zostanie wyświetlony po uruchomieniu zapytania Hive i Pig, oraz łącza do wyświetlania danych wyjściowych i rejestrowania informacji.

3. Można również zmodyfikować i ponownie prześlij zadanie w tym miejscu.

## <a name="view-hive-databases"></a>Wyświetl Hive baz danych

1. W **Eksploratora serwera**, rozwiń węzeł **lokalnego klastra usługi HDInsight** wpis, a następnie rozwiń węzeł **bazy danych programu Hive**. **Domyślne** i **xademo** są wyświetlane bazy danych w klastrze lokalnym. Rozszerzanie bazy danych zawiera tabele w bazie danych.

    ![Zrzut ekranu Eksploratora serwera z bazami danych rozwinięty](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Rozszerzanie tabeli są wyświetlane dla tej tabeli. Aby szybko wyświetlić dane, kliknij prawym przyciskiem myszy tabelę, a następnie wybierz **Wyświetl pierwszych 100 wierszy**.

    ![Zrzut ekranu z Eksploratora serwera, z tabeli rozwinięty i Wyświetl pierwszych 100 wierszy wybrane](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Właściwości bazy danych i tabeli

Można wyświetlić właściwości bazy danych lub tabeli. Wybieranie **właściwości** Wyświetla szczegóły wybranego elementu w oknie właściwości. Na przykład zapoznaj się z informacjami pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu właściwości okna](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Tworzenie tabeli

Aby utworzyć tabelę, kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz **Create Table**.

![Zrzut ekranu z Eksploratora serwera, z Create Table wyróżnione](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Następnie można utworzyć tabeli za pomocą formularza. Na dole Poniższy zrzut ekranu widać HiveQL raw, który jest używany do tworzenia tabeli.

![Zrzut ekranu przedstawiający formularz używany do tworzenia tabeli](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Kolejne kroki

* [Learning liny piaskownicy Hortonworks](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Samouczek Hadoop — wprowadzenie HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
