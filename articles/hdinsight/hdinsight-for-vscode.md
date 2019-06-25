---
title: Narzędzia usługi Azure HDInsight — na użytek programu Visual Studio Code Hive, LLAP lub PySpark
description: Dowiedz się, jak używać narzędzi HDInsight systemu Azure dla programu Visual Studio Code do tworzenia i przesyłania zapytań i skryptów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: aadfae9a7b74986fd0ac8857669dd3ccaf62af1f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166085"
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

3. Nadaj nowemu plikowi z oboma `.hql` (zapytań programu Hive) lub `.py` rozszerzenie pliku (skryptu platformy Spark).  W tym przykładzie użyto **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Ustaw środowisko platformy Azure

1. [Połącz](#connect-to-azure-account) do subskrypcji platformy Azure konta lub połączenia klastra, jeśli nie zostało to jeszcze zrobione.

2. Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Ustaw środowisko Azure**.

3. Wybierz środowisko jako domyślny wpis logowania.

4. W tym samym czasie narzędzie już zapisany domyślny wpis nazwy logowania w **. VSCode\settings.json**. Można również bezpośrednio zaktualizować w tym pliku konfiguracji. 

   ![Domyślna konfiguracja wpis logowania zestawu](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Łączenie się z kontem platformy Azure

Przed przesłaniem skryptów w klastrach HDInsight z programu Visual Studio Code, musisz nawiązać połączenie z kontem platformy Azure lub połączenia klastra (przy użyciu narzędzia Ambari nazwy użytkownika/hasła lub domeny dołączonych konta).  Wykonaj poniższe kroki, aby nawiązać połączenie z platformy Azure:

1. Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Zaloguj się**.

    ![HDInsight Tools for Visual Studio Code logowania](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Postępuj zgodnie z logowania zgodnie z instrukcjami w **dane wyjściowe** okienka.
    + Dla środowiska globalnej platformy Azure **HDInsight: Zaloguj się** wywoła polecenie **logowanie do platformy Azure** akcji w Eksploratorze HDInsight i na odwrót.

        ![Zaloguj się instrukcje dotyczące platformy azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + W innych środowiskach postępuj zgodnie z logowania w instrukcjach.

        ![Sign in instructions for innego środowiska logowania](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   Po nawiązaniu połączenia swojej nazwy konta platformy Azure są wyświetlane na pasku stanu w lewym dolnym rogu okna programu Visual Studio Code.  
  

## <a name="link-a-cluster"></a>Połącz klaster

### <a name="link-azure-hdinsight"></a>Link: Azure HDInsight

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



### <a name="link-generic-livy-endpoint"></a>Link: Punkt końcowy ogólnego usługi Livy

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

3. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz pozycję **HDInsight: Ustaw domyślne klastra**.  

4. [Połącz](#connect-to-azure-account) do subskrypcji platformy Azure konta lub połączenia klastra, jeśli nie zostało to jeszcze zrobione.

5. Wybieranie klastra jako klastra domyślnego dla bieżącego pliku skryptu. Narzędzia automatycznie zaktualizować plik konfiguracji **. VSCode\settings.json**. 

   ![Domyślna konfiguracja klastra zestawu](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Prześlij interakcyjnych zapytań Hive, Hive skryptów wsadowych

Za pomocą narzędzi HDInsight dla programu Visual Studio Code możesz przesłać interakcyjnych zapytań Hive i Hive skryptów wsadowych w klastrach HDInsight.

1. Otwórz folder **HDexample** utworzone [wcześniej](#open-hdinsight-work-folder) jeśli zamknięty.  

2. Wybierz plik **HelloWorld.hql** utworzone [wcześniej](#open-hdinsight-work-folder) i zostanie otwarty w Edytorze skryptów.


3. Skopiuj i wklej następujący kod do pliku Hive, a następnie zapisz go.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Połącz](#connect-to-azure-account) do subskrypcji platformy Azure konta lub połączenia klastra, jeśli nie zostało to jeszcze zrobione.

5. Kliknij prawym przyciskiem myszy w Edytorze skryptów, wybierz **HDInsight: Hive Interactive** przesłać zapytanie, lub użyj skrótu **Ctrl + Alt + I**.  Wybierz **HDInsight: Hive Batch** Prześlij skrypt lub użyj skrótu **Ctrl + Alt + H**.  

6. Wybierz klaster, jeśli nie określono klastra domyślnego. Narzędzia pozwalają również na przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. Po kilku chwilach wyniki zapytania są wyświetlane w nowej karcie.

   ![Wynik interakcyjnego klastra Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **WYNIKI**: Cały wynik można zapisać jako plik w formacie CSV, JSON lub Excel w lokalnej ścieżce lub można po prostu zaznaczyć wiele wierszy.

    - Panel **KOMUNIKATY**: Po wybraniu numeru **wiersza** następuje przejście do pierwszego wiersza uruchomionego skryptu.

## <a name="submit-interactive-pyspark-queries"></a>Przesyłanie zapytań interakcyjnych PySpark

Możesz przesłać zapytania interakcyjne PySpark, wykonując poniższe kroki:

1. Otwórz folder **HDexample** utworzone [wcześniej](#open-hdinsight-work-folder) jeśli zamknięty.  

2. Utwórz nowy plik **HelloWorld.py** następujące [wcześniej](#open-hdinsight-work-folder) kroki.

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

4. [Połącz](#connect-to-azure-account) do subskrypcji platformy Azure konta lub połączenia klastra, jeśli nie zostało to jeszcze zrobione.

5. Wybierz cały kod i kliknij prawym przyciskiem myszy w Edytorze skryptów, wybierz **HDInsight: Interakcyjnego PySpark** przesłać zapytanie, lub użyj skrótu **Ctrl + Alt + I**.

   ![Kliknij prawym przyciskiem myszy interakcyjnego pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Wybierz klaster, jeśli nie określono klastra domyślnego. Po kilku chwilach **Python Interactive powoduje** są wyświetlane w nowej karcie. Narzędzia pozwalają również na przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. 

   ![Okno interaktywne interaktywny język python pyspark](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Wprowadź **"%% info"** , a następnie naciśnij klawisz **Shift + Enter** Aby wyświetlić informacje o zadaniu. (Opcjonalnie)

   ![Wyświetl informacje o zadaniu](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Obsługuje także narzędzie **Spark SQL** zapytania.

   ![Wynik widoku interakcyjnego Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Po uruchomieniu zapytania, w lewym dolnym pasku stanu pojawi się stan przesyłania. Nie przesyłaj inne zapytania, jeśli jest w stanie **jądra PySpark (zajęty)** .  

   > [!NOTE] 
   >
   > Gdy **włączyć rozszerzenie języka Python** zaznaczono w ustawieniach (zaznaczone jest ustawienie domyślne), wyniki interakcji przesłane pyspark będzie używać starego okna.
   >
   > ![rozszerzenie interaktywny język python pyspark wyłączone](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Przesyłanie zadania usługi batch PySpark

1. Otwórz folder **HDexample** utworzone [wcześniej](#open-hdinsight-work-folder) jeśli zamknięty.  

2. Utwórz nowy plik **BatchFile.py** następujące [wcześniej](#open-hdinsight-work-folder) kroki.

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

4. [Połącz](#connect-to-azure-account) do subskrypcji platformy Azure konta lub połączenia klastra, jeśli nie zostało to jeszcze zrobione.

5. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz pozycję **HDInsight: PySpark Batch**, lub użyj skrótu **Ctrl + Alt + H**. 

6. Wybierz klaster, do którego należy przesłać zadanie PySpark. 

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

    | name | description | — typ | 
    | :- | :- | :- | 
    | file | Plik zawierający wykonywanie aplikacji | Ścieżka (wymagane) | 
    | proxyUser | Personifikację podczas uruchamiania zadania | string | 
    | className | Główna klasa platformy Java lub Spark aplikacji | string |
    | args | Argumenty wiersza polecenia dla aplikacji | Lista ciągów | 
    | jars | jars ma być używany w ramach tej sesji | Lista parametrów | 
    | pyFiles | Pliki języka Python do użycia w ramach tej sesji | Lista parametrów |
    | files | pliki, które zostaną użyte w tej sesji | Lista parametrów |
    | driverMemory | Ilość pamięci na potrzeby procesu sterownika | string |
    | driverCores | Liczba rdzeni do użycia dla procesu sterownika | int |
    | executorMemory | Ilość pamięci na działaniu proces wykonujący testy | string |
    | executorCores | Liczba rdzeni do użycia dla każdej funkcji wykonawczej | int |
    | numExecutors | Liczbie funkcji wykonawczych, aby uruchomić dla tej sesji | int |
    | archives | Archiwa ma być używany w ramach tej sesji | Lista parametrów |
    | queue | Nazwa kolejki YARN, do której przesłano | string |
    | name | Nazwa tej sesji | string |
    | conf | Właściwości konfiguracji aparatu Spark | Mapa klucza = val |

    Treść odpowiedzi   
    Utworzony obiekt usługi Batch.

    | name | description | — typ | 
    | :- | :- | :- | 
    | id | Identyfikator sesji | int | 
    | appId | Identyfikator aplikacji dla tej sesji |  String |
    | appInfo | Informacje o szczegółowe aplikacji | Mapa klucza = val |
    | log | Wiersze dziennika | Lista ciągów |
    | state |   Stan usługi batch | string |

>[!NOTE]
>Przypisanej konfiguracji usługi livy będą wyświetlane w okienku danych wyjściowych po Prześlij skrypt.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integracja z usługą Azure HDInsight z poziomu Eksploratora

**Usługa Azure HDInsight** została dodana do widoku Eksploratora. Możesz przeglądać i zarządzać klastry za pomocą **Azure HDInsight**.

1. [Połącz](#connect-to-azure-account) do subskrypcji platformy Azure konta lub połączenia klastra, jeśli nie zostało to jeszcze zrobione.

2. Na pasku menu, przejdź do **widoku** > **Explorer**.

3. W lewym okienku rozwiń **usługi AZURE HDINSIGHT**.  Zostaną wyświetlone dostępne subskrypcje i klastrów (Spark, Hadoop i HBase są obsługiwane). 

   ![Subskrypcja usługi Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Rozwiń węzeł klastra, aby wyświetlić schemat bazy danych i tabeli metadanych programu hive.

   ![Klaster usługi Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Tabela programu Hive (wersja zapoznawcza)
Możesz wyświetlić podgląd tabeli programu Hive w swojej klastry za pomocą **Azure HDInsight** explorer.
1. [Połącz](#connect-to-azure-account) do konta platformy Azure, jeśli nie zostało to jeszcze zrobione.

2. Kliknij przycisk **Azure** ikonę w skrajnej lewej kolumnie.

3. W lewym okienku rozwiń węzeł usługi AZURE HDINSIGHT. Zostaną wyświetlone dostępne subskrypcje i klastrów.

4. Rozwiń węzeł klastra, aby wyświetlić schemat bazy danych i tabeli metadanych programu hive.

5. Kliknij prawym przyciskiem myszy w tabeli programu Hive, np. hivesampletable. Wybierz **Podgląd**. 

   ![HDInsight dla tabeli hive za pomocą programu vscode (wersja zapoznawcza)](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. **Podgląd wyników** zostanie otwarte okno.

   ![Okno wyników HDInsight dla programu vscode (wersja zapoznawcza)](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **WYNIKI** panelu

   Cały wynik można zapisać jako plik w formacie CSV, JSON lub Excel w lokalnej ścieżce lub można po prostu zaznaczyć wiele wierszy.

- **KOMUNIKATY** panelu
   1. Gdy liczba wierszy w tabeli jest większy niż 100 wierszy, komunikat zostanie wyświetlony: **Pierwsze 100 wierszy są wyświetlane dla tabeli hive za pomocą**.
   2. Gdy liczba wierszy w tabeli jest mniejsza lub równa 100 wierszy, komunikat zostanie wyświetlony: **60 wiersze są wyświetlane dla tabeli hive za pomocą**.
   3. Gdy w tabeli jest Brak zawartości, komunikat zostanie wyświetlony: **wyświetlany jest wiersz 0 dla tabeli hive za pomocą**.

>[!NOTE]
>
>W systemie Linux należy zainstalować xclip, aby umożliwić kopiowanie tabeli danych.
>
>![HDInsight dla programu vscode w systemie linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Dodatkowe funkcje

HDInsight dla programu Visual Studio Code obsługuje następujące funkcje:

- **Automatycznego uzupełniania IntelliSense**. Sugestie wyskakujące — słowo kluczowe, metody, zmienne i tak dalej. Różne ikony reprezentują różne typy obiektów.

    ![Narzędzia HDInsight Tools for typy obiektów funkcji IntelliSense kodu Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Znacznik błędów funkcji IntelliSense**. Usługa językowa podkreśla błędy edycji skryptu Hive.     
- **Najważniejsze funkcje składni**. Usługa językowa używa różnych kolorów do odróżnienia zmiennych, słowa kluczowe, typ danych, funkcje i tak dalej. 

    ![Wyróżnia HDInsight Tools for Visual Studio Code składni](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Jedyną rolą Czytelnik

Użytkownicy z klastrem **czytnika** **tylko** **roli** nie będzie można przesłać zadania do klastra HDInsight ani wyświetlić bazę danych programu Hive. Musisz skontaktować się z administrator klastra, aby uaktualnić Twojej roli [ **HDInsight** **klastra** **Operator** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) w [ Witryna Azure portal](https://ms.portal.azure.com/). Jeśli znasz poświadczenia systemu Ambari, możesz ręcznie połączyć klastra zgodnie z instrukcjami poniżej.

### <a name="browse-hdinsight-cluster"></a>Przeglądaj klaster HDInsight  

Po kliknięciu Eksploratora usługi Azure HDInsight, aby rozwinąć klastra usługi HDInsight, wyświetli się monit o połączenia klastra, jeśli jesteś jedyną rolą Czytelnik dla klastra. Wykonaj poniższe kroki, aby połączyć się z klastrem przy użyciu poświadczeń systemu Ambari. 

### <a name="submit-job-to-hdinsight-cluster"></a>Przesyłanie zadania do klastra HDInsight

Podczas przesyłania zadania do klastra usługi HDInsight, wyświetli się monit o połączenia klastra, jeśli jesteś jedyną rolą Czytelnik dla klastra. Wykonaj poniższe kroki, aby połączyć się z klastrem przy użyciu poświadczeń systemu Ambari. 

### <a name="link-to-cluster"></a>Łączenie z klastrem

1.  Wprowadź nazwę użytkownika systemu Ambari 
2.  Wprowadź użytkownika Ambari hasło.

   ![Narzędzia HDInsight Tools for nazwy użytkownika kodu programu Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![HDInsight Tools for Visual Studio Code hasła](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>Możesz użyć HDInsight: Lista klaster do sprawdzenia połączone klastra.
>
>![Połączona narzędzia HDInsight Tools for czytnika kodu programu Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-reader-linked.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>Przeglądaj konta usługi ADLS Gen2

Po kliknięciu Eksploratora usługi Azure HDInsight, aby rozwinąć konta usługi Azure Data Lake Store Gen2, użytkownik jest monitowany o podanie magazynu **klucz dostępu** Jeśli Twoje konto platformy Azure nie ma dostępu do magazynu Gen2. Konto usługi ADLS Gen2 będzie automatycznie rozszerzona, gdy klucz dostępu jest pomyślnie zweryfikowane. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Przesyłanie zadań do klastrów HDInsight za pomocą usługi ADLS Gen2

Podczas przesyłania zadania do klastra usługi HDInsight przy użyciu usługi ADLS Gen2, użytkownik jest monitowany o podanie magazynu **klucz dostępu** Jeśli brak dostępu do zapisu w magazynie Gen2 konta platformy Azure.  Zadanie zostanie pomyślnie przesłano po klucz dostępu jest pomyślnie zweryfikowane. 

![Narzędzia HDInsight Tools for AccessKey kodu programu Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Klucz dostępu dla konta magazynu można uzyskać w witrynie Azure portal. Aby uzyskać informacje, zobacz [wyświetlanie i kopiowanie kluczy dostępu do](https://docs.microsoft.com/azure/storage/common/storage-account-manage#view-and-copy-access-keys).

## <a name="unlink-cluster"></a>Odłącz klastra

1. Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Odłącz klaster**.  

2. Wybierz klaster, aby odłączyć.  

3. Przegląd **dane wyjściowe** widok na potrzeby weryfikacji.  

## <a name="sign-out"></a>Wyloguj  

Na pasku menu wskaż **widoku** > **paleta poleceń...** , a następnie wprowadź **HDInsight: Wylogowywanie**.  Okno podręczne w będzie informacją dolnym rogu **wylogowania pomyślnie!** .


## <a name="next-steps"></a>Kolejne kroki
Wideo demonstracyjne użycia HDInsight dla programu Visual Studio Code, zobacz [HDInsight dla programu Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
