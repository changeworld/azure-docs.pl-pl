---
title: Apache Hadoop & narzędzia usługi Visual Studio Data Lake — Azure HDInsight
description: Dowiedz się, jak zainstalować i używać narzędzia usługi Data Lake Tools dla programu Visual Studio. Użyj narzędzia, aby połączyć się z klastrami Apache Hadoop w usłudze Azure HDInsight, a następnie uruchomić zapytania hive.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 7504826f267d717f30c5e88621578412c744e5f9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383505"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Użyj narzędzi usługi Data Lake Tools for Visual Studio, aby połączyć się z usługą Azure HDInsight i uruchomić kwerendy gałęzi Apache

Dowiedz się, jak korzystać z usług Microsoft Azure Data Lake i Stream Analytics Tools for Visual Studio (Data Lake Tools). Użyj narzędzia, aby połączyć się z [klastrami Apache Hadoop w usłudze Azure HDInsight](apache-hadoop-introduction.md) i przesłać zapytania hive.  

Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight, zobacz [Wprowadzenie do usługi HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Aby uzyskać więcej informacji na temat łączenia się z apache Storm, zobacz [Rozwiń topologie języka C# dla usługi Apache Storm przy użyciu narzędzi Usługi Data Lake](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Możesz użyć narzędzia Data Lake Tools for Visual Studio do uzyskiwania dostępu do usług Azure Data Lake Analytics i HDInsight. Aby uzyskać informacje na temat narzędzi Data Lake Tools, zobacz [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł i użyć narzędzia usługi Data Lake Tools for Visual Studio, potrzebne są następujące elementy:

* Klaster usługi Azure HDInsight. Aby utworzyć klaster HDInsight, zobacz [Wprowadzenie przy użyciu aplikacji Apache Hadoop w usłudze Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Aby uruchamiać interaktywne zapytania gałęzi Apache, potrzebny jest klaster [zapytań interaktywnych USŁUGI HDInsight.](../interactive-query/apache-interactive-query-get-started.md)  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). [Wersja społeczności programu Visual Studio](https://visualstudio.microsoft.com/vs/community/) jest bezpłatna. Instrukcje pokazane tutaj są dla [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalacja narzędzi Data Lake Tools for Visual Studio  

Postępuj zgodnie z odpowiednimi instrukcjami, aby zainstalować narzędzia usługi Data Lake Tools dla swojej wersji programu Visual Studio:

* Dla programu Visual Studio 2017 lub Visual Studio 2019:

    Podczas instalacji programu Visual Studio upewnij się, że uwzględnisz obciążenie **deweloperskie platformy Azure** lub obciążenie **magazynem i przetwarzaniem danych.**  

    W przypadku istniejących instalacji programu Visual Studio przejdź do paska menu IDE i wybierz pozycję **Narzędzia** > **Pobierz narzędzia i funkcje,** aby otworzyć Instalatora programu Visual Studio. Na karcie **Obciążenia** wybierz co najmniej obciążenie **programistyczne platformy Azure** (w obszarze & **Chmura sieci Web).** Możesz też wybrać obciążenie **związane z przechowywaniem i przetwarzaniem danych** (w obszarze Inne zestawy **narzędzi).**

  ![Wybór obciążenia, Instalator programu Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* Dla programu Visual Studio 2015:

    [Pobierz Narzędzia Usługi Data Lake](https://www.microsoft.com/download/details.aspx?id=49504). Należy wybrać wersję narzędzi Data Lake Tools zgodną z używaną wersją programu Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aktualizowanie narzędzi usługi Data Lake dla programu Visual Studio  

Następnie upewnij się, że aktualizujesz narzędzia usługi Data Lake Tools do najnowszej wersji.

1. Otwórz program Visual Studio.

2. W oknie **Start** wybierz pozycję **Kontynuuj bez kodu**.

3. Na pasku menu IDE programu Visual Studio wybierz pozycję **Rozszerzenia** > **zarządzaj rozszerzeniami**.

4. W oknie dialogowym **Zarządzanie rozszerzeniami** rozwiń węzeł **Aktualizacje.**

5. Jeśli lista dostępnych aktualizacji zawiera **usługi Azure Data Lake i narzędzia analityczne strumienia,** wybierz ją. Następnie wybierz przycisk **Aktualizuj.** Po **wyświetleniu i** zniknięciu okna dialogowego Pobieranie i instalowanie program Visual Studio dodaje rozszerzenie **usługi Azure Data Lake i Stream Analytic Tools** do harmonogramu aktualizacji.

6. Zamknij wszystkie okna programu Visual Studio. Zostanie wyświetlone okno dialogowe **Instalatora VSIX.**

7. Wybierz **licencję,** aby odczytać postanowienia licencyjne, a następnie wybierz pozycję **Zamknij,** aby powrócić do okna dialogowego **Instalatora VSIX.**

8. Wybierz **pozycję Modyfikuj**. Rozpoczyna się instalacja aktualizacji rozszerzenia. Po pewnym czasie okno dialogowe zmienia się, aby pokazać, że jest to zrobione wprowadzanie zmian. Wybierz pozycję **Zamknij**, a następnie uruchom ponownie program Visual Studio, aby zakończyć instalację.

> [!NOTE]  
> Możesz użyć tylko narzędzi Data Lake Tools w wersji 2.3.0.0 lub nowszej, aby nawiązać połączenie z klastrami zapytań interaktywnych oraz uruchamiać zapytania interaktywne usługi Hive.

## <a name="connect-to-azure-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami platformy Azure

Narzędzia usługi Data Lake Tools for Visual Studio umożliwiają łączenie się z klastrami usługi HDInsight, wykonywania podstawowych operacji zarządzania i uruchamianie zapytań hive.

> [!NOTE]  
> Aby uzyskać informacje dotyczące łączenia się z ogólnym klastrem Hadoop, zobacz [Jak pisać i przesyłać zapytania gałęzi przy użyciu programu Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Łączenie się z subskrypcją platformy Azure

Aby nawiązać połączenie z subskrypcją platformy Azure:

1. Otwórz program Visual Studio.

2. W oknie **Start** wybierz pozycję **Kontynuuj bez kodu**.

3. Na pasku menu IDE wybierz pozycję **Wyświetl** > **Eksploratora serwera**.

4. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy **pozycję Azure**, wybierz pozycję **Połącz z subskrypcją platformy Microsoft Azure**i zakończ proces uwierzytelniania. Z **Eksploratora serwera**rozwiń **usługę Azure** > **HDInsight,** aby wyświetlić listę istniejących klastrów usługi HDInsight.

5. Jeśli nie masz żadnych klastrów, utwórz je przy użyciu witryny Azure portal, usługi Azure PowerShell lub zestawu SDK usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastrów w umiań HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Lista klastrów usługi HDInsight, Eksplorator serwerów, program Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Rozwiń węzeł klastra usługi HDInsight. Klaster zawiera węzły baz **danych hive**. Ponadto domyślne konto magazynu, wszelkie dodatkowe połączone konta magazynu i **dziennik usługi Hadoop**. Jednostki można rozwinąć.

Po nawiązaniu połączenia z subskrypcją platformy Azure można wykonać następujące zadania.

### <a name="connect-to-azure-from-visual-studio"></a>Łączenie się z platformą Azure za pomocą programu Visual Studio

Nawiązywanie połączenia z witryną Azure Portal w programie Visual Studio:

1. W **Eksploratorze serwera**rozwiń węzeł **Azure** > **HDInsight** i wybierz klaster.

2. Kliknij prawym przyciskiem myszy klaster USŁUGI HDInsight i wybierz polecenie **Zarządzaj klastrem w witrynie Azure portal**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Zaoferuj pytania i opinie od programu Visual Studio

Aby zadawać pytania i przekazywać opinie z programu Visual Studio:

1. W Eksploratorze serwera wybierz pozycję **Azure** > **HDInsight**.

2. Kliknij prawym przyciskiem myszy **hdinsight** i wybierz **forum MSDN,** aby zadawać pytania, lub **przekaż opinię,** aby przekazać opinię.

## <a name="link-to-or-edit-a-cluster"></a>Łącze do klastra lub edytowanie go

> [!NOTE]
> Obecnie jedynym typem klastra HDInsight, z którego można utworzyć łącze, jest typ gałęzi.

Aby połączyć klaster HDInsight:

1. Kliknij prawym przyciskiem myszy pozycję **HDInsight**, a następnie wybierz polecenie **Połącz klaster HDInsight,** aby wyświetlić okno dialogowe **Łącze klastra HDInsight.**

2. Wprowadź **adres URL** połączenia `https://CLUSTERNAME.azurehdinsight.net`w formularzu . **Nazwa klastra** automatycznie wypełnia się częścią adresu URL nazwy klastra po przełączeniu do innego pola. Następnie wprowadź **nazwę użytkownika** i **hasło**i wybierz pozycję **Dalej**.

    ![Łączenie klastra, usługi HDInsight, programu Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Wybierz **pozycję Zakończ**. Jeśli łączenie klastra zakończy się pomyślnie, klaster jest następnie wyświetlany w węźle **HDInsight.**

Aby zaktualizować połączony klaster, kliknij prawym przyciskiem myszy klaster i wybierz polecenie **Edytuj**. Następnie można zaktualizować informacje klastra.

![Edytowanie połączonego klastra, usługi HDInsight, programu Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Eksplorowanie połączonych zasobów

Z poziomu Eksploratora serwera można zobaczyć domyślne konto magazynu i wszystkie połączone konta magazynu. Po rozwinięciu domyślnego konta magazynu można wyświetlić kontenery konta magazynu. Domyślne konto magazynu i domyślny kontener są oznaczone.

![Narzędzia usługi Data Lake Tools dla połączonych zasobów programu Visual Studio w Eksploratorze serwera](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Kliknij prawym przyciskiem myszy kontener i wybierz polecenie **Wyświetl kontener,** aby wyświetlić zawartość kontenera. Po otwarciu kontenera można użyć przycisków paska narzędzi, aby **odświeżyć** listę zawartości, **prześlij obiekt blob**, **Usuń wybrane obiekty blob**, **Otwórz obiekty blob**i pobrać (**Zapisz jako**) wybrane obiekty blob.

![Operacje listy kontenerów i obiektów blob, klaster HDInsight, program Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Uruchamianie interaktywnych zapytań gałęzi Apache

[Apache Hive](https://hive.apache.org) to infrastruktura magazynu danych oparta na platformie Hadoop. Usługa Hive jest używana do wykonywania podsumowań danych, zapytań i analizy. Możesz użyć narzędzi Data Lake Tools for Visual Studio, aby uruchomić zapytania usługi Hive z poziomu programu Visual Studio. Aby uzyskać więcej informacji na temat gałęzi, zobacz [Co to jest Gałąź Apache i HiveQL w usłudze Azure HDInsight?](hdinsight-use-hive.md).

[Interakcyjne zapytanie w usłudze Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) używa [hive na LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) w Apache Hive 2.1. Zapytanie interaktywne wprowadza interaktywność do złożonych zapytań w stylu magazynu danych na dużych, przechowywanych zestawach danych. Uruchamianie zapytań hive w interaktywnej kwerendzie jest znacznie szybsze niż tradycyjne zadania wsadowe gałęzi. 

> [!NOTE]  
> Uruchamianie interakcyjnych zapytań Hive jest możliwe tylko po nawiązaniu połączenia z klastrem [interakcyjnych zapytań usługi HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Można również użyć Narzędzia usługi Data Lake dla programu Visual Studio, aby zobaczyć, co znajduje się wewnątrz zadania gałęzi. Narzędzia Data Lake Tools for Visual Studio zbierają i ujawniają dzienniki Yarn dla wybranych zadań Hive.

W **Eksploratorze serwera**wybierz pozycję **Azure** > **HDInsight** i wybierz klaster.  Ten węzeł jest punktem wyjścia w **Eksploratorze serwera** dla sekcji do naśladowania.

### <a name="view-hivesampletable"></a>Zobacz tabelę hivesampletable

Wszystkie klastry HDInsight mają domyślną `hivesampletable`przykładową tabelę gałęzi o nazwie .  

W klastrze wybierz pozycję**Ulegnie rozjemce** > **domyślne** >  **bazy danych hive.**

* Aby wyświetlić `hivesampletable` schemat:

    Rozwiń **rozszerzeń .** Wyświetlane są nazwy i `hivesampletable` typy danych kolumn.

* Aby wyświetlić `hivesampletable` dane:

    Kliknij prawym przyciskiem myszy **ulesampletable**i wybierz **polecenie Wyświetl 100 wierszy.** Lista 100 wyników pojawia się w **tabeli ule: pouplebienia okna.** Ta akcja jest równoważna uruchomieniu następującej kwerendy hive przy użyciu sterownika ODBC hive:

    `SELECT * FROM hivesampletable LIMIT 100`

    Liczbę wierszy można dostosować, zmieniając **liczbę wierszy;** z listy rozwijanej można wybrać 50, 100, 200 lub 1000 wierszy.

### <a name="create-hive-tables"></a>Tworzenie tabel programu Hive

Do utworzenia tabeli programu Hive można użyć graficznego interfejsu użytkownika lub zapytań programu Hive. Aby uzyskać informacje dotyczące używania zapytań gałęzi, zobacz [Tworzenie i uruchamianie kwerend gałęzi](#create-and-run-hive-queries).

1. W klastrze wybierz pozycję **Bazy danych gałęzi domyślnie** > **default**.

2. Kliknij prawym przyciskiem **myszy pozycję domyślna**i wybierz polecenie **Utwórz tabelę**.

3. Skonfiguruj tabelę.

4. Wybierz przycisk **Utwórz tabelę,** aby przesłać zadanie, które tworzy nową tabelę Gałąź.

    ![Tworzenie okna tabeli, gałąź, klaster HDInsight, program Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Tworzenie i uruchamianie zapytań gałęzi

Masz dwie opcje umożliwiające utworzenie i uruchomienie zapytań Hive:

* Tworzenie zapytań ad hoc
* Tworzenie aplikacji Hive

#### <a name="create-an-ad-hoc-query"></a>Tworzenie kwerendy ad hoc

Aby utworzyć i uruchomić kwerendę ad hoc:

1. Kliknij prawym przyciskiem myszy klaster, w którym ma zostać uruchomione **kwerenda,** a następnie wybierz pozycję Napisz kwerendę gałęzi .  

2. Wprowadź kwerendę hive.

    Edytor Hive obsługuje funkcję IntelliSense. Narzędzia Data Lake Tools for Visual Studio obsługują ładowanie zdalnych metadanych podczas edycji skryptu Hive. Na przykład, jeśli `SELECT * FROM`wpiszesz , IntelliSense wyświetla listę wszystkich sugerowanych nazw tabel. Po określeniu nazwy tabeli funkcja IntelliSense wyświetla nazwy kolumn. Narzędzia obsługują większość instrukcji DML programu Hive, podzapytań i wbudowanych sterowników UDF.

    ![IntelliSense przykład 1, Zapytanie ad hoc gałęzi, klaster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense example 2, Zapytanie ad hoc hive, klaster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > Funkcja IntelliSense zasugeruje tylko metadane klastra zaznaczonego na pasku narzędzi usługi HDInsight.

    Oto przykładowa kwerenda, której można użyć:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Wybierz tryb wykonywania:

    * **Interaktywne**  

        Na pierwszej liście rozwijanej wybierz pozycję **Interaktywna**, a następnie wybierz pozycję **Wykonaj**.

        ![Tryb interaktywny, zapytanie ad hoc hive, klaster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Na pierwszej liście rozwijanej wybierz pozycję **Partia**, a następnie wybierz pozycję **Prześlij**. Lub wybierz ikonę rozwijaną obok pozycji **Prześlij** i wybierz pozycję **Zaawansowane**.

        ![Tryb wsadowy, kwerenda ad hoc gałęzi, klaster HDInsight, program Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Jeśli wybierzesz opcję przesyłania zaawansowanego, zostanie wyświetlone okno dialogowe **Prześlij skrypt.** Skonfiguruj dla skryptu **nazwę zadania,** **argumenty,** **dodatkowe konfiguracje**i **katalog stanu.**

        ![Okno dialogowe Prześlij skrypt, kwerenda ad hoc gałęzi, klaster HDInsight, program Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Nie można przesłać partii do klastrów zapytań interaktywnych.  Należy użyć trybu interaktywnego.

#### <a name="create-a-hive-application"></a>Tworzenie aplikacji Hive

Tworzenie i uruchamianie rozwiązania Hive:

1. Na pasku menu wybierz pozycję **Plik** > **nowego** > **projektu**.

2. W oknie **Utwórz nowy projekt** zaznacz pole wyszukiwania i wpisz **Pozycję Gałąź**. Następnie wybierz pozycję **Aplikacja gałęzi** i wybierz pozycję **Dalej**.

3. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu**, wybierz lub utwórz **lokalizację**projektu , a następnie wybierz pozycję **Utwórz**.

    ![Nowa aplikacja Hive, Konfigurowanie nowego okna projektu, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik **Script.hql**, aby otworzyć skrypt.

### <a name="view-job-summary-and-output"></a>Wyświetlanie podsumowania i danych wyjściowych zadania

Podsumowanie zadania różni się nieznacznie w trybie **partii** i **interaktywnym.**

![Okna podsumowania zadania gałęzi, tryb wsadowy i interaktywny, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Użyj ikony **Odśwież,** aby zaktualizować stan, dopóki stan zadania nie zmieni się na **Zakończone**.  

* Aby uzyskać szczegółowe informacje o zadaniu z trybu **wsadowego,** wybierz łącza u dołu, aby wyświetlić **kwerendę zlecenia, dane** **wyjściowe zlecenia**lub Dziennik **zadań**lub **wyświetlić dzienniki przędzy**.

* Aby uzyskać szczegółowe informacje o zadaniu z trybu **interaktywnego,** zobacz **dane wyjściowe i** dane **wyjściowe HiveServer2** okienka.

    ![Interaktywne dane wyjściowe zadania hive, klaster HDInsight, visual studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Wyświetlanie wykresu zadań

Obecnie wykresy zadań są wyświetlane tylko dla zadań hive, które używają Tez jako aparatu wykonywania.  Aby uzyskać informacje dotyczące włączania funkcji Tez, zobacz [Co to jest Gałąź apache i hiveQl w usłudze Azure HDInsight?](hdinsight-use-hive.md).  Zobacz też: [Użyj apache Tez zamiast map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Aby wyświetlić wszystkie operatory wewnątrz wierzchołka, kliknij dwukrotnie wierzchołki wykresu zadania. Możesz też wskazać konkretny operator, aby wyświetlić więcej jego szczegółów.

Nawet jeśli Tez jest określony jako aparat wykonywania, wykres zadania może nie być wyświetlany, jeśli nie tez aplikacji jest uruchomiona.  Taka sytuacja może wystąpić, ponieważ zadanie nie zawiera instrukcji DML. Lub dlatego, że instrukcje DML można zwrócić bez uruchamiania aplikacji Tez. Na przykład `SELECT * FROM table1` nie uruchomi aplikacji Tez.

![Wykres zadań gałęzi Apache, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Wyświetlanie szczegółów wykonania zadania

Na wykresie zadań można wybrać **szczegóły wykonania zadania,** aby uzyskać ustrukturyzowane i wizualizowane informacje dotyczące zadań gałęzi. Możesz również uzyskać więcej szczegółów pracy. Jeśli wystąpią problemy z wydajnością, możesz użyć widoku, aby uzyskać więcej szczegółów dotyczących problemu. Na przykład można pobrać informacje o tym, jak działa każde zadanie i szczegółowe informacje o każdym zadaniu (odczyt/zapis danych, harmonogram/godzina rozpoczęcia/zakończenia i inne). Skorzystaj z informacji, aby dostosować konfiguracje zadań lub architekturę systemu w oparciu o wizualizowane informacje.

![Okno Widok wykonywania zadań, narzędzia programu Data Lake Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Wyświetlanie zadań Hive

Istnieje możliwość wyświetlenia zapytań dotyczących zadań, danych wyjściowych zadań, dzienników zadań oraz dzienników Yarn dla zadań Hive.

W najnowszej wersji narzędzi możesz zobaczyć, co znajduje się w twoich zadaniach hive, zbierając i napawając dzienniki przędzy. Dziennik Yarn może być pomocny w badaniu problemów z wydajnością. Aby uzyskać więcej informacji o tym, jak hdinsight zbiera dzienniki przędzy, zobacz [Dostęp Apache Hadoop Dzienniki aplikacji YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Aby wyświetlić zadania Hive:

1. Kliknij prawym przyciskiem myszy klaster HDInsight i wybierz polecenie **Wyświetl zadania**.

    ![Wyświetlanie zadań, Gałąź Apache, klaster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Zostanie wyświetlona lista zadań Hive uruchomionych w klastrze.  

2. Wybierz zadanie. W oknie **Podsumowanie zadania gałęzi** wybierz jedno z następujących łączy:
    - **Zapytanie zadania**
    - **Produkcja zadania**
    - **Dziennik zadań**  
    - **Dziennik przędzy**

## <a name="run-apache-pig-scripts"></a>Uruchamianie skryptów Apache Pig

1. Na pasku menu wybierz pozycję **Plik** > **nowego** > **projektu**.

2. W oknie **Start** zaznacz pole wyszukiwania i wprowadź **pig**. Następnie wybierz **opcję Pig Application** i wybierz pozycję **Dalej**.

3. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu**i wybierz lub utwórz **lokalizację** dla projektu. Następnie wybierz pozycję **Utwórz**.

4. W okienku **Eksplorator rozwiązań** IDE kliknij dwukrotnie pozycję **Script.pig,** aby otworzyć skrypt.

## <a name="feedback-and-known-issues"></a>Opinie i znane problemy

* Problem, w którym wyniki uruchamiane z wartościami null nie są wyświetlane jako rozwiązane. W przypadku zablokowania na tym problemie należy skontaktować się z zespołem pomocy technicznej.

* Skrypt HQL, który tworzy program Visual Studio jest zakodowany, w zależności od ustawienia lokalnego regionu użytkownika. Skrypt nie jest wykonywany poprawnie, jeśli został przesłany do klastra jako plik binarny.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawierał informacje dotyczące sposobu używania pakietu Data Lake Tools for Visual Studio w celu nawiązywania połączenia z klastrami usługi HDInsight z programu Visual Studio. Przedstawiono też sposób uruchamiania zapytania Hive. 

* [Uruchamianie zapytań Apache Hive przy użyciu wtyczki Data Lake Tools for Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Co to jest Gałąź apache i hiveQl w usłudze Azure HDInsight?](hdinsight-use-hive.md)
* [Tworzenie klastra Apache Hadoop — szablon](apache-hadoop-linux-tutorial-get-started.md)
* [Prześlij oferty pracy Apache Hadoop w HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizuj dane z Twittera za pomocą Apache Hive i Apache Hadoop na HDInsight](../hdinsight-analyze-twitter-data-linux.md)
