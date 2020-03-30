---
title: Narzędzia usługi Data Lake dla programu Visual Studio & Hortonworks — Usługa Azure HDInsight
description: Dowiedz się, jak korzystać z narzędzi usługi Azure Data Lake dla programu Visual Studio z piaskownicą Hortonworks działającą na lokalnej maszynie wirtualnej. Za pomocą tych narzędzi można tworzyć i uruchamiać zadania hive i pig w piaskownicy oraz wyświetlać dane wyjściowe i historię zadań.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73500164"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Korzystanie z narzędzi usługi Azure Data Lake dla programu Visual Studio w piaskownicy Hortonworks

Usługa Azure Data Lake zawiera narzędzia do pracy z ogólnymi klastrami Apache Hadoop. Ten dokument zawiera kroki potrzebne do korzystania z narzędzi Usługi Data Lake z piaskownicy Hortonworks uruchomionej na lokalnej maszynie wirtualnej.

Korzystanie z piaskownicy Hortonworks umożliwia lokalną pracę z Hadoop w środowisku programistycznym. Po opracowaniu rozwiązania i chcesz wdrożyć je na dużą skalę, można następnie przejść do klastra HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Piaskownica Hortonworks, działająca na maszynie wirtualnej w środowisku programistycznym. Ten dokument został napisany i przetestowany z piaskownicy uruchomionej w Oracle VirtualBox. Aby uzyskać informacje na temat konfigurowania piaskownicy, zobacz [Wprowadzenie do piaskownicy Hortonworks.](hadoop/apache-hadoop-emulator-get-started.md) Dokumentu.

* Program Visual Studio.

