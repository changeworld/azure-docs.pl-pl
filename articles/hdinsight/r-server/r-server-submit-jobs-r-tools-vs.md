---
title: Przesyłanie zadań z programu R Tools for Visual Studio — Usługa Azure HDInsight
description: Prześlij zadania języka R z lokalnego komputera programu Visual Studio do klastra usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 73d1478ec2d6c90428f22a30ec82634df115d2f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435255"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Przesyłanie zadań z narzędzi R Tools for Visual Studio

[R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) to bezpłatne rozszerzenie typu open source dla wersji Community (bezpłatne), Professional i Enterprise zarówno programu Visual Studio [2017,](https://www.visualstudio.com/downloads/)jak i [visual studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) lub nowszej. RTVS nie jest dostępny dla [programu Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019).

RTVS usprawnia przepływ pracy języka R, oferując narzędzia, takie jak [okno R Interactive](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (uzupełnianie kodu), [wizualizacja wydruków](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) za pośrednictwem bibliotek R, takich jak ggplot2 i ggviz, [debugowanie kodu R](https://docs.microsoft.com/visualstudio/rtvs/debugging)i inne.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

1. Zainstaluj [narzędzia R dla programu Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![Instalowanie RTVS w programie Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Wybierz obciążenie *do nauki o danych i aplikacji analitycznych,* a następnie wybierz obsługę języka Języka **R**, obsługę **środowiska uruchomieniowego dla rozwoju języka R**i opcje klienta Microsoft **R.**

3. Do uwierzytelniania SSH należy mieć klucze publiczne i prywatne.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Zainstaluj [serwer ML](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) na swoim komputerze. ML Server [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) zapewnia `RxSpark` funkcje i funkcje.

5. Zainstaluj [putty,](https://www.putty.org/) aby zapewnić kontekst `RevoScaleR` obliczeniowy do uruchamiania funkcji z klienta lokalnego do klastra HDInsight.

6. Można zastosować ustawienia nauki o danych do środowiska programu Visual Studio, który udostępnia nowy układ dla obszaru roboczego dla narzędzi języka R.
   1. Aby zapisać bieżące ustawienia programu Visual Studio, użyj polecenia **Narzędzia > Ustawienia importu i eksportu,** a następnie wybierz pozycję **Eksportuj wybrane ustawienia środowiska** i określ nazwę pliku. Aby przywrócić te ustawienia, użyj tego samego polecenia i wybierz **pozycję Importuj wybrane ustawienia środowiska**.

   2. Przejdź do pozycji menu **Narzędzia R,** a następnie wybierz pozycję **Ustawienia nauki o danych...**.

       ![Ustawienia nauki o danych w programie Visual Studio](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Za pomocą podejścia w kroku 1, można również zapisać i przywrócić układ spersonalizowanych data scientist, zamiast powtarzać **ustawienia nauki o danych** polecenia.

## <a name="execute-local-r-methods"></a>Wykonywanie lokalnych metod języka R

1. Utwórz klaster usług HDInsight ML Services.
2. Zainstaluj [rozszerzenie RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Pobierz [przykładowy plik zip](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Otwórz, `examples/Examples.sln` aby uruchomić rozwiązanie w programie Visual Studio.
5. Otwórz `1-Getting Started with R.R` plik w `A first look at R` folderze rozwiązania.
6. Zaczynając od góry pliku, naciśnij klawisze Ctrl+Enter, aby wysłać każdy wiersz po jednym naraz do okna R Interactive. Niektóre wiersze mogą trochę potrwać, ponieważ instalują pakiety.
    * Alternatywnie można zaznaczyć wszystkie wiersze w pliku R (Ctrl+A), a następnie wykonać wszystkie (Ctrl+Enter) lub wybrać ikonę Wykonaj interaktywną na pasku narzędzi.

        ![Interaktywne wykonywanie w programie Visual Studio](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Po uruchomieniu wszystkich wierszy w skrypcie, powinieneś zobaczyć dane wyjściowe podobne do tego:

    ![Narzędzia R obszaru roboczego programu Visual Studio](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Przesyłanie zadań do klastra usług USŁUGI USŁUGI HDInsight

Korzystając z klienta Microsoft ML Server/Microsoft R z komputera z systemem Windows wyposażonego w `RevoScaleR` putty, można utworzyć kontekst obliczeniowy, który będzie uruchamiał funkcje rozproszone od klienta lokalnego do klastra HDInsight. Służy `RxSpark` do tworzenia kontekstu obliczeniowego, określając nazwę użytkownika, węzeł brzegowy klastra Apache Hadoop, przełączniki SSH i tak dalej.

1. Adres węzła brzegowego usług ML `CLUSTERNAME-ed-ssh.azurehdinsight.net` `CLUSTERNAME` w programie HDInsight to miejsce, w którym znajduje się nazwa klastra usług ML.

1. Wklej następujący kod do okna R Interactive w programie Visual Studio, zmieniając wartości zmiennych konfiguracji, aby dopasować go do środowiska.

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

   ![apache iskra ustawienie kontekstu](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Wykonaj następujące polecenia w oknie Interaktywna języka R:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących:

    ![Pomyślne wykonanie](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) polecenia rx a
1. Sprawdź, `rxHadoopCopy` czy `people.json` plik został pomyślnie skopiowany z przykładowego folderu danych do nowo utworzonego `/user/RevoShare/newUser` folderu:

    1. W okienku klastra usług HDInsight ml na platformie Azure wybierz **konta usługi Storage** z menu po lewej stronie.

        ![Konta usługi Azure HDInsight Storage](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Wybierz domyślne konto magazynu dla klastra, zanotując nazwę kontenera/katalogu.

    3. Wybierz **pozycję Kontenery** z menu po lewej stronie w okienku konta magazynu.

        ![Kontenery usługi Azure HDInsight Storage](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Wybierz nazwę kontenera klastra, przejdź do folderu **użytkownika** (może być trzeba kliknąć *przycisk Załaduj więcej* u dołu listy), a następnie wybierz *RevoShare*, a następnie **nowusowy**. Plik `people.json` powinien być wyświetlany `newUser` w folderze.

        ![Lokalizacja folderu skopiowanych plików hdinsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Po zakończeniu korzystania z bieżącego kontekstu Platformy Spark Apache należy go zatrzymać. Nie można uruchomić wiele kontekstów naraz.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Następne kroki

* [Opcje kontekstu obliczania usług ML w programie HDInsight](r-server-compute-contexts.md)
* [Połączenie ScaleR i SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) stanowi przykład prognoz opóźnienia lotu linii lotniczych.
