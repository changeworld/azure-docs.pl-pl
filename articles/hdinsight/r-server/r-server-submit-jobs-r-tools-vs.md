---
title: Przesyłanie zadań z R narzędzi dla programu Visual Studio - Azure HDInsight | Dokumentacja firmy Microsoft
description: Przedstawia zadania R z komputera lokalnego programu Visual Studio do klastra usługi HDInsight.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: maxluk
ms.openlocfilehash: eb7b538737e54b2c3d8a32e2ba65f7e0762a302c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047160"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Przesyłanie zadań z narzędzi R Tools for Visual Studio

[R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) to bezpłatne, open source rozszerzenie społeczność (bezpłatnie), Professional i w wersji Enterprise obu [programu Visual Studio 2017](https://www.visualstudio.com/downloads/), i [Visual Studio 2015 Update 3](http://go.microsoft.com/fwlink/?LinkId=691129)lub nowszej.

RTVS podnosi poziom R przepływu pracy, takich jak oferty narzędzia [okna interaktywnego R](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (uzupełnianie kodu), [wykreślenia wizualizacji](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) za pomocą biblioteki R, takich jak ggplot2 i ggviz, [Debugowanie kodu języka R](https://docs.microsoft.com/visualstudio/rtvs/debugging)itd.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

1. Zainstaluj [R Tools for Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Instalowanie RTVS w Visual Studio 2017 r.](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Wybierz *nauki dane i aplikacje analitycznych* obciążenie, następnie wybierz **obsługę języka R**, **Obsługa środowiska uruchomieniowego dla rozwoju R**, i  **Klient Microsoft R** opcje.

3. Musisz mieć klucze publiczne i prywatne dla uwierzytelniania SSH.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Zainstaluj [serwera ML](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) na tym komputerze. Udostępnia serwer ML [ `RevoScaleR` ](https://msdn.microsoft.com/microsoft-r/scaler/scaler) i `RxSpark` funkcji.

5. Zainstaluj [PuTTY](http://www.putty.org/) zapewnienie kontekstu obliczeń, aby uruchomić `RevoScaleR` funkcji z lokalnego klienta do klastra usługi HDInsight.

6. Istnieje możliwość zastosowania ustawień nauki danych do środowiska Visual Studio, który zapewnia nowy układ obszaru roboczego dla narzędzi R.
    1. Aby zapisać bieżące ustawienia programu Visual Studio, użyj **Narzędzia > Import i eksport ustawień** polecenia, a następnie wybierz **Eksportuj wybrane ustawienia środowiska** i określ nazwę pliku. Aby przywrócić te ustawienia, użyj tego samego polecenia, a następnie wybierz **Importuj wybrane ustawienia środowiska**.

    2. Przejdź do **narzędzia R** menu elementu, następnie wybierz **ustawienia nauki danych...** .

        ![Ustawienia analizy danych...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > Metoda w kroku 1, można także zapisać i przywrócić układ naukowca dane spersonalizowane, zamiast powtarzające się **ustawienia nauki danych** polecenia.

## <a name="execute-local-r-methods"></a>Wykonanie metody R lokalne

1. Tworzenie sieci [klastra usługi HDInsight w ML](r-server-get-started.md).
2. Zainstaluj [RTVS rozszerzenia](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Pobierz [pliku zip przykłady](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Otwórz `examples/Examples.sln` do uruchamiania rozwiązań w programie Visual Studio.
5. Otwórz `1-Getting Started with R.R` w pliku `A first look at R` folder rozwiązania.
6. Począwszy od góry pliku, naciśnij klawisze Ctrl + Enter do wysłania do okna interaktywnego R każdego wiersza, jeden z nich. Niektóre wiersze może potrwać kilka minut, zgodnie z ich instalowania pakietów.
    * Alternatywnie możesz zaznaczyć wszystkie wiersze w pliku R (Ctrl + A), albo wykonać wszystkie (Ctrl + Enter) lub wykonania interakcyjne ikonę na pasku narzędzi wybierz.
        ![Wykonanie interakcyjne](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Po uruchomieniu wszystkich wierszy w skrypcie, powinny pojawić się dane wyjściowe podobne do poniższego:

    ![Ustawienia analizy danych...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Przesyłanie zadań do klastra usługi HDInsight w ML

Za pomocą klienta firmy Microsoft R Server/Microsoft ML komputer z systemem Windows z programu PuTTY, można utworzyć kontekstu obliczeń uruchamianego rozproszonej `RevoScaleR` funkcji z lokalnego klienta do klastra usługi HDInsight. Użyj `RxSpark` można utworzyć kontekstu obliczeń, określając Twojej nazwy użytkownika, klastra usługi Hadoop węzła krawędzi, przełączniki SSH i tak dalej.

1. Aby znaleźć nazwy hosta z węzłem krawędzi, Otwórz swoje okienko klastra usługi HDInsight w ML na platformie Azure, a następnie wybierz **Secure Shell (SSH)** w górnym menu okienku przeglądu.

    ![Bezpieczna powłoka (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Kopiuj **nazwy hosta węzła krawędzi** wartość.

    ![Nazwa hosta węzła brzegowego](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Wklej następujący kod do okna interaktywnego R w programie Visual Studio, zmienianie wartości zmiennych Instalatora do danego środowiska.

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
    
    ![Ustawienie kontekstu Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. W oknie interaktywnym R, wykonaj następujące polecenia:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących:

    ![Pomyślne wykonanie polecenia rx](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Sprawdź, czy `rxHadoopCopy` pomyślnie skopiowane `people.json` plik z folderu danych przykład do nowo utworzony `/user/RevoShare/newUser` folderu:

    1. Wybierz z Twojej okienko klastra usługi HDInsight w ML na platformie Azure **kont magazynu** z menu po lewej stronie.

        ![Konta magazynu](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Wybierz domyślne konto magazynu dla klastra, co należy wziąć pod uwagę nazwa kontenera lub katalogu.

    3. Wybierz **kontenery** z menu po lewej stronie na swoje okienko konta magazynu.

        ![Containers](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Wybierz kontener nazwa_klastra, przejdź do **użytkownika** folder (może być konieczne kliknięcie pozycji *załadować więcej* w dolnej części listy), a następnie wybierz pozycję *RevoShare*, a następnie **newUser**. `people.json` Plik powinien być wyświetlany w `newUser` folderu.

        ![Skopiowany plik](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Po zakończeniu pracy w bieżącym kontekście Spark konieczne jest jej zatrzymanie. Nie można uruchomić wielu kontekstów jednocześnie.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Kolejne kroki

* [Obliczenia bazy danych opcji kontekstu dla usługi uczenia Maszynowego w usłudze HDInsight](r-server-compute-contexts.md)
* [Łączenie ScaleR i SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) zawiera przykład linii lotniczych transmitowane opóźnienie prognoz.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
