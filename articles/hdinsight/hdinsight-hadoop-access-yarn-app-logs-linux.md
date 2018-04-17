---
title: Dzienniki aplikacji Hadoop YARN dostępu w usłudze HDInsight opartych na systemie Linux - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostępu do dzienników aplikacji YARN w klastrze opartych na systemie Linux usługą HDInsight (Hadoop) przy użyciu wiersza polecenia i przeglądarki sieci web.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: larryfr
ms.openlocfilehash: 67732b3a4c63408d8fc6ab5ed75b40a0eb043167
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Dzienniki aplikacji YARN dostępu w usłudze HDInsight z systemem Linux

Dowiedz się, jak dostępu do dzienników YARN (jeszcze inny zasób moduł negocjowania) aplikacji na klastra usługi Hadoop w usłudze Azure HDInsight.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składnika usługi HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN osi czasu serwera

[YARN osi czasu serwera](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) zawiera ogólne informacje dotyczące aplikacji ukończone i informacje o określonej struktury aplikacji za pomocą dwóch różnych interfejsów. W szczególności:

* Przechowywanie i pobieranie informacji o aplikacji ogólnego w klastrach HDInsight została włączona w wersji 3.1.1.374 lub nowszej.
* Składnik informacji określonej struktury aplikacji serwera oś czasu nie jest aktualnie dostępny w klastrach usługi HDInsight.

Ogólne informacje na temat aplikacji zawiera następujący typ danych:

* Identyfikator aplikacji, unikatowy identyfikator aplikacji
* Użytkownik, który uruchomił aplikację
* Informacje o podjętych w celu wykonania aplikacji
* Kontenery używane przez wszystkie próby danej aplikacji

## <a name="YARNAppsAndLogs"></a>Dzienniki YARN aplikacji i

YARN obsługuje wiele modele programowania (MapReduce jest jednym z nich) dzięki rozdzieleniu zarządzanie zasobami z planowania/monitorowania aplikacji. YARN używa globalnym *ResourceManager* (RM) na węzła procesu roboczego *NodeManagers* (NMs) i dla każdej aplikacji *ApplicationMasters* (AMs). AM poszczególnych aplikacji negocjuje zasobów (Procesora, pamięci, dysku, sieci) do uruchamiania aplikacji z Menedżera zasobów. Menedżer zasobów współpracuje z NMs udzielenia tych zasobów, które są przyznawane jako *kontenery*. AM jest odpowiedzialny za śledzenie postępu kontenery przypisane do niej przez Menedżera zasobów. Aplikacja może wymagać wiele kontenerów w zależności od charakteru aplikacji.

Każda aplikacja może składać się z wielu *prób aplikacji*. Jeśli aplikacja nie powiedzie się, może zostać powtórzone, jako nowego próba. Każda próba uruchamia się w kontenerze. W tym sensie kontener udostępnia kontekst dla podstawowa jednostka pracy wykonanej przez aplikację YARN. Wszystkie pracy, którą można wykonać w ramach kontenera odbywa się w węźle pojedynczego procesu roboczego, na którym została przydzielona kontenera. Zobacz [pojęcia YARN] [ YARN-concepts] dla dalszego odwołania.

Dzienniki aplikacji (oraz Dzienniki skojarzone kontenera) są kluczowe w debugowaniu problematyczne aplikacje platformy Hadoop. YARN umożliwia nieuprzywilejowany framework do zbierania, agregację i przechowywania Dzienniki aplikacji z [agregacji dziennika] [ log-aggregation] funkcji. Funkcja agregacji dziennika umożliwia teraz bardziej przewidywalna podczas uzyskiwania dostępu do dzienników aplikacji. Go łączy dzienników wszystkich kontenerów w węźle procesu roboczego i przechowuje je jako jeden zagregowany plik dziennika na węzła procesu roboczego. Dziennik jest przechowywana na domyślny system plików, po zakończeniu działania aplikacji. Aplikacja może korzystać z setkami lub tysiącami kontenerów, ale dzienniki dla wszystkich kontenerów uruchomiona w węźle pojedynczego procesu roboczego zawsze są agregowane w jednym pliku. To jest tylko 1 dziennika w każdym węźle procesu roboczego używany przez aplikację. Agregacja dziennika jest domyślnie włączone w klastrach usługi HDInsight w wersji 3.0 lub nowszym. Zagregowane Dzienniki znajdują się w domyślny magazyn dla klastra. Następująca ścieżka jest ścieżka systemu plików HDFS w dziennikach:

    /app-logs/<user>/logs/<applicationId>

W ścieżce `user` to nazwa użytkownika, który uruchomił aplikację. `applicationId` Jest unikatowym identyfikatorem przypisany do aplikacji przez YARN Menedżera zasobów.

Dzienniki zagregowane nie mogą bezpośrednio do odczytu, ponieważ są one zapisywane [TFile][T-file], [format binarny] [ binary-format] indeksowane według kontenera. Umożliwia wyświetlanie dzienników jako zwykły tekst dla aplikacji lub kontenery odsetek w dzienników YARN ResourceManager lub narzędzi interfejsu wiersza polecenia.

## <a name="yarn-cli-tools"></a>Narzędzia YARN interfejsu wiersza polecenia

Użyj narzędzi interfejsu wiersza polecenia YARN, możesz nawiązać klastra usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Dzienniki te można wyświetlić jako zwykły tekst, uruchamiając jeden z następujących poleceń:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Określ &lt;applicationId >, &lt;-który — uruchomiona — aplikacji użytkownika >, &lt;identyfikatora kontenera >, i &lt;adres przypisany węzła procesu roboczego > informacje po uruchomieniu tych poleceń.

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

Interfejs użytkownika YARN ResourceManager działa na headnode klastra. Jest on dostępny za pośrednictwem interfejsu użytkownika sieci web Ambari. Aby wyświetlić dzienniki YARN, wykonaj następujące kroki:

1. W przeglądarce sieci web, przejdź do https://CLUSTERNAME.azurehdinsight.net. Zamień na nazwę klastra usługi HDInsight CLUSTERNAME.
2. Wybierz z listy usług po lewej stronie, **YARN**.

    ![Wybrana usługa yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Z **szybkie linki** listy rozwijanej wybierz jedną z głównymi węzłami klastra, a następnie wybierz **dziennika ResourceManager**.

    ![Yarn szybkie linki](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Jest wyświetlana lista linków do dzienników YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
