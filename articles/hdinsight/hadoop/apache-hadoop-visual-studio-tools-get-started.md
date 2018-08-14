---
title: Łączenie z usługą Azure HDInsight przy użyciu narzędzi Data Lake Tools for Visual Studio
description: Dowiedz się, jak zainstalować narzędzia Data Lake Tools for Visual Studio oraz korzystać z nich, aby połączyć się z klastrami Hadoop w usłudze Azure HDInsight, a następnie uruchomić zapytania programu Hive.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: get-started-article
ms.date: 05/16/2018
ms.openlocfilehash: 9433c54cdbcc534f12fa78b9750cfdbc10682c51
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599371"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>Łączenie z usługą Azure HDInsight i uruchamianie zapytań programu Hive przy użyciu narzędzi Data Lake Tools for Visual Studio

Dowiedz się, jak korzystać z narzędzi Data Lake Tools for Visual Studio (nazywanych również narzędziami usług Azure Data Lake i Stream Analytics for Visual Studio), aby połączyć się z klastrami Hadoop w usłudze [Azure HDInsight](../hdinsight-hadoop-introduction.md) i przesłać zapytania Hive. 

Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight, zobacz artykuły [Introduction to HDInsight](../hdinsight-hadoop-introduction.md) (Wprowadzenie do usługi HDInsight) i [Get started with HDInsight](apache-hadoop-linux-tutorial-get-started.md) (Rozpoczynanie pracy z usługą HDInsight). 

