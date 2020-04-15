---
title: Wysoka dostępność usługi Hadoop — usługa Azure HDInsight
description: Dowiedz się, jak klastry HDInsight zwiększają niezawodność i dostępność przy użyciu dodatkowego węzła głównego. Dowiedz się, jak wpływa to na usługi Hadoop, takie jak Ambari i Hive, a także jak indywidualnie łączyć się z każdym węzłem głównym przy użyciu funkcji SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop wysoka dostępność
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 767d87efcf94d720159dcf3b9dc42981ec957ef0
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381410"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Dostępność i niezawodność klastrów Apache Hadoop w systemie HDInsight

Klastry HDInsight zapewniają dwa węzły główny, aby zwiększyć dostępność i niezawodność usług Apache Hadoop i uruchomionych zadań.

Hadoop osiąga wysoką dostępność i niezawodność, replikując usługi i dane w wielu węzłach w klastrze. Jednak standardowe dystrybucje Hadoop zazwyczaj mają tylko jeden węzeł główny. Wszelkie awarie pojedynczego węzła głównego może spowodować, że klaster przestanie działać. HDInsight zapewnia dwie głowice, aby poprawić dostępność i niezawodność Hadoop.

## <a name="availability-and-reliability-of-nodes"></a>Dostępność i niezawodność węzłów

Węzły w klastrze HDInsight są implementowane przy użyciu maszyn wirtualnych platformy Azure. W poniższych sekcjach omówiono poszczególne typy węzłów używane z programem HDInsight.

> [!NOTE]  
> Nie wszystkie typy węzłów są używane dla typu klastra. Na przykład typ klastra Hadoop nie ma żadnych węzłów Nimbus. Aby uzyskać więcej informacji na temat węzłów używanych przez typy klastrów HDInsight, zobacz sekcję Typy klastrów [klastrów hadoop opartych na systemie Linux w dokumencie HDInsight.](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)

### <a name="head-nodes"></a>Węzły głowy

Aby zapewnić wysoką dostępność usług Hadoop, hdinsight udostępnia dwa węzły głowy. Oba węzły głównego są aktywne i działają jednocześnie w klastrze HDInsight. Niektóre usługi, takie jak Apache HDFS lub Apache Hadoop YARN, są "aktywne" tylko w jednym węźle głównym w danym momencie. Inne usługi, takie jak HiveServer2 lub Hive MetaStore są aktywne w obu węzłach głównego w tym samym czasie.

