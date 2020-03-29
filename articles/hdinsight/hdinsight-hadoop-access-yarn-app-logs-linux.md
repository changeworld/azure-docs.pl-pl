---
title: Dostęp do dzienników aplikacji Apache Hadoop YARN — Azure HDInsight
description: Dowiedz się, jak uzyskać dostęp do dzienników aplikacji YARN w klastrze HDInsight (Apache Hadoop) opartym na systemie Linux przy użyciu wiersza polecenia i przeglądarki internetowej.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764378"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Dostęp do dzienników aplikacji Apache Hadoop YARN na bazie Linux HDInsight

Dowiedz się, jak uzyskać dostęp do dzienników aplikacji [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) w klastrze [Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Co to jest Apache YARN?

YARN obsługuje wiele modeli programowania[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) jest jednym z nich) przez oddzielenie zarządzania zasobami od planowania/monitorowania aplikacji. YARN używa globalnego *ResourceManager* (RM), na węzeł-węzeł *NodeManagers* (NMs) i dla aplikacji *ApplicationMasters* (AMs). Am dla aplikacji negocjuje zasoby (procesora CPU, pamięci, dysku, sieci) do uruchamiania aplikacji z RM. Program RM współpracuje z plikami NM w celu przyznania tych zasobów, które są przyznawane jako *kontenery*. Am jest odpowiedzialny za śledzenie postępu kontenerów przypisanych do niego przez RM. Aplikacja może wymagać wielu kontenerów w zależności od charakteru aplikacji.

Każda aplikacja może składać się z wielu *prób aplikacji.* Jeśli aplikacja nie powiedzie się, może zostać ponowiona jako nowa próba. Każda próba jest uruchamiana w kontenerze. W pewnym sensie kontener zapewnia kontekst dla podstawowej jednostki pracy wykonywanej przez aplikację YARN. Cała praca wykonywana w kontekście kontenera jest wykonywana w węźle pojedynczego procesu roboczego, na którym przydzielono kontener. Zobacz [Hadoop: Pisanie aplikacji YARN](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)lub [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) dla dalszego odniesienia.

Aby skalować klaster w celu obsługi większej przepływności przetwarzania, można użyć [skalowania automatycznego](hdinsight-autoscale-clusters.md) lub [skalowania klastrów ręcznie przy użyciu kilku różnych języków](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>Serwer osi czasu YARN

[Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) dostarcza ogólnych informacji na temat ukończonych aplikacji

Serwer osi czasu YARN zawiera następujący typ danych:

* Identyfikator aplikacji, unikatowy identyfikator aplikacji
* Użytkownik, który uruchomił aplikację
* Informacje o próbach wypełnienia wniosku
* Pojemniki używane przez każdą próbę zastosowania

## <a name="yarn-applications-and-logs"></a>Aplikacje i dzienniki przędzy

YARN obsługuje wiele modeli programowania[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) jest jednym z nich) przez oddzielenie zarządzania zasobami od planowania/monitorowania aplikacji. YARN używa globalnego *ResourceManager* (RM), na węzeł-węzeł *NodeManagers* (NMs) i dla aplikacji *ApplicationMasters* (AMs). Am dla aplikacji negocjuje zasoby (procesora CPU, pamięci, dysku, sieci) do uruchamiania aplikacji z RM. Program RM współpracuje z plikami NM w celu przyznania tych zasobów, które są przyznawane jako *kontenery*. Am jest odpowiedzialny za śledzenie postępu kontenerów przypisanych do niego przez RM. Aplikacja może wymagać wielu kontenerów w zależności od charakteru aplikacji.

Każda aplikacja może składać się z wielu *prób aplikacji.* Jeśli aplikacja nie powiedzie się, może zostać ponowiona jako nowa próba. Każda próba jest uruchamiana w kontenerze. W pewnym sensie kontener zapewnia kontekst dla podstawowej jednostki pracy wykonywanej przez aplikację YARN. Cała praca wykonywana w kontekście kontenera jest wykonywana w węźle pojedynczego procesu roboczego, na którym przydzielono kontener. Zobacz [Apache Hadoop YARN Concepts](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) do dalszego odniesienia.