* Zestaw [SDK platformy Azure dla platformy .NET](https://azure.microsoft.com/downloads/) 2.7.1 lub nowszych.

* [Narzędzia usługi Azure Data Lake dla programu Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurowanie haseł do piaskownicy

Upewnij się, że piaskownica Hortonworks jest uruchomiona. Następnie wykonaj kroki opisane w dokumencie [Wprowadzenie w dokumencie Hortonworks Sandbox.](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) Te kroki konfigurują hasło `root` dla konta SSH i konta `admin` Apache Ambari. Te hasła są używane podczas łączenia się z piaskownicy z programu Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Podłączanie narzędzi do piaskownicy

1. Otwórz program Visual Studio, wybierz pozycję **Widok**, a następnie wybierz pozycję **Eksplorator serwera**.

2. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy wpis **HDInsight,** a następnie wybierz polecenie **Połącz z emulatorem HDInsight**.

    ![Eksplorator serwera z wyróżnionym emulatorem Połącz z hdinsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. W oknie dialogowym **Połącz z emulatorem HDInsight** wprowadź hasło skonfigurowane dla ambari.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym polem tekstowym hasła ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Kliknij przycisk **Dalej**, aby kontynuować.

4. Użyj pola **Hasło,** aby wprowadzić hasło `root` skonfigurowane dla konta. Pozostaw pozostałe pola po wartości domyślnej.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym polem tekstowym hasła głównego](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Kliknij przycisk **Dalej**, aby kontynuować.

5. Poczekaj na zakończenie sprawdzania poprawności usług. W niektórych przypadkach sprawdzanie poprawności może zakończyć się niepowodzeniem i monit o zaktualizowanie konfiguracji. Jeśli sprawdzanie poprawności nie powiedzie się, wybierz **opcję Aktualizuj**i poczekaj na zakończenie konfiguracji i weryfikacji usługi.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym przyciskiem Aktualizuj](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > Proces aktualizacji używa Ambari do modyfikowania konfiguracji piaskownicy Hortonworks do tego, co jest oczekiwane przez narzędzia usługi Data Lake dla programu Visual Studio.

6. Po zakończeniu sprawdzania poprawności wybierz **zakończ,** aby zakończyć konfigurację.
    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym przyciskiem Zakończ](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > W zależności od szybkości środowiska programistycznego i ilości pamięci przydzielonej do maszyny wirtualnej może upłynąć kilka minut, aby skonfigurować i zweryfikować usługi.

Po wykonać następujące kroki, masz teraz **wpis klastra lokalnego HDInsight** w Eksploratorze serwera, w sekcji **HDInsight.**

## <a name="write-an-apache-hive-query"></a>Pisanie kwerendy gałęzi Apache

Hive zapewnia język zapytań podobnych do SQL (HiveQL) do pracy z danymi strukturalnymi. Skorzystaj z poniższych kroków, aby dowiedzieć się, jak uruchamiać zapytania na żądanie względem klastra lokalnego.

1. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy wpis dla klastra lokalnego, który został dodany wcześniej, a następnie wybierz pozycję **Napisz kwerendę gałęzi**.

    ![Zrzut ekranu przedstawiający Eksploratora serwera z wyróżnioną wyróżnioną wyróżnioną wyróżnioną kwerendą rula](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Zostanie otwarte okno nowego zapytania. W tym miejscu można szybko napisać i przesłać zapytanie do klastra lokalnego.

2. W nowym oknie kwerendy wprowadź następujące polecenie:

        select count(*) from sample_08;

    Aby uruchomić kwerendę, wybierz **pozycję Prześlij** w górnej części okna. Pozostaw pozostałe wartości **(nazwa partii** i serwera) przy wartościach domyślnych.

    ![Zrzut ekranu przedstawiający okno kwerendy z wyróżnionym przyciskiem Prześlij](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    Można również użyć menu rozwijanego obok pozycji **Prześlij,** aby wybrać opcję **Zaawansowane**. Opcje zaawansowane umożliwiają podanie dodatkowych opcji podczas przesyłania zadania.

    ![Zrzut ekranu przedstawiający gałąź okna dialogowego Prześlij skrypt](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. Po przesłaniu kwerendy pojawi się stan zadania. Stan zadania wyświetla informacje o zadaniu, ponieważ jest ono przetwarzane przez Hadoop. **Stan zadania** zapewnia stan zadania. Stan jest okresowo aktualizowany lub można użyć ikony odświeżania, aby odświeżyć stan ręcznie.

    ![Zrzut ekranu przedstawiający okno dialogowe Widok zadania z wyróżnionym stanem zadania](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    Po zmianie **stanu zadania** na **Zakończone**zostanie wyświetlony wykres acykliczny (DAG). Na tym diagramie opisano ścieżkę wykonywania, która została określona przez Tez podczas przetwarzania zapytania hive. Tez jest aparatem wykonywania domyślnego dla gałęzi w klastrze lokalnym.

    > [!NOTE]  
    > Apache Tez jest również domyślny, gdy używasz klastrów HDInsight opartych na systemie Linux. Nie jest to ustawienie domyślne w systemie Windows HDInsight. Aby go tam używać, należy `set hive.execution.engine = tez;` dodać wiersz na początku zapytania hive.

    Użyj **łącza Wyjście zadania,** aby wyświetlić dane wyjściowe. W tym przypadku jest to 823, liczba wierszy w tabeli sample_08. Można wyświetlić informacje diagnostyczne o zadaniu za pomocą **łącza Dziennik zadań** i Pobierz dziennik **YARN.**

4. Zadania gałęzi można również uruchamiać interaktywnie, zmieniając pole **Partia** na **Interaktywne**. Następnie wybierz polecenie **Wykonaj**.

    ![Wyróżniony zrzut ekranu przedstawiający przyciski Interaktywne i Wykonywanie](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Zapytanie interaktywne strumieni dziennik danych wyjściowych wygenerowane podczas przetwarzania do **hiveServer2 output** okna.

    > [!NOTE]  
    > Informacje są takie same, które są dostępne w **łączu Dziennik zadań** po zakończeniu zadania.

    ![Zrzut ekranu przedstawiający dane wyjściowe HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Tworzenie projektu gałęzi

Można również utworzyć projekt, który zawiera wiele skryptów hive. Użyj projektu, gdy masz powiązane skrypty lub chcesz przechowywać skrypty w systemie kontroli wersji.

1. W programie Visual Studio wybierz pozycję **Plik**, **Nowy**, a następnie **pozycję Project**.

2. Z listy projektów rozwiń pozycję **Szablony**, rozwiń węzeł **Azure Data Lake**, a następnie wybierz pozycję **HIVE (HDInsight).** Z listy szablonów wybierz pozycję **Przykładu gałęzi**. Wprowadź nazwę i lokalizację, a następnie wybierz **przycisk OK**.

    ![Nowe okno projektu z usługą Azure Data Lake, Sample hive i OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

Przykładowy projekt **Hive** zawiera dwa skrypty: **WebLogAnalysis.hql** i **SensorDataAnalysis.hql**. Skrypty te można przesłać za pomocą tego samego przycisku **Prześlij** w górnej części okna.

## <a name="create-an-apache-pig-project"></a>Tworzenie projektu Apache Pig

Hive zapewnia język podobny do języka SQL do pracy z danymi strukturalnymi, Pig działa przez wykonywanie przekształceń na danych. Pig zapewnia język (Pig Latin), który pozwala na opracowanie potoku przekształceń. Aby użyć pig z klastrem lokalnym, wykonaj następujące kroki:

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Plik**, **Nowy**, a następnie **project**. Z listy projektów rozwiń pozycję **Szablony**, rozwiń pozycję **Usługa Azure Data Lake**, a następnie wybierz pozycję **Świnia (HDInsight).** Z listy szablonów wybierz **opcję Pig Application**. Wprowadź nazwę, lokalizację, a następnie wybierz **przycisk OK**.

    ![Zrzut ekranu przedstawiający okno Nowy projekt z wyróżnioną pojednaną pojednawką usługi Azure Data Lake, Pig, Pig Application i OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Wprowadź następujący tekst jako zawartość pliku **script.pig,** który został utworzony za pomocą tego projektu.

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

    Podczas gdy pig używa innego języka niż Hive, jak uruchomić zadania jest spójne między obu języków, za pomocą **przycisku Prześlij.** Wybranie listy rozwijanej obok **opcji Prześlij** powoduje wyświetlenie zaawansowanego okna dialogowego przesyłania dla świni.

    ![Zrzut ekranu przedstawiający okno dialogowe Prześlij skrypt](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. Wyświetlany jest również stan zadania i dane wyjściowe, takie same jak kwerenda hive.

    ![Zrzut ekranu przedstawiający ukończoną pracę świni](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Wyświetlanie ofert pracy

Narzędzia usługi Data Lake umożliwiają również łatwe wyświetlanie informacji o zadaniach uruchomionych na programie Hadoop. Poniższe kroki można wykonać, aby wyświetlić zadania, które zostały uruchomione w klastrze lokalnym.

1. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy klaster lokalny, a następnie wybierz polecenie **Wyświetl zadania**. Zostanie wyświetlona lista zadań przesłanych do klastra.

    ![Zrzut ekranu przedstawiający Eksploratora serwera z wyróżnioną pojednawką Zadania widoku](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. Z listy zadań wybierz jeden z nich, aby wyświetlić szczegóły zadania.

    ![Zrzut ekranu przedstawiający przeglądarkę zadań z wyróżnionym jednym z zadań](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    Wyświetlane informacje są podobne do tego, co widzisz po uruchomieniu kwerendy hive lub pig, w tym łącza do wyświetlania informacji wyjściowych i dziennika.

3. Można również zmodyfikować i ponownie przesłać zadanie w tym miejscu.

## <a name="view-hive-databases"></a>Wyświetlanie baz danych gałęzi

1. W **Eksploratorze serwera**rozwiń wpis **klastra lokalnego USŁUGI HDInsight,** a następnie rozwiń pozycję **Bazy danych hive**. Zostaną **Default** wyświetlone domyślne i **xademo** baz danych w klastrze lokalnym. Rozwijanie bazy danych pokazuje tabele w bazie danych.

    ![Zrzut ekranu przedstawiający Eksploratora serwera z rozszerzonymi bazami danych](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. Rozwijanie tabeli powoduje wyświetlenie kolumn dla tej tabeli. Aby szybko wyświetlić dane, kliknij prawym przyciskiem myszy tabelę i wybierz polecenie **Wyświetl 100 wierszy**.

    ![Eksplorator serwera z rozwiniętą tabelą i wybrano pozycję Wyświetl 100 wierszy](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Właściwości bazy danych i tabeli

Można wyświetlić właściwości bazy danych lub tabeli. Wybranie **opcji Właściwości** powoduje wyświetlenie szczegółów zaznaczonego elementu w oknie właściwości. Na przykład zobacz informacje wyświetlane na poniższym zrzucie ekranu:

![Zrzut ekranu z okna Właściwości](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Tworzenie tabeli

Aby utworzyć tabelę, kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Utwórz tabelę**.

![Zrzut ekranu przedstawiający Eksploratora serwera z wyróżnioną pozycją Utwórz tabelę](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Następnie można utworzyć tabelę za pomocą formularza. W dolnej części poniższego zrzutu ekranu można zobaczyć nieprzetworzony HiveQL, który jest używany do tworzenia tabeli.

![Zrzut ekranu przedstawiający formularz użyty do utworzenia tabeli](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Następne kroki

* [Nauka lin piaskownicy Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop samouczek - Pierwsze kroki z HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
