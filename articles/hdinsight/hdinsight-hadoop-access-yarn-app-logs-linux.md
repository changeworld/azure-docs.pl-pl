---
title: Dostęp do usługi Apache Hadoop YARN application loguje HDInsight opartych na systemie Linux — Azure
description: Dowiedz się, jak dostęp do dzienników aplikacji usługi YARN w klastrze HDInsight opartych na systemie Linux (Apache Hadoop) przy użyciu wiersza polecenia i przeglądarki sieci web.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: c0c5ecfba97c61288d08681006645eab0bdd23f2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059464"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Dostęp do usługi Apache Hadoop YARN application loguje HDInsight opartych na systemie Linux

Dowiedz się, jak dzienniki Aby uzyskać dostęp do [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) aplikacji (jeszcze inny zasób moduł negocjowania) na [Apache Hadoop](https://hadoop.apache.org/) klastra w usłudze Azure HDInsight.

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) zawiera ogólne informacje dotyczące ukończonych aplikacji

YARN Timeline Server zawiera następujący typ danych:

* Identyfikator aplikacji, unikatowy identyfikator aplikacji
* Użytkownik, który uruchomił aplikację
* Informacje o podjętych w celu wykonania aplikacji
* Kontenery posługują się wszelkie próby danej aplikacji

## <a name="YARNAppsAndLogs"></a>Dzienniki aplikacji usługi YARN i

YARN obsługuje wiele modeli programowania ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) jest jeden z nich) dzięki rozdzieleniu zarządzania zasobami z planowania/monitorowania aplikacji. YARN używa globalną *ResourceManager* (MB), każdy proces roboczy — węzeł *NodeManagers* (NMs) i poszczególnych aplikacji *ApplicationMasters* (AMs). AM poszczególnych aplikacji negocjuje zasobów (procesor CPU, pamięci, dysku, użycia sieci) do uruchamiania Twojej aplikacji za pomocą Menedżera zasobów. Menedżer zasobów działa z NMs, aby udzielić tych zasobów, które są przyznawane jako *kontenery*. AM jest odpowiedzialny za monitorowanie postępu kontenerów do niej przypisany, Menedżera zasobów. Aplikacja może wymagać wiele kontenerów, w zależności od rodzaju aplikacji.

Każda aplikacja może składać się z wielu *prób aplikacji*. Aplikacja zakończy się niepowodzeniem, może ponowiony po wybraniu nowej próby. Każda próba jest uruchamiany w kontenerze. W tym sensie kontener udostępnia kontekst dla podstawowa jednostka pracy wykonanej przez aplikacji usługi YARN. Cała praca, którą można wykonać tylko w kontekście kontenera odbywa się w węźle pojedynczego procesu roboczego, na którym została przydzielona kontenera. Zobacz [Apache Hadoop YARN pojęcia](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) dalsze odwołania.

Dzienniki aplikacji (i dzienniki skojarzony kontener) są krytyczne w debugowaniu aplikacji platformy Hadoop innych problematyczne. YARN umożliwia nieuprzywilejowany umożliwiająca zbieranie, agregując i przechowywania dzienników aplikacji za pomocą [agregacji dziennika](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) funkcji. Funkcja agregacji dziennika umożliwia uzyskiwanie dostępu do dzienników aplikacji bardziej. Ona agreguje dzienników dla wszystkich kontenerów na węzeł procesu roboczego i przechowuje je w postaci jednego pliku dziennika zagregowane na węzeł procesu roboczego. Dziennik jest przechowywany na domyślny system plików, po zakończeniu działania aplikacji. Aplikacja może używać setek lub tysięcy kontenerów, ale dzienników dla wszystkich kontenerów, uruchom w węźle pojedynczego procesu roboczego zawsze są agregowane do pojedynczego pliku. Dlatego jest tylko 1 dziennika na węzeł procesu roboczego używanych przez aplikację. Agregacja dziennik jest domyślnie włączona, klastrów HDInsight w wersji 3.0 lub nowszej. Zagregowane Dzienniki znajdują się w domyślny magazyn dla klastra. Następująca ścieżka jest ścieżka systemu plików HDFS w dziennikach:

    /app-logs/<user>/logs/<applicationId>

W ścieżce `user` to nazwa użytkownika, który uruchomił aplikację. `applicationId` To unikatowy identyfikator przypisany do aplikacji przez Menedżera zasobów usługi YARN.

Zagregowane dzienniki nie są bezpośrednio do odczytu, ponieważ są one zapisywane [TFile][T-file], [format binarny] [ binary-format] indeksowane przez kontener. Użyj dzienników YARN ResourceManager lub narzędzi interfejsu wiersza polecenia, aby wyświetlić te dzienniki jako zwykły tekst dla aplikacji lub kontenerów zainteresowania.

## <a name="yarn-cli-tools"></a>Narzędzia interfejsu wiersza polecenia usługi YARN

Do korzystania z narzędzi interfejsu wiersza polecenia usługi YARN, musi najpierw połączyć się z klastrem HDInsight przy użyciu protokołu SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Te dzienniki można wyświetlić jako zwykły tekst, uruchamiając jedno z następujących poleceń:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Określ &lt;applicationId >, &lt;— który pracy — aplikacji użytkownika >, &lt;containerId >, a &lt;adres w przypadku węzła procesu roboczego > informacje po uruchomieniu tych poleceń.

## <a name="yarn-resourcemanager-ui"></a>Interfejsie użytkownika YARN ResourceManager

Interfejsie użytkownika YARN ResourceManager działa na głównym węzłem klastra. Jest on dostępny za pośrednictwem interfejsu użytkownika sieci web Ambari. Aby wyświetlić dzienniki usługi YARN, wykonaj następujące kroki:

1. W przeglądarce internetowej przejdź do https://CLUSTERNAME.azurehdinsight.net. Zastąp CLUSTERNAME nazwą klastra usługi HDInsight.
2. Z listy usług po lewej stronie wybierz **YARN**.

    ![Wybranej usługi yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Z **szybkich łączy** listy rozwijanej wybierz jedną z głównymi węzłami klastra, a następnie wybierz pozycję **dziennika ResourceManager**.

    ![Szybkie linki yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Zostanie wyświetlona lista linków do dzienniki usługi YARN.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
