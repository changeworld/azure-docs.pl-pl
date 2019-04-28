---
title: Tworzenie klastrów HBase w sieci wirtualnej — platformy Azure
description: Rozpoczęcie korzystania z bazy danych HBase w usłudze Azure HDInsight. Dowiedz się, jak tworzyć klastry HDInsight HBase w usłudze Azure Virtual Network.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 85eaa81a0cfd7ccfe8ad3ae818f89966280d279e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123114"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Tworzenie klastrów Apache HBase w HDInsight w usłudze Azure Virtual Network
Dowiedz się, jak tworzyć klastry usługi Azure HDInsight Apache HBase w [Azure Virtual Network][1].

Dzięki integracji sieci wirtualnej można wdrożyć klastry Apache HBase do tej samej sieci wirtualnej jako aplikacji tak, aby aplikacje mogą komunikować się z bazą danych HBase bezpośrednio. Korzyści:

* Połączenie bezpośrednie w aplikacji sieci web do węzłów klastra HBase, która umożliwia komunikację za pomocą procedury zdalnej bazy danych HBase w języku Java wywoływać interfejsy API (RPC).
* Zwiększona wydajność przez nie ruchu przejdź przez wiele bram i moduły równoważenia obciążenia.
* Możliwość przetwarzania informacji poufnych w bezpieczniejszy sposób bez narażania publiczny punkt końcowy.

