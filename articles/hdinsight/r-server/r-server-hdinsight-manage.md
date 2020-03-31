---
title: Zarządzanie klastrem usług ML w usłudze HDInsight — Azure
description: Dowiedz się, jak zarządzać różnymi zadaniami w klastrze usług ML w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: b2c16c27c0dfc0c30a99c52544cc4d2278eadfc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647734"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Zarządzanie klastrem usług ML w usłudze Azure HDInsight

W tym artykule dowiesz się, jak zarządzać istniejącym klastrem usług ML w usłudze Azure HDInsight w celu wykonywania zadań, takich jak dodawanie wielu równoczesnych użytkowników, łączenie się zdalnie z klastrem usług ML, zmienianie kontekstu obliczeniowego itp.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usług ML w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję Usługi **ML** dla **typu klastra**.

* Klient protokołu Secure Shell (SSH): klient SSH jest używany do zdalnego łączenia z klastrem usługi HDInsight i uruchamiania poleceń bezpośrednio w klastrze. Aby uzyskać więcej informacji, zobacz [Korzystanie z funkcji SSH z hdinsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="enable-multiple-concurrent-users"></a>Włączanie obsługi równoczesnych użytkowników

Można włączyć wielu równoczesnych użytkowników klastra usług ML w programie HDInsight, dodając więcej użytkowników dla węzła brzegowego, na którym działa wersja społeczności RStudio. Podczas tworzenia klastra usługi HDInsight musisz podać dwóch użytkowników: użytkownika HTTP i użytkownika SSH:

![Parametry logowania do portalu HDI Azure](./media/r-server-hdinsight-manage/hdi-concurrent-users1.png)

- **Nazwa użytkownika logowania klastra**: użytkownik HTTP uwierzytelniany za pośrednictwem bramy HDInsight, która umożliwia ochronę utworzonych klastrów usługi HDInsight. Ten użytkownik HTTP jest używany do uzyskiwania dostępu do interfejsu użytkownika Apache Ambari, interfejsu użytkownika Apache Hadoop YARN, a także innych składników interfejsu użytkownika.
- **Nazwa użytkownika protokołu SSH (Secure Shell)**: użytkownik SSH zapewniający dostęp do klastra za pośrednictwem protokołu Secure Shell. Jest to użytkownik systemu Linux, który ma dostęp do wszystkich węzłów głównych, węzłów procesu roboczego oraz węzłów krawędzi. Pozwala to na korzystanie z dowolnego węzła klastra zdalnego za pomocą protokołu Secure Shell.

Wersja społeczności serwera R Studio używana w klastrze usług ML w programie HDInsight akceptuje tylko nazwę użytkownika i hasło systemu Linux jako mechanizm logowania. Przekazywanie tokenów nie jest obsługiwane. Tak, gdy próbujesz uzyskać dostęp do programu R Studio po raz pierwszy w klastrze usług ML, musisz zalogować się dwukrotnie.

- Najpierw zaloguj się przy użyciu poświadczeń użytkownika HTTP za pośrednictwem bramy HDInsight.

- Następnie użyj poświadczeń użytkownika SSH, aby zalogować się do RStudio.
  
Aktualnie podczas aprowizowania klastra usługi HDInsight można utworzyć tylko jedno konto użytkownika SSH. Tak więc, aby umożliwić wielu użytkownikom dostęp do klastra usług ML w programie HDInsight, należy utworzyć dodatkowych użytkowników w systemie Linux.

Ponieważ RStudio działa w węźle brzegowym klastra, w tym miejscu jest kilka kroków:

1. Logowanie się do węzła krawędzi za pomocą istniejącego użytkownika SSH
2. Dodaj użytkowników systemu Linux w węźle krawędzi
3. Przy pomocy utworzonego użytkownika możesz korzystać z programu RStudio Community

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Krok 1: Zaloguj się do węzła krawędzi za pomocą utworzonego użytkownika SSH

Postępuj zgodnie z instrukcjami podanymi w [aplikacji Connect to HDInsight (Apache Hadoop) przy użyciu funkcji SSH w](../hdinsight-hadoop-linux-use-ssh-unix.md) celu uzyskania dostępu do węzła krawędziowego. Adres węzła krawędzi dla klastra usług `CLUSTERNAME-ed-ssh.azurehdinsight.net`ML w programie HDInsight to .

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Krok 2. Dodawanie użytkowników systemu Linux w węźle krawędzi

Aby dodać użytkownika do węzła krawędzi, uruchom te polecenia:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Poniższy zrzut ekranu przedstawia wyjścia.

![zrzut ekranu wyjściowego równoczesnych użytkowników](./media/r-server-hdinsight-manage/hdi-concurrent-users2.png)

Po wyświetleniu monitu o podanie hasła "Bieżące hasło protokołu Kerberos:" wystarczy nacisnąć klawisz **Enter,** aby je zignorować. Podanie opcji `-m` w poleceniu `useradd` powoduje, że system utworzy folder macierzysty użytkownika, wymagany przez program RStudio Community.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Krok 3. Korzystanie z programu RStudio Community przy pomocy utworzonego użytkownika

Dostęp do RStudio z . `https://CLUSTERNAME.azurehdinsight.net/rstudio/` Jeśli logujesz się po raz pierwszy po utworzeniu klastra, wprowadź poświadczenia administratora klastra, a następnie poświadczenia użytkownika SSH, które zostały utworzone. Jeśli nie jest to twój pierwszy login, wprowadź tylko poświadczenia dla utworzonego użytkownika SSH.

Można również zalogować się przy użyciu oryginalnych poświadczeń (domyślnie jest *sshuser*) jednocześnie z innego okna przeglądarki.

Pamiętaj, że nowo dodani użytkownicy nie mają uprawnień użytkownika root w systemie Linux, ale mają takie same prawa dostępu do wszystkich plików w magazynie zdalnym HDFS i WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Zdalne łączenie się z usługami Microsoft ML

Dostęp do kontekstu obliczeniowego platformy HDInsight Spark można skonfigurować z zdalnego wystąpienia klienta ml uruchomionego na pulpicie. Aby to zrobić, należy określić opcje (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches i sshProfileScript) podczas definiowania kontekstu obliczeniowego RxSpark na pulpicie: Na przykład:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Aby uzyskać więcej informacji, zobacz sekcję "Korzystanie z serwera uczenia maszynowego firmy Microsoft jako klienta Apache Hadoop" w [sekcji Jak używać revoscaler w kontekście obliczeniowym Apache Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Używanie kontekstu obliczeniowego

Kontekst obliczeniowy pozwala określić, czy obliczenia są wykonywane lokalnie w węźle krawędzi czy są rozproszone w węzłach klastra usługi HDInsight.  Na przykład ustawiania kontekstu obliczeniowego za pomocą serwera RStudio, zobacz [Wykonywanie skryptu języka R w klastrze usług ML w usłudze Azure HDInsight przy użyciu serwera RStudio Server](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Dystrybucja kodu R do wielu węzłów

Za pomocą usług ML Services w programie HDInsight można wziąć istniejący kod `rxExec`języka R i uruchomić go w wielu węzłach w klastrze za pomocą programu . Funkcja ta jest przydatna podczas czyszczenia parametrów lub przeprowadzania symulacji. Poniższy kod przedstawia przykładowe użycie programu `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Jeśli nadal używasz kontekstu Platformy Spark, to polecenie zwraca wartość nazwy `(Sys.info()["nodename"])` węzła dla węzłów procesu roboczego, na których jest uruchomiony kod. Na przykład w klastrze z czterema węzłami oczekujesz, że otrzymasz dane wyjściowe podobne do następującego fragmentu kodu:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Dostęp do danych w Apache Hive i Parquet

Usługi HDInsight ML Services umożliwia bezpośredni dostęp do danych w uli i parkiet do użytku przez funkcje ScaleR w kontekście obliczeń platformy Spark. Te możliwości są dostępne za pomocą nowych funkcji źródła danych programu ScaleR o nazwie RxHiveData i RxParquetData, które używają kodu Spark SQL do ładowania danych bezpośrednio do elementów DataFrame aparatu Spark na potrzeby analizy przez program ScaleR.

Poniżej przedstawiono przykładowy kod korzystający z nowych funkcji:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Aby uzyskać dodatkowe informacje na temat korzystania z tych nowych `?RxHivedata` funkcji, zobacz pomoc online w usługach ML za pomocą poleceń i `?RxParquetData` poleceń.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalowanie dodatkowych pakietów R w klastrze

### <a name="to-install-r-packages-on-the-edge-node"></a>Aby zainstalować pakiety R w węźle brzegu

Jeśli chcesz zainstalować dodatkowe pakiety R w węźle brzega, możesz użyć `install.packages()` bezpośrednio z poziomu konsoli R, po podłączeniu do węzła krawędzi za pośrednictwem SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Aby zainstalować pakiety R w węźle procesu roboczego

Aby zainstalować pakiety języka R w węzłach procesu roboczego klastra, należy użyć akcji skryptu. Akcje skryptu to skrypty powłoki Bash używane do wprowadzania zmian w konfiguracji klastra usługi HDInsight lub instalowania dodatkowego oprogramowania, np. pakietów R. 

> [!IMPORTANT]  
> Dodatkowe pakiety R można zainstalować przy użyciu akcji skryptu dopiero po utworzeniu klastra. Nie należy używać tej procedury podczas tworzenia klastra, ponieważ skrypt opiera się na usługi ML jest całkowicie skonfigurowany.

1. Wykonaj kroki opisane w [obszarze Dostosowywanie klastrów przy użyciu akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

3. W przypadku **akcji Prześlij skrypt**podaj następujące informacje:

   * W przypadku **typu skryptu**wybierz opcję **Niestandardowe**.

   * W for **Name**podaj nazwę akcji skryptu.

     * W przypadku **identyfikatora URI skryptu Bash**wprowadź `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Jest to skrypt, który instaluje dodatkowe pakiety języka R w węźle procesu roboczego

   * Zaznacz to pole wyboru tylko dla **pracownika**.

   * **Parametry**: pakiety R do zainstalowania. Na przykład: `bitops stringr arules`

   * Zaznacz pole wyboru, **aby utrwalić tę akcję skryptu**.  

   > [!NOTE]
   > 1. Domyślnie wszystkie pakiety R są instalowane z migawki repozytorium MRAN firmy Microsoft zgodnej z zainstalowaną wersją serwera ML. Jeśli chcesz zainstalować nowsze wersje pakietów, musisz uwzględnić pewne ryzyko niezgodności. Jednak możesz to zrobić za pomocą parametru `useCRAN` użytego jako pierwszy element listy pakietów, na przykład `useCRAN bitops, stringr, arules`.  
   > 2. Niektóre pakiety R wymagają dodatkowych bibliotek systemu Linux. Dla wygody usługi HDInsight ML Services są fabrycznie zainstalowane z zależnościami wymaganymi przez 100 najpopularniejszych pakietów R. Jednak jeśli instalowane pakiety R wymagają jeszcze innych bibliotek, musisz pobrać skrypt podstawowy użyty tutaj i dodać kroki instalowania bibliotek systemowych. Następnie musisz przekazać zmodyfikowany skrypt do publicznego kontenera obiektów blob w usłudze Azure Storage i użyć zmodyfikowanego skryptu do zainstalowania pakietów.
   >    Aby uzyskać informacje na temat tworzenia akcji skryptu, zobacz [Script Action development](../hdinsight-hadoop-script-actions-linux.md) (Tworzenie akcji skryptu).

   ![Akcja przesyłania skryptu przez witrynę Azure portal](./media/r-server-hdinsight-manage/submit-script-action.png)

4. Wybierz polecenie **Utwórz**, aby uruchomić skrypt. Po zakończeniu działania skryptu pakiety R będą dostępne we wszystkich węzłach procesu roboczego.

## <a name="next-steps"></a>Następne kroki

* [Operationalize ML Services cluster on HDInsight (Obsługa operacji klastra usług ML w usłudze HDInsight)](r-server-operationalize.md)
* [Opcje kontekstu obliczania klastra usług ML w programie HDInsight](r-server-compute-contexts.md)
* [Azure Storage options for ML Services cluster on HDInsight (Opcje usługi Azure Storage dla klastra usług ML w usłudze HDInsight)](r-server-storage.md)
