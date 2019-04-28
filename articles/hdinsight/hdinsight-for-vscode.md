---
title: Narzędzia usługi Azure HDInsight — na użytek programu Visual Studio Code Hive, LLAP lub PySpark | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać narzędzi HDInsight systemu Azure dla programu Visual Studio Code do tworzenia i przesyłania zapytań i skryptów.
Keywords: Program Visual Studio Code, narzędzia Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktywne usługi Hive, interakcyjnych zapytań
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 04e607517bc806b60d7e76e5076f9d3518e530eb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098341"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Użyj usługi Azure HDInsight Tools for Visual Studio Code

Dowiedz się, jak używać usługi Azure HDInsight Tools for Visual Studio Code do tworzenia i przesyłania zadań wsadowych Apache Hive, interakcyjnych zapytań Hive oraz skrypty PySpark dla platformy Apache Spark. Najpierw opiszemy sposób instalowania narzędzi HDInsight tools w programie Visual Studio Code, a następnie omówimy sposób przesyłania zadań Hive i platformy Spark.  

Narzędzia usługi Azure HDInsight można zainstalować na platformach obsługiwanych przez program Visual Studio Code, które obejmują Windows, Linux i macOS. Poniżej przedstawiono wymagania wstępne dla różnych platform.


## <a name="prerequisites"></a>Wymagania wstępne

Wymagane do ukończenia kroków opisanych w tym artykule są następujące elementy:

