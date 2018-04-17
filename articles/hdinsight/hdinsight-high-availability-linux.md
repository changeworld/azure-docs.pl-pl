---
title: Wysoka dostępność dla platformy Hadoop - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak klastry usługi HDInsight poprawy niezawodności i dostępności przy użyciu dodatkowego węzła głównego. Dowiedz się, jak to ma wpływ na usługi Hadoop, takich jak Ambari i Hive, oraz nawiązać indywidualnie do każdego węzła głównego przy użyciu protokołu SSH.
services: hdinsight
editor: cgronlun
manager: cgronlun
author: Blackmist
documentationcenter: ''
tags: azure-portal
keywords: Wysoka dostępność usługi hadoop
ms.assetid: 99c9f59c-cf6b-4529-99d1-bf060435e8d4
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: larryfr
ms.openlocfilehash: 974ed70fbda88dfcb775e021474583f7afb0576b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>Dostępność i niezawodność klastrów Hadoop w usłudze HDInsight

Klastry HDInsight zapewniają dwa węzły head, aby zwiększyć dostępność i niezawodność usług Hadoop i zadania uruchomione.

Hadoop osiąga wysokiej dostępności i niezawodności, replikowanie usług i danych w wielu węzłach w klastrze. Jednak standardowe podziału Hadoop zwykle mają tylko pojedynczego węzła głównego. Wszelkie awarii jednego węzła głównego może spowodować klaster przestanie działać. Usługa HDInsight zapewnia dwa headnodes w celu zwiększenia dostępności i niezawodności na platformie Hadoop.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="availability-and-reliability-of-nodes"></a>Dostępność i niezawodność węzłów

Węzły w klastrze usługi HDInsight są implementowane za pomocą usługi Azure Virtual Machines. W poniższych sekcjach omówiono typy oddzielnego węzła używane z usługą HDInsight. 

> [!NOTE]
> Nie wszystkie typy węzłów są używane dla typu klastra. Na przykład typ klastra usługi Hadoop nie ma żadnych węzłów Nimbus. Aby uzyskać więcej informacji na węzłach używana przez typy klastrów usługi HDInsight, zobacz sekcję typy klastrów [utworzyć Linux opartych klastrów Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) dokumentu.

### <a name="head-nodes"></a>HEAD węzłów

Aby zapewnić wysoką dostępność usług Hadoop, usługa HDInsight zapewnia dwóch węzłów głównych. Obu węzłów głównych są jednocześnie aktywne i działa w klastrze usługi HDInsight. Niektóre usługi, takie jak system plików HDFS lub YARN, są tylko aktywne, w jednym węźle głównym w danym momencie. Inne usługi, takie jak serwera HiveServer2 lub na potrzeby magazynu metadanych Hive są aktywne w obu węzłów głównych w tym samym czasie.

Węzły HEAD (i inne węzły w usłudze HDInsight) ma wartość numeryczną jako część nazwy hosta węzła. Na przykład `hn0-CLUSTERNAME` lub `hn4-CLUSTERNAME`.

> [!IMPORTANT]
> Nie należy kojarzyć wartość liczbową z tego, czy węzeł jest podstawowy lub pomocniczy. Wartość liczbowa jest tylko do Podaj unikatową nazwę dla każdego węzła.

### <a name="nimbus-nodes"></a>Węzły Nimbus

Węzły nimbus są dostępne z klastrami Storm. Węzłów Nimbus zawiera funkcje podobne do Hadoop JobTracker za dystrybucji i monitorowania przetwarzania między węzłami procesów roboczych. HDInsight dostarcza dwóch węzłów Nimbus, w przypadku klastrów Storm

### <a name="zookeeper-nodes"></a>Węzły dozorcy

