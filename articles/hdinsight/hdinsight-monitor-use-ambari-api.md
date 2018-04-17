---
title: Monitorowanie klastrów platformy Hadoop w usłudze HDInsight przy użyciu interfejsu API Ambari - Azure | Dokumentacja firmy Microsoft
description: Przy użyciu interfejsów API Apache Ambari do tworzenia, zarządzania i monitorowania klastrów platformy Hadoop. Operator intuicyjne narzędzia i interfejsy API neutralizują złożoność platformy Hadoop.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
editor: cgronlun
manager: jhubbard
ms.assetid: 052135b3-d497-4acc-92ff-71cee49356ff
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 3c8b1af3ad151a7a901150352202ab0b85aa6ec8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Zarządzanie klastrami Hadoop w usłudze HDInsight przy użyciu interfejsów API systemu Ambari
Informacje o sposobie monitorowania klastrów usługi HDInsight przy użyciu Ambari API.

> [!NOTE]
> Informacje przedstawione w tym artykule jest głównie do klastrów usługi HDInsight opartej na systemie Windows, które udostępnia wersji interfejsu API REST Ambari tylko do odczytu. W klastrach opartych na systemie Linux, zobacz [klastry Hadoop zarządzanie przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>Co to jest Ambari?
[Apache Ambari] [ ambari-home] służy do inicjowania obsługi, zarządzania i monitorowania klastrów platformy Apache Hadoop. Obejmuje intuicyjny zestaw narzędzi operatora oraz niezawodny zestaw interfejsów API, które neutralizują złożoność platformy Hadoop, upraszczając działanie klastrów. Aby uzyskać więcej informacji na temat interfejsów API, zobacz [Ambari API Reference][ambari-api-reference]. 

HDInsight aktualnie obsługuje tylko funkcja monitorowania Ambari. Ambari API 1.0 jest obsługiwany przez klastry usługi HDInsight w wersji 3.0 i 2.1. W tym artykule omówiono podczas uzyskiwania dostępu do interfejsów API Ambari klastrów usługi HDInsight w wersji 3.1 i 2.1. Najważniejsza różnica między nimi jest niektóre składniki zostały zmienione wraz z wprowadzeniem nowych możliwości (na przykład serwer historii zadań). 

**Wymagania wstępne**

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Stacja robocza z programem Azure PowerShell**.
* (Opcjonalnie) [cURL][curl]. Aby go zainstalować, zobacz [cURL wersje i pliki do pobrania][curl-download].
  
  > [!NOTE]
  > Kiedy używać polecenia cURL w systemie Windows, użyj w podwójny cudzysłów zamiast pojedynczego cudzysłowu do wartości opcji.
  > 
  > 
* **Klaster Azure HDInsight**. Aby uzyskać instrukcje dotyczące inicjowania obsługi klastra, zobacz [rozpocząć korzystanie z usługi HDInsight] [ hdinsight-get-started] lub [Obsługa administracyjna klastrów HDInsight][hdinsight-provision]. Potrzebne są następujące dane do wykonywania kroków samouczka:
  
  | Właściwości klastra | Nazwa zmiennej platformy Azure PowerShell | Wartość | Opis |
  | --- | --- | --- | --- |
  |   Nazwa klastra usługi HDInsight |$clusterName | |Nazwa klastra usługi HDInsight. |
  |   Nazwa użytkownika klastra |$clusterUsername | |Określona nazwa użytkownika klastra podczas tworzenia klastra. |
  |   Hasło klastra |$clusterPassword | |Hasło użytkownika klastra. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Szybkie rozpoczęcie tworzenia
Istnieje kilka sposobów, aby używać narzędzia Ambari do monitorowania klastrów usługi HDInsight.

**Korzystanie z programu Azure PowerShell**

Poniższy skrypt programu PowerShell Azure pobiera informacji o śledzeniu zadania MapReduce *w klastrze HDInsight 3.5.*  Najważniejsza różnica polega na tym, że możemy pobierać te szczegóły usługi YARN (zamiast MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Poniższy skrypt programu PowerShell pobiera informacji o śledzeniu zadania MapReduce *w klastrze HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

Wynik jest:

![Dane wyjściowe Jobtracker][img-jobtracker-output]

**Korzystanie z programu cURL**

Poniższy przykład pobiera informacje o klastrze przy użyciu cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Wynik jest:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**W wersji 2014-10-8**:

Korzystając z punktu końcowego Ambari "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", *host_name* pola Zwraca pełną nazwę domeny (FQDN) węzła zamiast nazwy hosta. Przed wprowadzeniem 2014-10-8, w tym przykładzie zwracane po prostu "**headnode0**". Po wydaniu 2014-10-8, możesz uzyskać nazwę FQDN "**headnode0. { Gt;. azurehdinsight.NET ClusterDNS} .net**", jak pokazano w poprzednim przykładzie. Ta zmiana została wymagane do ułatwienia scenariuszy, w którym można wdrożyć wiele typów klastra (na przykład HBase i Hadoop) w jedną sieć wirtualną (VNET). Dzieje się tak, na przykład w przypadku używania bazy danych HBase jako platforma wewnętrzna dla platformy Hadoop.

## <a name="ambari-monitoring-apis"></a>Ambari API monitorowania
W poniższej tabeli wymieniono niektóre z najczęściej Ambari monitorowania wywołań interfejsu API. Aby uzyskać więcej informacji na temat interfejsu API, zobacz [Ambari API Reference][ambari-api-reference].

| Wywołanie interfejsu API Monitora | Identyfikator URI | Opis |
| --- | --- | --- |
| Pobierz klastrów |`/api/v1/clusters` | |
| Pobierz informacje o klastrze. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |klastry usług, hosty |
| Pobierz usługi |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Usługi obejmują: hdfs, mapreduce |
| Pobierz informacje o usługach. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Pobierz składniki usługi |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |System plików HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Pobierz informacje składnika. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, składniki hosta, metryki |
| Pobierz hostów |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0, workernode0 |
| Pobierz informacje o hoście. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Pobieranie składników hosta |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Pobierz informacje składnika hosta. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, składników, hosta i metryki |
| Uzyskiwanie konfiguracji |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Typy konfiguracji: lokacji podstawowej, lokacji systemu plików hdfs, mapred lokacji, lokacji gałęzi |
| Pobierz informacje o konfiguracji. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Typy konfiguracji: lokacji podstawowej, lokacji systemu plików hdfs, mapred lokacji, lokacji gałęzi |

## <a name="next-steps"></a>Następne kroki
Teraz ma pokazaliśmy, jak używać narzędzia Ambari monitorowania wywołań interfejsu API. Aby dowiedzieć się więcej, zobacz:

* [Zarządzanie klastrami HDInsight przy użyciu portalu Azure][hdinsight-admin-portal]
* [Zarządzanie klastrami HDInsight przy użyciu programu Azure PowerShell][hdinsight-admin-powershell]
* [Zarządzanie klastrami HDInsight przy użyciu interfejsu wiersza polecenia][hdinsight-admin-cli]
* [Dokumentacja dotycząca usługi HDInsight][hdinsight-documentation]
* [Rozpoczynanie pracy z usługą HDInsight][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