### <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Stacja robocza z programem Azure PowerShell**. Zobacz [instalacji i używania programu Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Tworzenie klastra Apache HBase w sieci wirtualnej
W tej sekcji tworzysz klaster oparty na systemie Linux bazy danych Apache HBase przy użyciu zależne konto usługi Azure Storage w sieci wirtualnej platformy Azure przy użyciu [szablonu usługi Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Inne metody tworzenia klastrów i opis ustawień, zobacz [klastrów HDInsight tworzenie](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji na temat przy użyciu szablonu, aby utworzyć klastry platformy Apache Hadoop w HDInsight, zobacz [klastrów utworzyć Apache Hadoop w HDInsight przy użyciu szablonów usługi Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Niektóre właściwości są zakodowane do szablonu. Na przykład:
>
> * **Lokalizacja**: Wschodnie stany USA 2
> * **Wersja klastra**: 3.6
> * **Liczba węzłów procesu roboczego klastra**: 2
> * **Domyślne konto magazynu**: unikatowy ciąg
> * **Nazwa sieci wirtualnej**: &lt;Nazwa klastra >-Sieć wirtualna
> * **Przestrzeń adresowa sieci wirtualnej**: 10.0.0.0/16
> * **Nazwa podsieci**: subnet1
> * **Zakres adresów podsieci**: 10.0.0.0/24
>
> &lt;Nazwa klastra > jest zastępowana nazwą klastra, możesz podać przy użyciu szablonu.
>
>

1. Kliknij poniższy obraz, aby otworzyć szablon w usłudze Azure Portal. Szablon znajduje się w [szablony szybkiego startu platformy](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Z **wdrożenie niestandardowe** bloku, wprowadź następujące właściwości:

   * **Subskrypcja**: Wybierz subskrypcję platformy Azure, użyty do utworzenia klastra HDInsight i zależne konto magazynu i sieci wirtualnej platformy Azure.
   * **Grupa zasobów**: Wybierz **Utwórz nową**, a następnie określ nazwę nowej grupy zasobów.
   * **Lokalizacja**: Wybierz lokalizację dla grupy zasobów.
   * **ClusterName**: Wprowadź nazwę klastra Hadoop, która ma zostać utworzony.
   * **Nazwa logowania i hasło klastra**: Domyślna nazwa logowania to **admin**.
   * **Nazwa użytkownika protokołu SSH i hasło**: Domyślna nazwa użytkownika to **sshuser**.  Tę nazwę można zmienić.
   * **Wyrażam zgodę na warunki i postanowienia, o których wspomniano**: (Wybierz)
3. Kliknij pozycję **Kup**. Utworzenie klastra trwa około 20 minut. Po utworzeniu klastra możesz kliknąć bloku klastra w portalu, aby go otworzyć.

Po ukończeniu tego samouczka warto usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Instrukcje usuwania klastra znajdują się [Zarządzanie Apache Hadoop clusters w HDInsight przy użyciu witryny Azure portal](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Aby rozpocząć pracę z nowego klastra HBase, można użyć procedurach dostępnych w [rozpoczęcie korzystania z bazy danych Apache HBase z użyciem usługi Apache Hadoop w HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Nawiąż połączenie z klastrem bazy danych Apache HBase przy użyciu interfejsów API programu Apache HBase Java RPC
1. Tworzenie infrastruktury jako usługi (IaaS) maszyny wirtualnej w tej samej sieci wirtualnej platformy Azure i tej samej podsieci. Aby uzyskać instrukcje dotyczące tworzenia nowej maszyny wirtualnej IaaS, zobacz [utworzyć serwer systemu Windows uruchomiona maszyna wirtualna](../../virtual-machines/windows/quick-create-portal.md). Gdy czynności opisane w niniejszym dokumencie, należy użyć następujących wartości dla konfiguracji sieci:

   * **Sieć wirtualna**: &lt;Nazwa klastra >-Sieć wirtualna
   * **Podsieci**: subnet1

   > [!IMPORTANT]  
   > Zastąp &lt;nazwa_klastra > o nazwie, którego użyto podczas tworzenia klastra HDInsight w poprzednich krokach.

   Korzystając z tych wartości, maszyna wirtualna jest umieszczana w tej samej sieci wirtualnej i podsieci co klaster HDInsight. Ta konfiguracja pozwala im może komunikować się bezpośrednio ze sobą. Istnieje sposób, aby utworzyć klaster usługi HDInsight za pomocą pustego węzła krawędzi. W węźle brzegowym może służyć do zarządzania klastrem.  Aby uzyskać więcej informacji, zobacz [używanie pustych węzłów brzegowych w HDInsight](../hdinsight-apps-use-edge-node.md).

2. Zdalne ustanawianie połączenia z bazy danych HBase za pomocą aplikacji w języku Java, należy użyć w pełni kwalifikowana nazwa domeny (FQDN). Aby to ustalić, należy uzyskać sufiks DNS konkretnego połączenia w klaster HBase. Aby to zrobić, użyj jednej z następujących metod:

   * Korzystanie z przeglądarki internetowej, aby [Apache Ambari](https://ambari.apache.org/) wywołania:

     Przejdź do https://&lt;Nazwa_klastra >.azurehdinsight.net/api/v1/clusters/&lt;Nazwa_klastra > / hostuje? minimal_response = true. Okazuje się plik JSON o sufiksy DNS.
   * Użyj witryny sieci Web Ambari:

     1. Przejdź do https://&lt;Nazwa_klastra >. azurehdinsight.net.
     2. Kliknij przycisk **hosty** z górnego menu.
   * Użyj Curl wykonywanie wywołań REST:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     W danych JavaScript Object Notation (JSON), zwrócone Znajdź pozycję "host_name". Zawiera on nazwę FQDN dla węzłów w klastrze. Na przykład:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Część domeny nazwa zaczyna się od nazwy klastra jest sufiks DNS. Na przykład mycluster.b1.cloudapp.net.
   * Korzystanie z programu Azure PowerShell

     Użyj poniższego skryptu programu Azure PowerShell do rejestrowania **Get ClusterDetail** funkcji, która może służyć do zwrócenia sufiks DNS:

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Po uruchomieniu skryptu programu Azure PowerShell, użyj następującego polecenia, aby zwrócić sufiks DNS przy użyciu **Get ClusterDetail** funkcji. Określ nazwę klastra HDInsight HBase, nazwę administratora i hasło administratora, korzystając z tego polecenia.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     To polecenie zwraca sufiks DNS. Na przykład **yourclustername.b4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Aby zweryfikować, że maszyna wirtualna może komunikować się z klastrem HBase, użyj polecenia `ping headnode0.<dns suffix>` z maszyny wirtualnej. Na przykład wysyłać polecenia ping headnode0.mycluster.b1.cloudapp.net.

Aby użyć tych informacji w aplikacji w języku Java, możesz wykonać kroki opisane w [Użyj narzędzia Apache Maven do tworzenia aplikacji Java korzystających z bazy danych Apache HBase z HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) do tworzenia aplikacji. Masz aplikację, połącz się ze zdalnym serwerem bazy danych HBase, należy zmodyfikować **hbase-site.xml** pliku, w tym przykładzie na potrzeby nazwę FQDN dozorcy. Na przykład:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Aby uzyskać więcej informacji na temat rozpoznawania nazw w systemie Azure sieci wirtualnych, takich jak używać własnego serwera DNS, zobacz [rozpoznawania nazw (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób tworzenia klastra Apache HBase. Aby dowiedzieć się więcej, zobacz:

* [Rozpoczynanie pracy z usługą HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Używanie pustych węzłów brzegowych w HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurowanie replikacji bazy danych Apache HBase w HDInsight](apache-hbase-replication.md)
* [Tworzenie klastrów usługi Apache Hadoop w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Rozpoczynanie pracy z usługą Apache Hadoop w HDInsight przy użyciu bazy danych Apache HBase](./apache-hbase-tutorial-get-started-linux.md)
* [Omówienie usługi Virtual Network](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

