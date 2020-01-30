---
title: Dostęp do Apache Hadoop dzienników aplikacji PRZĘDZy — Azure HDInsight
description: Dowiedz się, jak uzyskać dostęp do dzienników aplikacji PRZĘDZy w klastrze usługi HDInsight opartego na systemie Linux (Apache Hadoop) przy użyciu wiersza polecenia i przeglądarki sieci Web.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76764378"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Dostęp do Apache Hadoop dzienników aplikacji PRZĘDZy w usłudze HDInsight opartej na systemie Linux

Dowiedz się, jak uzyskać dostęp do dzienników [Apache HADOOP przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (a jeszcze inne) aplikacje w klastrze [Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Co to jest Apache PRZĘDZa?

PRZĘDZa obsługuje wiele modeli programistycznych ([Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) być jednym z nich) przez oddzielenie zarządzania zasobami od planowania/monitorowania aplikacji. PRZĘDZa *używa globalnego Menedżera zasobów (* RM) ( *NodeManagers* ) dla poszczególnych procesów roboczych (NMs) i *ApplicationMasters* dla aplikacji (AMs). Aplikacja negocjuje zasoby (procesor CPU, pamięć, dysk, Sieć) do uruchamiania aplikacji przy użyciu Menedżera zasobów. Menedżer zasobów współpracuje z usługą NMs, aby przyznać te zasoby, które są przyznawane jako *kontenery*. AM jest odpowiedzialny za śledzenie postępu kontenerów przypisanych do niego przez Menedżera zasobów. Aplikacja może wymagać wielu kontenerów w zależności od rodzaju aplikacji.

Każda aplikacja może składać się z wielu *prób aplikacji*. Jeśli aplikacja się nie powiedzie, może być ponowiona nowa próba. Każda próba przebiega w kontenerze. W sensie kontener zawiera kontekst dla podstawowej jednostki pracy wykonanej przez aplikację PRZĘDZenia. Wszystkie prace, które są wykonywane w kontekście kontenera, są wykonywane w jednym węźle procesu roboczego, na którym został przydzielony kontener. Zobacz [Hadoop: pisanie aplikacji przędzy](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)lub [Apache Hadoop przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) , aby uzyskać dalsze informacje.

Aby skalować klaster w celu obsługi większej przepływności przetwarzania, możesz użyć funkcji [automatycznego skalowania](hdinsight-autoscale-clusters.md) lub [ręcznie skalować swoje klastry przy użyciu kilku różnych języków](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>Serwer osi czasu PRZĘDZy

[Serwer osi czasu przędzy Apache Hadoop](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) dostarcza ogólnych informacji o ukończonych aplikacjach

Serwer osi czasu PRZĘDZy obejmuje następujący typ danych:

* Identyfikator aplikacji, unikatowy identyfikator aplikacji
* Użytkownik, który uruchomił aplikację
* Informacje o próbach wykonania aplikacji
* Kontenery używane przez daną próbkę aplikacji

## <a name="yarn-applications-and-logs"></a>Aplikacje i dzienniki PRZĘDZy

PRZĘDZa obsługuje wiele modeli programistycznych ([Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) być jednym z nich) przez oddzielenie zarządzania zasobami od planowania/monitorowania aplikacji. PRZĘDZa *używa globalnego Menedżera zasobów (* RM) ( *NodeManagers* ) dla poszczególnych procesów roboczych (NMs) i *ApplicationMasters* dla aplikacji (AMs). Aplikacja negocjuje zasoby (procesor CPU, pamięć, dysk, Sieć) do uruchamiania aplikacji przy użyciu Menedżera zasobów. Menedżer zasobów współpracuje z usługą NMs, aby przyznać te zasoby, które są przyznawane jako *kontenery*. AM jest odpowiedzialny za śledzenie postępu kontenerów przypisanych do niego przez Menedżera zasobów. Aplikacja może wymagać wielu kontenerów w zależności od rodzaju aplikacji.

Każda aplikacja może składać się z wielu *prób aplikacji*. Jeśli aplikacja się nie powiedzie, może być ponowiona nowa próba. Każda próba przebiega w kontenerze. W sensie kontener zawiera kontekst dla podstawowej jednostki pracy wykonanej przez aplikację PRZĘDZenia. Wszystkie prace, które są wykonywane w kontekście kontenera, są wykonywane w jednym węźle procesu roboczego, na którym został przydzielony kontener. Więcej informacji można znaleźć w temacie [Apache Hadoop koncepcje związane z przędzą](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) .

Dzienniki aplikacji (i skojarzone z nimi dzienniki kontenerów) są krytyczne w debugowaniu problemów z aplikacjami usługi Hadoop. PRZĘDZa to świetna platforma służąca do gromadzenia, agregowania i przechowywania dzienników aplikacji przy użyciu funkcji [agregacji dzienników](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) . Funkcja agregacji dzienników ułatwia uzyskiwanie dostępu do dzienników aplikacji. Agreguje dzienniki we wszystkich kontenerach w węźle procesu roboczego i zapisuje je jako jeden zagregowany plik dziennika na węzeł procesu roboczego. Dziennik jest przechowywany w domyślnym systemie plików po zakończeniu aplikacji. Twoja aplikacja może korzystać z setek lub tysięcy kontenerów, ale dzienniki dla wszystkich kontenerów uruchomionych w jednym węźle procesu roboczego są zawsze agregowane do pojedynczego pliku. W związku z tym w aplikacji jest używany tylko 1 dziennik na węzeł procesu roboczego. Agregacja dzienników jest domyślnie włączona w klastrach usługi HDInsight w wersji 3,0 i nowszych. Zagregowane dzienniki znajdują się w domyślnym magazynie klastra. Następująca ścieżka jest ścieżką systemu plików HDFS do dzienników:

```
/app-logs/<user>/logs/<applicationId>
```

W ścieżce `user` to nazwa użytkownika, który uruchomił aplikację. `applicationId` jest unikatowym identyfikatorem przypisanym do aplikacji przez przydzieloną RM.

Zagregowane dzienniki nie są odczytywane bezpośrednio, ponieważ są zapisywane w [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf) [formacie binarnym](https://issues.apache.org/jira/browse/HADOOP-3315) indeksowanym przez kontener. Użyj dzienników ResourceManager lub narzędzi interfejsu wiersza polecenia, aby wyświetlić te dzienniki jako zwykły tekst dla aplikacji lub kontenerów zainteresowania.

## <a name="yarn-logs-in-an-esp-cluster"></a>Dzienniki przędzy w klastrze ESP

Dwie konfiguracje należy dodać do `mapred-site` niestandardowej w Ambari.

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net`, gdzie `CLUSTERNAME` jest nazwą klastra.

1. W interfejsie użytkownika Ambari przejdź do **MapReduce2** > **configs** > **Advanced** > **Custom mapred-site**.

1. Dodaj *jeden* z następujących zestawów właściwości:

    **Ustaw 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Ustaw 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Zapisz zmiany i ponownie uruchom wszystkie usługi, których to dotyczy.

## <a name="yarn-cli-tools"></a>Narzędzia interfejsu wiersza polecenia dla PRZĘDZy

1. Użyj [polecenia SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Wyświetl listę wszystkich identyfikatorów aplikacji aktualnie uruchomionych aplikacji przędzy przy użyciu następującego polecenia:

    ```bash
    yarn top
    ```

    Zanotuj identyfikator aplikacji z kolumny `APPLICATIONID`, której dzienniki mają być pobierane.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Możesz wyświetlić te dzienniki jako zwykły tekst, uruchamiając jedno z następujących poleceń:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Określ > &lt;identyfikatora aplikacji, &lt;użytkowników, którzy uruchomili > aplikacji, &lt;containerId >, i &lt;informacje o >ch roboczych-Node-Address, gdy uruchamiają te polecenia.

### <a name="other-sample-commands"></a>Inne przykładowe polecenia

1. Pobierz dzienniki kontenerów przędzy dla wszystkich wzorców aplikacji przy użyciu poniższego polecenia. Spowoduje to utworzenie pliku dziennika o nazwie `amlogs.txt` w formacie tekstowym.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Pobierz dzienniki kontenerów przędzy tylko dla najnowszego wzorca aplikacji przy użyciu następującego polecenia:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Pobierz dzienniki kontenerów PRZĘDZy dla pierwszych dwóch wzorców aplikacji przy użyciu następującego polecenia:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Pobierz wszystkie dzienniki kontenerów przędzy za pomocą następującego polecenia:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Pobierz dziennik kontenera przędzy dla określonego kontenera za pomocą następującego polecenia:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>Interfejs użytkownika narzędzia ResourceManager

Interfejs użytkownika programu ResourceManager jest uruchamiany w klastrze węzła głównego. Dostęp do niego odbywa się za pomocą interfejsu użytkownika sieci Web Ambari. Aby wyświetlić dzienniki PRZĘDZy, wykonaj następujące kroki:

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net`. Zastąp wartość CLUSTERname nazwą klastra usługi HDInsight.

2. Z listy usług po lewej stronie wybierz pozycję **przędza**.

    ![Wybrano usługę Apache Ambari przędzę](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Z listy rozwijanej **szybkie linki** wybierz jeden z węzłów głównych klastra, a następnie wybierz pozycję **Dziennik ResourceManager**.

    ![Szybkie linki do przędzy w usłudze Apache Ambari](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Zostanie wyświetlona lista linków do dzienników PRZĘDZy.
