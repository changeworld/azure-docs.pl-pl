---
title: Kod usługi Azure HDInsight dla programu Visual Studio
description: Dowiedz się, jak tworzyć i przesyłać zapytania i skrypty za pomocą narzędzi hive (Azure HDInsight) platformy & Spark (Azure HDInsight) dla programu Visual Studio Code.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435680"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Korzystanie z narzędzi gałęzi spark & dla kodu programu Visual Studio

Dowiedz się, jak używać narzędzi hive & programu Spark dla programu Visual Studio code do tworzenia i przesyłania zadań wsadowych gałąź apache, interaktywnych zapytań hive i skryptów PySpark dla platformy Apache Spark. Najpierw opiszemy sposób instalowania narzędzi spark & hive w programie Visual Studio Code, a następnie przejdziemy przez sposób przesyłania zadań do programu Spark & Hive Tools.  

Narzędzia spark & Hive mogą być instalowane na platformach obsługiwanych przez program Visual Studio Code, które obejmują systemy Windows, Linux i macOS. Należy zwrócić uwagę na następujące wymagania wstępne dla różnych platform.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym artykule wymagane są następujące elementy:

- Klaster usługi Azure HDInsight. Aby utworzyć klaster, zobacz [Wprowadzenie do usługi HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Lub użyj klastra Platformy Spark i Hive, który obsługuje punkt końcowy Apache Livy.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono jest wymagane tylko dla systemów Linux i macOS.
- [Interaktywne środowisko PySpark dla programu Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Katalog lokalny. W tym artykule użyto **języka C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalowanie narzędzi spark & hive

Po spełnieniu wymagań wstępnych można zainstalować narzędzia gałęzi platformy Spark & dla programu Visual Studio Code, wykonując następujące kroki:

1. Otwórz program Visual Studio Code.

2. Na pasku menu przejdź do **pozycji Wyświetl** > **rozszerzenia**.

3. W polu wyszukiwania wpisz **Spark & Hive**.

4. Z wyników wyszukiwania wybierz pozycję **Spark & Hive Tools,** a następnie wybierz pozycję **Zainstaluj:**

   ![Instalacja programu Spark & Hive for Visual Studio Code Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. W razie potrzeby wybierz **opcję Przeładuj ponownie.**

## <a name="open-a-work-folder"></a>Otwieranie folderu roboczego

Aby otworzyć folder roboczy i utworzyć plik w programie Visual Studio Code, wykonaj następujące kroki:

1. Na pasku menu przejdź do pozycji **Folder otwierania** > **pliku...**  >  **C:\HD\HDexample**, a następnie wybierz przycisk **Wybierz folder.** Folder pojawi się w widoku **Eksploratora** po lewej stronie.

2. W widoku **Eksploratora** wybierz folder **HDexample,** a następnie wybierz ikonę **Nowy plik** obok folderu roboczego:

   ![visual studio kod nowa ikona pliku](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nazwij nowy plik za `.hql` pomocą rozszerzenia pliku (Zapytania gałęzi) lub `.py` (skrypt Platformy Spark). W tym przykładzie użyto **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Ustawianie środowiska platformy Azure

W przypadku użytkownika chmury krajowej wykonaj następujące kroki, aby najpierw ustawić środowisko platformy Azure, a następnie użyć polecenia **Azure: Sign In,** aby zalogować się na platformie Azure:

1. Przejdź do**ustawień****preferencji** >  **plików** > .
2. Wyszukaj następujący ciąg: **Azure: Cloud**.
3. Wybierz chmurę krajową z listy:

   ![Ustawianie domyślnej konfiguracji wpisu logowania](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Łączenie się z kontem platformy Azure

Aby można było przesłać skrypty do klastrów z programu Visual Studio Code, należy połączyć się z kontem platformy Azure lub połączyć się z klastrem (przy użyciu poświadczeń nazwy użytkownika i hasła Apache Ambari lub konta przyłączanego do domeny). Wykonaj następujące kroki, aby połączyć się z platformą Azure:

1. Na pasku menu przejdź do **pozycji Wyświetl** > **paletę poleceń...** i wprowadź **platformę Azure: Zaloguj się:**

    ![Narzędzia & gałęzi spark dla logowania kodu programu Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Postępuj zgodnie z instrukcjami logowania, aby zalogować się na platformie Azure. Po nawiązaniu połączenia nazwa konta platformy Azure jest wyświetlana na pasku stanu u dołu okna Kod programu Visual Studio.  

## <a name="link-a-cluster"></a>Łączenie klastra

### <a name="link-azure-hdinsight"></a>Łącze: Usługa Azure HDInsight

Możesz połączyć normalny klaster przy użyciu nazwy użytkownika zarządzanego przez [Apache Ambari](https://ambari.apache.org/)lub połączyć bezpieczny klaster `user1@contoso.com`Hadoop pakietu zabezpieczeń przedsiębiorstwa przy użyciu nazwy użytkownika domeny (takiej jak: ).

1. Na pasku menu przejdź do **pozycji Wyświetl** > **paletę poleceń...** i wprowadź **spark/ hive: Link a Cluster**.

   ![Polecenie klastra łączy palety poleceń polecenia](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Wybierz połączony typ klastra **Azure HDInsight**.

3. Wprowadź adres URL klastra HDInsight.

4. Wpisz swoją nazwę użytkownika Ambari; domyślnie jest **admin**.

5. Wprowadź hasło Ambari.

6. Wybierz typ klastra.

7. Ustaw nazwę wyświetlaną klastra (opcjonalnie).

8. Przejrzyj widok **DANE wyjściowe** w celu weryfikacji.

   > [!NOTE]  
   > Połączona nazwa użytkownika i hasło są używane, jeśli klaster jest zarówno zalogowany do subskrypcji platformy Azure, jak i połączony z klastrem.  

### <a name="link-generic-livy-endpoint"></a>Łącze: Ogólny punkt końcowy Livy

1. Na pasku menu przejdź do **pozycji Wyświetl** > **paletę poleceń...** i wprowadź **spark/ hive: Link a Cluster**.

2. Wybierz połączony typ klastra **Ogólny punkt końcowy livy**.

3. Wprowadź ogólny punkt końcowy Livy. Na przykład:\:http //10.172.41.42:18080.

4. Wybierz typ autoryzacji **Podstawowy** lub **Brak**.  W przypadku **wybrania**opcji Podstawowe :  
    &emsp;A. Wpisz swoją nazwę użytkownika Ambari; domyślnie jest **admin**.  
    &emsp;b. Wprowadź hasło Ambari.

5. Przejrzyj widok **DANE wyjściowe** w celu weryfikacji.

## <a name="list-clusters"></a>Listy klastrów

1. Na pasku menu przejdź do **pozycji Wyświetl** > **paletę poleceń...** i wprowadź **spark/ hive: List Cluster**.

2. Wybierz odpowiednią subskrypcję.

3. Przejrzyj widok **OUTPUT.** W tym widoku jest wyświetlany połączony klaster (lub klastry) oraz wszystkie klastry w ramach subskrypcji platformy Azure:

    ![Ustawianie domyślnej konfiguracji klastra](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Ustawianie klastra domyślnego

1. Otwórz ponownie folder **HDexample,** który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Wybierz plik **HelloWorld.hql,** który został utworzony [wcześniej](#open-a-work-folder). Otwiera się w edytorze skryptów.

3. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz pozycję **Spark / Hive: Set Default Cluster**.  

4. [Połącz się ze](#connect-to-an-azure-account) swoim kontem platformy Azure lub połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Wybierz klaster jako domyślny klaster dla bieżącego pliku skryptu. Narzędzia automatycznie aktualizują **plik . Plik konfiguracyjny VSCode\settings.json:**

   ![Ustawianie domyślnej konfiguracji klastra](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Przesyłanie interaktywnych zapytań gałęzi i skryptów wsadowych gałęzi

Za pomocą narzędzia hive & platformy Spark dla programu Visual Studio Code można przesyłać do klastrów interaktywne zapytania gałęzi i skrypty wsadowe gałęzi.

1. Otwórz ponownie folder **HDexample,** który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Wybierz plik **HelloWorld.hql,** który został utworzony [wcześniej](#open-a-work-folder). Otwiera się w edytorze skryptów.

3. Skopiuj i wklej następujący kod do pliku hive, a następnie zapisz go:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Połącz się ze](#connect-to-an-azure-account) swoim kontem platformy Azure lub połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Kliknij prawym przyciskiem myszy edytor skryptów i wybierz pozycję **Gałąź: Interakcyjny,** aby przesłać kwerendę, lub użyj skrótu klawiaturowego Ctrl+Alt+I.  Wybierz **pozycję Gałąź: Partia,** aby przesłać skrypt, lub użyj skrótu klawiaturowego Ctrl+Alt+H.  

6. Jeśli nie określono domyślnego klastra, wybierz klaster. Narzędzia umożliwiają również przesyłanie bloku kodu zamiast całego pliku skryptu przy użyciu menu kontekstowego. Po kilku chwilach wyniki kwerendy pojawią się na nowej karcie:

   ![Interaktywny wynik zapytania hive Apache](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Panel WYNIKI:** Cały wynik można zapisać w pliku CSV, JSON lub Excel w ścieżce lokalnej lub po prostu wybrać wiele wierszy.

    - **Panel WIADOMOŚCI:** Po wybraniu numeru **wiersza** przeskakuje on do pierwszego wiersza uruchomionego skryptu.

## <a name="submit-interactive-pyspark-queries"></a>Przesyłanie interaktywnych zapytań PySpark

Aby przesłać interaktywne zapytania PySpark, wykonaj następujące kroki:

1. Otwórz ponownie folder **HDexample,** który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Utwórz nowy plik **HelloWorld.py,** postępuje zgodnie z [wcześniejszymi](#open-a-work-folder) krokami.

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

4. [Połącz się ze](#connect-to-an-azure-account) swoim kontem platformy Azure lub połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Zaznacz cały kod, kliknij prawym przyciskiem myszy edytor skryptów i wybierz opcję **Spark: PySpark Interactive,** aby przesłać kwerendę. Możesz też użyć skrótu Ctrl+Alt+I.

   ![pyspark interaktywne menu kontekstowe](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Wybierz klaster, jeśli nie określono domyślnego klastra. Po kilku chwilach wyniki **Python Interactive** pojawiają się w nowej karcie. Narzędzia umożliwiają również przesłanie bloku kodu zamiast całego pliku skryptu przy użyciu menu kontekstowego:

   ![interaktywne okno interaktywne pyspark python](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Wprowadź **%%info**, a następnie naciśnij klawisze Shift+Enter, aby wyświetlić informacje o zadaniu (opcjonalnie):

   ![pyspark interaktywne informacje o pracy](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Narzędzie obsługuje również zapytanie **Spark SQL:**

   ![Pyspark Interaktywny wynik widoku](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Stan przesyłania pojawia się po lewej stronie dolnego paska stanu podczas uruchamiania kwerend. Nie przesyłaj innych zapytań, gdy stan to **PySpark Kernel (zajęty)**.  

   > [!NOTE]
   >
   > Gdy włączone rozszerzenie Języka Python jest **wyczyszczone** w ustawieniach (jest zaznaczone domyślnie), przesłane wyniki interakcji pyspark użyją starego okna:
   >
   > ![pyspark interaktywne rozszerzenie python wyłączone](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Prześlij zadanie wsadowe PySpark

1. Otwórz ponownie folder **HDexample,** który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Utwórz nowy plik **BatchFile.py,** wykonując [wcześniejsze](#open-a-work-folder) kroki.

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

4. [Połącz się ze](#connect-to-an-azure-account) swoim kontem platformy Azure lub połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz polecenie **Spark: PySpark Batch**lub użyj skrótu klawiaturowego Ctrl+Alt+H.

6. Wybierz klaster, do który chcesz przesłać zadanie PySpark do:

   ![Przesyłanie wyników zadania języka Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Po przesłaniu zadania języka Python dzienniki przesyłania są wyświetlane w oknie **DANE WYJŚCIOWE** w programie Visual Studio Code. Wyświetlany jest również adres URL interfejsu użytkownika platformy Spark i adres URL interfejsu użytkownika yarn. Możesz otworzyć adres URL w przeglądarce internetowej, aby śledzić stan zadania.

## <a name="apache-livy-configuration"></a>Konfiguracja Apache Livy

[Konfiguracja Apache Livy](https://livy.incubator.apache.org/) jest obsługiwana. Można go skonfigurować w pliku **. VSCode\settings.json** w folderze obszaru roboczego. Obecnie konfiguracja Livy obsługuje tylko skrypt Pythona. Aby uzyskać więcej informacji, zobacz [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak wyzwolić konfigurację Livy**

Metoda 1  
1. Na pasku menu przejdź do pozycji**Ustawienia****preferencji** >  **plików** > .
2. W polu **Ustawienia wyszukiwania** wprowadź **hdinsight Job Submission: Livy Conf**.  
3. Wybierz **pozycję Edytuj w pliku settings.json** dla odpowiedniego wyniku wyszukiwania.

Metoda 2 Prześlij plik i zwróć uwagę, że folder vscode jest automatycznie dodawany do folderu roboczego. Możesz zobaczyć konfigurację Livy, wybierając **.vscode\settings.json**.

+ Ustawienia projektu:

    ![Konfiguracja HDInsight Apache Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Dla **driverMemory** i **executor Ustawienia Memory,** ustawić wartość i jednostkę. Na przykład: 1g lub 1024m.

+ Obsługiwane konfiguracje Livy:

    **POST /partie** Treść żądania

    | name | description | type |
    | :- | :- | :- |
    |  — plik | Plik zawierający aplikację do wykonania | Ścieżka (wymagana) |
    | proxyUżytnik | Użytkownik do personifikacji podczas uruchamiania zadania | Ciąg |
    | Classname | Aplikacja Java/Spark klasa główna | Ciąg |
    | args | Argumenty wiersza polecenia dla aplikacji | Lista ciągów |
    | Słoiki | Słoiki do użycia w tej sesji | Lista ciągów | 
    | pyFiles (pliki) | Pliki Pythona do wykorzystania w tej sesji | Lista ciągów |
    | files | Pliki do użycia w tej sesji | Lista ciągów |
    | sterownikMemory | Ilość pamięci do użycia w procesie sterownika | Ciąg |
    | driverCores (9.00) | Liczba rdzeni do użycia w procesie sterownika | int |
    | wykonawcaMemory | Ilość pamięci do użycia na proces egzekutora | Ciąg |
    | executorCores (bramki wykonawcy) | Liczba rdzeni do użycia dla każdego wykonawcy | int |
    | numWyżarki | Liczba wykonawców do uruchomienia dla tej sesji | int |
    | Archiwum | Archiwa do wykorzystania w tej sesji | Lista ciągów |
    | kolejka | Nazwa kolejki YARN, do przesłania do| Ciąg |
    | name | Nazwa tej sesji | Ciąg |
    | Conf | Właściwości konfiguracji platformy Spark | Mapa key=val |

    Treść odpowiedzi Utworzony obiekt partii.

    | name | description | type |
    | :- | :- | :- |
    | id | Identyfikator sesji | int |
    | appId | Identyfikator aplikacji tej sesji | Ciąg |
    | appInfo | Szczegółowe informacje o aplikacji | Mapa key=val |
    | Dziennik | Wiersze dziennika | Lista ciągów |
    | state |Stan wsadowy | Ciąg |

    > [!NOTE]
    > Przypisana config Livy jest wyświetlana w okienku danych wyjściowych podczas przesyłania skryptu.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integracja z usługą Azure HDInsight z Eksploratora

Tabela gałęzi można wyświetlić podgląd w klastrach bezpośrednio za pośrednictwem eksploratora **usługi Azure HDInsight:**

1. [Połącz się ze](#connect-to-an-azure-account) swoim kontem platformy Azure, jeśli jeszcze tego nie zrobiono.

2. Wybierz ikonę **platformy Azure** z lewej kolumny.

3. Z lewego okienka rozwiń **pozycję AZURE: HDINSIGHT**. Dostępne subskrypcje i klastry są wyświetlane.

4. Rozwiń klaster, aby wyświetlić bazę danych i schemat tabeli metadanych hive.

5. Kliknij prawym przyciskiem myszy tabelę Gałąź. Na przykład: **pokrzywka**. Wybierz **opcję Podgląd**.

   ![Spark & Hive for Visual Studio Code — tabela gałęzi w wersji zapoznawczej](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Zostanie otwarte okno **Podgląd wyników:**

   ![Okno wyników podglądu kodu programu Spark & Hive for Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- PANEL WYNIKI

   Cały wynik można zapisać jako plik CSV, JSON lub Excel w ścieżce lokalnej lub po prostu wybrać wiele wierszy.

- Panel WIADOMOŚCI
   1. Gdy liczba wierszy w tabeli jest większa niż 100, zostanie wyświetlony następujący komunikat: "Pierwsze 100 wierszy są wyświetlane dla tabeli Gałąź".
   2. Gdy liczba wierszy w tabeli jest mniejsza lub równa 100, zostanie wyświetlony komunikat w następujący sposób: "60 wierszy są wyświetlane dla tabeli Hive."
   3. Jeśli w tabeli nie ma zawartości, zostanie wyświetlony następujący komunikat: "0 wierszy jest wyświetlanych dla tabeli Gałąź".

        >[!NOTE]
        >
        >W systemie Linux zainstaluj xclip, aby włączyć dane tabeli kopiowania.
        >
        >![Kod Spark & Hive for Visual Studio w systemie Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Dodatkowe funkcje

Spark & Hive for Visual Studio Code obsługuje również następujące funkcje:

- **Autouzupełnienie IntelliSense**. Sugestie pojawiają się dla słów kluczowych, metod, zmiennych i innych elementów programowania. Różne ikony reprezentują różne typy obiektów:

    ![Narzędzia & gałęzii programu Spark dla obiektów IntelliSense kodu programu Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Znacznik błędu IntelliSense**. Usługa języka podkreśla błędy edycji w skrypcie gałęzi.     
- **Podkreśla składnię**. Usługa językowa używa różnych kolorów do rozróżniania zmiennych, słów kluczowych, typów danych, funkcji i innych elementów programowania:

    ![Wyróżnienia składni programu Spark & Hive Tools for Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Rola tylko do czytnika

Użytkownicy, którym przypisano rolę tylko do czytnika dla klastra, nie mogą już przesyłać zadań do klastra HDInsight ani nie mogą wyświetlać bazy danych hive. Skontaktuj się z administratorem klastra, aby uaktualnić rolę do [**operatora klastra USŁUGI HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) w [witrynie Azure portal](https://ms.portal.azure.com/). Jeśli masz prawidłowe poświadczenia Ambari, możesz ręcznie połączyć klaster, korzystając z następujących wskazówek.

### <a name="browse-the-hdinsight-cluster"></a>Przeglądanie klastra USŁUGI HDInsight  

Po wybraniu eksploratora usługi Azure HDInsight, aby rozwinąć klaster HDInsight, zostanie wyświetlony monit o połączenie klastra, jeśli masz rolę tylko do czytnika dla klastra. Poniższa metoda służy do łączenia się z klastrem przy użyciu poświadczeń Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Przesyłanie zadania do klastra USŁUGI HDInsight

Podczas przesyłania zadania do klastra HDInsight zostanie wyświetlony monit o połączenie klastra, jeśli jesteś w roli tylko do czytnika dla klastra. Poniższe kroki można wykonać, aby połączyć się z klastrem przy użyciu poświadczeń Ambari.

### <a name="link-to-the-cluster"></a>Łącze do klastra

1. Wprowadź prawidłową nazwę użytkownika Ambari.
2. Wprowadź prawidłowe hasło.

   ![Narzędzia gałęzi spark & dla nazwy użytkownika kodu programu Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Narzędzia & gałęzii platformy Spark dla hasła kodu programu Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Za pomocą `Spark / Hive: List Cluster` funkcji sprawdzania połączonego klastra:
  >
  >![Spark & Narzędzia gałęzi dla programu Visual Studio Code Reader Połączone](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

### <a name="browse-a-data-lake-storage-gen2-account"></a>Przeglądanie konta Data Lake Storage Gen2

Po wybraniu eksploratora usługi Azure HDInsight, aby rozwinąć konto Usługi Data Lake Storage Gen2, zostanie wyświetlony monit o wprowadzenie klucza dostępu do magazynu, jeśli twoje konto platformy Azure nie ma dostępu do magazynu Gen2. Po sprawdzeniu poprawności klucza dostępu konto Data Lake Storage Gen2 jest automatycznie rozszerzane.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Przesyłanie zadań do klastra usługi HDInsight z pamięcią data lake storage gen2

Podczas przesyłania zadania do klastra HDInsight przy użyciu usługi Data Lake Storage Gen2, zostanie wyświetlony monit o wprowadzenie klucza dostępu do magazynu, jeśli konto platformy Azure nie ma dostępu do zapisu do magazynu Gen2. Po sprawdzeniu poprawności klucza dostępu zadanie zostanie pomyślnie przesłane.

![Narzędzia gałęzi & platformy Spark dla klucza dostępu do kodu programu Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Klucz dostępu do konta magazynu można uzyskać z witryny Azure portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Odłączanie klastra

1. Na pasku menu przejdź do **polecenia Wyświetl** > **paletę poleceń,** a następnie wpisz **Spark/Hive: Unlink a Cluster**.  

2. Wybierz klaster, który chcesz odłączyć.  

3. Zobacz widok **WYJŚCIE** do weryfikacji.  

## <a name="sign-out"></a>Wyloguj  

Na pasku menu przejdź do **polecenia Wyświetl** > **paletę poleceń,** a następnie wprowadź polecenie **Azure: Wyloguj .**

## <a name="next-steps"></a>Następne kroki

Aby uzyskać film wideo, który pokazuje przy użyciu spark & hive for Visual Studio Code, zobacz [Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
