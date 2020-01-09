---
title: Dostęp do Apache Hadoop dzienników aplikacji PRZĘDZy — Azure HDInsight
description: Dowiedz się, jak uzyskać dostęp do dzienników aplikacji PRZĘDZy w klastrze usługi HDInsight opartego na systemie Linux (Apache Hadoop) przy użyciu wiersza polecenia i przeglądarki sieci Web.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 437a0c95ea4b48baa74bf6a577dc06429833bc31
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644583"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Dostęp do Apache Hadoop dzienników aplikacji PRZĘDZy w usłudze HDInsight opartej na systemie Linux

Dowiedz się, jak uzyskać dostęp do dzienników [Apache HADOOP przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (a jeszcze inne) aplikacje w klastrze [Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Co to jest Apache PRZĘDZa?

PRZĘDZa obsługuje wiele modeli programistycznych ([Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) być jednym z nich) przez oddzielenie zarządzania zasobami od planowania/monitorowania aplikacji. PRZĘDZa *używa globalnego Menedżera zasobów (* RM) ( *NodeManagers* ) dla poszczególnych procesów roboczych (NMs) i *ApplicationMasters* dla aplikacji (AMs). Aplikacja negocjuje zasoby (procesor CPU, pamięć, dysk, Sieć) do uruchamiania aplikacji przy użyciu Menedżera zasobów. Menedżer zasobów współpracuje z usługą NMs, aby przyznać te zasoby, które są przyznawane jako *kontenery*. AM jest odpowiedzialny za śledzenie postępu kontenerów przypisanych do niego przez Menedżera zasobów. Aplikacja może wymagać wielu kontenerów w zależności od rodzaju aplikacji.

Każda aplikacja może składać się z wielu *prób aplikacji*. Jeśli aplikacja się nie powiedzie, może być ponowiona nowa próba. Każda próba przebiega w kontenerze. W sensie kontener zawiera kontekst dla podstawowej jednostki pracy wykonanej przez aplikację PRZĘDZenia. Wszystkie prace, które są wykonywane w kontekście kontenera, są wykonywane w jednym węźle procesu roboczego, na którym został przydzielony kontener. Zobacz [Hadoop: pisanie aplikacji przędzy](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)lub [Apache Hadoop przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) , aby uzyskać dalsze informacje.

Aby skalować klaster w celu obsługi większej przepływności przetwarzania, możesz użyć funkcji [automatycznego skalowania](hdinsight-autoscale-clusters.md) lub [ręcznie skalować swoje klastry przy użyciu kilku różnych języków](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="YARNTimelineServer"></a>Serwer osi czasu PRZĘDZy

[Serwer osi czasu przędzy Apache Hadoop](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) dostarcza ogólnych informacji o ukończonych aplikacjach

Serwer osi czasu PRZĘDZy obejmuje następujący typ danych:

* Identyfikator aplikacji, unikatowy identyfikator aplikacji
* Użytkownik, który uruchomił aplikację
* Informacje o próbach wykonania aplikacji
* Kontenery używane przez daną próbkę aplikacji

## <a name="YARNAppsAndLogs"></a>Aplikacje i dzienniki PRZĘDZy

PRZĘDZa obsługuje wiele modeli programistycznych ([Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) być jednym z nich) przez oddzielenie zarządzania zasobami od planowania/monitorowania aplikacji. PRZĘDZa *używa globalnego Menedżera zasobów (* RM) ( *NodeManagers* ) dla poszczególnych procesów roboczych (NMs) i *ApplicationMasters* dla aplikacji (AMs). Aplikacja negocjuje zasoby (procesor CPU, pamięć, dysk, Sieć) do uruchamiania aplikacji przy użyciu Menedżera zasobów. Menedżer zasobów współpracuje z usługą NMs, aby przyznać te zasoby, które są przyznawane jako *kontenery*. AM jest odpowiedzialny za śledzenie postępu kontenerów przypisanych do niego przez Menedżera zasobów. Aplikacja może wymagać wielu kontenerów w zależności od rodzaju aplikacji.

Każda aplikacja może składać się z wielu *prób aplikacji*. Jeśli aplikacja się nie powiedzie, może być ponowiona nowa próba. Każda próba przebiega w kontenerze. W sensie kontener zawiera kontekst dla podstawowej jednostki pracy wykonanej przez aplikację PRZĘDZenia. Wszystkie prace, które są wykonywane w kontekście kontenera, są wykonywane w jednym węźle procesu roboczego, na którym został przydzielony kontener. Więcej informacji można znaleźć w temacie [Apache Hadoop koncepcje związane z przędzą](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) .

Dzienniki aplikacji (i skojarzone z nimi dzienniki kontenerów) są krytyczne w debugowaniu problemów z aplikacjami usługi Hadoop. PRZĘDZa to świetna platforma służąca do gromadzenia, agregowania i przechowywania dzienników aplikacji przy użyciu funkcji [agregacji dzienników](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) . Funkcja agregacji dzienników ułatwia uzyskiwanie dostępu do dzienników aplikacji. Agreguje dzienniki we wszystkich kontenerach w węźle procesu roboczego i zapisuje je jako jeden zagregowany plik dziennika na węzeł procesu roboczego. Dziennik jest przechowywany w domyślnym systemie plików po zakończeniu aplikacji. Twoja aplikacja może korzystać z setek lub tysięcy kontenerów, ale dzienniki dla wszystkich kontenerów uruchomionych w jednym węźle procesu roboczego są zawsze agregowane do pojedynczego pliku. W związku z tym w aplikacji jest używany tylko 1 dziennik na węzeł procesu roboczego. Agregacja dzienników jest domyślnie włączona w klastrach usługi HDInsight w wersji 3,0 i nowszych. Zagregowane dzienniki znajdują się w domyślnym magazynie klastra. Następująca ścieżka jest ścieżką systemu plików HDFS do dzienników:

    /app-logs/<user>/logs/<applicationId>

W ścieżce `user` to nazwa użytkownika, który uruchomił aplikację. `applicationId` jest unikatowym identyfikatorem przypisanym do aplikacji przez przydzieloną RM.

Zagregowane dzienniki nie są odczytywane bezpośrednio, ponieważ są zapisywane w [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf) [formacie binarnym](https://issues.apache.org/jira/browse/HADOOP-3315) indeksowanym przez kontener. Użyj dzienników ResourceManager lub narzędzi interfejsu wiersza polecenia, aby wyświetlić te dzienniki jako zwykły tekst dla aplikacji lub kontenerów zainteresowania.

## <a name="yarn-cli-tools"></a>Narzędzia interfejsu wiersza polecenia dla PRZĘDZy

Aby korzystać z narzędzi interfejsu wiersza polecenia, należy najpierw połączyć się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Możesz wyświetlić te dzienniki jako zwykły tekst, uruchamiając jedno z następujących poleceń:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Określ > &lt;identyfikatora aplikacji, &lt;użytkowników, którzy uruchomili > aplikacji, &lt;containerId >, i &lt;informacje o >ch roboczych-Node-Address, gdy uruchamiają te polecenia.

## <a name="yarn-resourcemanager-ui"></a>Interfejs użytkownika narzędzia ResourceManager

Interfejs użytkownika programu ResourceManager jest uruchamiany w klastrze węzła głównego. Dostęp do niego odbywa się za pomocą interfejsu użytkownika sieci Web Ambari. Aby wyświetlić dzienniki PRZĘDZy, wykonaj następujące kroki:

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net`. Zastąp wartość CLUSTERname nazwą klastra usługi HDInsight.
2. Z listy usług po lewej stronie wybierz pozycję **przędza**.

    ![Wybrano usługę Apache Ambari przędzę](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Z listy rozwijanej **szybkie linki** wybierz jeden z węzłów głównych klastra, a następnie wybierz pozycję **Dziennik ResourceManager**.

    ![Szybkie linki do przędzy w usłudze Apache Ambari](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Zostanie wyświetlona lista linków do dzienników PRZĘDZy.