Aby uzyskać więcej informacji na temat łączenia się z klastrem Storm, zobacz artykuł [Develop C# topologies for Apache Storm on HDInsight using Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md) (Tworzenie topologii C# dla usługi Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio).

Możesz użyć narzędzia Data Lake Tools for Visual Studio do uzyskiwania dostępu do usług Azure Data Lake Analytics i HDInsight. Aby uzyskać informacje na temat narzędzi Data Lake Tools, zobacz [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka i korzystania z narzędzi Data Lake Tools w programie Visual Studio potrzebne są:

* Klaster usługi Azure HDInsight. Aby utworzyć klaster usługi HDInsight, zobacz [Get started using Hadoop in Azure HDInsight (Rozpoczynanie pracy z usługą Hadoop w usłudze Azure HDInsight)](apache-hadoop-linux-tutorial-get-started.md). Aby uruchomić interakcyjne zapytania Hive, potrzebujesz klastra [interakcyjnych zapytań usługi HDInsight](../interactive-query/apache-interactive-query-get-started.md).
* Komputer z zainstalowanym programem Visual Studio 2017, 2015 lub 2013.
    
    > [!NOTE]
    > Obecnie narzędzia Data Lake Tools for Visual Studio są dostępne tylko w angielskiej wersji językowej.
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>Instalowanie lub aktualizowanie narzędzi Data Lake Tools for Visual Studio

### <a name="install-data-lake-tools"></a>Instalowanie narzędzi Data Lake Tools

Narzędzia Data Lake Tools są domyślnie instalowane w programie Visual Studio 2017. W przypadku starszych wersji programu Visual Studio możesz zainstalować narzędzia Data Lake Tools przy użyciu [Instalatora platformy internetowej](https://www.microsoft.com/web/downloads/platform.aspx). Należy wybrać wersję narzędzi Data Lake Tools zgodną z używaną wersją programu Visual Studio. 

### <a name="install-visual-studio"></a>Instalacja programu Visual Studio

Jeśli nie masz zainstalowanego programu Visual Studio, możesz użyć [Instalatora platformy internetowej](https://www.microsoft.com/web/downloads/platform.aspx), aby zainstalować najnowsze wersje programu Visual Studio Community oraz zestawu Azure SDK:

![Zrzut ekranu instalatora platformy internetowej dla narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Korzystanie z instalatora platformy internetowej w celu zainstalowania narzędzi Data Lake Tools for Visual Studio")

### <a name="update-the-tools"></a>Aktualizacja narzędzi

1. Otwórz program Visual Studio.
2. W menu **Narzędzia** wybierz polecenie **Rozszerzenia i aktualizacje**.
3. Rozwiń węzeł **Aktualizacje**, a następnie wybierz **narzędzia usług Azure Data Lake i Stream Analytics** (jeśli są zainstalowane).

> [!NOTE]
>
> Możesz użyć tylko narzędzi Data Lake Tools w wersji 2.3.0.0 lub nowszej, aby nawiązać połączenie z klastrami zapytań interaktywnych oraz uruchamiać zapytania interaktywne usługi Hive.

## <a name="connect-to-azure-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami platformy Azure
Narzędzia Data Lake Tools for Visual Studio umożliwiają nawiązywanie połączenia z klastrami HDInsight, wykonywanie podstawowych operacji zarządzania i uruchamianie zapytań programu Hive.

> [!NOTE]
> Informacje dotyczące nawiązywania połączenia z ogólnym klastrem Hadoop znajdują się w temacie [Write and submit Hive queries using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Zapisywanie i wysyłanie zapytań programu Hive za pomocą programu Visual Studio).
> 
> 

Aby nawiązać połączenie z subskrypcją platformy Azure:

1. Otwórz program Visual Studio.
2. W menu **Widok** wybierz opcję **Eksplorator serwera**.
3. W Eksploratorze serwera rozwiń węzeł **Azure**, a następnie rozwiń pozycję **HDInsight**.
   
   > [!NOTE]
   > Okno **Lista zadań HDInsight** powinno być otwarte. Jeśli nie widzisz okna, w menu **Widok** wybierz pozycję **Inne okna**, a następnie wybierz pozycję **Okno listy zadań usługi HDInsight**.  
   > 
   > 
4. Wprowadź swoje poświadczenia subskrypcji platformy Azure, a następnie wybierz przycisk **Zaloguj**. Uwierzytelnianie jest wymagane tylko wtedy, gdy nigdy wcześniej nie nawiązano połączenia z subskrypcją platformy Azure w programie Visual Studio na danym komputerze.
5. W oknie Eksploratora serwera zostanie wyświetlona lista istniejących klastrów usługi HDInsight. W przypadku braku klastrów możesz go utworzyć za pomocą witryny Azure Portal, programu Azure PowerShell lub zestawu SDK usługi HDInsight. Więcej informacji można znaleźć w artykule [Create HDInsight clusters](../hdinsight-hadoop-provision-linux-clusters.md) (Tworzenie klastrów usługi HDInsight).
   
   ![Zrzut ekranu listy klastrów Eksploratora serwera narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Lista serwerów Eksploratora serwera narzędzi Data Lake Tools for Visual Studio")
6. Rozwiń węzeł klastra usługi HDInsight. Zostaną wyświetlone **Bazy danych programu Hive**, domyślne konto magazynu, połączone konta magazynu i **Dziennik usługi Hadoop**. Jednostki można rozwinąć.

Po nawiązaniu połączenia z subskrypcją platformy Azure można wykonać następujące zadania.

Nawiązywanie połączenia z witryną Azure Portal w programie Visual Studio:

1. W Eksploratorze serwera wybierz kolejno pozycje **Azure** > **HDInsight**.
2. Kliknij prawym przyciskiem myszy klaster usługi HDInsight, a następnie wybierz pozycję **Zarządzaj klastrem w witrynie Azure Portal**.

Zadawanie pytań i wyrażanie opinii w programie Visual Studio:

1. W menu **Narzędzia** wybierz pozycję **HDInsight**.
2. Aby zadać pytania, wybierz pozycję **Forum MSDN**. Aby przekazać opinię, wybierz pozycję **Prześlij opinię**.

## <a name="explore-linked-resources"></a>Eksplorowanie połączonych zasobów
W Eksploratorze serwera można zobaczyć domyślne konto magazynu i wszystkie połączone konta magazynu. Po rozwinięciu domyślnego konta magazynu można wyświetlić kontenery konta magazynu. Domyślne konto magazynu i domyślny kontener są oznaczone. Kliknij prawym przyciskiem myszy dowolny kontener, aby wyświetlić jego zawartość.

![Zrzut ekranu listy połączonych zasobów Eksploratora serwera narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Wyświetlanie listy połączonych zasobów")

Po otwarciu kontenera można używać następujących przycisków do przekazywania, usuwania i pobierania obiektów blob:

![Zrzut ekranu operacji obiektu blob narzędzi Data Lake Tools for Visual Studio w Eksploratorze serwera](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Przekazywanie, usuwanie i pobieranie obiektów blob w Eksploratorze serwera")

## <a name="run-interactive-hive-queries"></a>Uruchamianie interakcyjnych zapytań Hive
[Apache Hive](http://hive.apache.org) to infrastruktura magazynu danych oparta na platformie Hadoop. Usługa Hive jest używana do wykonywania podsumowań danych, zapytań i analizy. Możesz użyć narzędzi Data Lake Tools for Visual Studio, aby uruchomić zapytania usługi Hive z poziomu programu Visual Studio. Aby uzyskać więcej informacji na temat programu Hive, zobacz artykuł [Use Hive with HDInsight](hdinsight-use-hive.md) (Korzystanie z programu Hive z usługą HDInsight).

[Zapytanie interakcyjne](../interactive-query/apache-interactive-query-get-started.md) używa narzędzia [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) w wersji Apache Hive 2.1. Zapytanie interakcyjne zapewnia interakcyjność w złożonych zapytaniach opartych na magazynie danych kierowanych do dużych, magazynowanych zestawów danych. Zapytania Hive w zapytaniu interakcyjnym działają dużo szybciej niż tradycyjne zadania wsadowe Hive. Aby uzyskać więcej informacji, zobacz [Uruchamianie zadań wsadowych Hive](#run-hive-batch-jobs).

> [!NOTE]
>
> Uruchamianie interakcyjnych zapytań Hive jest możliwe tylko po nawiązaniu połączenia z klastrem [interakcyjnych zapytań usługi HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Możesz też użyć narzędzi Data Lake Tools for Visual Studio, aby zobaczyć zawartość zadania Hive. Narzędzia Data Lake Tools for Visual Studio zbierają i ujawniają dzienniki Yarn dla wybranych zadań Hive.

### <a name="view-hivesampletable"></a>Zobacz tabelę **hivesampletable**
Wszystkie klastry usługi HDInsight zawierają domyślnie przykładową tabelę programu Hive o nazwie hivesampletable. W tej tabeli zdefiniowano, w jaki sposób należy wyświetlać tabele programu Hive, przeglądać schematy tabeli i wyświetlać wiersze tabeli programu Hive.

Aby wyświetlić tabele programu Hive oraz schemat tabeli programu Hive:

1. Aby wyświetlić schemat tabeli, w **Eksploratorze serwera** wybierz kolejno pozycje **Azure** > **HDInsight**. Wybierz klaster, a następnie wybierz pozycję **Bazy danych programu Hive** > **Domyślne** > **hivesampletable**.
2. Kliknij prawym przyciskiem myszy tabelę **hivesampletable**, a następnie kliknij przycisk **Wyświetl pierwszych 100 wierszy**, aby wyświetlić wiersze. Jest to równoważne uruchomieniu następującego zapytania programu Hive za pomocą sterownika ODBC programu Hive:
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   Liczbę wierszy można dostosować.
   
   ![Zrzut ekranu zapytania schematu HDInsight Hive Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Wyniki zapytania programu Hive")

### <a name="create-hive-tables"></a>Tworzenie tabel programu Hive
Do utworzenia tabeli programu Hive można użyć graficznego interfejsu użytkownika lub zapytań programu Hive. Aby uzyskać informacje o używaniu zapytań programu Hive, zobacz temat [Uruchamianie zapytań Hive](#run.queries).

Aby utworzyć tabelę programu Hive:

1. W **Eksploratorze serwera** wybierz kolejno pozycje **Azure** > **Klastry usługi HDInsight**. Wybierz klaster usługi HDInsight, a następnie wybierz pozycję **Bazy danych programu Hive**.
2. Kliknij prawym przyciskiem myszy pozycje **domyślna**, a następnie wybierz pozycję **Utwórz tabelę**.
3. Skonfiguruj tabelę.  
4. Wybierz przycisk **Utwórz tabelę**, aby przesłać zadanie i utworzyć nową tabelę programu Hive.
   
    ![Zrzut ekranu przedstawiający okno tworzenia tabeli w narzędziach HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Tworzenie tabeli programu Hive")

### <a name="run.queries"></a>Weryfikowanie i uruchamianie zapytań Hive
Masz dwie opcje umożliwiające utworzenie i uruchomienie zapytań Hive:

* Tworzenie zapytań ad hoc
* Tworzenie aplikacji Hive

Tworzenie, weryfikowanie i uruchamianie zapytań ad hoc:

1. W **Eksploratorze serwera** wybierz kolejno pozycje **Azure** > **Klastry usługi HDInsight**.
2. Kliknij prawym przyciskiem myszy klaster, w którym chcesz uruchomić zapytanie, a następnie wybierz przycisk **Napisz zapytanie Hive**.  
3. Wprowadź zapytania Hive. 

    Edytor Hive obsługuje funkcję IntelliSense. Narzędzia Data Lake Tools for Visual Studio obsługują ładowanie zdalnych metadanych podczas edycji skryptu Hive. Na przykład po wpisaniu **SELECT * FROM** funkcja IntelliSense wyświetla wszystkie sugerowane nazwy tabel. Po określeniu nazwy tabeli funkcja IntelliSense wyświetla nazwy kolumn. Narzędzia obsługują większość instrukcji DML programu Hive, podzapytań i wbudowanych sterowników UDF.
   
    ![Zrzut ekranu przykładu 1 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")
   
    ![Zrzut ekranu przykładu 2 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")
   
   > [!NOTE]
   > Funkcja IntelliSense zasugeruje tylko metadane klastra zaznaczonego na pasku narzędzi usługi HDInsight.
   > 
   
4. (Opcjonalnie) Wybierz pozycję **Weryfikuj skrypt**, aby sprawdzić składnię skryptu pod kątem błędów.
   
    ![Zrzut ekranu lokalnej weryfikacji narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "Weryfikacja skryptu")
5. Wybierz pozycję **Prześlij** lub **Prześlij (zaawansowane)**. W przypadku wybrania zaawansowanych opcji przesyłania należy skonfigurować dla skryptu wartości takie jak **Nazwa zadania**, **Argumenty**, **Dodatkowe konfiguracje** i **Katalog stanu**:
   
    ![Zrzut ekranu zapytania programu Hive w usłudze HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Przesłanie zapytań")
   
    Po przesłaniu zadania zostanie wyświetlone okno **Podsumowanie zadania Hive**.
   
    ![Zrzut ekranu podsumowania zapytania programu Hive w usłudze HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Podsumowanie zadania Hive")
6. Użyj przycisku **Odśwież**, aby aktualizować stan do momentu wartości **Ukończono** stanu zadania.
7. Wybierz linki na dole, aby wyświetlić następujące wartości: **Zapytanie dotyczące zadań**, **Dane wyjściowe zadań**, **Dziennik zadań** i **Dziennik Yarn**.

Tworzenie i uruchamianie rozwiązania Hive:

1. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
2. W lewym okienku wybierz opcję **HDInsight**. W środkowym okienku wybierz opcję **Aplikacja Hive**. Wprowadź właściwości, a następnie wybierz opcję **OK**.
   
    ![Zrzut ekranu nowego projektu programu Hive w narzędziach HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Tworzenie aplikacji Hive z programu Visual Studio")
3. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik **Script.hql**, aby otworzyć skrypt.
4. Aby sprawdzić poprawność skryptu Hive, wybierz przycisk **Zweryfikuj skrypt**. Możesz też kliknąć prawym przyciskiem myszy skrypt w edytorze Hive, a następnie wybrać opcję **Zweryfikuj skrypt** z menu kontekstowego.

### <a name="view-hive-jobs"></a>Wyświetlanie zadań Hive
Istnieje możliwość wyświetlenia zapytań dotyczących zadań, danych wyjściowych zadań, dzienników zadań oraz dzienników Yarn dla zadań Hive. Aby uzyskać więcej informacji, zobacz poprzedni zrzut ekranu.

Za pomocą najnowszej wersji narzędzi można sprawdzić zawartość zadań Hive, zbierając i udostępniając dzienniki Yarn. Dziennik Yarn może być pomocny w badaniu problemów z wydajnością. Więcej informacji na temat sposobu zbierania dzienników Yarn przez usługę HDInsight można znaleźć w artykule [Access HDInsight Application Logs Programmatically (Programowe uzyskiwanie dostępu do dzienników aplikacji usługi HDInsight)](../hdinsight-hadoop-access-yarn-app-logs.md).

Aby wyświetlić zadania Hive:

1. W **Eksploratorze serwera** rozwiń węzeł **Azure**, a następnie rozwiń pozycję **HDInsight**.
2. Kliknij prawym przyciskiem myszy klaster usługi HDInsight, a następnie wybierz przycisk **Wyświetl zadania**. Zostanie wyświetlona lista zadań Hive uruchomionych w klastrze.  
3. Wybierz zadanie. W oknie **Podsumowanie zadania Hive** wybierz jedną z następujących opcji:
    - **Zapytanie zadania**
    - **Dane wyjściowe zadania**
    - **Dziennik zadań**  
    - **Dziennik usługi Yarn**
   
    ![Zrzut ekranu przedstawiający okno zadań Hive widoku narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Wyświetlanie zadań Hive")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Szybsza ścieżka wykonywania zadań Hive za pośrednictwem serwera HiveServer2
> [!NOTE]
> Ta funkcja działa tylko w klastrze w usłudze HDInsight w wersji 3.2 lub nowszej.
 
Narzędzia Data Lake Tools for Visual Studio używane do przesyłania zadań Hive za pośrednictwem usługi [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (znanej także jako Templeton). Zwrócenie szczegółów zadania i informacji o błędzie zajęło dużo czasu w tej metodzie przesyłania zadań Hive.

Aby rozwiązać ten problem z wydajnością, narzędzia Data Lake Tools for Visual Studio mogą pominąć użycie protokołu RDP/SSH i wykonać zadania Hive bezpośrednio w klastrze za pośrednictwem serwera HiveServer2.

Oprócz korzystania z lepszej wydajności w tej metodzie użytkownicy mogą także wyświetlać na wykresach Apache Tez zadania Hive oraz szczegóły zadania.

W przypadku klastra HDInsight w wersji 3.2 lub nowszej widoczny jest przycisk **Wykonaj na serwerze HiveServer2**:

![Zrzut ekranu przedstawiający opcję Wykonaj na serwerze HiveServer2 narzędzi Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "Wykonywanie zapytań Hive przy użyciu serwera HiveServer2")

Możesz też zobaczyć dzienniki przesyłane strumieniowo z powrotem w czasie rzeczywistym. Ponadto możliwe jest wyświetlenie wykresów zadań, jeśli zapytanie Hive zostało wykonane w aplikacji Tez.

![Zrzut ekranu przedstawiający szybszą ścieżkę wykonania zapytań Hive przy użyciu serwera HiveServer2 w narzędziach Data Lake Tools for Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Wyświetlanie wykresów zadań")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>Wykonywanie zapytań za pośrednictwem serwera HiveServer2 w porównaniu do przesyłania zapytań przy użyciu usługi WebHCat

Mimo że wykonywanie zapytań za pośrednictwem serwera HiveServer2 ma wiele zalet dotyczących wydajności, ta metoda ma również kilka ograniczeń. Niektóre ograniczenia sprawiają, że ta metoda nie jest odpowiednia w środowisku produkcyjnym. 

Poniższa tabela zawiera różnice między wykonywaniem zapytań za pomocą serwera HiveServer2 i wysyłaniem zapytań przy użyciu usługi WebHCat:

|  | Wykonywanie zapytań za pomocą serwera HiveServer2 | Wysyłanie zapytań przy użyciu usługi WebHCat |
| --- | --- | --- |
| Wykonywanie zapytań |Eliminuje obciążenie w usłudze WebHCat (które powoduje uruchomienie zadania MapReduce o nazwie TempletonControllerJob). |Jeśli zapytanie jest wykonywane przy użyciu usługi WebHCat, spowoduje to uruchomienie zadania MapReduce, które wprowadza dodatkowe opóźnienie. |
| Strumieniowe przesyłanie dzienników z powrotem |Prawie w czasie rzeczywistym. |Dzienniki wykonywania zadania są dostępne tylko po zakończeniu zadania. |
| Wyświetlanie historii zadań |Jeśli zapytanie jest wykonywane przy użyciu serwera HiveServer2, jego historia zadania, dziennik zadania ani dane wyjściowe zadania nie są zachowywane. Możesz wyświetlić aplikację w interfejsie użytkownika Yarn z ograniczonymi informacjami. |Jeśli zapytanie jest wykonywane przy użyciu usługi WebHCat, jego historia zadania, dziennik zadania oraz dane wyjściowe zadania zostaną zachowane. Możesz wyświetlić historię zadania przy użyciu programu Visual Studio, zestawu HDInsight SDK lub programu PowerShell. |
| Zamykanie okna |Wykonywanie za pośrednictwem serwera HiveServer2 jest *synchroniczne*. Jeśli okna zostaną zamknięte, wykonanie zapytania zostanie anulowane. |Przesyłanie przy użyciu usługi WebHCat jest *asynchroniczne*. Możesz przesłać zapytanie przy użyciu usługi WebHCat, a następnie zamknąć program Visual Tudio. Można w dowolnym momencie wrócić i wyświetlić wyniki. |

### <a name="tez-hive-job-performance-graph"></a>Wykres wydajności zadania Hive w aplikacji Tez
W narzędziach Data Lake Tools for Visual Studio możesz zobaczyć wykresy wydajności dla zadań Hive uruchamianych przez aparat wykonywania platformy Tez. Aby uzyskać informacje na temat włączania aplikacji Tez, zobacz artykuł [Use Hive in HDInsight](hdinsight-use-hive.md) (Używanie programu Hive w usłudze HDInsight). 

Po przesłaniu zadania Hive w programie Visual Studio program ten wyświetla wykres po zakończeniu zadania. Może być konieczne wybranie przycisku **Odśwież** w celu wyświetlenia najnowszego stanu zadania.

> [!NOTE]
> Ta funkcja jest dostępna tylko w klastrze w usłudze HDInsight w wersji 3.2.4.593 lub nowszej. Ta funkcja działa tylko wobec zakończonych zadań. Ponadto musisz przesłać zadanie przy użyciu usługi WebHCat, aby użyć tej funkcji. Poniższy obraz zostanie wyświetlony w przypadku wykonywania zapytania za pośrednictwem serwera HiveServer2: 
> 
> ![Zrzut ekranu przedstawiający wykres wydajności aplikacji Tez w usłudze Hadoop Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "Stan zadania")

Aby umożliwić użytkownikom lepsze rozumienie zapytania Hive, w tej wersji narzędzia dodano widok operatorów Hive. Aby zobaczyć wszystkie operatory wewnątrz wierzchołka, dwukrotnie kliknij wierzchołki grafu zadania. Możesz też wskazać konkretny operator, aby wyświetlić więcej jego szczegółów.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Widok wykonywania zadania dla zadań Hive w aplikacji Tez
Widok wykonywania zadania dla zadań Hive w aplikacji Tez może służyć do uzyskiwania strukturalnych i wizualizowanych informacji na temat zadań Hive. Możesz też uzyskać więcej szczegółów zadania. Jeśli wystąpią problemy z wydajnością, możesz użyć widoku, aby uzyskać więcej szczegółów dotyczących problemu. Na przykład możesz uzyskać informacje o sposobie działania każdego zadania oraz szczegółowe informacje o każdym zadaniu (odczyt/zapis danych, czas zaplanowany/rozpoczęcia/zakończenia itp.). Skorzystaj z informacji, aby dostosować konfiguracje zadań lub architekturę systemu w oparciu o wizualizowane informacje.

![Zrzut ekranu przedstawiający okno Widok wykonywania zadań narzędzi Data Lake Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Widok wykonywania zadań")

## <a name="run-hive-batch-jobs"></a>Uruchamianie zadań wsadowych Hive
Testowanie skryptu Hive na klastrze usługi HDInsight może być czasochłonne (nie dotyczy to klastra zapytania interakcyjnego). Proces może potrwać kilka minut lub dłużej. Narzędzia Data Lake Tools for Visual Studio mogą sprawdzić poprawność skryptu Hive lokalnie bez konieczności nawiązywania połączenia z działającym klastrem. Aby uzyskać więcej informacji na temat uruchamiania interakcyjnych zapytań, zobacz [Uruchamianie interakcyjnych zapytań Hive](#run-interactive-hive-queries).

Przy użyciu narzędzi Data Lake Tools for Visual Studio możesz zobaczyć zawartość zadania Hive, zbierając i udostępniając dzienniki Yarn wybranych zadań Hive.

Aby dowiedzieć się więcej o uruchamianiu zadań wsadowych Hive, zobacz [Uruchamianie interakcyjnych zapytań Hive](#run-interactive-hive-queries). Informacje przedstawione w tej sekcji dotyczą uruchamiania zadań wsadowych Hive o dłuższym czasie uruchamiania.

## <a name="run-pig-scripts"></a>Uruchamianie skryptów usługi Pig
Możesz użyć narzędzi Data Lake Tools for Visual Studio, aby utworzyć i przesłać skrypty usługi Pig do klastrów usługi HDInsight. Najpierw utwórz projekt usługi Pig na podstawie szablonu. Następnie prześlij skrypt do klastrów usługi HDInsight.

## <a name="feedback-and-known-issues"></a>Opinie i znane problemy
* Obecnie wyniki serwera HiveServer2 są wyświetlane w postaci czystego tekstu, co nie jest idealnym rozwiązaniem. Firma Microsoft pracuje nad rozwiązaniem tego problemu.
* Problem, w którym wyniki uruchamiane z wartościami null nie są wyświetlane jako rozwiązane. W przypadku zablokowania na tym problemie należy skontaktować się z zespołem pomocy technicznej.
* Skrypt HQL utworzony przez program Visual Studio jest zakodowany w zależności od ustawienia lokalnego regionu użytkownika. Skrypt nie jest wykonywany poprawnie, jeśli został przesłany do klastra jako plik binarny.

## <a name="next-steps"></a>Następne kroki
Ten artykuł zawierał informacje dotyczące sposobu używania pakietu Data Lake Tools for Visual Studio w celu nawiązywania połączenia z klastrami usługi HDInsight z programu Visual Studio. Przedstawiono też sposób uruchamiania zapytania Hive. Więcej informacji można znaleźć w tych artykułach:

* [Use Hadoop Hive in HDInsight](hdinsight-use-hive.md) (Używanie usługi Hadoop Hive w usłudze HDInsight)
* [Rozpoczęcie korzystania z usługi Hadoop w usłudze HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Przesyłanie zadań Hadoop w usłudze HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizowanie danych z serwisu Twitter na platformie Hadoop w usłudze HDInsight](../hdinsight-analyze-twitter-data.md)

