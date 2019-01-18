---
title: Narzędzia usługi Azure HDInsight — na użytek programu Visual Studio Code Hive, LLAP lub PySpark | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać narzędzi HDInsight systemu Azure dla programu Visual Studio Code do tworzenia i przesyłania zapytań i skryptów.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/15/2018
ms.openlocfilehash: cd9f2df7987f033b4d4c234109cf712b8291d1b7
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382842"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Użyj usługi Azure HDInsight Tools for Visual Studio Code

Dowiedz się, jak używać narzędzi HDInsight systemu Azure dla programu Visual Studio Code (VS Code) do tworzenia i przesyłania zadań wsadowych Apache Hive, interakcyjnych zapytań Hive oraz skrypty PySpark dla platformy Apache Spark. Najpierw opiszemy sposób instalowania narzędzi HDInsight tools w programie VS Code, a następnie omówimy sposób przesyłania zadań Hive i platformy Spark. 

Narzędzia HDInsight systemu Azure można zainstalować na platformach obsługiwanych przez program VS Code, obejmujących Windows, Linux i macOS. Poniżej przedstawiono wymagania wstępne dla różnych platform.


## <a name="prerequisites"></a>Wymagania wstępne

Wymagane do ukończenia kroków opisanych w tym artykule są następujące elementy:

- Klaster HDInsight. Aby utworzyć klaster, zobacz [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Program Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)
- [Narzędzie mono](https://www.mono-project.com/docs/getting-started/install/). Narzędzie mono jest tylko wymagane dla systemów Linux i macOS.

## <a name="install-the-hdinsight-tools"></a>Instalowanie narzędzi HDInsight
   
Po zainstalowaniu wymagań wstępnych, można zainstalować narzędzi HDInsight systemu Azure dla programu VS Code. 

### <a name="to-install-azure-hdinsight-tools"></a>Aby zainstalować narzędzia usługi Azure HDInsight

1. Otwórz program Visual Studio Code.

2. W okienku po lewej stronie wybierz **rozszerzenia**. W polu wyszukiwania wprowadź **HDInsight**.

3. Obok pozycji **Azure HDInsight Tools**, wybierz opcję **zainstalować**. Po kilku sekundach **zainstalować** przycisku zmienia się na **Załaduj ponownie**.

4. Wybierz **Załaduj ponownie** aktywować **Azure HDInsight Tools** rozszerzenia.

5. Wybierz **Reload Window** o potwierdzenie. Możesz zobaczyć **Azure HDInsight Tools** w **rozszerzenia** okienka.

   ![HDInsight dla instalacji programu Visual Studio Code w języku Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Otwórz obszar roboczy HDInsight

Utwórz obszar roboczy w programie VS Code, aby nawiązać połączenie na platformie Azure.

### <a name="to-open-a-workspace"></a>Aby otworzyć obszar roboczy

1. Na **pliku** menu, wybierz opcję **Otwórz Folder**. Następnie wyznaczyć istniejącego folderu do folderu roboczego, lub Utwórz nową. Folder pojawia się w okienku po lewej stronie.

2. W okienku po lewej stronie wybierz **nowy plik** ikon umieszczonych obok nazw folderów roboczych.

   ![Nowy plik](./media/hdinsight-for-vscode/new-file.png)

3. Nadaj nowemu plikowi .hql (zapytania programu Hive) lub z rozszerzeniem PY (skryptu platformy Spark). 

## <a name="connect-to-hdinsight-cluster"></a>Nawiąż połączenie z klastrem HDInsight

Przed przesłaniem skryptów w klastrach HDInsight z programu VS Code, musisz nawiązać połączenie z kontem platformy Azure lub połączenia klastra (przy użyciu narzędzia Ambari nazwy użytkownika/hasła lub domeny dołączonych konta).

### <a name="to-connect-to-azure"></a>Do połączenia z platformą Azure

1. Utwórz nowy folder roboczy i nowy plik skryptu, jeśli nie masz jeszcze je.

2. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie w menu kontekstowym wybierz **HDInsight: Zaloguj się**. Możesz też wprowadzić **Ctrl + Shift + P**, a następnie wprowadź **HDInsight: Zaloguj się**.

    ![HDInsight Tools for Visual Studio Code logowania](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Aby się zarejestrować, postępuj zgodnie z instrukcjami rejestrowania w **dane wyjściowe** okienka.
    + Globalne środowiska logowania HDInsight wyzwoli Azure Zaloguj się w procesie.

        ![Zaloguj się instrukcje dotyczące platformy azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + W innych środowiskach postępuj zgodnie z instrukcjami dotyczącymi logowania.

        ![Sign in instructions for innego środowiska logowania](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

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
    - Konfiguracja zestawu

<h3 id="linkcluster">Aby połączyć klaster</h3>

Normalne klastra można połączyć za pomocą [Apache Ambari](https://ambari.apache.org/) zarządzane nazwy użytkownika lub połączyć pakiet zabezpieczeń dla przedsiębiorstw bezpiecznego klastra usługi Hadoop przy użyciu domena nazwa użytkownika (takie jak: user1@contoso.com).
1. Otwórz paletę poleceń, wybierając **CTRL + SHIFT + P**, a następnie wprowadź **HDInsight: Połącz klaster**.

   ![polecenie klastra łącze](./media/hdinsight-for-vscode/link-cluster-command.png)

2. HDInsight wprowadź adres URL klastra -> wejście-Username > Wprowadź hasło -> Wybierz typ klastra -> on informacje o powodzeniu pokazuje jeśli przekazany weryfikacji.
   
   ![okno dialogowe klastra łącza](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]  
   > Połączone nazwę użytkownika i hasło są używane, jeśli klaster w subskrypcji platformy Azure i połączone w klaster. 
   
3. Zobaczysz klastra połączonej za pomocą polecenia **klastra listy**. Możesz teraz przesłać skrypt do tego klastra połączonych.

   ![połączone klastra](./media/hdinsight-for-vscode/linked-cluster.png)

4. Wstawiając może zostać odłączyć klaster **HDInsight: Odłącz klaster** z palety poleceń.


### <a name="to-link-a-generic-apache-livy-endpoint"></a>Aby połączyć ogólnego endpoint Apache, usługi Livy

1. Otwórz paletę poleceń, wybierając **CTRL + SHIFT + P**, a następnie wprowadź **HDInsight: Połącz klaster**.
2. Wybierz **punktu końcowego usługi Livy ogólny**.
3. Wprowadź ogólny punkt końcowy usługi Livy, na przykład: http\:/ / 10.172.41.42:18080.
4. Wybierz **podstawowe** podczas autoryzacji dla ogólnego usługi Livy punktu końcowego, w przeciwnym razie, wybrać **Brak**.
5. Nazwa danych wejściowych użytkownika podczas wybierz **podstawowe** w step4.
6. Wprowadź hasło, gdy wybierz **podstawowe** w step4.
7. Ogólny punkt końcowy usługi livy łączenie zakończyło się pomyślnie.

   ![połączone z ogólnym klastrem usługi livy](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>Wyświetlanie listy klastrów HDInsight

Aby przetestować połączenie, można wyświetlić listę klastry usługi HDInsight:

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>Aby wyświetlić listę klastrów HDInsight w ramach subskrypcji platformy Azure
1. Otwórz obszar roboczy, a następnie łączenie z platformą Azure. Aby uzyskać więcej informacji, zobacz [HDInsight Otwórz obszar roboczy](#open-hdinsight-workspace) i [Połącz z platformą Azure](#connect-to-hdinsight-cluster).

2. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz pozycję **HDInsight: Lista klastrów** z menu kontekstowego. 

3. Klastry HDInsight są wyświetlane w **dane wyjściowe** okienka.

    ![Ustaw domyślną konfigurację klastra](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Ustaw domyślne klastra
1. Otwórz obszar roboczy i łączenie z platformą Azure. Zobacz [HDInsight Otwórz obszar roboczy](#open-hdinsight-workspace) i [Połącz z platformą Azure](#connect-to-hdinsight-cluster).

2. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz pozycję **HDInsight: Ustaw domyślne klastra**. 

3. Wybieranie klastra jako klastra domyślnego dla bieżącego pliku skryptu. Narzędzia automatycznie zaktualizować plik konfiguracji **. VSCode\settings.json**. 

   ![Domyślna konfiguracja klastra zestawu](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Ustaw środowisko platformy Azure
1. Otwórz paletę poleceń, wybierając pozycję **CTRL + SHIFT + P**.

2. Wprowadź **HDInsight: Ustaw środowisko Azure**.

3. Wybierz środowisko, np. "Azure" lub "AzureChina" jako domyślny wpis logowania.

4. W tym samym czasie narzędzie już zapisany domyślny wpis nazwy logowania w **. VSCode\settings.json**. Należy również bezpośrednio zaktualizować go w tym pliku konfiguracji. 

   ![Domyślna konfiguracja wpis logowania zestawu](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Prześlij interakcyjnych zapytań Hive, Hive skryptów wsadowych

Za pomocą narzędzi HDInsight dla programu VS Code możesz przesłać interakcyjnych zapytań Hive, skryptów wsadowych Hive HDInsight w klastrach.

1. Utwórz nowy folder roboczy i nowy plik skryptu Hive, jeśli jeszcze ich nie masz.

2. Łączenie się z usługi Azure konta lub łącze klastrami.

3. Skopiuj i wklej następujący kod do pliku Hive, a następnie zapisz go.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Kliknij prawym przyciskiem myszy w Edytorze skryptów, wybierz **HDInsight: Hive Interactive** przesłać zapytanie, lub użyj skrótu **Ctrl + Alt + I**. Wybierz **HDInsight: Hive Batch** Prześlij skrypt lub użyj skrótu **Ctrl + Alt + H**. 

5. Wybierz klaster, jeśli nie określono klastra domyślnego. Narzędzia pozwalają również na przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. Po kilku chwilach wyniki zapytania są wyświetlane w nowej karcie.

   ![Wynik interakcyjnego klastra Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **WYNIKI**: Cały wynik można zapisać jako plik w formacie CSV, JSON lub Excel w lokalnej ścieżce lub można po prostu zaznaczyć wiele wierszy.

    - Panel **KOMUNIKATY**: Po wybraniu numeru **wiersza** następuje przejście do pierwszego wiersza uruchomionego skryptu.

## <a name="submit-interactive-pyspark-queries"></a>Przesyłanie zapytań interakcyjnych PySpark

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>Aby przesłać interakcyjnego PySpark zapytania do klastrów usługi HDInsight Spark.

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
4. Zaznacz ten skrypt. Następnie kliknij prawym przyciskiem myszy w Edytorze skryptów i wybierz **HDInsight: Interakcyjnego PySpark**, lub użyj skrótu **Ctrl + Alt + I**.

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

### <a name="to-disable-environment-check"></a>Aby wyłączyć sprawdzanie środowiska

Domyślnie, narzędzia HDInsight sprawdzi środowiska i zainstaluj pakiety zależne podczas przesyłania zapytań interakcyjnych PySpark. Aby wyłączyć sprawdzanie środowiska, należy ustawić **hdinsight.disablePysparkEnvironmentValidation** do **tak** w obszarze **ustawienia użytkownika**.

   ![Ustaw wyboru środowiska z ustawień](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check.png)

Alternatywnie kliknij **wyłączyć sprawdzanie poprawności** przycisku pojawi się okno dialogowe.

   ![Ustaw wyboru środowiska z okna dialogowego](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check-dialog.png)

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 jest nieobsługiwane w przypadku Spark2.2/2.3

PySpark3 nie jest już obsługiwany przy użyciu klastra Spark 2.2 i Spark2.3 klastra, tylko "PySpark" jest obsługiwana w przypadku języka Python. Jest znany, problem, który przesyła do platformy spark 2.2/2.3 kończy się niepowodzeniem z środowiska python3 jako.

   ![Przesyłanie do środowiska python3 jako błąd uzyskiwania](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Wykonaj kroki, aby użyć Python2.x: 

1. Zainstaluj środowisko Python 2.7 na komputer lokalny i dodaj go do ścieżki systemowej.

2. Ponowne uruchomienie programu VSCode.

3. Przełącz się do języka Python 2, klikając **Python XXX** stan paska, a następnie wybierz obiekt docelowy języka Python.

   ![Wybierz wersję języka python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

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
4. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz pozycję **HDInsight: PySpark Batch**, lub użyj skrótu **Ctrl + Alt + H**. 

5. Wybierz klaster, do którego należy przesłać zadanie PySpark. 

   ![Przedstawia wynik zadania języka Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Po przesłaniu zadania Python przesyłania dzienników pojawia się w **dane wyjściowe** okna w programie VS Code. **Adresu URL interfejsu użytkownika platformy Spark** i **adresu URL interfejsu użytkownika usługi Yarn** są także wyświetlane. Można otworzyć adresu URL w przeglądarce sieci web, aby śledzić stan zadania.

## <a name="apache-livy-configuration"></a>Konfiguracja Apache, usługi Livy

[Apache, usługi Livy](https://livy.incubator.apache.org/) konfiguracja jest obsługiwana, mógł zostać ustawiony na **. VSCode\settings.json** w folderu obszaru roboczego. Obecnie konfiguracyjnych usługi livy obsługuje tylko skrypt w języku Python. Więcej informacji, zobacz [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Porady: wyzwalanie konfiguracyjnych usługi livy**
   
Można znaleźć na **pliku** menu, wybierz opcję **preferencje**i wybierz polecenie **ustawienia** w menu kontekstowym. Kliknij przycisk **ustawienia obszaru roboczego** karty, możesz rozpocząć Ustawianie konfiguracji usługi livy.

Możesz również przesłać plik, ogłoszenie folderu .vscode jest automatycznie dodawany do folderu roboczego. Konfiguracja usługi livy można znaleźć, klikając **.vscode\settings.json**.

+ Ustawienia projektu:

    ![Konfiguracja usługi Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>W przypadku ustawień **driverMomory** i **executorMomry**, ustaw wartość z jednostką, na przykład 1 g lub 1024 m. 

+ Obsługiwane konfiguracje programu Livy:   

    **POST /batches**   
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
    | driverCores | Liczba rdzeni do użycia dla procesu sterownika | int |
    | executorMemory | Ilość pamięci na działaniu proces wykonujący testy | ciąg |
    | executorCores | Liczba rdzeni do użycia dla każdej funkcji wykonawczej | int |
    | numExecutors | Liczbie funkcji wykonawczych, aby uruchomić dla tej sesji | int |
    | Archiwa | Archiwa ma być używany w ramach tej sesji | Lista parametrów |
    | kolejka | Nazwa kolejki YARN, do której przesłano | ciąg |
    | name | Nazwa tej sesji | ciąg |
    | potwierdzenie | Właściwości konfiguracji aparatu Spark | Mapa klucza = val |

    Treść odpowiedzi   
    Utworzony obiekt usługi Batch.

    | name | description | type | 
    | :- | :- | :- | 
    | id | Identyfikator sesji | int | 
    | appId | Identyfikator aplikacji dla tej sesji |  Ciąg |
    | appInfo | Informacje o szczegółowe aplikacji | Mapa klucza = val |
    | log | Wiersze dziennika | Lista ciągów |
    | state |   Stan usługi batch | ciąg |

>[!NOTE]
>Przypisanej konfiguracji usługi livy będą wyświetlane w okienku danych wyjściowych po Prześlij skrypt.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integracja z usługą Azure HDInsight z poziomu Eksploratora

Usługa Azure HDInsight, dodano do panelu po lewej stronie. Możesz przeglądać i zarządzania klastrem bezpośrednio.

1. Rozwiń **usługi AZURE HDINSIGHT**, jeśli znak nie, widoczna będzie **logowanie do platformy Azure...**  łącza.

    ![Zaloguj się obraz linku](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. Kliknij przycisk **logowanie do platformy Azure**, punktów POP logowania linku i kodu w prawej dolnej.

    ![Sign in instructions for innego środowiska logowania](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. Kliknij przycisk **Kopiuj & Otwórz** przycisku spowoduje otwarcie przeglądarki, Wklej kod, kliknij przycisk **Kontynuuj** przycisk, a następnie pojawi się wskazówki dotyczące logowania pomyślnie.

4. Po zalogowaniu dostępne subskrypcje i klastrów (Spark, Hadoop i HBase są obsługiwane) zostaną wyświetlone w **usługi AZURE HDINSIGHT**. 

   ![Subskrypcja usługi Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. Rozwiń węzeł klastra, aby wyświetlić schemat bazy danych i tabeli metadanych programu hive.

   ![Klaster usługi Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="additional-features"></a>Dodatkowe funkcje

HDInsight dla programu VS Code obsługuje następujące funkcje:

- **Automatyczne uzupełnianie IntelliSense**. Sugestie wyskakujące — słowo kluczowe, metody, zmienne i tak dalej. Różne ikony reprezentują różne typy obiektów.

    ![Narzędzia HDInsight Tools for typy obiektów funkcji IntelliSense kodu Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Znacznik błędów funkcji IntelliSense**. Usługa językowa podkreśla błędy edycji skryptu Hive.     
- **Najważniejsze funkcje składni**. Usługa językowa używa różnych kolorów do odróżnienia zmiennych, słowa kluczowe, typ danych, funkcje i tak dalej. 

    ![Wyróżnia HDInsight Tools for Visual Studio Code składni](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Kolejne kroki

### <a name="demo"></a>Demonstracja
* HDInsight dla programu VS Code: [Film wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem protokołu SSH przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Korzystaj z narzędzi HDInsight dla programu IntelliJ z Piaskownicą Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Używanie narzędzia HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](spark/apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Apache Hive data with Microsoft Power BI in Azure HDInsight (Wizualizowanie danych programu Apache Hive przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight)](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark przy użyciu Power BI: Interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](spark/apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](spark/apache-spark-job-debugging.md)



