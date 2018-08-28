---
title: Narzędzia platformy Azure HDInsight — Użyj programu Visual Studio Code dla programu Hive, LLAP lub pySpark
description: Dowiedz się, jak używać narzędzi HDInsight systemu Azure dla programu Visual Studio Code do tworzenia i przesyłania zapytań i skryptów.
keywords: Program VS Code, narzędzia Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interakcyjnych zapytań
services: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: b66658bde818e2b4cce2cbc0dc5b419ba33dc737
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43108832"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Użyj usługi Azure HDInsight Tools for Visual Studio Code

Dowiedz się, jak używać narzędzi HDInsight systemu Azure dla programu Visual Studio Code (VS Code) do tworzenia i przesyłania zadań wsadowych Hive, interaktywne zapytania programu Hive oraz skrypty pySpark. Narzędzia HDInsight systemu Azure można zainstalować na platformach, które są obsługiwane przez program VS Code. Są to systemy Windows, Linux i macOS. Wymagania wstępne dla różnych platform można znaleźć.


## <a name="prerequisites"></a>Wymagania wstępne

Wymagane do ukończenia kroków opisanych w tym artykule są następujące elementy:

- Klaster HDInsight. Aby utworzyć klaster, zobacz [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Program Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)
- [Narzędzie mono](http://www.mono-project.com/docs/getting-started/install/). Narzędzie mono jest tylko wymagane dla systemów Linux i macOS.

## <a name="install-the-hdinsight-tools"></a>Instalowanie narzędzi HDInsight
   
Po zainstalowaniu wymagań wstępnych, można zainstalować narzędzi HDInsight systemu Azure dla programu VS Code. 

**Do zainstalowania usługi Azure HDInsight tools**

1. Otwórz program Visual Studio Code.

2. W okienku po lewej stronie wybierz **rozszerzenia**. W polu wyszukiwania wprowadź **HDInsight**.

3. Obok pozycji **narzędzia Azure HDInsight**, wybierz opcję **zainstalować**. Po kilku sekundach **zainstalować** przycisku zmienia się na **Załaduj ponownie**.

4. Wybierz **Załaduj ponownie** aktywować **narzędzia Azure HDInsight** rozszerzenia.

5. Wybierz **Reload Window** o potwierdzenie. Możesz zobaczyć **narzędzia Azure HDInsight** w **rozszerzenia** okienka.

   ![HDInsight dla instalacji programu Visual Studio Code w języku Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Otwórz obszar roboczy HDInsight

Utwórz obszar roboczy w programie VS Code, aby nawiązać połączenie na platformie Azure.

**Aby otworzyć obszar roboczy**

1. Na **pliku** menu, wybierz opcję **Otwórz Folder**. Następnie wyznaczyć istniejącego folderu do folderu roboczego, lub Utwórz nową. Folder pojawia się w okienku po lewej stronie.

2. W okienku po lewej stronie wybierz **nowy plik** ikon umieszczonych obok nazw folderów roboczych.

   ![Nowy plik](./media/hdinsight-for-vscode/new-file.png)

3. Nadaj nowemu plikowi .hql (zapytania programu Hive) lub z rozszerzeniem PY (skryptu platformy Spark). Należy zauważyć, że **XXXX_hdi_settings.json** plik konfiguracji jest automatycznie dodawany do folderu roboczego.

4. Otwórz **XXXX_hdi_settings.json** z **EXPLORER**, lub kliknij prawym przyciskiem myszy skrypt w edytorze wybierz **konfiguracji ustaw**. Można skonfigurować wpis logowania klastra domyślnego i parametry przesyłania zadania, jak pokazano w przykładzie w pliku. Możesz też pozostawić pozostałych parametrów pusty.

## <a name="connect-to-hdinsight-cluster"></a>Nawiąż połączenie z klastrem HDInsight

Przed przesłaniem skryptów w klastrach HDInsight z programu VS Code, musisz nawiązać połączenie z kontem platformy Azure lub połączenia klastra (przy użyciu narzędzia Ambari nazwy użytkownika/hasła lub domeny dołączonych konta).

**Do połączenia z platformą Azure**

1. Utwórz nowy folder roboczy i nowy plik skryptu, jeśli nie masz jeszcze je.

2. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie w menu kontekstowym wybierz **HDInsight: logowania**. Możesz też wprowadzić **Ctrl + Shift + P**, a następnie wprowadź **HDInsight: logowania**.

    ![Zaloguj się do niego narzędzia HDInsight Tools for Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Aby się zarejestrować, postępuj zgodnie z instrukcjami rejestrowania w **dane wyjściowe** okienka.

    **Azure:** ![HDInsight Tools for Visual Studio Code logowania](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Po nawiązaniu połączenia swojej nazwy konta platformy Azure są wyświetlane na pasku stanu w lewym dolnym rogu okna programu VS Code. 

    > [!NOTE]
    > Z powodu problemu z znanych uwierzytelniania platformy Azure należy otworzyć w trybie prywatnym lub w trybie incognito w przeglądarce. Jeśli Twoje konto platformy Azure ma dwa czynniki włączona, zaleca się przy użyciu uwierzytelniania phone zamiast numeru PIN, uwierzytelnianie.
  

4. Kliknij prawym przyciskiem myszy edytora skryptów, aby otworzyć menu kontekstowe. Z menu kontekstowego należy wykonać następujące zadania:

    - Wyloguj się
    - Wyświetlanie listy klastrów
    - Ustaw domyślne klastrów
    - Przesyłanie interaktywnych zapytań Hive
    - Przesyłanie skryptów wsadowych Hive
    - Przesyłanie zapytań interakcyjnych PySpark
    - Przesyłanie skryptów wsadowych PySpark
    - Ustaw konfiguracje

<a id="linkcluster"></a>**Aby połączyć klaster**

Można połączyć normalny klaster przy użyciu nazwy użytkownika systemu Ambari zarządzane, również połączyć klaster hadoop zabezpieczeń przy użyciu nazwy użytkownika domeny (takich jak: user1@contoso.com).
1. Otwórz paletę poleceń, wybierając pozycję **CTRL + SHIFT + P**, a następnie wprowadź **HDInsight: Połącz klaster**.

   ![polecenie klastra łącze](./media/hdinsight-for-vscode/link-cluster-command.png)

2. HDInsight wprowadź adres URL klastra -> wejście-Username > Wprowadź hasło -> Wybierz typ klastra -> on informacje o powodzeniu pokazuje jeśli przekazany weryfikacji.
   
   ![okno dialogowe klastra łącza](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Połączone nazwę użytkownika i hasło są używane, jeśli klaster w subskrypcji platformy Azure i połączone w klaster. 
   
3. Zobaczysz klastra połączonej za pomocą polecenia **klastra listy**. Możesz teraz przesłać skrypt do tego klastra połączonych.

   ![połączone klastra](./media/hdinsight-for-vscode/linked-cluster.png)

4. Wstawiając może zostać odłączyć klaster **HDInsight: odłączyć klaster** z palety poleceń.

## <a name="list-hdinsight-clusters"></a>Wyświetlanie listy klastrów HDInsight

Aby przetestować połączenie, można wyświetlić listę klastry usługi HDInsight:

**Aby wyświetlić listę klastrów HDInsight w ramach subskrypcji platformy Azure**
1. Otwórz obszar roboczy, a następnie łączenie z platformą Azure. Aby uzyskać więcej informacji, zobacz [HDInsight Otwórz obszar roboczy](#open-hdinsight-workspace) i [Połącz z platformą Azure](#connect-to-azure).

2. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz **HDInsight: lista klastrów** z menu kontekstowego. 

3. Klastry Hive, jak i platformy Spark są wyświetlane w **dane wyjściowe** okienka.

    ![Ustaw domyślną konfigurację klastra](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Ustaw domyślne klastra
1. Otwórz obszar roboczy i łączenie z platformą Azure. Zobacz [HDInsight Otwórz obszar roboczy](#open-hdinsight-workspace) i [Połącz z platformą Azure](#connect-to-azure).

2. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz **HDInsight: Ustawianie domyślnych klastra**. 

3. Wybieranie klastra jako klastra domyślnego dla bieżącego pliku skryptu. Narzędzia automatycznie zaktualizować plik konfiguracji **XXXX_hdi_settings.json**. 

   ![Domyślna konfiguracja klastra zestawu](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Ustaw środowisko platformy Azure 
1. Otwórz paletę poleceń, wybierając pozycję **CTRL + SHIFT + P**.

2. Wprowadź **HDInsight: należy ustawić środowisko Azure**.

3. Wybierz jeden ze sposobów z platformy Azure i AzureChina jako domyślny wpis logowania.

4. W tym samym czasie narzędzie już zapisany domyślny wpis nazwy logowania w **XXXX_hdi_settings.json**. Należy również bezpośrednio zaktualizować go w tym pliku konfiguracji. 

   ![Domyślna konfiguracja wpis logowania zestawu](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Przesyłanie interaktywnych zapytań Hive

Za pomocą narzędzi HDInsight dla programu VS Code możesz przesłać interakcyjnych zapytań Hive z HDInsight klastrami interakcyjnych zapytań.

1. Utwórz nowy folder roboczy i nowy plik skryptu Hive, jeśli jeszcze ich nie masz.

2. Połącz się z kontem platformy Azure, a następnie skonfiguruj klaster domyślny, jeśli nie zostało to jeszcze zrobione.

3. Skopiuj i wklej następujący kod do pliku Hive, a następnie zapisz go.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz pozycję **HDInsight: interakcyjny klaster Hive**, aby przesłać zapytanie. Narzędzia pozwalają również na przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. Wkrótce wyniki zapytań zostają wyświetlone w nowej tabeli.

   ![Wynik interakcyjnego klastra Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **WYNIKI**: cały wynik można zapisać jako plik w formacie CSV, JSON lub Excel w lokalnej ścieżce lub po prostu zaznaczyć wiele wierszy.

    - Panel **KOMUNIKATY**: po wybraniu numeru **wiersza** następuje przejście do pierwszego wiersza uruchomionego skryptu.

Uruchamianie interaktywnego zapytania zajmuje o wiele mniej czasu niż [uruchamianie zadania wsadowego Hive](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Przesyłanie skryptów wsadowych Hive

1. Utwórz nowy folder roboczy i nowy plik skryptu Hive, jeśli jeszcze ich nie masz.

2. Połącz się z kontem platformy Azure, a następnie skonfiguruj klaster domyślny, jeśli nie zostało to jeszcze zrobione.

3. Skopiuj i wklej następujący kod do pliku Hive, a następnie zapisz go.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz pozycję **HDInsight: partia Hive**, aby przesłać zadanie Hive. 

4. Wybierz klaster, do którego chcesz przesłać.  

    Po przesłaniu zadania Hive informacje o powodzeniu przesłania oraz identyfikator zadania zostają wyświetlone w panelu **DANE WYJŚCIOWE**. Zadanie Hive otwiera również **PRZEGLĄDARKĘ SIECI WEB**, w której są wyświetlane w czasie rzeczywistym dzienniki i stan zadań.

   ![przysyłanie wyniku zadania Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[Przesyłanie interaktywnych zapytań Hive](#submit-interactive-hive-queries) zajmuje dużo mniej czasu niż przesyłanie zadania wsadowego.

## <a name="submit-interactive-pyspark-queries"></a>Przesyłanie zapytań interakcyjnych PySpark
Narzędzia HDInsight Tools for VS Code umożliwia również przesyłanie zapytań interakcyjnych PySpark do klastrów platformy Spark.
1. Utwórz nowy folder roboczy i nowy plik skryptu z rozszerzeniem PY, jeśli nie masz jeszcze je.

2. Nawiąż połączenie z kontem platformy Azure, jeśli nie zostało to jeszcze zrobione.

3. Skopiuj i wklej następujący kod do pliku skryptu:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Wyróżnianie tych skryptów. Następnie kliknij prawym przyciskiem myszy w Edytorze skryptów i wybierz **HDInsight: interakcyjnego PySpark**.

5. Jeśli jeszcze nie zainstalowano **Python** rozszerzenia w programie VS Code wybierz **zainstalować** przycisk, jak pokazano na poniższej ilustracji:

    ![HDInsight dla instalacji programu Visual Studio Code w języku Python](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Jeśli jeszcze nie, zainstaluj środowisko Python w systemie. 
   - Dla Windows, Pobierz i zainstaluj [Python](https://www.python.org/downloads/). Następnie upewnij się, że `Python` i `pip` znajdują się w ŚCIEŻCE systemu.

   - Aby uzyskać instrukcje dla systemu macOS i Linux, zobacz [Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Wybierz klaster, do którego należy przesłać zapytanie PySpark. Wkrótce potem wynik zapytania jest wyświetlany w nowej karcie odpowiednie:

   ![Przedstawia wynik zadania języka Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Obsługuje także narzędzie **klauzuli SQL** zapytania.

   ![Przedstawia wynik zadania Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) Stan przesyłania pojawia się po lewej stronie stanu dolny pasek po uruchomieniu zapytania. Nie przesyłaj inne zapytania, jeśli jest w stanie **jądra PySpark (zajęty)**. 

>[!NOTE]
>Klastry można zachować informacje o sesji. Zdefiniowanej zmiennej, funkcji i odpowiadające im wartości są przechowywane w tej sesji, dzięki czemu mogą być przywoływane przez wiele wywołań usługi dla tego samego klastra. 
 

## <a name="submit-pyspark-batch-job"></a>Przesyłanie zadania usługi batch PySpark

1. Utwórz nowy folder roboczy i nowy plik skryptu z rozszerzeniem PY, jeśli nie masz jeszcze je.

2. Nawiąż połączenie z kontem platformy Azure, jeśli jeszcze tego nie zrobiłeś.

3. Skopiuj i wklej następujący kod do pliku skryptu:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz **HDInsight: partii PySpark**. 

5. Wybierz klaster, do którego należy przesłać zadanie PySpark. 

   ![Przedstawia wynik zadania języka Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Po przesłaniu zadania Python przesyłania dzienników pojawia się w **dane wyjściowe** okna w programie VS Code. **Adresu URL interfejsu użytkownika platformy Spark** i **adresu URL interfejsu użytkownika usługi Yarn** są także wyświetlane. Można otworzyć adresu URL w przeglądarce sieci web, aby śledzić stan zadania.

>[!NOTE]
>PySpark3 nie jest już obsługiwany w Livy Update 0.4 (czyli klaster platformy spark 2.2 HDI). Tylko "PySpark" jest obsługiwany dla języka python. Jest znany, problem, który przedstawia do platformy spark 2.2 kończy się niepowodzeniem z środowiska python3 jako.
   
## <a name="livy-configuration"></a>Konfiguracja usługi Livy
Konfiguracja usługi Livy jest obsługiwana, mógł zostać ustawiony w ustawieniach projektu w folderze miejsca pracy. Więcej informacji, zobacz [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

+ Ustawienia projektu:

    ![Konfiguracja usługi Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

+ Obsługiwane konfiguracje programu Livy:   

    **/Batches WPIS**   
    Treść żądania

    | name | description | type | 
    | :- | :- | :- | 
    | plik | Plik zawierający wykonywanie aplikacji | Ścieżka (wymagane) | 
    | proxyUser | Personifikację podczas uruchamiania zadania | ciąg | 
    | className | Główna klasa platformy Java lub Spark aplikacji | ciąg |
    | args | Argumenty wiersza polecenia dla aplikacji | Lista ciągów | 
    | plikach JAR | jars ma być używany w ramach tej sesji | Lista parametrów | 
    | pyFiles | Pliki języka Python do użycia w ramach tej sesji | Lista parametrów |
    | plików | pliki, które zostaną użyte w tej sesji | Lista parametrów |
    | driverMemory | Ilość pamięci na potrzeby procesu sterownika | ciąg |
    | driverCores | Liczba rdzeni do użycia dla procesu sterownika | Int |
    | executorMemory | Ilość pamięci na działaniu proces wykonujący testy | ciąg |
    | executorCores | Liczba rdzeni do użycia dla każdej funkcji wykonawczej | Int |
    | numExecutors | Liczbie funkcji wykonawczych, aby uruchomić dla tej sesji | Int |
    | Archiwa | Archiwa ma być używany w ramach tej sesji | Lista parametrów |
    | kolejka | Nazwa kolejki YARN, do której przesłano | ciąg |
    | name | Nazwa tej sesji | ciąg |
    | potwierdzenie | Właściwości konfiguracji aparatu Spark | Mapa klucza = val |

    Treść odpowiedzi   
    Utworzony obiekt usługi Batch.

    | name | description | type | 
    | :- | :- | :- | 
    | id | Identyfikator sesji | Int | 
    | appId | Identyfikator aplikacji dla tej sesji |  Ciąg |
    | appInfo | Informacje o szczegółowe aplikacji | Mapa klucza = val |
    | Dziennik | Wiersze dziennika | Lista ciągów |
    | state |   Stan usługi batch | ciąg |


## <a name="additional-features"></a>Dodatkowe funkcje

HDInsight dla programu VS Code obsługuje następujące funkcje:

- **Automatyczne uzupełnianie IntelliSense**. Sugestie wyskakujące — słowo kluczowe, metody, zmienne i tak dalej. Różne ikony reprezentują różne typy obiektów.

    ![Narzędzia HDInsight Tools for typy obiektów funkcji IntelliSense kodu Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Znacznik błędów funkcji IntelliSense**. Usługa językowa podkreśla błędy edycji skryptu Hive.     
- **Najważniejsze funkcje składni**. Usługa językowa używa różnych kolorów do odróżnienia zmiennych, słowa kluczowe, typ danych, funkcje i tak dalej. 

    ![Wyróżnia HDInsight Tools for Visual Studio Code składni](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Kolejne kroki

### <a name="demo"></a>Demonstracja
* HDInsight dla programu VS Code: [wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Debugowanie aplikacji Spark zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem protokołu SSH przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Korzystaj z narzędzi HDInsight dla programu IntelliJ z Piaskownicą Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Narzędzia HDInsight znajdujące się w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Hive data with Microsoft Power BI in Azure HDInsight (Wizualizowanie danych programu Hive przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight)](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Uruchamianie zapytań programu Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](spark/apache-spark-job-debugging.md)



