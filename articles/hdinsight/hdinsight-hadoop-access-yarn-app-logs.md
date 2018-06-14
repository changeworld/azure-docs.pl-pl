---
title: Dzienniki aplikacji Hadoop YARN dostępu programowo - Azure | Dokumentacja firmy Microsoft
description: Dostęp do aplikacji programowo loguje klastra usługi Hadoop w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: aab7865548c034cb550874c31977b05936dc45b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403938"
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Dzienniki aplikacji YARN dostępu w usłudze HDInsight z systemu Windows
W tym dokumencie opisano sposób dostępu do dzienników YARN aplikacji, które zakończyły się w klastrze z systemem Windows Hadoop w usłudze Azure HDInsight

> [!IMPORTANT]
> Informacje przedstawione w tym dokumencie ma zastosowanie tylko w klastrach HDInsight opartych na systemie Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Aby uzyskać informacje na temat uruchamiania YARN loguje się klastrów usługi HDInsight opartych na systemie Linux, zobacz [logowania YARN dostęp do aplikacji opartych na systemie Linux platformą Hadoop w usłudze HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Wymagania wstępne
* Klaster usługi HDInsight opartej na systemie Windows.  Zobacz [klastrów systemu Windows, Utwórz Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>YARN osi czasu serwera
<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN osi czasu serwera</a> zawiera ogólne informacje na temat aplikacji ukończone również jako framework informacje specyficzne dla aplikacji za pomocą dwóch różnych interfejsów. W szczególności:

* Przechowywanie i pobieranie informacji o aplikacji ogólnego w klastrach HDInsight została włączona w wersji 3.1.1.374 lub nowszej.
* Składnik informacji określonej struktury aplikacji serwera oś czasu nie jest aktualnie dostępny w klastrach usługi HDInsight.

Ogólne informacje na temat aplikacji obejmuje następujące rodzaje danych:

* Identyfikator aplikacji, unikatowy identyfikator aplikacji
* Użytkownik, który uruchomił aplikację
* Informacje o podjętych w celu wykonania aplikacji
* Kontenery używane przez wszystkie próby danej aplikacji

W klastrach HDInsight te informacje są przechowywane przez Menedżera zasobów Azure. Informacje są zapisywane w magazynie historii w domyślny magazyn dla klastra. Za pomocą interfejsu API REST można pobrać tego ogólnego danych w aplikacjach zakończone:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Dzienniki YARN aplikacji i
YARN obsługuje wiele modele programowania Dzięki rozdzieleniu zarządzanie zasobami z planowania/monitorowania aplikacji. YARN używa globalnym *ResourceManager* (RM) na węzła procesu roboczego *NodeManagers* (NMs) i dla każdej aplikacji *ApplicationMasters* (AMs). AM poszczególnych aplikacji negocjuje zasobów (Procesora, pamięci, dysku, sieci) do uruchamiania aplikacji z Menedżera zasobów. Menedżer zasobów współpracuje z NMs udzielenia tych zasobów, które są przyznawane jako *kontenery*. AM jest odpowiedzialny za śledzenie postępu kontenery przypisane do niej przez Menedżera zasobów. Aplikacja może wymagać wiele kontenerów w zależności od charakteru aplikacji.

* Każda aplikacja może składać się z wielu *prób aplikacji*. 
* Kontenery są przyznawane określonych próba aplikacji. 
* Kontener udostępnia kontekst dla podstawowa jednostka pracy. 
* Praca odbywa się w kontekście kontenera odbywa się w węźle pojedynczego procesu roboczego, który kontenera została przydzielona do. 

Aby uzyskać więcej informacji, zobacz [pojęcia YARN][YARN-concepts].

Dzienniki aplikacji (oraz Dzienniki skojarzone kontenera) są kluczowe w debugowaniu problematyczne aplikacje platformy Hadoop. YARN umożliwia nieuprzywilejowany framework do zbierania, agregację i przechowywania Dzienniki aplikacji z [agregacji dziennika] [ log-aggregation] funkcji. Funkcja agregacji dziennika sprawia, że podczas uzyskiwania dostępu do dzienników aplikacji teraz bardziej przewidywalna, ponieważ agreguje dzienniki we wszystkich kontenerów w węźle procesu roboczego i przechowuje je jako jeden zagregowany plik dziennika na węzła procesu roboczego na domyślny system plików, po zakończeniu działania aplikacji. Aplikacja może korzystać z setkami lub tysiącami kontenerów, ale dzienniki dla wszystkich kontenerów uruchomiona w węźle pojedynczego procesu roboczego są agregowane w pojedynczym pliku, co w jednym pliku na węzeł roboczych używanych przez aplikację. Agregacji dziennik jest domyślnie włączone w klastrach HDInsight (w wersji 3.0 lub nowszej), i zagregowane Dzienniki znajdują się w domyślnym kontenerze klastra w następującej lokalizacji:

    wasb:///app-logs/<user>/logs/<applicationId>

W tym miejscu *użytkownika* to nazwa użytkownika, który uruchomił aplikację, i *applicationId* jest unikatowy identyfikator aplikacji przypisany przez YARN Menedżera zasobów.

Dzienniki zagregowane nie mogą bezpośrednio do odczytu, ponieważ są one zapisywane [TFile][T-file], [format binarny] [ binary-format] indeksowane według kontenera. YARN umożliwia narzędzi interfejsu wiersza polecenia do zrzutu te dzienniki jako zwykły tekst dla aplikacji lub kontenery zainteresowań. Dzienniki te można wyświetlić jako zwykły tekst, uruchamiając jeden z następujących YARN polecenia bezpośrednio w węzłach klastra (po nawiązaniu połączenia do niego za pośrednictwem protokołu RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI
Interfejsie użytkownika YARN ResourceManager działa na headnode klastra i jest możliwy za pośrednictwem pulpitu nawigacyjnego portalu Azure:

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).
2. W menu po lewej stronie kliknij **Przeglądaj**, kliknij przycisk **klastrów usługi HDInsight**, kliknij klaster opartych na systemie Windows, który ma dostępu do dzienników YARN w aplikacji.
3. W górnym menu, kliknij przycisk **pulpitu nawigacyjnego**. Zobacz stronę na nowym oknie przeglądarki otworzyć kartę o nazwie **HDInsight zapytania konsoli**.
4. Z **konsoli zapytania HDInsight**, kliknij przycisk **interfejsie użytkownika Yarn**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