[Dozorcy](http://zookeeper.apache.org/) węzły są używane do wyboru wiodące głównego usługi na węzłach głównych. Są one również używane do upewnić się, że usług, danych (proces roboczy) węzły i bram wiedzieć, które węzła głównego jest aktywny w głównej usługi. Domyślnie usługa HDInsight zapewnia trzy węzły dozorcy.

### <a name="worker-nodes"></a>Węzłów procesu roboczego

Węzłów procesu roboczego do wykonywania analizy danych rzeczywistych po przesłaniu zadania do klastra. W przypadku niepowodzenia węzłem procesu roboczego zadania, które zostało zostanie wykonana jest przesyłany do innego węzła procesu roboczego. Domyślnie HDInsight tworzy czterech węzłów procesu roboczego. Ten numer do własnych potrzeb, podczas i po utworzeniu klastra można zmienić.

### <a name="edge-node"></a>Węzeł brzegowy

Węzeł krawędzi nie aktywnie uczestniczy w analizy danych w ramach klastra. Jest on używany przez programistów i analityków danych, podczas pracy z platformą Hadoop. Węzeł brzegowy znajduje się w tej samej sieci wirtualnej Azure, co inne węzły w klastrze i można uzyskać dostęp do innych węzłów. Można użyć węzła krawędzi bez konieczności przełączania zasoby zadań analizy i krytycznych usług Hadoop.

Obecnie R Server w usłudze HDInsight jest tylko typ klastra, zapewniająca węzła krawędzi domyślnie. R Server w usłudze HDInsight służy węzła krawędzi kodu testowego R lokalnie w węźle przed przesłaniem ich do klastra na potrzeby przetwarzania rozproszonego.

Informacje o korzystaniu z węzłem krawędzi z klastra o typie innym niż serwer R, zobacz [użycia węzłów krawędzi w usłudze HDInsight](hdinsight-apps-use-edge-node.md) dokumentu.

## <a name="accessing-the-nodes"></a>Uzyskiwanie dostępu do węzłów

Dostęp do klastra za pośrednictwem Internetu jest realizowane za pośrednictwem publicznego bramy. Dostęp jest ograniczony do nawiązywania połączenia z głównymi węzłami i (jeśli istnieje) węzła krawędzi. Dostęp do usług działających na węzłach głównych nie odbywa się przez wiele węzłów głównych. Publiczny brama kieruje żądania do węzła głównego, który jest hostem żądanej usługi. Na przykład jeśli Ambari znajduje się obecnie na drugiego węzła głównego, brama trasy żądań przychodzących dla narzędzia Ambari do tego węzła.

Dostęp za pośrednictwem publicznego bramy jest ograniczona do portu 443 (HTTPS), 22 i 23.

* Port __443__ umożliwia dostęp do innych sieci web interfejsu użytkownika lub hostowanych w węzłach głównych interfejsów API REST i Ambari.

* Port __22__ służy do uzyskania dostępu do podstawowego węzła głównego lub węzłem krawędzi przy użyciu protokołu SSH.

* Port __23__ umożliwia dostęp do drugiego węzła głównego przy użyciu protokołu SSH. Na przykład `ssh username@mycluster-ssh.azurehdinsight.net` nawiązuje połączenie z podstawowym węzłem głównym klastra o nazwie **mycluster**.

Aby uzyskać więcej informacji o korzystaniu z protokołu SSH, zobacz [używanie SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentu.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Wewnętrzne w pełni kwalifikowanej nazwy domeny (FQDN)

Węzły w klastrze HDInsight mają wewnętrzny adres IP i nazwy FQDN, która jest możliwy tylko z klastra. Podczas uzyskiwania dostępu do usługi w klastrze za pomocą wewnętrzny adres FQDN lub adres IP, należy używać narzędzia Ambari do Sprawdź adres IP lub nazwa FQDN używana podczas uzyskiwania dostępu do usługi.

Na przykład usługi Oozie można uruchomić tylko na jednym węźle głównym, a za pomocą `oozie` polecenia w sesji SSH wymaga adresu URL do usługi. Ten adres URL można pobrać z Ambari za pomocą następującego polecenia:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

To polecenie zwróci wartość podobne do następujące polecenie, które zawiera wewnętrzny adres URL do użycia z `oozie` polecenia:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Aby uzyskać więcej informacji na temat pracy z interfejsu API REST Ambari, zobacz [monitorować i zarządzać HDInsight przy użyciu interfejsu API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Uzyskiwanie dostępu do innych typów węzła

Możesz nawiązać połączenie węzły, które nie są bezpośrednio dostępne w Internecie przy użyciu następujących metod:

* **SSH**: po nawiązaniu połączenia z węzłem głównym przy użyciu protokołu SSH, można następnie użyć SSH węzła głównego do nawiązania połączenia inne węzły w klastrze. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Tunel SSH**: Jeśli potrzebujesz dostępu do usługi sieci web hostowanych w jednym z węzłów, które nie jest dostępne w Internecie, należy użyć tunelu SSH. Aby uzyskać więcej informacji, zobacz [tunelu SSH za pomocą usługi HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

* **Sieć wirtualna platformy Azure**: Jeśli z klastrem usługi HDInsight jest częścią sieci wirtualnej platformy Azure, dowolnego zasobu w tej samej sieci wirtualnej można uzyskać dostęp do wszystkich węzłów w klastrze. Aby uzyskać więcej informacji, zobacz [rozszerzyć HDInsight przy użyciu usługi Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) dokumentu.

## <a name="how-to-check-on-a-service-status"></a>Jak sprawdzić stanu usługi

Aby sprawdzić stan usługi działające na węzłach głównych, należy użyć Interfejsu sieci Web Ambari lub interfejsu API REST Ambari.

### <a name="ambari-web-ui"></a>Interfejs użytkownika sieci Web Ambari

Interfejs sieci Web Ambari są widoczne w https://CLUSTERNAME.azurehdinsight.net. Zastąp ciąg **CLUSTERNAME** nazwą klastra. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia użytkownika HTTP dla klastra. Domyślna nazwa użytkownika HTTP jest **admin** , a hasło to hasło wprowadzone podczas tworzenia klastra.

Nadejściu na stronie Ambari zainstalowane usługi są wyświetlane po lewej stronie.

![Zainstalowane usługi](./media/hdinsight-high-availability-linux/services.png)

Istnieje szereg ikony wyświetlane obok usługi, aby wskazać stan. Alerty dotyczące usługi można wyświetlić przy użyciu **alerty** łącze w górnej części strony. Można wybrać poszczególnych usług, aby wyświetlić więcej informacji na nim.

Gdy na stronie usługi zawiera informacje dotyczące stanu i konfiguracji poszczególnych usług, nie ma informacji, na które węzła głównego usługa jest uruchomiona na. Aby wyświetlić te informacje, należy użyć **hostów** łącze w górnej części strony. Ta strona wyświetla hosty w klastrze, w tym węzłów głównych.

![listy hostów](./media/hdinsight-high-availability-linux/hosts.png)

Kliknąć łącze dla jednego z węzłów głównych Wyświetla usług i składników uruchomione w tym węźle.

![Stan składnika](./media/hdinsight-high-availability-linux/nodeservices.png)

Aby uzyskać więcej informacji na temat używania narzędzia Ambari, zobacz [monitora i zarządzać nimi za pomocą interfejsu użytkownika sieci Web Ambari HDInsight](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Interfejs API REST Ambari

Interfejs API REST Ambari jest dostępna za pośrednictwem Internetu. Brama publicznego HDInsight obsługuje żądania routingu z węzłem głównym, które obecnie obsługuje interfejsu API REST.

Aby sprawdzić stan usługi za pośrednictwem interfejsu API REST Ambari służy następujące polecenie:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Zastąp **hasło** z hasłem konta użytkownika (Administrator) HTTP.
* Zamień ciąg **CLUSTERNAME** na nazwę klastra.
* Zastąp **SERVICENAME** z nazwy usługi, aby sprawdzić stan.

Na przykład, aby sprawdzić stan **HDFS** usługi w klastrze o nazwie **mycluster**, używając hasła **hasła**, należy użyć następującego polecenia:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

Odpowiedź jest podobny do następującego formatu JSON:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

Adres URL określa, że nam czy usługa jest obecnie uruchomiona na węzła głównego o nazwie **hn0 CLUSTERNAME**.

Stan informuje, nam czy usługa jest obecnie uruchomiona, lub **uruchomiono**.

Jeśli nie wiesz, jakie usługi są zainstalowane w klastrze, służy następujące polecenie, aby pobrać listę:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Aby uzyskać więcej informacji na temat pracy z interfejsu API REST Ambari, zobacz [monitorować i zarządzać HDInsight przy użyciu interfejsu API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Składniki usługi

Usługi mogą obejmować składników, które chcesz sprawdzić stan pojedynczo. Na przykład system plików HDFS zawiera składnik NameNode. Aby wyświetlić informacje składnika, polecenie będzie:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Jeśli nie wiadomo, które składniki są dostarczane przez usługę, służy następujące polecenie do pobierania listy:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Jak uzyskać dostęp do plików dziennika o węzłach głównych

### <a name="ssh"></a>Protokół SSH

Podczas połączenia z węzłem głównym za pośrednictwem protokołu SSH, pliki dziennika można znaleźć w **/var/log**. Na przykład **/var/log/hadoop-yarn/yarn** zawiera dzienników YARN.

Każdego węzła głównego może mieć wpisy dziennika unikatowy, należy sprawdzić dzienniki na serwerze.

### <a name="sftp"></a>SFTP

Można również nawiązać połączenia z węzłem głównym przy użyciu SSH protokołu transferu plików lub Secure File Transfer Protocol (SFTP), a bezpośrednio pobierać pliki dziennika.

Podobnie jak przy użyciu klienta SSH, gdy połączenie z klastrem należy podać nazwę konta użytkownika SSH i adres SSH klastra. Na przykład `sftp username@mycluster-ssh.azurehdinsight.net`. Podaj hasło dla konta, po wyświetleniu monitu, lub podaj publicznego klucza przy użyciu `-i` parametru.

Po nawiązaniu połączenia jest wyświetlana `sftp>` wiersza. Z wiersza polecenia, można zmienić katalogi, przekazywanie i pobieranie plików. Na przykład następujące polecenia Zmień katalog na **/var/log/hadoop/hdfs** katalogu, a następnie Pobierz wszystkie pliki w katalogu.

    cd /var/log/hadoop/hdfs
    get *

Aby uzyskać listę dostępnych poleceń, wprowadź `help` na `sftp>` wiersza.

> [!NOTE]
> Dostępne są także graficzne interfejsy, które pozwala na wizualizowanie systemu plików, gdy nawiązano połączenie przy użyciu protokołu SFTP. Na przykład [MobaXTerm](http://mobaxterm.mobatek.net/) możesz wybrać system plików przy użyciu interfejsem podobnym do Eksploratora Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]
> Aby uzyskać dostęp do plików dziennika przy użyciu Ambari, należy użyć tunelu SSH. Interfejsy sieci web dla poszczególnych usług nie są widoczne publicznie w Internecie. Aby uzyskać informacje na temat używania tunelu SSH, zobacz [Użyj SSH Tunneling](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

Z poziomu interfejsu użytkownika sieci Web Ambari wybierz usługi, którą chcesz obejrzeć dzienniki dla (na przykład YARN). Następnie użyj **szybkie linki** które węzła głównego, aby wyświetlić dzienniki, aby wybrać.

![Aby wyświetlić dzienniki przy użyciu szybkie linki](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Jak skonfigurować rozmiaru węzła

Rozmiar węzła można wybrać tylko podczas tworzenia klastra. Możesz znaleźć listę różnych dostępnych rozmiarów maszyny Wirtualnej dla usługi HDInsight w [cennikiem usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Podczas tworzenia klastra, można określić rozmiaru węzłów. Poniższe informacje znajdują się wskazówki dotyczące sposobu określania przy użyciu rozmiaru [portalu Azure][preview-portal], [programu Azure PowerShell][azure-powershell]i [interfejsu wiersza polecenia Azure][azure-cli]:

* **Azure portal**: podczas tworzenia klastra, można ustawić rozmiar węzłów, używane przez klaster:

    ![Obraz kreatora tworzenia klastra z węzła rozmiar zaznaczenia](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**: korzystając z `azure hdinsight cluster create` polecenia, należy określić rozmiar, head, proces roboczy i węzły dozorcy przy użyciu `--headNodeSize`, `--workerNodeSize`, i `--zookeeperNodeSize` parametrów.

* **Program Azure PowerShell**: korzystając z `New-AzureRmHDInsightCluster` polecenia cmdlet, należy określić rozmiar, head, proces roboczy i węzły dozorcy przy użyciu `-HeadNodeVMSize`, `-WorkerNodeSize`, i `-ZookeeperNodeSize` parametrów.

## <a name="next-steps"></a>Kolejne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat rzeczy, o których wspomniano w tym dokumencie.

* [Dokumentacja REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](../cli-install-nodejs.md)
* [Zainstaluj i skonfiguruj program Azure PowerShell](/powershell/azure/overview)
* [Zarządzanie HDInsight przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md)
* [Obsługa administracyjna klastrów HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