Aby uzyskać nazwy hostów dla różnych typów węzłów w klastrze, użyj [interfejsu API Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Nie należy kojarzyć wartości liczbowej z tym, czy węzeł jest podstawowy czy pomocniczy. Wartość liczbowa jest obecna tylko w celu zapewnienia unikatowej nazwy dla każdego węzła.

### <a name="nimbus-nodes"></a>Węzły Nimbusa

Węzły Nimbus są dostępne z klastrami Apache Storm. Węzły Nimbus zapewniają podobną funkcjonalność do Usługi Hadoop JobTracker, dystrybuując i monitorując przetwarzanie między węzłami procesu roboczego. HDInsight udostępnia dwa węzły Nimbus dla klastrów Storm

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper węzły

[Węzły ZooKeeper](https://zookeeper.apache.org/) są używane do wyboru lidera usług głównych w węzłach głównych. Są one również używane do ubezpieczania, że usługi, węzły danych (procesowe) i bramy wiedzą, w którym węźle głównym usługa główna jest aktywna. Domyślnie HDInsight udostępnia trzy węzły ZooKeeper.

### <a name="worker-nodes"></a>Węzły procesu roboczego

Węzły procesu roboczego wykonują rzeczywistą analizę danych, gdy zadanie jest przesyłane do klastra. Jeśli węzeł procesu roboczego nie powiedzie się, wykonywane zadanie jest przesyłane do innego węzła procesu roboczego. Domyślnie hdinsight tworzy cztery węzły procesu roboczego. Można zmienić ten numer, aby odpowiadał twoim potrzebom zarówno podczas tworzenia klastra, jak i po jego zakończeniu.

### <a name="edge-node"></a>węzeł krawędzi

Węzeł brzegowy nie uczestniczy aktywnie w analizie danych w klastrze. Jest używany przez deweloperów lub analityków danych podczas pracy z Hadoop. Węzeł brzegowy znajduje się w tej samej sieci wirtualnej platformy Azure, co inne węzły w klastrze i może bezpośrednio uzyskiwać dostęp do wszystkich innych węzłów. Węzeł krawędzi może służyć bez zabierania zasobów z krytycznych usług Hadoop lub zadań analizy.

Obecnie usługi ML w programie HDInsight jest jedynym typem klastra, który domyślnie udostępnia węzeł krawędzi. W przypadku usług ML w programie HDInsight węzeł brzegowy jest używany lokalnie testowy kod R w węźle przed przesłaniem go do klastra w celu przetwarzania rozproszonego.

Aby uzyskać informacje na temat używania węzła krawędzi z innymi typami klastra, zobacz Użyj węzłów krawędzi w dokumencie [HDInsight.](hdinsight-apps-use-edge-node.md)

## <a name="accessing-the-nodes"></a>Uzyskiwanie dostępu do węzłów

Dostęp do klastra przez Internet jest zapewniany za pośrednictwem bramy publicznej. Dostęp jest ograniczony do łączenia się z węzłami głównymi i, jeśli istnieje, węzłem krawędzi. Dostęp do usług uruchomionych w węzłach głównego nie ma wpływu na wiele węzłów głównego. Brama publiczna kieruje żądania do węzła głównego, który obsługuje żądaną usługę. Na przykład jeśli Apache Ambari jest obecnie hostowany w pomocniczym węźle głównym, brama kieruje przychodzące żądania ambari do tego węzła.

Dostęp przez bramę publiczną jest ograniczony do portów 443 (HTTPS), 22 i 23.

|Port |Opis |
|---|---|
|443|Służy do uzyskiwania dostępu do ambari i innych interfejsów API sieci web lub interfejsów API REST hostowanych w węzłach głównego.|
|22|Służy do uzyskiwania dostępu do węzła głównego lub węzła krawędzi za pomocą SSH.|
|23|Służy do uzyskiwania dostępu do pomocniczego węzła głównego z SSH. Na przykład `ssh username@mycluster-ssh.azurehdinsight.net` łączy się z głównym węzłem głównym klastra o nazwie **mycluster**.|

Aby uzyskać więcej informacji na temat korzystania z SSH, zobacz [Użyj SSH z HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentu.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Wewnętrzne w pełni kwalifikowane nazwy domen (FQDN)

Węzły w klastrze HDInsight mają wewnętrzny adres IP i numer FQDN, do których można uzyskać dostęp tylko z klastra. Podczas uzyskiwania dostępu do usług w klastrze przy użyciu wewnętrznego adresu FQDN lub IP należy użyć polecenia Ambari, aby zweryfikować adres IP lub FQDN do użycia podczas uzyskiwania dostępu do usługi.

Na przykład usługa Apache Oozie może działać tylko w `oozie` jednym węźle głównym, a użycie polecenia z sesji SSH wymaga adresu URL usługi. Ten adres URL można pobrać z Ambari za pomocą następującego polecenia:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

To polecenie zwraca wartość podobną do następującej, która `oozie` zawiera wewnętrzny adres URL do użycia z poleceniem:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Aby uzyskać więcej informacji na temat pracy z interfejsem API Ambari REST, zobacz [Monitorowanie i zarządzanie programem HDInsight za pomocą interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Uzyskiwanie dostępu do innych typów węzłów

Można połączyć się z węzłami, które nie są bezpośrednio dostępne przez Internet przy użyciu następujących metod:

|Metoda |Opis |
|---|---|
|Protokół SSH|Po podłączeniu do węzła głównego za pomocą SSH, można następnie użyć SSH z węzła głównego, aby połączyć się z innymi węzłami w klastrze. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|Tunel SSH|Jeśli chcesz uzyskać dostęp do usługi sieci web hostowane w jednym z węzłów, które nie są narażone na Internet, należy użyć tunelu SSH. Aby uzyskać więcej informacji, zobacz [Użyj tunelu SSH z dokumentem HDInsight.](hdinsight-linux-ambari-ssh-tunnel.md)|
|Azure Virtual Network|Jeśli klaster HDInsight jest częścią sieci wirtualnej platformy Azure, każdy zasób w tej samej sieci wirtualnej może bezpośrednio uzyskiwać dostęp do wszystkich węzłów w klastrze. Aby uzyskać więcej informacji, zobacz Planowanie sieci wirtualnej dla dokumentu [HDInsight.](hdinsight-plan-virtual-network-deployment.md)|

## <a name="how-to-check-on-a-service-status"></a>Jak sprawdzić stan usługi

Aby sprawdzić stan usług uruchamianych w węzłach głównego, użyj interfejsu użytkownika sieci Web Ambari lub interfejsu API AMbari REST.

### <a name="ambari-web-ui"></a>Interfejs użytkownika sieci Web Ambari

Interfejs użytkownika sieci Web Ambari `https://CLUSTERNAME.azurehdinsight.net`jest widoczny w pliku . Zamień **nazwę CLUSTERNAME** na nazwę klastra. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia użytkownika HTTP dla klastra. Domyślna nazwa użytkownika HTTP jest **administratorem,** a hasło jest hasłem wprowadzonym podczas tworzenia klastra.

Po przybyciu na stronę Ambari zainstalowane usługi są wyświetlane po lewej stronie.

![Apache Ambari zainstalowane usługi](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Istnieje szereg ikon, które mogą pojawić się obok usługi, aby wskazać stan. Wszystkie alerty związane z usługą można wyświetlać za pomocą łącza **Alerty** u góry strony.  Ambari oferuje kilka wstępnie zdefiniowanych alertów.

Następujące alerty pomagają monitorować dostępność klastra:

| Nazwa alertu                               | Opis                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stan monitora metrycznego                    | Ten alert wskazuje stan procesu Metrics Monitor określony przez skrypt stanu monitora.                                                                                   |
| Ambari Agent Bicie serca                   | Ten alert jest wyzwalany, jeśli serwer utracił kontakt z agentem.                                                                                                                        |
| Proces serwera ZooKeeper                 | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić, że proces serwera ZooKeeper jest wyższy i nasłuchiwanie w sieci.                                                               |
| Stan serwera metadanych usługi IOCache           | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można określić, że serwer metadanych IOCache jest wyższy i odpowiada na żądania klientów                                                            |
| Interfejs użytkownika sieci Web JournalNode                       | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web JournalNode jest nieosiągalny.                                                                                                                 |
| Serwer Spark2 Thrift                     | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić, że serwer Spark2 Thrift jest wyższy.                                                                                                |
| Proces serwera historii                   | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustanowić procesu serwera historii w sieci.                                                                |
| Interfejs użytkownika sieci Web serwera historii                    | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web serwera historii jest nieosiągalny.                                                                                                              |
| `ResourceManager`Interfejs użytkownika sieci Web                   | Ten alert na poziomie hosta `ResourceManager` jest wyzwalany, jeśli interfejs użytkownika sieci Web jest nieosiągalny.                                                                                                             |
| Podsumowanie kondycji nodemanager               | Ten alert na poziomie usługi jest wyzwalany, jeśli istnieją złej kondycji NodeManagers                                                                                                                    |
| Interfejs użytkownika osi czasu aplikacji w sieci Web                      | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web serwera osi czasu aplikacji jest nieosiągalny.                                                                                                         |
| Podsumowanie kondycji DataNode                  | Ten alert na poziomie usługi jest wyzwalany, jeśli istnieją złej kondycji DataNodes                                                                                                                       |
| Interfejs użytkownika sieci Web NameNode                          | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web NameNode jest nieosiągalny.                                                                                                                    |
| Proces kontrolera trybu failover zookeeper    | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można potwierdzić, że proces kontrolera trybu failover zookeeper jest wyższy i nasłuchuje w sieci.                                                   |
| Interfejs użytkownika sieci Web serwera Oozie                      | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web serwera Oozie jest nieosiągalny.                                                                                                                |
| Stan serwera Oozie                      | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić, że serwer Oozie jest wyższy i odpowiada na żądania klientów.                                                                      |
| Proces metastore hive                   | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można określić, że proces protokołu Hive Metastore jest rozwijany i nasłuchiwanie w sieci.                                                                 |
| Proces HiveServer2                      | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić, że usługa HiveServer jest w górę i odpowiada na żądania klientów.                                                                        |
| Stan serwera WebHCat                    | Ten alert na poziomie hosta `templeton` jest wyzwalany, jeśli stan serwera nie jest w dobrej kondycji.                                                                                                            |
| Procent serwerów ZooKeeper dostępne      | Ten alert jest wyzwalany, jeśli liczba w dół serwerów ZooKeeper w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki kontroli procesu ZooKeeper.     |
| Spark2 Livy Serwer                       | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić, że serwer Livy2 jest wyższy.                                                                                                        |
| Serwer historii Spark2                    | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić, że serwer historii Spark2 jest wyższy.                                                                                               |
| Proces modułu zbierającego metryki                | Ten alert jest wyzwalany, jeśli nie można potwierdzić, że moduł zbierający metryki jest nasłuchiwaniem na skonfigurowanym porcie przez liczbę sekund równą progowi.                                 |
| Moduł zbierający metryki — proces wzorca bazy danych HBase | Ten alert jest wyzwalany, jeśli nie można potwierdzić, że procesy główne bazy danych HBase modułu zbierającego moduł metrics nie są włączone i nasłuchiwanie w sieci skonfigurowany próg krytyczny podany w sekundach. |
| Dostępne monitory wskaźników procentowych       | Ten alert jest wyzwalany, jeśli procent procesów Metrics Monitor nie jest w górę i nasłuchuje w sieci skonfigurowanych alertów i progów krytycznych.                             |
| Dostępne menedżery węzłów procentowych           | Ten alert jest wyzwalany, jeśli liczba w dół NodeManagers w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki kontroli procesu NodeManager.        |
| Kondycja nodemanager                       | Ten alert na poziomie hosta sprawdza właściwość kondycji węzła dostępną ze składnika NodeManager.                                                                                              |
| Interfejs użytkownika sieci Web usługi NodeManager                       | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web NodeManager jest nieosiągalny.                                                                                                                 |
| NameNode Wysoka dostępność Kondycji        | Ten alert na poziomie usługi jest wyzwalany, jeśli nie są uruchomione aktywne namenode lub standby namenode.                                                                                     |
| Proces DataNode                         | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustanowić poszczególnych procesów DataNode, aby były w górę i nasłuchiwały w sieci.                                                         |
| Interfejs użytkownika sieci Web DataNode                          | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web DataNode web jest nieosiągalny.                                                                                                                    |
| Dostępne dzienniki procentowe           | Ten alert jest wyzwalany, jeśli liczba w dół JournalNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki journalnode kontroli procesu.        |
| Dostępne dane procentowe              | Ten alert jest wyzwalany, jeśli liczba w dół DataNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki kontroli procesu DataNode.              |
| Stan serwera Zeppelin                   | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić, że serwer Zeppelin jest wyższy i odpowiada na żądania klientów.                                                                   |
| Interaktywny proces HiveServer2          | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można określić, że hiveServerInteractive jest w górę i odpowiada na żądania klientów.                                                             |
| Aplikacja LLAP                         | Ten alert jest wyzwalany, jeśli nie można ustalić, że aplikacja LLAP jest w górę i odpowiada na żądania.                                                                                    |

Można wybrać każdą usługę, aby wyświetlić więcej informacji na jej temat.

Strona usługi zawiera informacje o stanie i konfiguracji każdej usługi, ale nie zawiera informacji o tym, w którym węźle głównym usługa jest uruchomiona. Aby wyświetlić te informacje, użyj łącza **Hosts** u góry strony. Na tej stronie są wyświetlane hosty w klastrze, w tym węzły głównej.

![Apache Ambari headnode lista gospodarzy](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Wybranie łącza dla jednego z węzłów głównego powoduje wyświetlenie usług i składników uruchomionych w tym węźle.

![Stan komponentu Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Aby uzyskać więcej informacji na temat korzystania z ambari, zobacz [Monitorowanie i zarządzanie programem HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

Api Ambari REST jest dostępne przez Internet. Brama publiczna HDInsight obsługuje żądania routingu do węzła głównego, który jest obecnie hostujący interfejs API REST.

Za pomocą następującego polecenia można sprawdzić stan usługi za pośrednictwem interfejsu API AMbari REST:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Zamień **hasło na** hasło konta użytkownika (administratora) HTTP.
* Zamień ciąg **CLUSTERNAME** na nazwę klastra.
* Zastąp **SERVICENAME** nazwą usługi, której chcesz sprawdzić stan.

Na przykład, aby sprawdzić stan usługi **HDFS** w klastrze o nazwie **mycluster**, z hasłem **hasła,** należy użyć następującego polecenia:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

Odpowiedź jest podobna do następującej JSON:

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

Adres URL mówi nam, że usługa jest obecnie uruchomiona na węźle głównym o nazwie **mycluster.wutj3h4ic1zejluqhxzvckxq0g**.

Państwo informuje nas, że usługa jest aktualnie uruchomiona lub **STARTED**.

Jeśli nie wiesz, jakie usługi są zainstalowane w klastrze, możesz użyć następującego polecenia, aby pobrać listę:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Aby uzyskać więcej informacji na temat pracy z interfejsem API Ambari REST, zobacz [Monitorowanie i zarządzanie programem HDInsight za pomocą interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Składniki serwisu

Usługi mogą zawierać składniki, które chcesz sprawdzić stan indywidualnie. Na przykład hdfs zawiera namenode składnika. Aby wyświetlić informacje o komponencie, polecenie będzie:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Jeśli nie wiesz, jakie składniki są dostarczane przez usługę, możesz użyć następującego polecenia, aby pobrać listę:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Jak uzyskać dostęp do plików dziennika w węzłach głównego

### <a name="ssh"></a>Protokół SSH

Po podłączeniu do węzła głównego za pośrednictwem SSH, pliki dziennika można znaleźć w **obszarze /var/log**. Na przykład **/var/log/hadoop-yarn/yarn** zawiera dzienniki dla przędzy.

Każdy węzeł główny może mieć unikatowe wpisy dziennika, więc należy sprawdzić dzienniki na obu.

### <a name="sftp"></a>SFTP

Można również połączyć się z węzłem głównym za pomocą protokołu SSH File Transfer Protocol lub Secure File Transfer Protocol (SFTP) i pobrać pliki dziennika bezpośrednio.

Podobnie jak przy użyciu klienta SSH, podczas łączenia się z klastrem należy podać nazwę konta użytkownika SSH i adres SSH klastra. Na przykład `sftp username@mycluster-ssh.azurehdinsight.net`. Podaj hasło do konta po wyświetleniu monitu `-i` lub podaj klucz publiczny przy użyciu parametru.

Po nawiązaniu połączenia zostanie `sftp>` wyświetlony monit. W tym wierszu można zmieniać katalogi, przesyłać i pobierać pliki. Na przykład następujące polecenia zmieniają katalogi w katalogu **/var/log/hadoop/hdfs,** a następnie pobierają wszystkie pliki w katalogu.

    cd /var/log/hadoop/hdfs
    get *

Aby uzyskać listę dostępnych poleceń, wprowadź `help` w wierszu. `sftp>`

> [!NOTE]  
> Istnieją również interfejsy graficzne, które umożliwiają wizualizację systemu plików po podłączeniu za pomocą SFTP. Na przykład [MobaXTerm](https://mobaxterm.mobatek.net/) umożliwia przeglądanie systemu plików przy użyciu interfejsu podobnego do Eksploratora Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Aby uzyskać dostęp do plików dziennika przy użyciu Ambari, należy użyć tunelu SSH. Interfejsy internetowe poszczególnych usług nie są udostępniane publicznie w Internecie. Aby uzyskać informacje na temat korzystania z tunelu SSH, zobacz [użyj dokumentu Tunelowanie SSH.](hdinsight-linux-ambari-ssh-tunnel.md)

W interfejsie użytkownika sieci Web Ambari wybierz usługę, dla której chcesz wyświetlać dzienniki (na przykład YARN). Następnie użyj **szybkich łączy,** aby wybrać węzeł główny, dla którego mają być wyświetlane dzienniki.

![Używanie szybkich linków do wyświetlania dzienników](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Jak skonfigurować rozmiar węzła

Rozmiar węzła można wybrać tylko podczas tworzenia klastra. Listę różnych rozmiarów maszyn wirtualnych dostępnych dla hdinsight można znaleźć na [stronie cenowej HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Podczas tworzenia klastra można określić rozmiar węzłów. Poniższe informacje zawierają wskazówki dotyczące określania rozmiaru przy użyciu [witryny Azure portal,](https://portal.azure.com/) [modułu Azure PowerShell Az](/powershell/azureps-cmdlets-docs)i [interfejsu wiersza polecenia platformy Azure:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

* **Azure portal:** Podczas tworzenia klastra można ustawić rozmiar węzłów używanych przez klaster:

    ![Obraz kreatora tworzenia klastra z wyborem rozmiaru węzła](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI:** Podczas [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) korzystania z polecenia, można ustawić rozmiar węzłów głowy, pracownika `--headnode-size` `--workernode-size`i `--zookeepernode-size` ZooKeeper za pomocą , i parametrów.

* **Azure PowerShell:** Podczas korzystania z polecenia cmdlet [New-AzHDInsightCluster,](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) można ustawić rozmiar węzłów głowy, `-WorkerNodeSize`pracownika `-ZookeeperNodeSize` i ZooKeeper przy użyciu `-HeadNodeSize`, i parametrów.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o elementach omówionych w tym artykule, zobacz:

* [Apache Ambari REST Odwołanie](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalowanie i konfigurowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Instalowanie i konfigurowanie modułu Az programu Azure PowerShell](/powershell/azure/overview)
* [Zarządzanie hdinsight za pomocą Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Aprowizuj klastry HDInsight oparte na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
