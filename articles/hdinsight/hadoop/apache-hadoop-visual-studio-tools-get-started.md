---
title: Łączenie z usługą Apache Hadoop przy użyciu narzędzi Data Lake Tools for Visual Studio — Azure HDInsight
description: Dowiedz się, jak zainstalować i Data Lake Tools for Visual Studio umożliwiają łączenie się z klastrami Apache Hadoop w usłudze Azure HDInsight, a następnie uruchom zapytania programu Hive.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 485f3a4b6a5fde532229873fe1f3feaa30ece523
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450195"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Użyj narzędzi Data Lake Tools for Visual Studio, aby nawiązać połączenie z usługi Azure HDInsight i uruchomić zapytania usługi Apache Hive

Dowiedz się, jak używać [Microsoft Azure Data Lake i Stream Analytics Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) klastry (nazywane również narzędzia Data Lake Tools) do łączenia z usługą Apache Hadoop, w [Azure HDInsight](../hdinsight-hadoop-introduction.md) i przesłać zapytania Hive.  

Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight, zobacz artykuły [Introduction to HDInsight](../hdinsight-hadoop-introduction.md) (Wprowadzenie do usługi HDInsight) i [Get started with HDInsight](apache-hadoop-linux-tutorial-get-started.md) (Rozpoczynanie pracy z usługą HDInsight).  

