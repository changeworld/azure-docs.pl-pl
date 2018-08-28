---
title: Access Hadoop YARN application programowo - dzienników platformy Azure
description: Dostęp do aplikacji programowo rejestruje się w klastrze usługi Hadoop w HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: e92f9f7bb49b0b7cc33c73a9c5eb2d0ca7532592
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094405"
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Dostęp do aplikacji usługi YARN loguje HDInsight z systemem Windows
W tym dokumencie wyjaśniono, jak dostęp do dzienników aplikacji usługi YARN, które została zakończona w klastrze usługi Hadoop z systemem Windows w usłudze Azure HDInsight

> [!IMPORTANT]
> Informacje przedstawione w tym dokumencie mają zastosowanie tylko w klastrach HDInsight z systemem Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Informacji na temat uzyskiwania dostępu do usługi YARN dzienników w klastrach HDInsight opartych na systemie Linux, zobacz [aplikacji usługi YARN dostęp do logowania opartych na systemie Linux z platformą Hadoop w HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Wymagania wstępne
* Klaster HDInsight z systemem Windows.  Zobacz [oparte na Windows tworzenie klastrów usługi Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>YARN Timeline Server
<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> zawiera ogólne informacje dotyczące ukończonych aplikacji oraz jak framework informacje specyficzne dla aplikacji za pośrednictwem dwóch różnych interfejsów. W szczególności:

* Przechowywanie i pobieranie informacji o aplikacji ogólnego w klastrach HDInsight została włączona przy użyciu wersji 3.1.1.374 lub nowszej.
* Składnik informacji o aplikacjach dla określonej platformy Timeline Server nie jest obecnie dostępna w klastrach HDInsight.

Ogólne informacje dotyczące aplikacji zawiera następujące rodzaje danych:

* Identyfikator aplikacji, unikatowy identyfikator aplikacji
* Użytkownik, który uruchomił aplikację
* Informacje o podjętych w celu wykonania aplikacji
* Kontenery posługują się wszelkie próby danej aplikacji

W klastrach usługi HDInsight te informacje są przechowywane przez usługę Azure Resource Manager. Informacje są zapisywane do magazynu historii w domyślny magazyn dla klastra. Te dane ogólne dotyczące ukończonych aplikacji mogą być pobierane za pośrednictwem interfejsu API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Dzienniki aplikacji usługi YARN i
YARN obsługuje wiele modeli programowania Dzięki rozdzieleniu zarządzania zasobami z planowania/monitorowania aplikacji. YARN używa globalną *ResourceManager* (MB), każdy proces roboczy — węzeł *NodeManagers* (NMs) i poszczególnych aplikacji *ApplicationMasters* (AMs). AM poszczególnych aplikacji negocjuje zasobów (procesor CPU, pamięci, dysku, użycia sieci) do uruchamiania Twojej aplikacji za pomocą Menedżera zasobów. Menedżer zasobów działa z NMs, aby udzielić tych zasobów, które są przyznawane jako *kontenery*. AM jest odpowiedzialny za monitorowanie postępu kontenerów do niej przypisany, Menedżera zasobów. Aplikacja może wymagać wiele kontenerów, w zależności od rodzaju aplikacji.

* Każda aplikacja może składać się z wielu *prób aplikacji*. 
* Kontenery są przyznawane określonych próba aplikacji. 
* Kontener udostępnia kontekst umożliwiający podstawowa jednostka pracy. 
* Prac, które jest wykonywane w kontekście kontenera odbywa się w węźle pojedynczego procesu roboczego, który został przydzielony kontenera. 

Aby uzyskać więcej informacji, zobacz [pojęcia usługi YARN][YARN-concepts].

Dzienniki aplikacji (i dzienniki skojarzony kontener) są krytyczne w debugowaniu aplikacji platformy Hadoop innych problematyczne. YARN umożliwia nieuprzywilejowany umożliwiająca zbieranie, agregując i przechowywania dzienników aplikacji za pomocą [agregacji dziennika] [ log-aggregation] funkcji. Funkcja agregacji dziennika sprawia, że uzyskiwania dostępu do dzienników aplikacji bardziej deterministyczna, agreguje dzienników dla wszystkich kontenerów na węzeł procesu roboczego i przechowuje je w postaci jednego zagregowane pliku dziennika na węzeł procesu roboczego na domyślny system plików po zakończeniu działania aplikacji. Aplikacja może używać setek lub tysięcy kontenerów, ale dzienników dla wszystkich kontenerów, uruchom w węźle pojedynczego procesu roboczego są agregowane do pojedynczego pliku, co w jednym pliku na węzeł procesu roboczego używanych przez aplikację. Agregacja dziennik jest domyślnie włączona, w klastrach HDInsight (w wersji 3.0 lub nowszej), i zagregowane Dzienniki znajdują się w domyślnym kontenerze usługi klastrowania w następującej lokalizacji:

    wasb:///app-logs/<user>/logs/<applicationId>

W tej lokalizacji *użytkownika* to nazwa użytkownika, który uruchomił aplikację, i *applicationId* jest unikatowy identyfikator aplikacji przypisany przez Menedżera zasobów usługi YARN.

Zagregowane dzienniki nie są bezpośrednio do odczytu, ponieważ są one zapisywane [TFile][T-file], [format binarny] [ binary-format] indeksowane przez kontener. YARN umożliwia narzędzi interfejsu wiersza polecenia do porzucenia te dzienniki jako zwykły tekst dla aplikacji lub kontenerów zainteresowania. Te dzienniki można wyświetlić jako zwykły tekst, uruchamiając jeden z następujących YARN poleceń bezpośrednio w węzłach klastra (po nawiązania z nim za pośrednictwem protokołu RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Interfejsie użytkownika YARN ResourceManager
Interfejsie użytkownika YARN ResourceManager działa na głównym węzłem klastra i jest możliwy za pośrednictwem pulpitu nawigacyjnego portalu platformy Azure:

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).
2. W menu po lewej stronie, kliknij polecenie **Przeglądaj**, kliknij przycisk **klastry HDInsight**, kliknij klaster systemem Windows, który chcesz uzyskać dostęp do dzienników aplikacji usługi YARN.
3. W górnym menu, kliknij przycisk **pulpit nawigacyjny**. Zostanie wyświetlona strona, otwarty w nowym oknie przeglądarki kartę o nazwie **konsolę zapytań HDInsight**.
4. Z **konsolę zapytań HDInsight**, kliknij przycisk **interfejsie użytkownika Yarn**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