- Klaster usługi HDInsight. Aby utworzyć klaster, zobacz [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Narzędzie mono](https://www.mono-project.com/docs/getting-started/install/). Narzędzie mono jest tylko wymagane dla systemów Linux i macOS.
- [Rozszerzenie Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) dla programu Visual Studio Code.
- [Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Lokalny katalog o nazwie **HDexample**.  W tym artykule wykorzystano **C:\HD\HDexample**.

## <a name="install-azure-hdinsight-tools"></a>Instalowanie narzędzi usługi Azure HDInsight

Po wykonaniu kroków wymagań wstępnych, można zainstalować Azure HDInsight Tools for Visual Studio Code.  Wykonaj poniższe kroki, aby zainstalować narzędzia HDInsight Azure:

1. Otwórz program Visual Studio Code.

2. Na pasku menu, przejdź do **widoku** > **rozszerzenia**.

3. W polu wyszukiwania wprowadź **HDInsight**.

4. Wybierz **Azure HDInsight Tools** z wyników wyszukiwania, a następnie wybierz **zainstalować**.  

   ![HDInsight dla instalacji programu Visual Studio Code w języku Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Wybierz **Załaduj ponownie** aktywować **Azure HDInsight Tools** rozszerzenia po jej zainstalowaniu.


## <a name="open-hdinsight-work-folder"></a>Otwórz folder roboczy HDInsight

Wykonaj poniższe kroki, aby otworzyć folder roboczy i Utwórz plik w programie Visual Studio Code:

1. Na pasku menu, przejdź do **pliku** > **Otwórz Folder...**   >  **C:\HD\HDexample**, a następnie wybierz **wybierz Folder** przycisku. Folder pojawia się w **Explorer** widoku po lewej stronie.

2. Z **Explorer** wyświetlić, wybierz folder, a **HDexample**, a następnie **nowy plik** ikon umieszczonych obok nazw folderów roboczych.

   ![Nowy plik](./media/hdinsight-for-vscode/new-file.png)

3. Nadaj nowemu plikowi .hql (zapytania programu Hive) lub z rozszerzeniem PY (skryptu platformy Spark).  W tym przykładzie użyto **HelloWorld.hql**.

## <a name="connect-to-hdinsight-cluster"></a>Nawiąż połączenie z klastrem HDInsight

Przed przesłaniem skryptów w klastrach HDInsight z programu Visual Studio Code, musisz nawiązać połączenie z kontem platformy Azure lub połączenia klastra (przy użyciu narzędzia Ambari nazwy użytkownika/hasła lub domeny dołączonych konta).  Wykonaj poniższe kroki, aby nawiązać połączenie z platformy Azure:

1. Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Zaloguj się**.

    ![HDInsight Tools for Visual Studio Code logowania](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Postępuj zgodnie z instrukcjami rejestrowania w **dane wyjściowe** okienka.
    + Dla środowiska globalnej platformy Azure **HDInsight: Zaloguj się** wywoła polecenie **logowanie do platformy Azure** akcji w Eksploratorze HDInsight i na odwrót.

        ![Zaloguj się instrukcje dotyczące platformy azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + W innych środowiskach postępuj zgodnie z instrukcjami dotyczącymi logowania.

        ![Sign in instructions for innego środowiska logowania](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   Po nawiązaniu połączenia swojej nazwy konta platformy Azure są wyświetlane na pasku stanu w lewym dolnym rogu okna programu Visual Studio Code.  
  

<h2 id="linkcluster">Utwórz łącze: Azure HDInsight</h2>

Normalne klastra można połączyć za pomocą [Apache Ambari](https://ambari.apache.org/) zarządzane nazwy użytkownika lub połączyć pakiet zabezpieczeń dla przedsiębiorstw bezpiecznego klastra usługi Hadoop przy użyciu domena nazwa użytkownika (takie jak: user1@contoso.com).

1. Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Połącz klaster**.

   ![polecenie klastra łącze](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Wybierz typ klastra połączonych **Azure HDInsight**.

3. Wprowadź adres URL klastra HDInsight.

4. Wprowadź nazwę użytkownika systemu Ambari, wartością domyślną jest **administratora**.

5. Wprowadź hasło Ambari.

6. Wybierz typ klastra.

7. Przegląd **dane wyjściowe** widok na potrzeby weryfikacji.

   > [!NOTE]  
   > Połączone nazwę użytkownika i hasło są używane, jeśli klaster w subskrypcji platformy Azure i połączone w klaster.  


## <a name="create-link-generic-livy-endpoint"></a>Utwórz łącze: Punkt końcowy ogólnego usługi Livy

1. Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Połącz klaster**.

2. Wybierz typ klastra połączonych **ogólny punkt końcowy usługi Livy**.

3. Wprowadź ogólny punkt końcowy usługi Livy, na przykład: http\:/ / 10.172.41.42:18080.

4. Wybierz typ autoryzacji **podstawowe** lub **Brak**.  Jeśli **podstawowe**, następnie:  
    &emsp;a. Wprowadź nazwę użytkownika systemu Ambari, wartością domyślną jest **administratora**.  
    &emsp;b. Wprowadź hasło Ambari.

5. Przegląd **dane wyjściowe** widok na potrzeby weryfikacji.

## <a name="list-hdinsight-clusters"></a>Wyświetlanie listy klastrów HDInsight

1. Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Lista klastrów**.

2. Wybierz odpowiednią subskrypcję.

3. Przegląd **dane wyjściowe** widoku.  W widoku będzie wyświetlana, usługi połączone klastry i wszystkie klastry w ramach Twojej subskrypcji platformy Azure.

    ![Ustaw domyślną konfigurację klastra](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Ustaw domyślne klastra

1. Ponownie otwórz folder **HDexample** utworzone [wcześniej](#open-hdinsight-work-folder) jeśli zamknięty.  

2. Wybierz plik **HelloWorld.hql** utworzone [wcześniej](#open-hdinsight-work-folder) i zostanie otwarty w Edytorze skryptów.

3. [Połącz](#connect-to-hdinsight-cluster) do konta platformy Azure, jeśli nie zostało to jeszcze zrobione.

4. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz pozycję **HDInsight: Ustaw domyślne klastra**.  

5. Wybieranie klastra jako klastra domyślnego dla bieżącego pliku skryptu. Narzędzia automatycznie zaktualizować plik konfiguracji **. VSCode\settings.json**. 

   ![Domyślna konfiguracja klastra zestawu](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Ustaw środowisko platformy Azure

1. [Połącz](#connect-to-hdinsight-cluster) do konta platformy Azure, jeśli nie zostało to jeszcze zrobione.

2. Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Ustaw środowisko Azure**.

3. Wybierz środowisko jako domyślny wpis logowania.

4. W tym samym czasie narzędzie już zapisany domyślny wpis nazwy logowania w **. VSCode\settings.json**. Można również bezpośrednio zaktualizować w tym pliku konfiguracji. 

   ![Domyślna konfiguracja wpis logowania zestawu](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Prześlij interakcyjnych zapytań Hive, Hive skryptów wsadowych

Za pomocą narzędzi HDInsight dla programu Visual Studio Code możesz przesłać interakcyjnych zapytań Hive i Hive skryptów wsadowych w klastrach HDInsight.

1. Otwórz folder **HDexample** utworzone [wcześniej](#open-hdinsight-work-folder) jeśli zamknięty.  

2. Wybierz plik **HelloWorld.hql** utworzone [wcześniej](#open-hdinsight-work-folder) i zostanie otwarty w Edytorze skryptów.

3. [Połącz](#connect-to-hdinsight-cluster) do konta platformy Azure, jeśli nie zostało to jeszcze zrobione.

4. Skopiuj i wklej następujący kod do pliku Hive, a następnie zapisz go.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. Kliknij prawym przyciskiem myszy w Edytorze skryptów, wybierz **HDInsight: Hive Interactive** przesłać zapytanie, lub użyj skrótu **Ctrl + Alt + I**.  Wybierz **HDInsight: Hive Batch** Prześlij skrypt lub użyj skrótu **Ctrl + Alt + H**.  

6. Wybierz klaster, jeśli nie określono klastra domyślnego. Narzędzia pozwalają również na przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. Po kilku chwilach wyniki zapytania są wyświetlane w nowej karcie.

   ![Wynik interakcyjnego klastra Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **WYNIKI**: Cały wynik można zapisać jako plik w formacie CSV, JSON lub Excel w lokalnej ścieżce lub można po prostu zaznaczyć wiele wierszy.

    - Panel **KOMUNIKATY**: Po wybraniu numeru **wiersza** następuje przejście do pierwszego wiersza uruchomionego skryptu.

## <a name="submit-interactive-pyspark-queries"></a>Przesyłanie zapytań interakcyjnych PySpark

1. Otwórz folder **HDexample** utworzone [wcześniej](#open-hdinsight-work-folder) jeśli zamknięty.  

2. Utwórz nowy plik **HelloWorld.py** następujące [wcześniej](#open-hdinsight-work-folder) kroki.

3. Okno dialogowe zalecenia rozszerzenia języka Python zostanie wyświetlony, jeśli nie zainstalowano języka Python dla wstępnie wymaganego składnika.  Zainstaluj i załaduj ponownie Visual Studio Code w celu ukończenia instalacji.

    >![HDInsight dla instalacji programu Visual Studio Code w języku Python](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [Połącz](#connect-to-hdinsight-cluster) do konta platformy Azure, jeśli nie zostało to jeszcze zrobione.

5. Skopiuj i wklej następujący kod do pliku skryptu:
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

6. Kliknij prawym przyciskiem myszy w Edytorze skryptów, wybierz **HDInsight: Interakcyjnego PySpark** przesłać zapytanie, lub użyj skrótu **Ctrl + Alt + I**.  

7. Wybierz klaster, jeśli nie określono klastra domyślnego. Narzędzia pozwalają również na przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. Po kilku chwilach wyniki zapytania są wyświetlane w nowej karcie.

   ![Przedstawia wynik zadania języka Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. Obsługuje także narzędzie **klauzuli SQL** zapytania.

   ![Przedstawia wynik zadania Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) Stan przesyłania pojawia się po lewej stronie stanu dolny pasek po uruchomieniu zapytania. Nie przesyłaj inne zapytania, jeśli jest w stanie **jądra PySpark (zajęty)**.  

>[!NOTE]  
>Klastry można zachować informacje o sesji. Zdefiniowanej zmiennej, funkcji i odpowiadające im wartości są przechowywane w tej sesji, dzięki czemu mogą być przywoływane przez wiele wywołań usługi dla tego samego klastra. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 jest nieobsługiwane w przypadku Spark2.2/2.3

PySpark3 nie jest już obsługiwany przy użyciu klastra Spark 2.2 i Spark2.3 klastra, tylko "PySpark" jest obsługiwana w przypadku języka Python. Jest znany, problem, który przesyła do platformy spark 2.2/2.3 kończy się niepowodzeniem z środowiska python3 jako.

   ![Przesyłanie do środowiska python3 jako błąd uzyskiwania](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Wykonaj kroki, aby użyć Python2.x: 

1. Zainstaluj środowisko Python 2.7 na komputer lokalny i dodaj go do ścieżki systemowej.

2. Uruchom ponownie program Visual Studio Code.

3. Przełącz się do języka Python 2, klikając **Python XXX** stan paska, a następnie wybierz obiekt docelowy języka Python.

   ![Wybierz wersję języka python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>Przesyłanie zadania usługi batch PySpark

1. Otwórz folder **HDexample** utworzone [wcześniej](#open-hdinsight-work-folder) jeśli zamknięty.  

2. Utwórz nowy plik **BatchFile.py** następujące [wcześniej](#open-hdinsight-work-folder) kroki.

3. [Połącz](#connect-to-hdinsight-cluster) do konta platformy Azure, jeśli nie zostało to jeszcze zrobione.

4. Skopiuj i wklej następujący kod do pliku skryptu:

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

Po przesłaniu zadania Python przesyłania dzienników pojawia się w **dane wyjściowe** okna w programie Visual Studio Code. **Adresu URL interfejsu użytkownika platformy Spark** i **adresu URL interfejsu użytkownika usługi Yarn** są także wyświetlane. Można otworzyć adresu URL w przeglądarce sieci web, aby śledzić stan zadania.

## <a name="apache-livy-configuration"></a>Konfiguracja Apache, usługi Livy

[Apache, usługi Livy](https://livy.incubator.apache.org/) konfiguracja jest obsługiwana, można ustawić na **. VSCode\settings.json** w folderu obszaru roboczego. Obecnie konfiguracyjnych usługi livy obsługuje tylko skrypt w języku Python. Więcej informacji, zobacz [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Porady: wyzwalanie konfiguracyjnych usługi livy**

Metoda 1  
1. Na pasku menu, przejdź do **pliku** > **preferencje** > **ustawienia**.  
2. W **ustawienia wyszukiwania** polu tekstowym wprowadź **Sumission zadań HDInsight: Potwierdzenie Livy**.  
3. Wybierz **edytować w settings.json** wynik wyszukiwania istotne.

Metoda 2   
Przesyłanie pliku, zwróć uwagę, że .vscode folder jest automatycznie dodawany do folderu roboczego. Konfiguracja usługi livy można znaleźć, klikając **.vscode\settings.json**.

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
    | proxyUser | Personifikację podczas uruchamiania zadania | string | 
    | className | Główna klasa platformy Java lub Spark aplikacji | string |
    | args | Argumenty wiersza polecenia dla aplikacji | Lista ciągów | 
    | plikach JAR | jars ma być używany w ramach tej sesji | Lista parametrów | 
    | pyFiles | Pliki języka Python do użycia w ramach tej sesji | Lista parametrów |
    | plików | pliki, które zostaną użyte w tej sesji | Lista parametrów |
    | driverMemory | Ilość pamięci na potrzeby procesu sterownika | string |
    | driverCores | Liczba rdzeni do użycia dla procesu sterownika | int |
    | executorMemory | Ilość pamięci na działaniu proces wykonujący testy | string |
    | executorCores | Liczba rdzeni do użycia dla każdej funkcji wykonawczej | int |
    | numExecutors | Liczbie funkcji wykonawczych, aby uruchomić dla tej sesji | int |
    | Archiwa | Archiwa ma być używany w ramach tej sesji | Lista parametrów |
    | kolejka | Nazwa kolejki YARN, do której przesłano | string |
    | name | Nazwa tej sesji | string |
    | potwierdzenie | Właściwości konfiguracji aparatu Spark | Mapa klucza = val |

    Treść odpowiedzi   
    Utworzony obiekt usługi Batch.

    | name | description | type | 
    | :- | :- | :- | 
    | id | Identyfikator sesji | int | 
    | appId | Identyfikator aplikacji dla tej sesji |  String |
    | appInfo | Informacje o szczegółowe aplikacji | Mapa klucza = val |
    | dziennik | Wiersze dziennika | Lista ciągów |
    | state |   Stan usługi batch | string |

>[!NOTE]
>Przypisanej konfiguracji usługi livy będą wyświetlane w okienku danych wyjściowych po Prześlij skrypt.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integracja z usługą Azure HDInsight z poziomu Eksploratora

**Usługa Azure HDInsight** została dodana do widoku Eksploratora. Możesz przeglądać i zarządzać klastry za pomocą **Azure HDInsight**.

1. [Połącz](#connect-to-hdinsight-cluster) do konta platformy Azure, jeśli nie zostało to jeszcze zrobione.

2. Na pasku menu, przejdź do **widoku** > **Explorer**.

3. W lewym okienku rozwiń **usługi AZURE HDINSIGHT**.  Zostaną wyświetlone dostępne subskrypcje i klastrów (Spark, Hadoop i HBase są obsługiwane). 

   ![Subskrypcja usługi Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Rozwiń węzeł klastra, aby wyświetlić schemat bazy danych i tabeli metadanych programu hive.

   ![Klaster usługi Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>Dodatkowe funkcje

HDInsight dla programu Visual Studio Code obsługuje następujące funkcje:

- **Automatyczne uzupełnianie IntelliSense**. Sugestie wyskakujące — słowo kluczowe, metody, zmienne i tak dalej. Różne ikony reprezentują różne typy obiektów.

    ![Narzędzia HDInsight Tools for typy obiektów funkcji IntelliSense kodu Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Znacznik błędów funkcji IntelliSense**. Usługa językowa podkreśla błędy edycji skryptu Hive.     
- **Najważniejsze funkcje składni**. Usługa językowa używa różnych kolorów do odróżnienia zmiennych, słowa kluczowe, typ danych, funkcje i tak dalej. 

    ![Wyróżnia HDInsight Tools for Visual Studio Code składni](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>Odłącz klastra

1. Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Odłącz klaster**.  

2. Wybierz klaster, aby odłączyć.  

3. Przegląd **dane wyjściowe** widok na potrzeby weryfikacji.  


## <a name="logout"></a>Logout  

Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Wylogowywanie**.  Okno podręczne w będzie informacją dolnym rogu **wylogowania pomyślnie!**.


## <a name="next-steps"></a>Kolejne kroki
Wideo demonstracyjne użycia HDInsight dla programu Visual Studio Code, zobacz [HDInsight dla programu Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