Aby uzyskać więcej informacji na temat łączenia się z klastrem platformy Apache Storm, zobacz [programowanie C# topologii STORM Apache na HDInsight przy użyciu programu Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Możesz użyć narzędzia Data Lake Tools for Visual Studio do uzyskiwania dostępu do usług Azure Data Lake Analytics i HDInsight. Aby uzyskać informacje na temat narzędzi Data Lake Tools, zobacz [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby zakończyć w tym artykule i korzystać z narzędzi Data Lake Tools for Visual Studio, potrzebne są następujące elementy:

* Klaster usługi Azure HDInsight. Aby utworzyć klaster usługi HDInsight, zobacz [Rozpoczynanie pracy przy użyciu technologii Apache Hadoop w usłudze Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Aby uruchomić interakcyjne zapytania usługi Apache Hive, potrzebujesz [interaktywnego zapytania HDInsight](../interactive-query/apache-interactive-query-get-started.md) klastra.  

* [Program Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 lub nowsza).  [Programu Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/) jest bezpłatna.  Zobacz też [instalacji programu Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) i [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Istnieją różnice nieznaczne interfejs za pomocą programu Visual Studio 2019 r.

  > [!IMPORTANT]  
  > Narzędzia Data Lake Tools nie jest już obsługiwana dla programu Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalacja narzędzi Data Lake Tools for Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 lub Visual Studio 2019 r.  
  Podczas instalacji upewnij się, zawiera w co najmniej obciążeń **programowanie na platformie Azure** lub **przechowywanie i przetwarzanie danych**.  

  W przypadku istniejących instalacji, na pasku menu, przejdź do **narzędzia** > **Pobierz narzędzia i funkcje...**  do Otwórz Instalator programu Visual Studio.  Następnie wybierz co najmniej obciążeń **programowanie na platformie Azure** lub **przechowywanie i przetwarzanie danych**.

  ![Zrzut ekranu przedstawiający Instalatora programu Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 i 2015  
  [Pobierz program Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Należy wybrać wersję narzędzi Data Lake Tools zgodną z używaną wersją programu Visual Studio.  

> [!NOTE]  
> Obecnie narzędzia Data Lake Tools for Visual Studio są dostępne tylko w angielskiej wersji językowej.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aktualizowanie narzędzi Data Lake Tools for Visual Studio  

1. Otwórz program Visual Studio.

2. Na pasku menu, przejdź do **narzędzia** > **rozszerzenia i aktualizacje...** .

3. Z **rozszerzenia i aktualizacje** okna, rozwiń węzeł **aktualizacje** po lewej stronie.

4. Jeśli aktualizacja jest dostępna, **Azure Data Lake i Stream Analytics Tools** pojawi się w głównym oknie.  Wybierz pozycję **Update** (Aktualizuj).

> [!NOTE]  
> Możesz użyć tylko narzędzi Data Lake Tools w wersji 2.3.0.0 lub nowszej, aby nawiązać połączenie z klastrami zapytań interaktywnych oraz uruchamiać zapytania interaktywne usługi Hive.

## <a name="connect-to-azure-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami platformy Azure
Narzędzia Data Lake Tools for Visual Studio umożliwiają nawiązywanie połączenia z klastrami HDInsight, wykonywanie podstawowych operacji zarządzania i uruchamianie zapytań programu Hive.

> [!NOTE]  
> Informacje dotyczące nawiązywania połączenia z ogólnym klastrem Hadoop znajdują się w temacie [Write and submit Hive queries using Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Zapisywanie i wysyłanie zapytań programu Hive za pomocą programu Visual Studio).

Aby nawiązać połączenie z subskrypcją platformy Azure:

1. Otwórz program Visual Studio.

2. Na pasku menu, przejdź do **widoku** > **Eksploratora serwera**.

3. Z poziomu Eksploratora serwera, kliknij prawym przyciskiem myszy **Azure**, wybierz opcję **nawiązywanie połączenia z subskrypcją platformy Microsoft Azure...** i ukończyć proces logowania.

4. Z poziomu Eksploratora serwera zostanie wyświetlona lista istniejących klastrów HDInsight. W przypadku braku klastrów możesz go utworzyć za pomocą witryny Azure Portal, programu Azure PowerShell lub zestawu SDK usługi HDInsight. Więcej informacji można znaleźć w artykule [Create HDInsight clusters](../hdinsight-hadoop-provision-linux-clusters.md) (Tworzenie klastrów usługi HDInsight).

   ![Zrzut ekranu listy klastrów Eksploratora serwera narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Lista serwerów Eksploratora serwera narzędzi Data Lake Tools for Visual Studio")

5. Rozwiń węzeł klastra usługi HDInsight. **Bazy danych programu hive**, domyślne konto magazynu, połączone konta magazynu, a **dziennik usługi Hadoop** są wyświetlane. Jednostki można rozwinąć.

Po nawiązaniu połączenia z subskrypcją platformy Azure, można wykonać poniższe zadania.

Nawiązywanie połączenia z witryną Azure Portal w programie Visual Studio:

1. Z poziomu Eksploratora serwera, przejdź do **Azure** > **HDInsight** i wybierz klaster.

2. Kliknij prawym przyciskiem myszy klaster usługi HDInsight, a następnie wybierz pozycję **Zarządzaj klastrem w portalu Azure [SKI]** .

Aby zadawać pytania i/lub wyrażanie opinii w programie Visual Studio:

1. Z poziomu Eksploratora serwera, przejdź do **Azure** > **HDInsight**.

2. Kliknij prawym przyciskiem myszy **HDInsight** i wybierz opcję **forum MSDN dotyczącym** zadawać pytania, lub **Prześlij opinię** przesłać opinię.

## <a name="link-a-cluster"></a>Połącz klaster
Można połączyć klaster, klikając prawym przyciskiem myszy **HDInsight** polecenie **połączenia klastra HDInsight**. Wprowadź **adresu Url składnika połączenia**, **nazwa_użytkownika** i **hasło**, kliknij przycisk **dalej** następnie **Zakończ**, klastra powinny być wymienione w węźle HDInsight się pomyślnie.

![Zrzut ekranu z narzędzi Data Lake Tools dla programu Visual Studio łącze klastra z okna dialogowego](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Kliknij prawym przyciskiem myszy na klastrze połączone, wybierz opcję **Edytuj**, użytkownik może zaktualizować informacje o klastrze. Dodawanie klastra usługi HDInsight obsługuje tylko gałąź teraz.

![Zrzut ekranu z narzędzi Data Lake Tools dla programu Visual Studio łącze klastra aktualizacji](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Eksplorowanie połączonych zasobów
Z poziomu Eksploratora serwera można zobaczyć domyślne konto magazynu i wszystkie połączone konta magazynu. Po rozwinięciu domyślnego konta magazynu można wyświetlić kontenery konta magazynu. Domyślne konto magazynu i domyślny kontener są oznaczone. Kliknij prawym przyciskiem myszy dowolny kontener, aby wyświetlić jego zawartość.

![Zrzut ekranu listy połączonych zasobów Eksploratora serwera narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Wyświetlanie listy połączonych zasobów")

Po otwarciu kontenera można używać następujących przycisków do przekazywania, usuwania i pobierania obiektów blob:

![Zrzut ekranu operacji obiektu blob narzędzi Data Lake Tools for Visual Studio w Eksploratorze serwera](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Przekazywanie, usuwanie i pobieranie obiektów blob w Eksploratorze serwera")

## <a name="run-interactive-apache-hive-queries"></a>Uruchamianie interakcyjnych zapytań technologii Hive
[Apache Hive](https://hive.apache.org) to infrastruktura magazynu danych oparta na platformie Hadoop. Usługa Hive jest używana do wykonywania podsumowań danych, zapytań i analizy. Możesz użyć narzędzi Data Lake Tools for Visual Studio, aby uruchomić zapytania usługi Hive z poziomu programu Visual Studio. Aby uzyskać więcej informacji na temat programu Hive, zobacz [używanie programu Apache Hive z HDInsight](hdinsight-use-hive.md).

[Zapytanie interakcyjne](../interactive-query/apache-interactive-query-get-started.md) używa narzędzia [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) w wersji Apache Hive 2.1. Zapytanie interakcyjne zapewnia interakcyjność w złożonych zapytaniach opartych na magazynie danych kierowanych do dużych, magazynowanych zestawów danych. Zapytania Hive w zapytaniu interakcyjnym działają dużo szybciej niż tradycyjne zadania wsadowe Hive. 

> [!NOTE]  
> Uruchamianie interakcyjnych zapytań Hive jest możliwe tylko po nawiązaniu połączenia z klastrem [interakcyjnych zapytań usługi HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Możesz też użyć narzędzi Data Lake Tools for Visual Studio, aby zobaczyć zawartość zadania Hive. Narzędzia Data Lake Tools for Visual Studio zbierają i ujawniają dzienniki Yarn dla wybranych zadań Hive.

Z poziomu Eksploratora serwera, przejdź do **Azure** > **HDInsight** i wybierz klaster.  Są to punkt początkowy w Eksploratorze serwera dla sekcji, aby wykonać.

### <a name="view-hivesampletable"></a>Widok hivesampletable
Wszystkie klastry HDInsight mają domyślne przykładową tabelę programu Hive o nazwie `hivesampletable`.  

W klastrze, przejdź do **bazy danych programu Hive** > **domyślne** > **hivesampletable**.

* Aby wyświetlić `hivesampletable` schematu:  
Rozwiń **hivesampletable**.

* Aby wyświetlić `hivesampletable` danych:  
Kliknij prawym przyciskiem myszy **hivesampletable**i wybierz **Wyświetl pierwszych 100 wierszy**.  Jest to równoważne uruchomieniu następującego zapytania programu Hive za pomocą sterownika ODBC programu Hive:

   `SELECT * FROM hivesampletable LIMIT 100`

  Liczbę wierszy można dostosować.

  ![Zrzut ekranu zapytania schematu HDInsight Hive Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Wyniki zapytania programu Hive")

### <a name="create-hive-tables"></a>Tworzenie tabel programu Hive
Do utworzenia tabeli programu Hive można użyć graficznego interfejsu użytkownika lub zapytań programu Hive. Aby uzyskać informacje o używaniu zapytań programu Hive, zobacz [uruchamianie Apache zapytań Hive](#run.queries).

1. W klastrze, przejdź do **bazy danych programu Hive** > **domyślne**.

2. Kliknij prawym przyciskiem myszy **domyślne**i wybierz **Create Table**.

3. Skonfiguruj tabelę zgodnie z potrzebami.  

4. Wybierz przycisk **Utwórz tabelę**, aby przesłać zadanie i utworzyć nową tabelę programu Hive.

    ![Zrzut ekranu przedstawiający okno tworzenia tabeli w narzędziach HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Tworzenie tabeli programu Hive")

### <a name="run.queries"></a>Tworzenie i uruchamianie zapytań Hive
Masz dwie opcje umożliwiające utworzenie i uruchomienie zapytań Hive:

* Tworzenie zapytań ad hoc
* Tworzenie aplikacji Hive

Aby utworzyć i uruchamianie zapytań ad hoc:

1. Kliknij prawym przyciskiem myszy klaster, w którym chcesz uruchomić zapytanie, a następnie wybierz pozycję **Napisz zapytanie Hive**.  

2. Wprowadź następujące zapytanie programu Hive:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Edytor Hive obsługuje funkcję IntelliSense. Narzędzia Data Lake Tools for Visual Studio obsługują ładowanie zdalnych metadanych podczas edycji skryptu Hive. Na przykład, jeśli wpiszesz `SELECT * FROM`, funkcja IntelliSense wyświetla wszystkie sugerowane nazwy tabeli. Po określeniu nazwy tabeli funkcja IntelliSense wyświetla nazwy kolumn. Narzędzia obsługują większość instrukcji DML programu Hive, podzapytań i wbudowanych sterowników UDF.

    ![Zrzut ekranu przykładu 1 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![Zrzut ekranu przykładu 2 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > Funkcja IntelliSense zasugeruje tylko metadane klastra zaznaczonego na pasku narzędzi usługi HDInsight.

3. Wybierz tryb wykonania:

    * **Interaktywne**  

      Upewnij się, **Interactive** jest zaznaczone, a następnie wybierz pozycję **Execute**.

      ![Zrzut ekranu zapytania i wykonywanie](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      Upewnij się, **partii** jest zaznaczone, a następnie wybierz pozycję **przesyłania**.  W przypadku wybrania zaawansowanych opcji przesyłania, skonfigurować **Nazwa zadania**, **argumenty**, **dodatkowe konfiguracje**, i **katalog stanu**skryptu.

      ![Zrzut ekranu przedstawiający zapytań i przetwarzania wsadowego](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Zrzut ekranu zapytania programu Hive w usłudze HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Przesłanie zapytań")

      > [!NOTE]  
      > Nie można przesłać partii z klastrami interakcyjnych zapytań.  Należy użyć trybu interakcyjnego.

Tworzenie i uruchamianie rozwiązania Hive:

1. Na pasku menu, przejdź do **pliku** > **New** > **projektu...** .

2. W okienku po lewej stronie przejdź do **zainstalowane** > **usługi Azure Data Lake** > **HIVE (HDInsight)** .  

3. W środkowym okienku wybierz opcję **Aplikacja Hive**. Wprowadź właściwości, a następnie wybierz opcję **OK**.

    ![Zrzut ekranu nowego projektu programu Hive w narzędziach HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Tworzenie aplikacji Hive z programu Visual Studio")

4. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik **Script.hql**, aby otworzyć skrypt.

### <a name="view-job-summary-and-output"></a>Wyświetl podsumowanie zadania i dane wyjściowe

Podsumowanie zadania różni się nieco między sobą **partii** i **Interactive** trybu.

![Podsumowanie zadania](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Podsumowanie zadania Hive")

Użyj **Odśwież** przycisk, aby zaktualizować stan, aż stan zadania zmieni się na **Zakończono**.  

* Szczegóły zadania z **partii** trybie wybierz linki na dole, aby wyświetlić **zapytanie dotyczące zadań**, **dane wyjściowe zadania**, **dziennik zadań**, lub **Dziennik usługi yarn**.

* Szczegóły zadania z **Interactive** tryb, kartach zobacz **dane wyjściowe** i **wyjście usługi HiveServer2**.

  ![Szczegóły zadania](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "szczegółów zadania Hive")

### <a name="view-job-graph"></a>Wyświetl wykres zadania

Wykresy zadań są obecnie wyświetlane tylko dla zadań Hive, które używają Tez jako aparatu wykonywania.  Aby uzyskać informacje na temat włączania aplikacji Tez, zobacz [używanie Apache Hive w HDInsight](hdinsight-use-hive.md).  Zobacz też [Użyj Apache Tez zamiast Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Aby zobaczyć wszystkie operatory wewnątrz wierzchołka, dwukrotnie kliknij wierzchołki grafu zadania. Możesz też wskazać konkretny operator, aby wyświetlić więcej jego szczegółów.

Wykres zadania nie może występować, nawet jeżeli określono Tez jako aparatu wykonywania, jeśli żadna aplikacja Tez nie jest uruchamiany.  Może się to zdarzyć, ponieważ zadanie nie zawiera instrukcji DML lub instrukcje DML mogą powrócić bez uruchamiania aplikacji Tez. Na przykład `SELECT * FROM table1` nie spowoduje uruchomienia aplikacji Tez.

![Wykres zadania](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Podsumowanie zadania Hive")


### <a name="task-execution-detail"></a>Szczegóły wykonywania zadania

Z wykresu zadania, można wybrać **szczegóły wykonywania zadania** można pobrać strukturalnych i wizualizowanych informacji na temat zadań Hive. Możesz też uzyskać więcej szczegółów zadania. Jeśli wystąpią problemy z wydajnością, możesz użyć widoku, aby uzyskać więcej szczegółów dotyczących problemu. Na przykład możesz uzyskać informacje o sposobie działania każdego zadania oraz szczegółowe informacje o każdym zadaniu (odczyt/zapis danych, czas zaplanowany/rozpoczęcia/zakończenia itp.). Skorzystaj z informacji, aby dostosować konfiguracje zadań lub architekturę systemu w oparciu o wizualizowane informacje.

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


## <a name="run-apache-pig-scripts"></a>Uruchamianie skryptów Apache Pig

1. Na pasku menu, przejdź do **pliku** > **New** > **projektu...** .

2. W okienku po lewej stronie przejdź do **zainstalowane** > **usługi Azure Data Lake** > **Pig (HDInsight)** .  

3. W środkowym okienku wybierz **aplikacji Pig**. Wprowadź właściwości, a następnie wybierz opcję **OK**.

4. W **Eksploratora rozwiązań**, kliknij dwukrotnie **Script.pig** aby otworzyć skrypt.

## <a name="feedback-and-known-issues"></a>Opinie i znane problemy
* Problem, w którym wyniki uruchamiane z wartościami null nie są wyświetlane jako rozwiązane. W przypadku zablokowania na tym problemie należy skontaktować się z zespołem pomocy technicznej.
* Skrypt HQL utworzony przez program Visual Studio jest zakodowany w zależności od ustawienia lokalnego regionu użytkownika. Skrypt nie jest wykonywany poprawnie, jeśli został przesłany do klastra jako plik binarny.

## <a name="next-steps"></a>Kolejne kroki
Ten artykuł zawierał informacje dotyczące sposobu używania pakietu Data Lake Tools for Visual Studio w celu nawiązywania połączenia z klastrami usługi HDInsight z programu Visual Studio. Przedstawiono też sposób uruchamiania zapytania Hive. Więcej informacji można znaleźć w tych artykułach:

* [Uruchamianie zapytania usługi Apache Hive przy użyciu narzędzi Data Lake tools for Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Use Hadoop Hive in HDInsight](hdinsight-use-hive.md) (Używanie usługi Hadoop Hive w usłudze HDInsight)
* [Rozpoczynanie pracy przy użyciu technologii Apache Hadoop w HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Przesyłanie zadań usługi Apache Hadoop w HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizowanie danych serwisu Twitter przy użyciu technologii Apache Hadoop w HDInsight](../hdinsight-analyze-twitter-data.md)

