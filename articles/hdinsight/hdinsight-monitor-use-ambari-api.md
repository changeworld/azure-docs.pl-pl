---
title: Zarządzanie klastrami Hadoop w HDInsight przy użyciu interfejsu API systemu Ambari - Azure
description: Użyj interfejsy API systemu Apache Ambari do tworzenia, zarządzania i monitorowania klastrów platformy Hadoop. Intuicyjne narzędzia operatora i interfejsów API ukrywają złożoność architektury Hadoop.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 062925f7e072651f4b4189cec7ca73144c0cf994
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436334"
---
# <a name="monitor-apache-hadoop-clusters-in-hdinsight-using-the-apache-ambari-api"></a>Monitorowanie klastrów Apache Hadoop w HDInsight przy użyciu interfejsu API Apache Ambari
Dowiedz się, jak monitorowanie klastrów HDInsight za pomocą interfejsy API systemu Apache Ambari.

> [!NOTE]  
> Informacje przedstawione w tym artykule jest przede wszystkim dla klastrów HDInsight z systemem Windows, które zapewniają interfejs API REST Ambari w wersji tylko do odczytu. W przypadku klastrów opartych na systemie Linux, zobacz [Zarządzanie Apache Hadoop clusters, przy użyciu narzędzia Apache Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>Co to jest Ambari?
[Apache Ambari] [ ambari-home] służy do inicjowania obsługi, zarządzania i monitorowania klastrów Apache Hadoop. Obejmuje intuicyjny zestaw narzędzi operatora oraz niezawodny zestaw interfejsów API, które neutralizują złożoność platformy Hadoop, upraszczając działanie klastrów. Aby uzyskać więcej informacji na temat interfejsów API, zobacz [Ambari API Reference][ambari-api-reference]. 

HDInsight aktualnie obsługuje tylko funkcja monitorowania Ambari. Ambari API w wersji 1.0 jest obsługiwany przez klastry HDInsight w wersji, 3.0 i 2.1. W tym artykule opisano uzyskiwania dostępu do interfejsy API systemu Ambari w klastrach HDInsight w wersji 3.1 i 2.1. Główną różnicą między nimi jest niektóre składniki zostały zmienione wraz z wprowadzeniem nowych możliwości (na przykład serwer historii zadania). 

**Wymagania wstępne**

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Stacja robocza z programem Azure PowerShell**.
* (Opcjonalnie) [cURL][curl]. Aby go zainstalować, zobacz [cURL wydań i pliki do pobrania][curl-download].
  
  > [!NOTE]  
  > Kiedy używać Windows, użyj w podwójny cudzysłów zamiast pojedynczego cudzysłowu do wartości opcji polecenia cURL.
  > 
  > 
* **Klaster usługi Azure HDInsight**. Aby uzyskać instrukcje na temat inicjowania obsługi klastra, zobacz [rozpoczęcie korzystania z HDInsight] [ hdinsight-get-started] lub [Provision HDInsight clusters][hdinsight-provision]. Potrzebne są następujące dane, aby wykonać kroki samouczka:
  
  | Właściwości klastra | Nazwa zmiennej usługi Azure PowerShell | Wartość | Opis |
  | --- | --- | --- | --- |
  |   Nazwa klastra HDInsight |$clusterName | |Nazwa klastra usługi HDInsight. |
  |   Nazwa użytkownika klastra |$clusterUsername | |Nazwa użytkownika klastra określona podczas tworzenia klastra. |
  |   Hasło klastra |$clusterPassword | |Hasło użytkownika klastra. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Szybko Rozpocznij korzystanie z
Istnieje kilka sposobów na monitorowanie klastrów HDInsight za pomocą systemu Ambari.

**Korzystanie z programu Azure PowerShell**

Poniższy skrypt programu Azure PowerShell pobiera informacje dotyczące śledzenia zadań MapReduce *w klastrze usługi HDInsight 3.5.*  Klucza różnica polega na tym, że możemy ściągnąć te informacje z usługi YARN (zamiast technologii MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Poniższy skrypt programu PowerShell pobiera informacje dotyczące śledzenia zadań MapReduce *w klastrze usługi HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

Dane wyjściowe to:

![Dane wyjściowe Jobtracker][img-jobtracker-output]

**Korzystanie z programu cURL**

Poniższy przykład pobiera informacje o klastrze przy użyciu programu cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Dane wyjściowe to:

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

Korzystając z punktu końcowego Ambari "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", *host_name* pola Zwraca w pełni kwalifikowana nazwa domeny (FQDN) węzła zamiast nazwy hosta. Przed wydaniem 2014-10-8, w tym przykładzie zwracany po prostu "**headnode0**". Po wydaniu wersji 2014-10-8, uzyskaj nazwę FQDN "**headnode0. { ClusterDNS} gt; .azurehdinsight .net**", jak pokazano w poprzednim przykładzie. Ta zmiana była wymagana w celu ułatwienia scenariuszy, w której wiele typów klastra (na przykład baza danych HBase i Hadoop), można wdrożyć w jednej sieci wirtualnej (VNET). Dzieje się tak, na przykład w przypadku używania bazy danych HBase jako platforma zaplecza dla usługi Hadoop.

## <a name="ambari-monitoring-apis"></a>Monitorowanie interfejsów API systemu Ambari
Poniższa lista zawiera niektóre z najbardziej typowych Ambari, monitorowanie wywołań interfejsu API. Aby uzyskać więcej informacji na temat interfejsu API, zobacz [Apache Ambari API Reference][ambari-api-reference].

| Wywołanie interfejsu API Monitora | URI | Opis |
| --- | --- | --- |
| Pobierz klastry |`/api/v1/clusters` | |
| Uzyskaj informacje o klastrze. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |klastry usług, hosty |
| Pobierz usługi |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Usługi obejmują: systemu plików hdfs, mapreduce |
| Pobierz informacje z usługi. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Pobierz składniki usługi |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |System plików HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Uzyskaj składnik informacji. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, składniki hosta, metryk |
| Uzyskiwanie hostów |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0, workernode0 |
| Pobieranie informacji o hoście. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Pobierz hostowane będą składniki |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Uzyskiwanie hosta komponencie. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, składnik hosta, metryk |
| Pobieranie konfiguracji |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Typy konfiguracji: lokacji podstawowej, lokacji systemu plików hdfs, mapred lokacji, witryny programu hive |
| Uzyskaj informacje o konfiguracji. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Typy konfiguracji: lokacji podstawowej, lokacji systemu plików hdfs, mapred lokacji, witryny programu hive |

## <a name="next-steps"></a>Następne kroki
Teraz masz przedstawiono sposób korzystania z monitorowania wywołań interfejsu API Apache Ambari. Aby dowiedzieć się więcej, zobacz:

* [Zarządzanie klastrami HDInsight przy użyciu witryny Azure portal][hdinsight-admin-portal]
* [Zarządzanie klastrami HDInsight przy użyciu programu Azure PowerShell][hdinsight-admin-powershell]
* [Zarządzanie klastrami HDInsight przy użyciu interfejsu wiersza polecenia][hdinsight-admin-cli]
* [Dokumentacja usługi HDInsight][hdinsight-documentation]
* [Rozpoczynanie pracy z usługą HDInsight][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: https://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
