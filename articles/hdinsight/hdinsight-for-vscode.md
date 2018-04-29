---
title: Narzędzia HDInsight Azure - Użyj Visual Studio Code Hive, LLAP lub pySpark | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą narzędzi HDInsight Azure dla programu Visual Studio Code tworzenie i przesyłanie zapytań i skryptów.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: jgao
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: e8dc802d67b4cd2e38ab195b771ceeaa07876e58
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Użyj narzędzia Azure HDInsight dla kodu programu Visual Studio

Dowiedz się, jak za pomocą narzędzi HDInsight Azure dla programu Visual Studio (kod VS) do tworzenia i przesyłania zadań Hive, interakcyjnych zapytań Hive i skryptów pySpark. Narzędzia HDInsight Azure można zainstalować na platformach obsługiwanych przez kod programu VS. Obejmują one systemu Windows, Linux i macOS. Wymagania wstępne dla różnych platform można znaleźć.


## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane dla wykonaniu kroków w tym artykule:

- Klaster usługi HDInsight. Aby utworzyć klaster, zobacz [Rozpoczynanie pracy z usługą HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Program Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono jest tylko wymagane dla systemów Linux i macOS.

## <a name="install-the-hdinsight-tools"></a>Instalowanie narzędzi HDInsight Tools
   
Po zainstalowaniu wymagań wstępnych, można zainstalować narzędzi HDInsight Azure dla kodu programu VS. 

**Aby zainstalować usługi Azure HDInsight tools**

1. Otwórz program Visual Studio Code.

2. W okienku po lewej stronie wybierz **rozszerzenia**. W polu wyszukiwania wprowadź **HDInsight**.

3. Obok pozycji **narzędzia Azure HDInsight**, wybierz pozycję **zainstalować**. Po kilku sekundach **zainstalować** przycisku zmienia się na **Załaduj ponownie**.

4. Wybierz **Załaduj ponownie** aktywować **narzędzia Azure HDInsight** rozszerzenia.

5. Wybierz **ponownego załadowania okna** o potwierdzenie. Widać **narzędzia Azure HDInsight** w **rozszerzenia** okienka.

   ![HDInsight dla instalacji programu Visual Studio kodu Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Otwórz obszar roboczy usługi HDInsight

Tworzenie obszaru roboczego w kodzie VS przed podłączeniem do platformy Azure.

**Aby otworzyć obszar roboczy**

1. Na **pliku** menu, wybierz opcję **Otwórz Folder**. Następnie wybierz istniejący folder jako folder roboczy lub Utwórz nową. Folder zostanie wyświetlony w okienku po lewej stronie.

2. W lewym okienku wybierz **nowy plik** ikona obok folderu roboczego.

   ![Nowy plik](./media/hdinsight-for-vscode/new-file.png)

3. Nazwa nowego pliku .hql (zapytań programu Hive) lub rozszerzenie pliku .py (Spark skryptu). Zwróć uwagę, że **XXXX_hdi_settings.json** plik konfiguracji jest automatycznie dodawany do folderu roboczego.

4. Otwórz **XXXX_hdi_settings.json** z **EXPLORER**, lub kliknij prawym przyciskiem myszy edytora skryptów, aby wybrać **ustawić konfiguracji**. Można skonfigurować zapisu logowania, klastra domyślnego i parametry przesyłania zadania, jak pokazano w przykładzie w pliku. Można także pozostawić pozostałe parametry puste.

## <a name="connect-to-hdinsight-cluster"></a>Łączenie z klastrem usługi HDInsight

Przed skryptów można przesłać do klastrów usługi HDInsight w kodzie VS, należy połączyć z kontem platformy Azure albo link klastra (przy użyciu Ambari nazwy użytkownika i hasła lub domeny połączone konta).

**Do nawiązania połączenia platformy Azure**

1. Utwórz nowy folder roboczy i nowy plik skryptu, jeśli nie masz jeszcze je.

2. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie w menu kontekstowym wybierz **HDInsight: logowania**. Możesz też wprowadzić **Ctrl + Shift + P**, a następnie wprowadź **HDInsight: logowania**.

    ![Narzędzia HDInsight Tools for Visual Studio Code Zaloguj](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Aby się zarejestrować, postępuj zgodnie z instrukcjami rejestrowania w **dane wyjściowe** okienka.

    **Azure:** ![narzędzia HDInsight Tools for Visual Studio Code informacje dotyczące logowania](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Po nawiązaniu połączenia nazwa konta platformy Azure jest wyświetlany na pasku stanu, w lewym dolnym rogu okna programu VS kodu. 

    > [!NOTE]
    > Z powodu problemu z znane uwierzytelniania platformy Azure należy otworzyć przeglądarkę w trybie prywatnych lub incognito. Jeśli konta platformy Azure ma dwa składniki włączone, zaleca się przy użyciu uwierzytelniania phone zamiast numeru PIN, uwierzytelnianie.
  

4. Kliknij prawym przyciskiem myszy edytora skryptów, aby otworzyć menu kontekstowego. Z menu kontekstowego można wykonywać następujące zadania:

    - Wyloguj się
    - Lista klastrów
    - Ustaw domyślny klastrów
    - Wysyłanie zapytań programu Hive interakcyjne
    - Przesyłanie skryptów usługi partia zadań Hive
    - Przedstawia interakcyjnych zapytań PySpark
    - Przedstawia PySpark partii skryptów
    - Ustaw konfiguracje

<a id="linkcluster"></a>**Aby połączyć klastra**

Można połączyć normalne klastra przy użyciu Ambari zarządzane username, także połączyć zabezpieczeń klastra usługi hadoop przy użyciu nazwy użytkownika domeny (takich jak: user1@contoso.com).
1. Otwórz palety polecenia, wybierając **CTRL + SHIFT + P**, a następnie wprowadź **HDInsight: Link klastra**.

   ![polecenie klastra łącze](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Wprowadź HDInsight wprowadzania nazwy użytkownika -> -> adres URL klastra wprowadzania hasła -> Wybierz typ klastra -> go pokazuje Powodzenie informacji po weryfikacji.
   
   ![okno dialogowe klastra łącza](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Nazwa połączonego użytkownika i hasło są używane, jeśli klaster rejestrowane w subskrypcji platformy Azure i połączone klastra. 
   
3. Widać klastra połączone za pomocą polecenia **klaster listą**. Teraz możesz przesłać skrypt do tego klastra połączony.

   ![połączone klastra](./media/hdinsight-for-vscode/linked-cluster.png)

4. Również można odłączyć klastra przez wprowadzanie **HDInsight: odłączyć klastra** z palety polecenia.

## <a name="list-hdinsight-clusters"></a>Lista klastrów usługi HDInsight

Aby przetestować połączenie, można wyświetlić listę z klastrami HDInsight:

**Aby wyświetlić listę klastrów usługi HDInsight w ramach Twojej subskrypcji platformy Azure**
1. Otwórz obszar roboczy, a następnie podłącz do platformy Azure. Aby uzyskać więcej informacji, zobacz [HDInsight Otwórz obszar roboczy](#open-hdinsight-workspace) i [Connect Azure](#connect-to-azure).

2. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **HDInsight: klaster listą** z menu kontekstowego. 

3. Hive i Spark klastry są wyświetlane w **dane wyjściowe** okienka.

    ![Wartość domyślna konfiguracja klastra](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Ustaw domyślny klastra
1. Otwórz obszar roboczy i połączenia z platformą Azure. Zobacz [HDInsight Otwórz obszar roboczy](#open-hdinsight-workspace) i [Connect Azure](#connect-to-azure).

2. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **HDInsight: Ustaw domyślny klastra**. 

3. Wybierz klaster jako klastra domyślnego dla bieżącego pliku skryptu. Narzędzia automatycznie Aktualizuj plik konfiguracji **XXXX_hdi_settings.json**. 

   ![Domyślna konfiguracja klastra zestawu](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Ustaw środowiska platformy Azure 
1. Otwórz palety polecenia, wybierając **CTRL + SHIFT + P**.

2. Wprowadź **HDInsight: Ustaw środowiska platformy Azure**.

3. Wybierz jedną z metod z platformy Azure i AzureChina jako domyślny wpis nazwy logowania.

4. W tym samym czasie narzędzie ma już zapisane domyślny wpis nazwy logowania w **XXXX_hdi_settings.json**. Można również bezpośrednio zaktualizować go w tym pliku konfiguracji. 

   ![Domyślna konfiguracja wpis logowania zestawu](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Wysyłanie zapytań programu Hive interakcyjne

Z narzędzia HDInsight Tools for VS kod mogą przesyłać interakcyjnych zapytań Hive do interaktywnego zapytania w usłudze hdinsight.

1. Utwórz nowy folder roboczy i nowy plik skryptu Hive, jeśli nie masz jeszcze je.

2. Połącz się z kontem platformy Azure, a następnie skonfiguruj klastra domyślnego, jeśli jeszcze tego nie zrobiono.

3. Skopiuj i wklej następujący kod do pliku programu Hive, a następnie zapisz go.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **HDInsight: Hive interakcyjne** przesłać zapytanie. Narzędzia pozwalają również na przesyłanie bloku kodu zamiast pliku całego skryptu, za pomocą menu kontekstowego. Wkrótce po wyniki zapytania są wyświetlane na nowej karcie.

   ![interakcyjne wynik gałęzi](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **WYNIKI** panel: Zapisz wynik całego jako plik CSV, JSON lub Excel na ścieżkę lokalną lub wystarczy zaznaczyć wiele wierszy.

    - **KOMUNIKATY** panel: po wybraniu **wiersza** numer, jego przechodzi do pierwszego wiersza uruchamianie skryptu.

Interakcyjne kwerendy zajmuje dużo mniej czasu niż [wykonywania zadania wsadowego Hive](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Przesyłanie skryptów usługi partia zadań Hive

1. Utwórz nowy folder roboczy i nowy plik skryptu Hive, jeśli nie masz jeszcze je.

2. Połącz się z kontem platformy Azure, a następnie skonfiguruj klastra domyślnego, jeśli jeszcze tego nie zrobiono.

3. Skopiuj i wklej następujący kod do pliku programu Hive, a następnie zapisz go.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **HDInsight: Hive partii** można przesłać zadania Hive. 

4. Wybierz klaster, do którego chcesz przesłać.  

    Po przesłaniu zadania Hive przesyłanie informacji sukcesu i identyfikatora zadania zostanie wyświetlona w **dane wyjściowe** panelu. Otwiera również zadania Hive **PRZEGLĄDARKI sieci WEB**, który wskazuje z dziennikami zadań w czasie rzeczywistym i stanu.

   ![przedstawia wynik zadania Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[Przesyłanie interakcyjnych zapytań Hive](#submit-interactive-hive-queries) zajmuje dużo mniej czasu niż przesyłania zadania wsadowego.

## <a name="submit-interactive-pyspark-queries"></a>Przedstawia interakcyjnych zapytań PySpark
Narzędzia HDInsight Tools for VS kod umożliwia także przesłać interakcyjnych zapytań PySpark do klastry Spark.
1. Utwórz nowy folder roboczy i nowy plik skryptu z rozszerzeniem PY i, jeśli nie masz jeszcze je.

2. Połącz z kontem platformy Azure, jeśli nie zostało to jeszcze zrobione.

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
4. Zaznacz tych skryptów. Następnie kliknij prawym przyciskiem myszy Edytor skryptów i wybierz **HDInsight: PySpark interakcyjne**.

5. Jeśli jeszcze nie zainstalowano **Python** wybierz rozszerzenie w kodzie VS **zainstalować** przycisku, jak pokazano na poniższej ilustracji:

    ![HDInsight dla instalacji programu Visual Studio kodu Python](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Jeśli nie jest jeszcze należy zainstalować środowisko Python w systemie. 
   - W systemie Windows, Pobierz i zainstaluj [Python](https://www.python.org/downloads/). Następnie upewnij się, `Python` i `pip` znajdują się w systemie ścieżki.

   - Aby uzyskać instrukcje dotyczące macOS i Linux, zobacz [Konfigurowanie PySpark interaktywnego środowiska dla programu Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Wybierz klaster, do którego należy przesłać zapytanie PySpark. Wkrótce po, wynik zapytania jest wyświetlany na nowej karcie prawa:

   ![Przedstawia wynik zadania języka Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Narzędzie obsługuje również **klauzuli SQL** zapytania.

   ![Przedstawia wynik zadania Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) na lewym pasku po uruchomieniu kwerendy stanu dolnej zostanie wyświetlony stan przesyłania. Nie przesyłaj inne zapytania, jeśli jest w stanie **jądra PySpark (zajęty)**. 

>[!NOTE]
>Klastrów można zachować informacje o sesji. Zdefiniowana zmienna, funkcji i odpowiadające im wartości są przechowywane w sesji, więc może być przywoływany w wielu wywołań usługi dla tego samego klastra. 
 

## <a name="submit-pyspark-batch-job"></a>Prześlij zadanie wsadowe PySpark

1. Utwórz nowy folder roboczy i nowy plik skryptu z rozszerzeniem PY i, jeśli nie masz jeszcze je.

2. Połącz z kontem platformy Azure, jeśli jeszcze tego nie zrobiono.

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
4. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **HDInsight: partii PySpark**. 

5. Wybierz klaster, do którego należy przesłać zadanie PySpark. 

   ![Przedstawia wynik zadania języka Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Po przesłaniu zadania Python przesyłanie dzienników pojawia się w **dane wyjściowe** okna w kodzie VS. **URL interfejsu użytkownika Spark** i **URL interfejsu użytkownika Yarn** są również wyświetlane. Adres URL można otworzyć w przeglądarce sieci web, aby śledzić stan zadania.

>[!NOTE]
>PySpark3 nie jest już obsługiwany w Livy 0,4 (czyli HDI klastra spark 2.2). Tylko "PySpark" jest obsługiwana dla języka python. Jest znany, problem, który przedstawia spark 2.2 niepowodzenie python3.
   
## <a name="livy-configuration"></a>Konfiguracja programu Livy
Konfiguracja programu Livy jest obsługiwana, mógł zostać ustawiony w ustawieniach projektu w folderze miejsca pracy. Więcej informacji, zobacz [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

+ Ustawienia projektu:

    ![Konfiguracja programu Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

+ Obsługiwane konfiguracje programu Livy:   

    **POST /batches**   
    Treść żądania

    | name | description | type | 
    | :- | :- | :- | 
    | plik | Plik zawierający aplikację do wykonania | Ścieżka (wymagane) | 
    | proxyUser | Personifikację podczas uruchamiania zadania | ciąg | 
    | className | Klasy głównym aplikacji Java/Spark | ciąg |
    | argumentów | Argumenty wiersza polecenia dla aplikacji | Lista ciągów | 
    | słoików | jars używanego w tej sesji | Listy parametrów | 
    | pyFiles | Pliki języka Python do użycia w tej sesji | Listy parametrów |
    | plików | pliki do użycia w tej sesji | Listy parametrów |
    | driverMemory | Ilość pamięci dla procesu sterownika | ciąg |
    | driverCores | Liczba rdzeni do użycia na potrzeby procesu sterownika | int |
    | executorMemory | Ilość pamięci na działaniu proces wykonujący testy | ciąg |
    | executorCores | Liczba rdzeni do użycia dla każdego modułu wykonującego | int |
    | numExecutors | Liczba modułów, aby uruchomić dla tej sesji | int |
    | Archiwa | Archiwa używanego w tej sesji | Listy parametrów |
    | Kolejki | Nazwa kolejki YARN, do której przesłano | ciąg |
    | name | Nazwa tej sesji | ciąg |
    | conf | Właściwości konfiguracji Spark | Mapa klucza = val |

    Treść odpowiedzi   
    Utworzony obiekt partii.

    | name | description | type | 
    | :- | :- | :- | 
    | id | Identyfikator sesji | int | 
    | appId | Identyfikator sesji |  Ciąg |
    | appInfo | Informacje szczegółowe aplikacji | Mapa klucza = val |
    | Dziennik | Wiersze dziennika | Lista ciągów |
    | state |   Stan usługi partia zadań | ciąg |


## <a name="additional-features"></a>Dodatkowe funkcje

HDInsight dla kodu VS obsługuje następujące funkcje:

- **Funkcja automatycznego uzupełniania IntelliSense**. Sugestie wyskakujące dla słowa kluczowego, metod, zmiennych i tak dalej. Inne ikony reprezentują różne typy obiektów.

    ![Narzędzia HDInsight Tools for Visual Studio kodu IntelliSense typy obiektów](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Znacznik błąd IntelliSense**. Usługa języka podkreśla błędy edycji skryptu Hive.     
- **Najważniejsze funkcje składni**. Usługa języka używa różne kolory do odróżnienia zmienne, słowa kluczowe, typ danych, funkcje i tak dalej. 

    ![Narzędzia HDInsight Tools for Visual Studio Code prezentuje składni](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Kolejne kroki

### <a name="demo"></a>Demonstracja
* HDInsight dla wersji programu VS kodu: [wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Debugowanie aplikacji Spark zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi Azure for IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem usługi SSH przy użyciu zestawu narzędzi Azure for IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Użyj narzędzia HDInsight Tools for IntelliJ z Hortonworks piaskownicy](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Użyj narzędzia HDInsight Tools w zestawie narzędzi Azure dla programu Eclipse tworzenie aplikacji Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Hive data with Microsoft Power BI in Azure HDInsight (Wizualizowanie danych programu Hive przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight)](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Wizualizuj dane interakcyjne zapytań Hive z usługą Power BI w usłudze Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Konfigurowanie środowiska interakcyjne PySpark dla kodu programu Visual Studio](set-up-pyspark-interactive-environment.md)
* [Umożliwia uruchamianie zapytań Hive w usłudze Azure HDInsight Zeppelin ](./hdinsight-connect-hive-zeppelin.md)

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



