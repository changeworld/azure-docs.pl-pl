---
title: Przesyłanie zadań z R Tools for Visual Studio — Azure HDInsight
description: Przesyłaj zadania R z lokalnej maszyny programu Visual Studio do klastra usługi HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 2d53f1bfc6eade535bfb1b3bb07d5115ffe5fc80
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967942"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Przesyłanie zadań z narzędzi R Tools for Visual Studio

[R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) to bezpłatne rozszerzenie typu open source dla społeczności (wersje bezpłatna), Professional i Enterprise programu [Visual studio 2017](https://www.visualstudio.com/downloads/)i [programu Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) lub nowszego. RTVS nie jest dostępny dla [programu Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019).

RTVS ulepsza przepływ pracy w języku R, oferując narzędzia, takie jak [okno R Interactive](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), IntelliSense (uzupełnianie kodu), [rysowanie wizualizacji](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) za pomocą bibliotek języka r, takich jak ggplot2 i Ggviz, [Debugowanie kodu języka r](https://docs.microsoft.com/visualstudio/rtvs/debugging)i inne.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

1. Zainstaluj [R Tools for Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Instalowanie RTVS w programie Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Wybierz obciążenie analizy *danych i aplikacji analitycznych* , a następnie wybierz opcję **Obsługa języka r**, **Obsługa środowiska uruchomieniowego w środowisku r**i opcje **Microsoft R Client** .

3. Musisz mieć klucze publiczny i prywatny na potrzeby uwierzytelniania SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Zainstaluj [ml Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) na swojej maszynie. ML Server udostępnia [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) funkcje i `RxSpark` .

5. Zainstaluj [program](https://www.putty.org/) , aby udostępnić kontekst obliczeniowy służący `RevoScaleR` do uruchamiania funkcji z lokalnego klienta do klastra usługi HDInsight.

6. Istnieje możliwość zastosowania Nauka o danych — ustawienia do środowiska programu Visual Studio, które udostępnia nowy układ obszaru roboczego dla narzędzi języka R.
   1. Aby zapisać bieżące ustawienia programu Visual Studio, użyj **narzędzia > Importuj i Eksportuj ustawienia** , a następnie wybierz opcję **Eksportuj wybrane ustawienia środowiska** i określ nazwę pliku. Aby przywrócić te ustawienia, użyj tego samego polecenia i wybierz opcję **Importuj wybrane ustawienia środowiska**.

   2. Przejdź do elementu menu **Narzędzia języka R** , a następnie wybierz pozycję **nauka o danych — ustawienia...** .

       ![Nauka o danych — ustawienia...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Korzystając z metody w kroku 1, można również zapisać i przywrócić spersonalizowany układ Analityka danych, zamiast powtarzać **nauka o danych — ustawienia** polecenie.

## <a name="execute-local-r-methods"></a>Wykonaj lokalne metody języka R

1. Utwórz klaster usługi HDInsight ML.
2. Zainstaluj [rozszerzenie RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Pobierz [plik zip Samples](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Otwórz `examples/Examples.sln` , aby uruchomić rozwiązanie w programie Visual Studio.
5. `1-Getting Started with R.R` Otwórz plik `A first look at R` w folderze rozwiązania.
6. Zaczynając od góry pliku, naciśnij klawisze CTRL + ENTER, aby wysłać każdy wiersz pojedynczo do okna R Interactive. Niektóre wiersze mogą chwilę potrwać, ponieważ zainstalują pakiety.
    * Alternatywnie możesz wybrać wszystkie wiersze w pliku R (Ctrl + A), a następnie wykonać wszystkie (Ctrl + Enter) lub wybrać ikonę Execute Interactive na pasku narzędzi.

        ![Wykonaj interaktywnie](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Po uruchomieniu wszystkich wierszy w skrypcie powinny zostać wyświetlone dane wyjściowe podobne do tego:

    ![Obszar roboczy](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Przesyłanie zadań do klastra usługi HDInsight ML

Za pomocą ml Server lub Microsoft R Client firmy Microsoft z komputera z systemem Windows, który jest wyposażony w program, można utworzyć kontekst obliczeniowy, `RevoScaleR` który będzie uruchamiać funkcje rozproszone z lokalnego klienta do klastra usługi HDInsight. Służy `RxSpark` do tworzenia kontekstu obliczeniowego, określania nazwy użytkownika, węzła brzegowego klastra Apache Hadoop, przełączników SSH i tak dalej.

1. Adres węzła brzegowego usługi ml w usłudze HDInsight `CLUSTERNAME-ed-ssh.azurehdinsight.net` to `CLUSTERNAME` , gdzie jest nazwą klastra usługi ml.

1. Wklej następujący kod do okna R Interactive w programie Visual Studio, zmieniając wartości zmiennych konfiguracji w celu dopasowania do środowiska.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![Ustawianie kontekstu Spark](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. W oknie R Interactive wykonaj następujące polecenia:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących:

    ![Pomyślne wykonanie](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) polecenia RX
1. Sprawdź, czy `rxHadoopCopy` pomyślnie `people.json` skopiowano plik z przykładowego folderu danych do nowo utworzonego `/user/RevoShare/newUser` folderu:

    1. W okienku klastra usługi HDInsight ML na platformie Azure wybierz pozycję **konta magazynu** z menu po lewej stronie.

        ![Konta magazynu](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Wybierz domyślne konto magazynu dla klastra, pamiętając o nazwie kontenera/katalogu.

    3. Wybierz pozycję **kontenery** z menu po lewej stronie w okienku konto magazynu.

        ![Containers](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Wybierz nazwę kontenera klastra, przejdź do folderu **User** (może być konieczne kliknięcie przycisku *Załaduj więcej* w dolnej części listy), a następnie wybierz pozycję *RevoShare*, a następnie **newUser**. Plik powinien być wyświetlony `newUser` w folderze. `people.json`

        ![Skopiowano plik](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Po zakończeniu korzystania z bieżącego kontekstu Apache Spark należy go zatrzymać. Nie można uruchamiać wielu kontekstów jednocześnie.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Następne kroki

* [Opcje kontekstu obliczeniowego dla usług ML w usłudze HDInsight](r-server-compute-contexts.md)
* [Łączenie skalowania i platformy Spark](../hdinsight-hadoop-r-scaler-sparkr.md) zawiera przykład prognoz opóźnień lotów dla linii lotniczych.

