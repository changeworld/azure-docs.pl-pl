---
title: Narzędzia Apache Hadoop i Data Lake dla programu Visual Studio — Azure HDInsight
description: Dowiedz się, jak zainstalować narzędzia Data Lake Tools for Visual Studio i używać ich do łączenia się z klastrami Apache Hadoop w usłudze Azure HDInsight, a następnie uruchamiać zapytania programu Hive.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 706bcbed39ca2a3fe0a38eba864edb863c31b4ec
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884045"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Używanie Data Lake narzędzi dla programu Visual Studio do nawiązywania połączenia z usługą Azure HDInsight i uruchamiania zapytań Apache Hive

Dowiedz się, jak używać [narzędzi Microsoft Azure Data Lake i Stream Analytics Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (nazywanych również narzędziami Data Lake) do łączenia się z klastrami Apache Hadoop w [usłudze Azure HDInsight](../hdinsight-hadoop-introduction.md) i przesyłania zapytań programu Hive.  

Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight, zobacz artykuły [Introduction to HDInsight](../hdinsight-hadoop-introduction.md) (Wprowadzenie do usługi HDInsight) i [Get started with HDInsight](apache-hadoop-linux-tutorial-get-started.md) (Rozpoczynanie pracy z usługą HDInsight).  

Aby uzyskać więcej informacji na temat nawiązywania połączenia z klastrem Apache Storm, zobacz [ C# tworzenie topologii dla Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Możesz użyć narzędzia Data Lake Tools for Visual Studio do uzyskiwania dostępu do usług Azure Data Lake Analytics i HDInsight. Aby uzyskać informacje na temat narzędzi Data Lake Tools, zobacz [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł i korzystać z narzędzi Data Lake Tools for Visual Studio, potrzebne są następujące elementy:

* Klaster usługi Azure HDInsight. Aby utworzyć klaster usługi HDInsight, zobacz [Rozpoczynanie pracy przy użyciu Apache Hadoop w usłudze Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Aby uruchamiać interakcyjne zapytania Apache Hive, wymagany jest klaster [interakcyjnych zapytań usługi HDInsight](../interactive-query/apache-interactive-query-get-started.md) .  

* [Program Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 lub nowszy).  [Program Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) jest bezpłatny.  Zobacz również temat [Instalowanie programu Visual studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) i [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Istnieją niewielkie różnice interfejsu w programie Visual Studio 2019.

  > [!IMPORTANT]  
  > Narzędzia Data Lake nie są już obsługiwane dla Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalacja narzędzi Data Lake Tools for Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 lub Visual Studio 2019  
  W trakcie instalacji upewnij się, że zawarto co najmniej obciążenia związane z **programowaniem Azure** lub **przechowywaniem i przetwarzaniem danych**.  

  W przypadku istniejących instalacji na pasku menu Przejdź do opcji **Narzędzia** > **Pobierz narzędzia i funkcje..** ., aby otworzyć Instalator programu Visual Studio.  Następnie wybierz co najmniej obciążenie **platformy Azure — programowanie** lub **przechowywanie i przetwarzanie danych**.

  ![Zrzut ekranu przedstawiający Instalator programu Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-2017-installation.png)

* Visual Studio 2013 i 2015  
  [Pobierz narzędzia Data Lake](https://www.microsoft.com/download/details.aspx?id=49504). Należy wybrać wersję narzędzi Data Lake Tools zgodną z używaną wersją programu Visual Studio.  

> [!NOTE]  
> Obecnie narzędzia Data Lake Tools for Visual Studio są dostępne tylko w angielskiej wersji językowej.

## <a name="update-data-lake-tools-for-visual-studio"></a>Narzędzia aktualizacji Data Lake Tools for Visual Studio  

1. Otwórz program Visual Studio.

2. Na pasku menu Przejdź do okna **Narzędzia** > **rozszerzenia i aktualizacje..** ..

3. W oknie **rozszerzenia i aktualizacje** rozwiń pozycję **aktualizacje** po lewej stronie.

4. Jeśli dostępna jest aktualizacja, w oknie głównym zostaną wyświetlone **narzędzia Azure Data Lake i Stream Analytics** .  Wybierz pozycję **Update** (Aktualizuj).

> [!NOTE]  
> Możesz użyć tylko narzędzi Data Lake Tools w wersji 2.3.0.0 lub nowszej, aby nawiązać połączenie z klastrami zapytań interaktywnych oraz uruchamiać zapytania interaktywne usługi Hive.

## <a name="connect-to-azure-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami platformy Azure
Narzędzia Data Lake Tools for Visual Studio umożliwiają nawiązywanie połączenia z klastrami HDInsight, wykonywanie podstawowych operacji zarządzania i uruchamianie zapytań programu Hive.

> [!NOTE]  
> Informacje dotyczące nawiązywania połączenia z ogólnym klastrem Hadoop znajdują się w temacie [Write and submit Hive queries using Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Zapisywanie i wysyłanie zapytań programu Hive za pomocą programu Visual Studio).

Aby nawiązać połączenie z subskrypcją platformy Azure:

1. Otwórz program Visual Studio.

2. Na pasku menu Przejdź do **widoku** > **Eksplorator serwera**.

3. W Eksplorator serwera kliknij prawym przyciskiem myszy pozycję **Azure**, wybierz pozycję **Połącz z Microsoft Azure subskrypcją...** , a następnie ukończ proces logowania.

4. Z Eksplorator serwera zostanie wyświetlona lista istniejących klastrów usługi HDInsight. W przypadku braku klastrów możesz go utworzyć za pomocą witryny Azure Portal, programu Azure PowerShell lub zestawu SDK usługi HDInsight. Więcej informacji można znaleźć w artykule [Create HDInsight clusters](../hdinsight-hadoop-provision-linux-clusters.md) (Tworzenie klastrów usługi HDInsight).

   ![Zrzut ekranu listy klastrów Eksploratora serwera narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Lista serwerów Eksploratora serwera narzędzi Data Lake Tools for Visual Studio")

5. Rozwiń węzeł klastra usługi HDInsight. Są wyświetlane **bazy danych programu Hive**, domyślne konto magazynu, połączone konta magazynu i **Dziennik usługi Hadoop** . Jednostki można rozwinąć.

Po nawiązaniu połączenia z subskrypcją platformy Azure można wykonać następujące zadania.

Nawiązywanie połączenia z witryną Azure Portal w programie Visual Studio:

1. W obszarze Eksplorator serwera przejdź do **usługi Azure** > **HDInsight** i wybierz swój klaster.

2. Kliknij prawym przyciskiem myszy klaster usługi HDInsight, a następnie wybierz pozycję **Zarządzaj klastrem w Azure Portal [Ski]** .

Aby zadawać pytania i/lub przekazać Opinie z programu Visual Studio:

1. W obszarze Eksplorator serwera przejdź do **usługi Azure** > **HDInsight**.

2. Kliknij prawym przyciskiem myszy pozycję **HDInsight** i wybierz opcję **forum MSDN** , aby zadawać pytania, lub **Przekaż opinię** , aby przesłać opinię.

## <a name="link-a-cluster"></a>Łączenie klastra
Aby połączyć klaster, kliknij prawym przyciskiem myszy pozycję **HDInsight** , a następnie wybierz pozycję **Połącz klaster usługi HDInsight**. Wprowadź **adres URL połączenia**, **nazwę użytkownika** i **hasło**, a następnie kliknij przycisk **dalej** **Zakończ**. klaster powinien znajdować się na liście w obszarze węzeł usługi HDInsight.

![Zrzut ekranu przedstawiający okno dialogowe tworzenia klastra linków narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Kliknij prawym przyciskiem myszy połączony klaster, wybierz opcję **Edytuj**, użytkownik może zaktualizować informacje o klastrze. Dodawanie klastra usługi HDInsight obsługuje teraz tylko usługi Hive.

![Zrzut ekranu przedstawiający aktualizację klastra programu Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Eksplorowanie połączonych zasobów
Z poziomu Eksploratora serwera można zobaczyć domyślne konto magazynu i wszystkie połączone konta magazynu. Po rozwinięciu domyślnego konta magazynu można wyświetlić kontenery konta magazynu. Domyślne konto magazynu i domyślny kontener są oznaczone. Kliknij prawym przyciskiem myszy dowolny kontener, aby wyświetlić jego zawartość.

![Zrzut ekranu listy połączonych zasobów Eksploratora serwera narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Wyświetlanie listy połączonych zasobów")

Po otwarciu kontenera można używać następujących przycisków do przekazywania, usuwania i pobierania obiektów blob:

![Zrzut ekranu operacji obiektu blob narzędzi Data Lake Tools for Visual Studio w Eksploratorze serwera](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Przekazywanie, usuwanie i pobieranie obiektów blob w Eksploratorze serwera")

## <a name="run-interactive-apache-hive-queries"></a>Uruchamianie interakcyjnych zapytań Apache Hive
[Apache Hive](https://hive.apache.org) to infrastruktura magazynu danych oparta na platformie Hadoop. Usługa Hive jest używana do wykonywania podsumowań danych, zapytań i analizy. Możesz użyć narzędzi Data Lake Tools for Visual Studio, aby uruchomić zapytania usługi Hive z poziomu programu Visual Studio. Aby uzyskać więcej informacji na temat programu Hive, zobacz [Korzystanie z Apache Hive z usługą HDInsight](hdinsight-use-hive.md).

[Zapytanie interakcyjne](../interactive-query/apache-interactive-query-get-started.md) używa narzędzia [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) w wersji Apache Hive 2.1. Zapytanie interakcyjne zapewnia interakcyjność w złożonych zapytaniach opartych na magazynie danych kierowanych do dużych, magazynowanych zestawów danych. Zapytania Hive w zapytaniu interakcyjnym działają dużo szybciej niż tradycyjne zadania wsadowe Hive. 

> [!NOTE]  
> Uruchamianie interakcyjnych zapytań Hive jest możliwe tylko po nawiązaniu połączenia z klastrem [interakcyjnych zapytań usługi HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Możesz też użyć narzędzi Data Lake Tools for Visual Studio, aby zobaczyć zawartość zadania Hive. Narzędzia Data Lake Tools for Visual Studio zbierają i ujawniają dzienniki Yarn dla wybranych zadań Hive.

W obszarze Eksplorator serwera przejdź do **usługi Azure** > **HDInsight** i wybierz swój klaster.  Będzie to punkt początkowy w Eksplorator serwera dla sekcji do wykonania.

### <a name="view-hivesampletable"></a>Wyświetl hivesampletable
Wszystkie klastry usługi HDInsight mają domyślną przykładową tabelę `hivesampletable`programu Hive o nazwie.  

W klastrze przejdź do **baz danych** > programu Hive jako**default** > **hivesampletable**.

* Aby wyświetlić `hivesampletable` schemat:  
Rozwiń węzeł **hivesampletable**.

* Aby wyświetlić `hivesampletable` dane:  
Kliknij prawym przyciskiem myszy pozycję **hivesampletable**, a następnie wybierz pozycję **Wyświetl pierwsze 100 wierszy**.  Jest to równoważne uruchomieniu następującego zapytania programu Hive za pomocą sterownika ODBC programu Hive:

   `SELECT * FROM hivesampletable LIMIT 100`

  Liczbę wierszy można dostosować.

  ![Zrzut ekranu zapytania schematu HDInsight Hive Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Wyniki zapytania programu Hive")

### <a name="create-hive-tables"></a>Tworzenie tabel programu Hive
Do utworzenia tabeli programu Hive można użyć graficznego interfejsu użytkownika lub zapytań programu Hive. Aby uzyskać informacje o używaniu zapytań Hive, zobacz [uruchamianie Apache Hive zapytań](#run.queries).

1. W klastrze przejdź do**domyślnych** **baz danych** > programu Hive.

2. Kliknij prawym przyciskiem myszy pozycję **domyślne**, a następnie wybierz pozycję **Utwórz tabelę**.

3. Skonfiguruj tabelę zgodnie z potrzebami.  

4. Wybierz przycisk **Utwórz tabelę**, aby przesłać zadanie i utworzyć nową tabelę programu Hive.

    ![Zrzut ekranu przedstawiający okno tworzenia tabeli w narzędziach HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Tworzenie tabeli programu Hive")

### <a name="run.queries"></a>Tworzenie i uruchamianie zapytań programu Hive
Masz dwie opcje umożliwiające utworzenie i uruchomienie zapytań Hive:

* Tworzenie zapytań ad hoc
* Tworzenie aplikacji Hive

Aby utworzyć i uruchomić zapytania ad hoc:

1. Kliknij prawym przyciskiem myszy klaster, w którym chcesz uruchomić zapytanie, a następnie wybierz polecenie **Napisz zapytanie programu Hive**.  

2. Wprowadź następujące zapytanie programu Hive:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Edytor Hive obsługuje funkcję IntelliSense. Narzędzia Data Lake Tools for Visual Studio obsługują ładowanie zdalnych metadanych podczas edycji skryptu Hive. Na przykład, jeśli wpiszesz `SELECT * FROM`, IntelliSense wyświetla wszystkie sugerowane nazwy tabel. Po określeniu nazwy tabeli funkcja IntelliSense wyświetla nazwy kolumn. Narzędzia obsługują większość instrukcji DML programu Hive, podzapytań i wbudowanych sterowników UDF.

    ![Zrzut ekranu przykładu 1 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![Zrzut ekranu przykładu 2 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > Funkcja IntelliSense zasugeruje tylko metadane klastra zaznaczonego na pasku narzędzi usługi HDInsight.

3. Wybierz tryb wykonywania:

    * **Interaktywne**  

      Upewnij się, że jest zaznaczona opcja **interaktywny** , a następnie wybierz pozycję **Wykonaj**.

      ![Zrzut ekranu zapytania i wykonania](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

      Upewnij się, że została wybrana opcja **Partia zadań** , a następnie wybierz pozycję **Prześlij**.  W przypadku wybrania opcji przesyłania zaawansowanego Skonfiguruj **nazwę zadania**, **argumenty**, **dodatkowe konfiguracje**i **katalog stanu** dla skryptu.

      ![Zrzut ekranu zapytania i partii](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)  

      ![Zrzut ekranu zapytania programu Hive w usłudze HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Przesłanie zapytań")

      > [!NOTE]  
      > Nie można przesyłać partii do interaktywnych klastrów zapytań.  Należy używać trybu interaktywnego.

Tworzenie i uruchamianie rozwiązania Hive:

1. Na pasku menu Przejdź do **pliku** > **Nowy** > **projekt..** ..

2. W lewym okienku przejdź do **instalacji** > **Azure Data Lake** > **Hive (HDInsight)** .  

3. W środkowym okienku wybierz opcję **Aplikacja Hive**. Wprowadź właściwości, a następnie wybierz opcję **OK**.

    ![Zrzut ekranu nowego projektu programu Hive w narzędziach HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Tworzenie aplikacji Hive z programu Visual Studio")

4. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik **Script.hql**, aby otworzyć skrypt.

### <a name="view-job-summary-and-output"></a>Wyświetlanie podsumowania i danych wyjściowych zadania

Podsumowanie zadania różni się **nieco w zależności od trybu** **wsadowego** .

![Podsumowanie zadania](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png "Podsumowanie zadania programu Hive")

Użyj przycisku **Odśwież** , aby zaktualizować stan do momentu, gdy stan zadania zmieni się na **zakończone**.  

* W obszarze Szczegóły zadania w trybie **wsadowym** wybierz linki u dołu, aby wyświetlić **zapytanie dotyczące zadania**, **dane wyjściowe zadania**, **Dziennik zadań**lub **Dziennik przędzy**.

* Aby uzyskać szczegółowe informacje o zadaniu w trybie **interaktywnym** , zobacz karty **dane wyjściowe** i **serwera hiveserver2 Output**.

  ![szczegóły zadania](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png "Szczegóły zadania programu Hive")

### <a name="view-job-graph"></a>Wyświetl wykres zadania

Obecnie wykresy zadań są wyświetlane tylko w przypadku zadań Hive, które używają tez jako aparatu wykonywania.  Aby uzyskać informacje na temat włączania usługi tez, zobacz [Korzystanie z Apache Hive w usłudze HDInsight](hdinsight-use-hive.md).  Zobacz również, [użyj Apache tez zamiast mapowania mapy](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Aby zobaczyć wszystkie operatory wewnątrz wierzchołka, dwukrotnie kliknij wierzchołki grafu zadania. Możesz też wskazać konkretny operator, aby wyświetlić więcej jego szczegółów.

Wykres zadania może się nie pojawić, nawet jeśli tez jest określony jako aparat wykonywania, jeśli nie zostanie uruchomiona żadna aplikacja tez.  Może się tak zdarzyć, ponieważ zadanie nie zawiera instrukcji DML lub Instrukcje DML mogą zwracać bez uruchamiania aplikacji tez. Na przykład `SELECT * FROM table1` program nie uruchomi aplikacji tez.

![Wykres zadania](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Podsumowanie zadania programu Hive")


### <a name="task-execution-detail"></a>Szczegóły wykonywania zadania

Na wykresie zadania można wybrać **szczegóły wykonania zadania** , aby uzyskać informacje o strukturze i wizualizacji zadań Hive. Możesz też uzyskać więcej szczegółów zadania. Jeśli wystąpią problemy z wydajnością, możesz użyć widoku, aby uzyskać więcej szczegółów dotyczących problemu. Na przykład możesz uzyskać informacje o sposobie działania każdego zadania oraz szczegółowe informacje o każdym zadaniu (odczyt/zapis danych, czas zaplanowany/rozpoczęcia/zakończenia itp.). Skorzystaj z informacji, aby dostosować konfiguracje zadań lub architekturę systemu w oparciu o wizualizowane informacje.

![Zrzut ekranu przedstawiający okno Widok wykonywania zadań narzędzi Data Lake Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Widok wykonywania zadań")


### <a name="view-hive-jobs"></a>Wyświetlanie zadań Hive
Istnieje możliwość wyświetlenia zapytań dotyczących zadań, danych wyjściowych zadań, dzienników zadań oraz dzienników Yarn dla zadań Hive.

Za pomocą najnowszej wersji narzędzi można sprawdzić zawartość zadań Hive, zbierając i udostępniając dzienniki Yarn. Dziennik Yarn może być pomocny w badaniu problemów z wydajnością. Więcej informacji na temat sposobu zbierania dzienników Yarn przez usługę HDInsight można znaleźć w artykule [Access HDInsight Application Logs Programmatically (Programowe uzyskiwanie dostępu do dzienników aplikacji usługi HDInsight)](../hdinsight-hadoop-access-yarn-app-logs.md).

Aby wyświetlić zadania Hive:

1. Kliknij prawym przyciskiem myszy klaster usługi HDInsight, a następnie wybierz pozycję **Wyświetl zadania**. Zostanie wyświetlona lista zadań Hive uruchomionych w klastrze.  

2. Wybierz zadanie. W oknie **Podsumowanie zadania Hive** wybierz jedną z następujących opcji:
    - **Zapytanie zadania**
    - **Dane wyjściowe zadania**
    - **Dziennik zadań**  
    - **Dziennik usługi Yarn**

    ![Zrzut ekranu przedstawiający okno zadań Hive widoku narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Wyświetlanie zadań Hive")


## <a name="run-apache-pig-scripts"></a>Uruchamianie skryptów Apache świni

1. Na pasku menu Przejdź do **pliku** > **Nowy** > **projekt..** ..

2. W lewym okienku przejdź do **instalacji** > **Azure Data Lake** > **świni (HDInsight)** .  

3. W środkowym okienku wybierz pozycję **aplikacja dla trzody chlewnej**. Wprowadź właściwości, a następnie wybierz opcję **OK**.

4. W **Eksplorator rozwiązań**kliknij dwukrotnie **skrypt. świni** , aby otworzyć skrypt.

## <a name="feedback-and-known-issues"></a>Opinie i znane problemy
* Problem, w którym wyniki uruchamiane z wartościami null nie są wyświetlane jako rozwiązane. W przypadku zablokowania na tym problemie należy skontaktować się z zespołem pomocy technicznej.
* Skrypt HQL utworzony przez program Visual Studio jest zakodowany w zależności od ustawienia lokalnego regionu użytkownika. Skrypt nie jest wykonywany poprawnie, jeśli został przesłany do klastra jako plik binarny.

## <a name="next-steps"></a>Następne kroki
Ten artykuł zawierał informacje dotyczące sposobu używania pakietu Data Lake Tools for Visual Studio w celu nawiązywania połączenia z klastrami usługi HDInsight z programu Visual Studio. Przedstawiono też sposób uruchamiania zapytania Hive. Więcej informacji można znaleźć w tych artykułach:

* [Uruchamianie zapytań Apache Hive przy użyciu wtyczki Data Lake Tools for Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Use Hadoop Hive in HDInsight](hdinsight-use-hive.md) (Używanie usługi Hadoop Hive w usłudze HDInsight)
* [Wprowadzenie do korzystania z Apache Hadoop w usłudze HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Przesyłanie Apache Hadoop zadań w usłudze HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizowanie danych usługi Twitter za pomocą Apache Hadoop w usłudze HDInsight](../hdinsight-analyze-twitter-data.md)

