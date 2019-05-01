---
title: Przesyłanie zadań z narzędzi R Tools for Visual Studio — Azure HDInsight
description: Prześlij zadania R na maszynie lokalnej programu Visual Studio z klastrem usługi HDInsight.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 8f1ed582b7abf43afd38ca5c358aa7e179bfecb3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702270"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Przesyłanie zadań z narzędzi R Tools for Visual Studio

[Narzędzia języka R dla programu Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) to bezpłatne, typu open-source rozszerzenie dla społeczności (wersja bezpłatna), Professional i w wersji Enterprise zarówno [programu Visual Studio 2017](https://www.visualstudio.com/downloads/), i [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)lub nowszej.

RTVS zwiększa R przepływu pracy, udostępniając narzędzia, takie jak [okno interaktywne R](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL) funkcja intellisense (uzupełnianie kodu) [kreślenia wizualizacji](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) przy użyciu bibliotek języka R, np. ggplot2 i ggviz, [Debugowanie kodu języka R](https://docs.microsoft.com/visualstudio/rtvs/debugging)i nie tylko.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

1. Zainstaluj [R Tools for Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Instalowanie RTVS w programie Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Wybierz *aplikacji analitycznych i naukowych opracowań danych* obciążenie, następnie wybierz pozycję **obsługę języka R**, **obsługę środowiska uruchomieniowego dla Programowanie w języku R**, i  **Microsoft R Client** opcje.

3. Musisz mieć publiczny i prywatny klucz do uwierzytelniania SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Zainstaluj [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) na swojej maszynie. ML Server zapewnia [ `RevoScaleR` ](https://msdn.microsoft.com/microsoft-r/scaler/scaler) i `RxSpark` funkcji.

5. Zainstaluj [PuTTY](https://www.putty.org/) zapewnienie kontekst obliczeniowy pozwala uruchomić `RevoScaleR` funkcji z lokalnego klienta do klastra usługi HDInsight.

6. Masz możliwość zastosowania ustawień do nauki o danych do środowiska programu Visual Studio, co zapewnia nowy układ obszaru roboczego dla narzędzia języka R.
   1. Aby zapisać bieżące ustawienia programu Visual Studio, należy użyć **Narzędzia > Import i eksport ustawień** polecenia, a następnie wybierz **Eksportuj wybrane ustawienia środowiska** i określ nazwę pliku. Aby przywrócić te ustawienia, użyj tego samego polecenia, a następnie wybierz pozycję **Importuj ustawienia wybranego środowiska**.

   2. Przejdź do **R Tools** menu elementów, następnie wybierz pozycję **ustawienia do nauki o danych...** .

       ![Ustawienia do nauki o danych...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Przy użyciu metody w kroku 1, można także zapisać i przywrócić układ osób przetwarzających dane spersonalizowane, zamiast powtarzania **ustawienia do nauki o danych** polecenia.

## <a name="execute-local-r-methods"></a>Wykonanie metody R lokalne

1. Utwórz swoje [klastra HDInsight usługi ML](r-server-get-started.md).
2. Zainstaluj [rozszerzenie RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Pobierz [plik zip przykłady](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Otwórz `examples/Examples.sln` do uruchamiania rozwiązań w programie Visual Studio.
5. Otwórz `1-Getting Started with R.R` w pliku `A first look at R` folderu rozwiązania.
6. Uruchamianie w górnej części pliku, naciśnij klawisze Ctrl + Enter wysyłać każdego wiersza po kolei, okno interaktywne R. Niektóre linie może trochę potrwać podczas instalacji pakietów.
    * Alternatywnie możesz można wybierz wszystkie wiersze w pliku języka R (Ctrl + A), następnie wykonania wszystkich (Ctrl + Enter) albo wykonaj interaktywne ikonę na pasku narzędzi.
        ![Wykonaj interaktywne](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Po uruchomieniu wszystkich wierszy w skrypcie, powinny pojawić się dane wyjściowe podobne do poniższego:

    ![Ustawienia do nauki o danych...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Przesyłanie zadań do klastra usługi HDInsight usługi ML

Przy użyciu Microsoft ML Server i Microsoft R Client z komputera Windows wyposażone w programie PuTTY, można utworzyć kontekstu obliczeniowego, przechowywaną uruchamiającą rozproszonych `RevoScaleR` funkcji z lokalnego klienta do klastra usługi HDInsight. Użyj `RxSpark` można utworzyć kontekstu obliczeniowego, określając Twojej nazwy użytkownika, klastra Apache Hadoop węzła krawędzi, SSH przełączniki i tak dalej.

1. Aby znaleźć nazwę hosta węzła brzegowego, otwórz okienko klastra usługi HDInsight usługi ML w na platformie Azure, a następnie wybierz **Secure Shell (SSH)** w menu górnej części okienka Przegląd.

    ![Bezpieczna powłoka (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Kopiuj **nazwa hosta węzła brzegowego** wartość.

    ![Nazwa hosta węzła brzegowego](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Wklej następujący kod do okno interaktywne R w programie Visual Studio, zmieniając wartości zmiennych konfiguracji pod kątem danego środowiska.

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
    
    ![Ustawianie kontekstu aparatu Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. W oknie interaktywnym języka R, wykonaj następujące polecenia:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących:

    ![Pomyślne wykonanie polecenia rx](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Upewnij się, że `rxHadoopCopy` skopiowano `people.json` plik z folderu danych przykład do nowo utworzonego `/user/RevoShare/newUser` folderu:

    1. W okienku klastra usługi HDInsight usługi ML, na platformie Azure, wybierz **kont magazynu** z menu po lewej stronie.

        ![Konta magazynu](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Wybierz domyślne konto magazynu dla klastra, dzięki czemu Zanotuj nazwę kontenera lub bieżącym katalogu.

    3. Wybierz **kontenery** z menu po lewej stronie w okienku konta magazynu.

        ![Containers](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Wybierz nazwę kontenera w klastrze, przejdź do **użytkownika** folder (może być konieczne, kliknij przycisk *Załaduj więcej* w dolnej części listy), a następnie wybierz *RevoShare*, a następnie **newUser**. `people.json` Pliku powinna być wyświetlana w `newUser` folderu.

        ![Skopiowanego pliku](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Po zakończeniu przy użyciu bieżącego kontekstu platformy Apache Spark, konieczne jest jej zatrzymanie. Nie można jednocześnie uruchomić wielu kontekstach.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Kolejne kroki

* [COMPUTE context options obliczeniowego usługi ML w HDInsight](r-server-compute-contexts.md)
* [Łączenie programu ScaleR i SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) stanowi przykład prognozowania opóźnień lotów lotniczej.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
