---
title: Usługa Azure HDInsight dla Visual Studio Code
description: Dowiedz się, jak tworzyć i przesyłać zapytania i skrypty Visual Studio Code za pomocą narzędzi Hive & platformy Spark (Azure HDInsight).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: da5cdd36e70166d274d50fcb093c0889cf534172
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489014"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Użyj narzędzi Hive & platformy Spark dla Visual Studio Code

Dowiedz się, jak używać narzędzi Hive & platformy Spark dla Visual Studio Code do tworzenia i przesyłania Apache Hive zadań wsadowych, interakcyjnych zapytań Hive i skryptów PySpark dla Apache Spark. Najpierw opisano sposób instalowania narzędzi Hive & platformy Spark w programie Visual Studio Code, a następnie przeprowadzimy Cię przez proces przesyłania zadań do programu Hive i platformy Spark.  

Narzędzia programu Hive & platformy Spark można instalować na platformach obsługiwanych przez Visual Studio Code, takich jak Windows, Linux i macOS. Poniżej znajdziesz wymagania wstępne dla różnych platform.


## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są następujące elementy:

- HDInsight An klaster. Aby utworzyć klaster, zobacz [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Lub klaster platformy Spark/Hive obsługujący punkt końcowy usługi Livy.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Narzędzie mono jest wymagane tylko dla systemów Linux i macOS.
- [Skonfiguruj środowisko interaktywne PySpark dla Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Katalog lokalny o nazwie **HDexample**.  W tym artykule jest używanych **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalowanie narzędzi Hive & platformy Spark

Po spełnieniu wymagań wstępnych można zainstalować narzędzia Hive & platformy Spark dla Visual Studio Code.  Wykonaj następujące kroki, aby zainstalować narzędzia programu Hive & platformy Spark:

1. Otwórz program Visual Studio Code.

2. Na pasku menu Przejdź do **widoku Wyświetl** > **rozszerzenia**.

3. W polu wyszukiwania wprowadź **gałąź & platformy Spark**.

4. W wynikach wyszukiwania wybierz pozycję **Narzędzia platformy Spark & Hive** , a następnie wybierz pozycję **Zainstaluj**.  

   ![Gałąź & platformy Spark dla Visual Studio Code instalacji języka Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. **Załaduj ponownie** w razie konieczności.


## <a name="open-work-folder"></a>Otwórz folder roboczy

Wykonaj następujące kroki, aby otworzyć folder roboczy i utworzyć plik w Visual Studio Code:

1. Na pasku menu Przejdź do **pliku** > **Otwórz folder...** C:\HD\HDexample, a następnie wybierz przycisk **Wybierz folder** .   >  Folder zostanie wyświetlony w widoku **Eksploratora** po lewej stronie.

2. W widoku **Eksploratora** wybierz folder, **HDexample**, a następnie ikonę **nowego pliku** obok folderu Work.

   ![Nowy plik](./media/hdinsight-for-vscode/new-file.png)

3. Nazwij nowy plik za pomocą programu `.hql` (zapytania Hive) `.py` lub rozszerzenia pliku (skrypt platformy Spark).  W tym przykładzie zastosowano **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Ustawianie środowiska platformy Azure

W przypadku użytkownika w chmurze krajowej wykonaj kroki, aby najpierw ustawić środowisko platformy Azure, **a następnie użyj platformy Azure:**  Polecenie Zaloguj się, aby zalogować się do platformy Azure.
   
1. Kliknij pozycję **File\Preferences\Settings**.
2. Wyszukaj **platformę Azure: Chmury**
3. Wybierz chmurę krajową z listy.

   ![Ustaw domyślną konfigurację wpisu logowania](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Połącz z kontem platformy Azure

Aby można było przesłać skrypty do klastrów z poziomu Visual Studio Code, należy połączyć się z kontem platformy Azure lub połączyć klaster (przy użyciu Ambari nazwa użytkownika/hasło lub konto dołączone do domeny).  Wykonaj następujące kroki, aby nawiązać połączenie z platformą Azure:

1. Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie **wprowadź Azure: Zaloguj się**.

    ![Narzędzia programu Hive & platformy Spark dla Visual Studio Code logowania](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Postępuj zgodnie z instrukcjami logowania, aby zalogować się na platformie Azure. Po nawiązaniu połączenia nazwa konta platformy Azure zostanie wyświetlona na pasku stanu u dołu okna Visual Studio Code.  
  

## <a name="link-a-cluster"></a>Łączenie klastra

### <a name="link-azure-hdinsight"></a>Łącze: Azure HDInsight

Możesz połączyć normalny klaster przy użyciu zarządzanej nazwy użytkownika [Apache Ambari](https://ambari.apache.org/) lub połączyć bezpieczny klaster usługi Hadoop z pakietem zabezpieczeń przedsiębiorstwa przy użyciu nazwy użytkownika domeny (takiej jak `user1@contoso.com`:).

1. Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie **wprowadź Spark/Hive: Połącz klaster**.

   ![Link klastra — polecenie](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Wybierz pozycję połączony klaster wpisz **Azure HDInsight**.

3. Wprowadź adres URL klastra usługi HDInsight.

4. Wprowadź nazwę użytkownika Ambari — wartość domyślna to **admin**.

5. Wprowadź hasło Ambari.

6. Wybierz typ klastra.

7. Ustaw nazwę wyświetlaną klastra (opcjonalnie).

8. Przejrzyj widok **danych wyjściowych** na potrzeby weryfikacji.

   > [!NOTE]  
   > Połączona nazwa użytkownika i hasło są używane, Jeśli klaster jest zarejestrowany w ramach subskrypcji platformy Azure i połączony z klastrem.  


### <a name="link-generic-livy-endpoint"></a>Łącze: Ogólny punkt końcowy usługi Livy

1. Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie **wprowadź Spark/Hive: Połącz klaster**.

2. Wybierz pozycję połączony klaster typ **rodzajowy punkt końcowy usługi Livy**.

3. Wprowadź ogólny punkt końcowy usługi Livy, na przykład: http\://10.172.41.42:18080.

4. Wybierz pozycję typ autoryzacji **podstawowa** lub **Brak**.  Jeśli **podstawowa**, wówczas:  
    &emsp;a. Wprowadź nazwę użytkownika Ambari — wartość domyślna to **admin**.  
    &emsp;b. Wprowadź hasło Ambari.

5. Przejrzyj widok **danych wyjściowych** na potrzeby weryfikacji.

## <a name="list-clusters"></a>Wyświetl listę klastrów

1. Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie **wprowadź Spark/Hive: Wyświetlanie listy**klastrów.

2. Wybierz żądaną subskrypcję.

3. Przejrzyj widok **danych wyjściowych** .  W widoku zostaną wyświetlone połączone klastry i wszystkie klastry w ramach subskrypcji platformy Azure.

    ![Ustaw domyślną konfigurację klastra](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Ustaw domyślny klaster

1. Ponownie otwórz folder **HDexample** utworzony [wcześniej](#open-work-folder) , jeśli został zamknięty.  

2. Wybierz plik **HelloWorld. HQL** utworzony [wcześniej](#open-work-folder) i zostanie otwarty w Edytorze skryptów.

3. Kliknij prawym przyciskiem myszy Edytor skryptów, a **następnie wybierz pozycję Spark/Hive: Ustaw domyślny klaster**.  

4. [Połącz](#connect-to-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Wybierz klaster jako domyślny klaster dla bieżącego pliku skryptu. Narzędzia automatycznie aktualizują plik konfiguracji **. VSCode\settings.json**. 

   ![Ustaw domyślną konfigurację klastra](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Przesyłanie interakcyjnych zapytań Hive, skryptów wsadowych Hive

Za pomocą narzędzi platformy Spark & Hive dla Visual Studio Code można przesyłać interaktywne zapytania Hive oraz skrypty wsadowe Hive do klastrów.

1. Ponownie otwórz folder **HDexample** utworzony [wcześniej](#open-work-folder) , jeśli został zamknięty.  

2. Wybierz plik **HelloWorld. HQL** utworzony [wcześniej](#open-work-folder) i zostanie otwarty w Edytorze skryptów.


3. Skopiuj i wklej następujący kod do pliku Hive, a następnie zapisz go.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Połącz](#connect-to-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Kliknij prawym przyciskiem myszy Edytor skryptów, **wybierz polecenie Hive: Aby przesłać zapytanie, użyj skrótu **Ctrl + Alt + I.****  Wybierz **gałąź Hive: Partia** do przesłania skryptu lub użyj skrótu **CTRL + ALT + H**.  

6. Wybierz klaster, jeśli nie został określony domyślny klaster. Narzędzia pozwalają również na przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. Po kilku chwilach wyniki zapytania pojawią się na nowej karcie.

   ![Wynik interakcyjnego klastra Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **WYNIKI**: Cały wynik można zapisać jako plik w formacie CSV, JSON lub Excel w lokalnej ścieżce lub można po prostu zaznaczyć wiele wierszy.

    - Panel **KOMUNIKATY**: Po wybraniu numeru **wiersza** następuje przejście do pierwszego wiersza uruchomionego skryptu.

## <a name="submit-interactive-pyspark-queries"></a>Prześlij interaktywne zapytania PySpark

Interaktywne zapytania PySpark można przesłać, wykonując poniższe kroki:

1. Ponownie otwórz folder **HDexample** utworzony [wcześniej](#open-work-folder) , jeśli został zamknięty.  

2. Utwórz nowy plik **HelloWorld.py** , postępując zgodnie [](#open-work-folder) z wcześniejszymi krokami.

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

4. [Połącz](#connect-to-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Wybierz cały kod i kliknij prawym przyciskiem myszy Edytor skryptów, wybierz **pozycję Spark: PySpark Interactive** do przesyłania zapytania lub użyj skrótu **Ctrl + Alt + I**.

   ![interakcyjne menu kontekstowe pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Wybierz klaster, jeśli nie został określony domyślny klaster. Po kilku chwilach interaktywne wyniki języka **Python** pojawiają się na nowej karcie. Narzędzia pozwalają również na przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. 

   ![interaktywne okno interaktywnego środowiska Python pyspark](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Wprowadź **wartość "%% info"** , a następnie naciśnij klawisze **SHIFT + ENTER** , aby wyświetlić informacje o zadaniu. (Opcjonalnie)

   ![Wyświetl informacje o zadaniu](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Narzędzie obsługuje również zapytanie **Spark SQL** .

   ![Wynik widoku interaktywnego Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Stan przesyłania jest wyświetlany po lewej stronie dolnego paska stanu podczas wykonywania zapytań. Nie przesyłaj innych zapytań, gdy stanem jest **PySpark jądra (zajęty)** .  

   > [!NOTE] 
   >
   > Gdy **włączone rozszerzenie języka Python** jest niezaznaczone w ustawieniach (ustawienie domyślne jest zaznaczone), przesłane wyniki interakcji pyspark będą używać starego okna.
   >
   > ![pyspark interaktywne rozszerzenie Python jest wyłączone](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Prześlij zadanie wsadowe PySpark

1. Ponownie otwórz folder **HDexample** utworzony [wcześniej](#open-work-folder) , jeśli został zamknięty.  

2. Utwórz nowy plik **BatchFile.py** , postępując zgodnie [](#open-work-folder) z wcześniejszymi krokami.

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

4. [Połącz](#connect-to-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie **wybierz pozycję Spark: PySpark Batch**lub użyj skrótu **CTRL + ALT + H**. 

6. Wybierz klaster, do którego chcesz przesłać zadanie PySpark. 

   ![Prześlij wynik zadania w języku Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Po przesłaniu zadania w języku Python dzienniki przesyłania są wyświetlane w oknie **dane wyjściowe** w Visual Studio Code. Wyświetlany jest również **adres URL interfejsu użytkownika platformy Spark** i **adres URL interfejsu użytkownika przędzy** . Możesz otworzyć adres URL w przeglądarce sieci Web, aby śledzić stan zadania.

## <a name="apache-livy-configuration"></a>Konfiguracja Apache usługi Livy

Konfiguracja [Apache usługi Livy](https://livy.incubator.apache.org/) jest obsługiwana. można ją ustawić na stronie **. VSCode\settings.json** w folderze miejsce pracy. Obecnie konfiguracja usługi Livy obsługuje tylko skrypt języka Python. Więcej szczegółów, zobacz [plik Readme usługi Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak wyzwolić konfigurację usługi Livy**

Metoda 1  
1. Na pasku menu Przejdź do**ustawień** **Preferencje** >  **plików** > .  
2. W polu tekstowym **Ustawienia wyszukiwania** wprowadź **sumission zadania usługi HDInsight:** Usługi Livy.  
3. Wybierz pozycję **Edytuj w pliku Settings. JSON** , aby uzyskać odpowiedni wynik wyszukiwania.

Metoda 2   
Prześlij plik, Zauważ, że folder. programu vscode jest automatycznie dodawany do folderu Work. Konfigurację usługi Livy można znaleźć, klikając pozycję **. vscode\settings.JSON**.

+ Ustawienia projektu:

    ![Konfiguracja usługi Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Dla opcji Settings **driverMomory** i **executorMomry**ustaw wartość przy użyciu jednostki, na przykład 1g lub 1024m. 

+ Obsługiwane konfiguracje usługi Livy:   

    **Opublikuj/Batches**   
    Treść żądania

    | name | description | type | 
    | :- | :- | :- | 
    | file | Plik zawierający aplikację do wykonania | ścieżka (wymagana) | 
    | proxyUser | Użytkownik do personifikacji podczas uruchamiania zadania | ciąg | 
    | className | Aplikacja Java/Spark klasy głównej | ciąg |
    | args | Argumenty wiersza polecenia dla aplikacji | lista ciągów | 
    | jars | Jars do użycia w tej sesji | Lista ciągów | 
    | pyFiles | Pliki języka Python, które mają być używane w tej sesji | Lista ciągów |
    | files | pliki, które mają być używane w tej sesji | Lista ciągów |
    | driverMemory | Ilość pamięci do użycia w procesie sterownika | ciąg |
    | driverCores | Liczba rdzeni do użycia w procesie sterownika | int |
    | executorMemory | Ilość pamięci do użycia na proces wykonawczy | ciąg |
    | executorCores | Liczba rdzeni do użycia dla każdego wykonawcy | int |
    | numExecutors | Liczba uruchomień do uruchomienia dla tej sesji | int |
    | archives | Archiwa, które mają być używane w tej sesji | Lista ciągów |
    | queue | Nazwa kolejki PRZĘDZy, do której zostało przesłane | ciąg |
    | name | Nazwa tej sesji | ciąg |
    | conf | Właściwości konfiguracji platformy Spark | Mapa klucza = Val |

    Treść odpowiedzi   
    Utworzony obiekt wsadowy.

    | name | description | type | 
    | :- | :- | :- | 
    | id | Identyfikator sesji | int | 
    | appId | Identyfikator aplikacji dla tej sesji |  String |
    | appInfo | Szczegółowe informacje o aplikacji | Mapa klucza = Val |
    | dziennik | Wiersze dziennika | lista ciągów |
    | state |   Stan partii | ciąg |

>[!NOTE]
>Przypisana usługi Livy konfiguracja zostanie wyświetlona w okienku danych wyjściowych podczas przesyłania skryptu.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integracja z usługą Azure HDInsight z poziomu Eksploratora

**Usługa Azure HDInsight** została dodana do widoku Eksploratora. Możesz przeglądać klastry i zarządzać nimi bezpośrednio za pomocą **usługi Azure HDInsight**.

1. [Połącz](#connect-to-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

2. Na pasku menu Przejdź do**Eksploratora** **widoku** > .

3. W okienku po lewej stronie rozwiń węzeł **Azure HDINSIGHT**.  Zostaną wyświetlone dostępne subskrypcje i klastry (platformy Spark, Hadoop i HBase). 

   ![Subskrypcja usługi Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Rozwiń klaster, aby wyświetlić bazę danych metadanych Hive i schemat tabeli.

   ![Klaster usługi Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Podgląd tabeli programu Hive
Tabelę programu Hive można wyświetlić w klastrach bezpośrednio za pomocą Eksploratora **usługi Azure HDInsight** .
1. [Połącz](#connect-to-azure-account) się z kontem platformy Azure, jeśli jeszcze tego nie zrobiono.

2. Kliknij ikonę **platformy Azure** od kolumny z lewej strony.

3. W okienku po lewej stronie rozwiń węzeł **Azure HDINSIGHT**. Dostępne subskrypcje i klastry zostaną wyświetlone na liście.

4. Rozwiń klaster, aby wyświetlić bazę danych metadanych Hive i schemat tabeli.

5. Kliknij prawym przyciskiem myszy tabelę programu Hive, np. hivesampletable. Wybierz pozycję **Podgląd**. 

   ![Platforma Spark & Hive dla tabeli programu Hive w programie Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Zostanie otwarte okno **Podgląd wyników** .

   ![Okno wyników programu Spark & Hive dla programu Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- Panel **wyników**

   Cały wynik można zapisać jako plik w formacie CSV, JSON lub Excel w lokalnej ścieżce lub można po prostu zaznaczyć wiele wierszy.

- Panel **komunikatów**
   1. Gdy liczba wierszy w tabeli przekracza 100 wierszy, komunikat pokazuje: **Pierwsze 100 wierszy jest wyświetlanych dla tabeli programu Hive**.
   2. Gdy liczba wierszy w tabeli jest mniejsza lub równa 100 wierszy, komunikat pokazuje: **60 wierszy jest wyświetlanych dla tabeli programu Hive**.
   3. Gdy w tabeli nie ma żadnej zawartości, komunikat pokazuje: **dla tabeli programu Hive jest wyświetlany wiersz 0**.

>[!NOTE]
>
>W systemie Linux Zainstaluj XCLIP, aby włączyć kopiowanie danych tabeli.
>
>![Platforma Spark & Hive dla programu Visual Studio Code w systemie Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Dodatkowe funkcje

Gałąź & platformy Spark dla Visual Studio Code obsługuje następujące funkcje:

- **Autouzupełnianie funkcji IntelliSense**. Sugestie są wyskakujące dla słowa kluczowego, metod, zmiennych i tak dalej. Różne ikony reprezentują różne typy obiektów.

    ![Narzędzia programu Hive & platformy Spark dla Visual Studio Code typów obiektów IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Znacznik błędu funkcji IntelliSense**. Usługa językowa podkreśla błędy edytowania skryptu programu Hive.     
- **Najważniejsze informacje o składni**. Usługa językowa używa różnych kolorów do odróżniania zmiennych, słów kluczowych, typu danych, funkcji i tak dalej. 

    ![Narzędzia programu Hive & platformy Spark dla Visual Studio Code składni](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Rola tylko czytelnik

Użytkownicy z rolą  **tylko**  czytnika klastrów nie mogą już przesyłać zadań do klastra usługi HDInsight ani wyświetlać bazy danych programu Hive. Skontaktuj się z administratorem klastra, aby uaktualnić rolę do [ **operatora** **klastra** usługi **HDInsight** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) w [Azure Portal](https://ms.portal.azure.com/). Jeśli znasz poświadczenia Ambari, możesz ręcznie połączyć klaster, postępując zgodnie z poniższą instrukcją.

### <a name="browse-hdinsight-cluster"></a>Przeglądaj klaster usługi HDInsight  

Po kliknięciu Eksploratora Azure HDInsight w celu rozszerzenia klastra usługi HDInsight zostanie wyświetlony monit o połączenie klastra, jeśli jest to rola tylko do odczytu dla klastra. Wykonaj poniższe kroki, aby połączyć się z klastrem za pośrednictwem poświadczeń Ambari. 

### <a name="submit-job-to-hdinsight-cluster"></a>Prześlij zadanie do klastra usługi HDInsight

Podczas przesyłania zadania do klastra usługi HDInsight zostanie wyświetlony monit o połączenie klastra, jeśli jest to rola tylko do odczytu dla klastra. Wykonaj poniższe kroki, aby połączyć się z klastrem za pośrednictwem poświadczeń Ambari. 

### <a name="link-to-cluster"></a>Połącz z klastrem

1.  Wprowadź nazwę użytkownika Ambari 
2.  Wprowadź hasło użytkownika Ambari.

   ![Narzędzia programu Hive & platformy Spark dla Visual Studio Code nazwa użytkownika](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Narzędzia programu Hive & platformy Spark dla hasła Visual Studio Code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>Można używać platformy Spark/Hive: Wyświetl listę klastrów, aby sprawdzić połączony klaster.
>
>![Narzędzia programu Hive & platformy Spark dla połączonego czytnika Visual Studio Code](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>Przeglądanie konta ADLS Gen2

Po kliknięciu Eksploratora usługi Azure HDInsight w celu rozszerzenia konta ADLS Gen2 zostanie wyświetlony monit o wprowadzenie **klucza dostępu** do magazynu, jeśli Twoje konto platformy Azure nie ma dostępu do magazynu Gen2. Konto ADLS Gen2 zostanie rozwinięte po pomyślnym zweryfikowaniu klucza dostępu. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Przesyłanie zadań do klastra usługi HDInsight przy użyciu ADLS Gen2

Podczas przesyłania zadania do klastra usługi HDInsight przy użyciu ADLS Gen2 zostanie wyświetlony monit o wprowadzenie **klucza dostępu** do magazynu, jeśli konto platformy Azure nie ma dostępu do zapisu do magazynu Gen2.  Zadanie zostanie pomyślnie przesłane po pomyślnym zweryfikowaniu klucza dostępu. 

![Narzędzia programu Hive & platformy Spark dla Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Możesz uzyskać klucz dostępu dla konta magazynu z Azure Portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i kopiowanie kluczy dostępu](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Odłącz klaster

1. Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie wprowadź **Spark/Hive: Odłącz klaster**.  

2. Wybierz pozycję klaster, aby odłączyć.  

3. Przejrzyj widok **danych wyjściowych** na potrzeby weryfikacji.  

## <a name="sign-out"></a>Wyloguj  

Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie wprowadź **Azure: Wyloguj się**.


## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z pokazem wideo z użyciem programu Spark & Hive dla Visual Studio Code, zobacz [gałąź & platformy Spark dla programu Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