Dzienniki aplikacji (i skojarzone dzienniki kontenerów) są krytyczne w debugowaniu problematycznych aplikacji Hadoop. YARN zapewnia dobrą platformę do zbierania, agregowania i przechowywania dzienników aplikacji za pomocą funkcji [agregacji dziennika.](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) Funkcja agregacji dziennika sprawia, że dostęp do dzienników aplikacji jest bardziej deterministyczny. Agreguje dzienniki we wszystkich kontenerach w węźle procesu roboczego i przechowuje je jako jeden zagregowany plik dziennika na węzeł procesu roboczego. Dziennik jest przechowywany w domyślnym systemie plików po zakończeniu aplikacji. Aplikacja może używać setek lub tysięcy kontenerów, ale dzienniki dla wszystkich kontenerów uruchamianych w węźle procesu roboczego są zawsze agregowane do pojedynczego pliku. Więc istnieje tylko 1 dziennik na węzeł procesu roboczego używane przez aplikację. Agregacja dzienników jest domyślnie włączona w klastrach HDInsight w wersji 3.0 lub wyższej. Zagregowane dzienniki znajdują się w domyślnym magazynie dla klastra. Następująca ścieżka to ścieżka HDFS do dzienników:

```
/app-logs/<user>/logs/<applicationId>
```

W ścieżce `user` jest nazwa użytkownika, który uruchomił aplikację. Jest `applicationId` unikatowy identyfikator przypisany do aplikacji przez YARN RM.

Zagregowane dzienniki nie są bezpośrednio czytelne, ponieważ są one zapisywane w [pliku TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [formacie binarnym](https://issues.apache.org/jira/browse/HADOOP-3315) indeksowanym przez kontener. Użyj dzienników Menedżera zasobów YARN lub narzędzi interfejsu wiersza polecenia, aby wyświetlić te dzienniki jako zwykły tekst dla aplikacji lub kontenerów zainteresowania.

## <a name="yarn-logs-in-an-esp-cluster"></a>Dzienniki przędzy w klastrze ESP

Dwie konfiguracje muszą zostać `mapred-site` dodane do niestandardowego w Ambari.

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. W interfejsie użytkownika Ambari przejdź do **mapreduce2** > **Configs** > **Advanced** > Custom**mapred-site**.

1. Dodaj *jeden* z następujących zestawów właściwości:

    **Zestaw 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Zestaw 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Zapisz zmiany i uruchom ponownie wszystkie usługi, których dotyczy problem.

## <a name="yarn-cli-tools"></a>Narzędzia CLI przędzy

1. Użyj [polecenia ssh,](./hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Wyświetl listę wszystkich identyfikatorów aplikacji aktualnie uruchomionych aplikacji przędzy za pomocą następującego polecenia:

    ```bash
    yarn top
    ```

    Zwróć uwagę na identyfikator `APPLICATIONID` aplikacji z kolumny, której dzienniki mają zostać pobrane.

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

1. Dzienniki te można wyświetlić jako zwykły tekst, uruchamiając jedno z następujących poleceń:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Określ &lt;> &lt;>,>> użytkownika, &lt;> identyfikatora kontenera i &lt;> informacji o>>.

### <a name="other-sample-commands"></a>Inne przykładowe polecenia

1. Pobierz dzienniki kontenerów przędzy dla wszystkich wzorców aplikacji za pomocą poniższego polecenia. Spowoduje to utworzenie pliku `amlogs.txt` dziennika o nazwie w formacie tekstowym.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Pobierz dzienniki kontenerów przędzy tylko dla najnowszego wzorca aplikacji za pomocą następującego polecenia:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Pobierz dzienniki kontenerów YARN dla pierwszych dwóch wzorców aplikacji za pomocą następującego polecenia:

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

## <a name="yarn-resourcemanager-ui"></a>Interfejs użytkownika menedżera zasobów YARN

Interfejs użytkownika YARN ResourceManager działa na centrali klastra. Jest on dostępny za pośrednictwem interfejsu użytkownika sieci Ambari. Aby wyświetlić dzienniki YARN, należy wykonać następujące czynności:

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź do pliku . Zastąp CLUSTERNAME nazwą klastra usługi HDInsight:

2. Z listy usług po lewej stronie wybierz pozycję **YARN**.

    ![Apache Ambari Wybrano usługę przędzy](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Z listy rozwijanej **Szybkie łącza** wybierz jeden z węzłów głównego klastra, a następnie wybierz pozycję Dziennik **menedżera zasobów**.

    ![Apache Ambari Przędza szybkie linki](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Zostanie wyświetlona lista linków do dzienników YARN.
