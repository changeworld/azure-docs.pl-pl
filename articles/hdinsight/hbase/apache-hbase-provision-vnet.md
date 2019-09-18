---
title: Tworzenie klastrów HBase Virtual Network na platformie Azure
description: Rozpocznij korzystanie z usługi HBase w usłudze Azure HDInsight. Dowiedz się, jak tworzyć klastry HBase usługi HDInsight w usłudze Azure Virtual Network.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 60a7afb6e610294ccaa535eaa7371ff8d5015db3
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077206"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Tworzenie klastrów Apache HBase w usłudze HDInsight na platformie Azure Virtual Network
Dowiedz się, jak tworzyć klastry Apache HBase usługi Azure HDInsight w usłudze [azure Virtual Network][1].

Integracja z siecią wirtualną umożliwia wdrażanie klastrów Apache HBase w tej samej sieci wirtualnej co aplikacje, dzięki czemu aplikacje mogą komunikować się bezpośrednio z HBase. Korzyści obejmują:

* Bezpośrednie połączenie aplikacji sieci Web z węzłami klastra HBase, co umożliwia komunikację za pośrednictwem interfejsów API zdalnego wywoływania procedur (RPC) języka Java.
* Zwiększona wydajność dzięki braku ruchu w wielu bramach i modułach równoważenia obciążenia.
* Możliwość przetwarzania poufnych informacji w bezpieczniejszy sposób bez ujawniania publicznego punktu końcowego.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z tym artykułem należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Stacja robocza z programem Azure PowerShell**. Zobacz [Instalowanie i używanie Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Tworzenie klastra Apache HBase w sieci wirtualnej
W tej sekcji utworzysz oparty na systemie Linux Klaster Apache HBase z zależnym kontem usługi Azure Storage w sieci wirtualnej platformy Azure przy użyciu [szablonu Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Aby poznać inne metody tworzenia klastra i zrozumieć ustawienia, zobacz [Tworzenie klastrów usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji o używaniu szablonu do tworzenia klastrów Apache Hadoop w usłudze HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Niektóre właściwości są trwale kodowane w szablonie. Na przykład:
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
> &lt;Nazwa klastra > jest zastępowana nazwą klastra, którą podano podczas korzystania z szablonu.

1. Kliknij poniższy obraz, aby otworzyć szablon w usłudze Azure Portal. Szablon znajduje się w szablonach [szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. W bloku **wdrożenie niestandardowe** wprowadź następujące właściwości:

   * **Subskrypcja**: Wybierz subskrypcję platformy Azure używaną do tworzenia klastra usługi HDInsight, zależnego konta magazynu i sieci wirtualnej platformy Azure.
   * **Grupa zasobów**: Wybierz pozycję **Utwórz nową**, a następnie określ nową nazwę grupy zasobów.
   * **Lokalizacja**: Wybierz lokalizację dla grupy zasobów.
   * **ClusterName**: Wprowadź nazwę klastra Hadoop, który ma zostać utworzony.
   * **Nazwa logowania i hasło klastra**: Domyślna nazwa logowania to **admin**.
   * **Nazwa użytkownika i hasło ssh**: Domyślna nazwa użytkownika to **sshuser**.  Tę nazwę można zmienić.
   * **Zgadzam się na warunki i powyższe warunki**: Zaznaczenia
3. Kliknij pozycję **Kup**. Utworzenie klastra trwa około 20 minut. Po utworzeniu klastra można kliknąć blok klaster w portalu, aby go otworzyć.

Po zakończeniu tego artykułu możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Instrukcje usuwania klastra znajdują się [w temacie Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Aby rozpocząć pracę z nowym klastrem HBase, możesz użyć procedur znajdujących się w temacie Wprowadzenie do korzystania z platformy [Apache HBase z Apache Hadoop w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Nawiązywanie połączenia z klastrem Apache HBase przy użyciu interfejsów API protokołu Java platformy Apache HBase
1. Utwórz maszynę wirtualną "infrastruktura jako usługa" (IaaS) w tej samej sieci wirtualnej platformy Azure i tej samej podsieci. Instrukcje dotyczące tworzenia nowej maszyny wirtualnej IaaS można znaleźć w sekcji [Tworzenie maszyny wirtualnej z systemem Windows Server](../../virtual-machines/windows/quick-create-portal.md). Wykonując kroki opisane w tym dokumencie, należy użyć następujących wartości konfiguracji sieci:

   * **Sieć wirtualna**: &lt;Nazwa klastra >-Sieć wirtualna
   * **Podsieć**: subnet1

   > [!IMPORTANT]  
   > Zastąp &lt;nazwę klastra > nazwą użytą podczas tworzenia klastra usługi HDInsight w poprzednich krokach.

   Korzystając z tych wartości, maszyna wirtualna jest umieszczana w tej samej sieci wirtualnej i podsieci co klaster usługi HDInsight. Ta konfiguracja pozwala im bezpośrednio komunikować się ze sobą. Istnieje możliwość utworzenia klastra usługi HDInsight z pustym węzłem krawędzi. Węzeł brzegowy może służyć do zarządzania klastrem.  Aby uzyskać więcej informacji, zobacz [używanie pustych węzłów brzegowych w usłudze HDInsight](../hdinsight-apps-use-edge-node.md).

2. W przypadku korzystania z aplikacji Java do zdalnego łączenia się z usługą HBase należy użyć w pełni kwalifikowanej nazwy domeny (FQDN). Aby to ustalić, należy uzyskać sufiks DNS konkretnego połączenia klastra HBase. W tym celu można użyć jednej z następujących metod:

   * Użyj przeglądarki sieci Web, aby nawiązać połączenie [Apache Ambari](https://ambari.apache.org/) :

     Przejdź do https://&lt;ClusterName >. azurehdinsight. NET/API/V1/klastrów/&lt;ClusterName >/hosts? minimal_response = true. Powoduje to zamianę pliku JSON na sufiksy DNS.
   * Użyj witryny sieci Web Ambari:

     1. Przejdź do https://&lt;ClusterName >. azurehdinsight. NET.
     2. Kliknij pozycję **hosty** w górnym menu.
   * Użyj Zwinięciea, aby nawiązywać wywołania REST:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     W zwróconych danych JavaScript Object Notation (JSON) Znajdź wpis "HOST_NAME". Zawiera nazwę FQDN dla węzłów w klastrze. Na przykład:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Częścią nazwy domeny rozpoczynającą się nazwą klastra jest sufiks DNS. Na przykład mycluster.b1.cloudapp.net.
   * Korzystanie z programu Azure PowerShell

     Użyj poniższego skryptu Azure PowerShell, aby zarejestrować funkcję **Get-ClusterDetail** , której można użyć do zwrócenia sufiksu DNS:

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

     Po uruchomieniu skryptu Azure PowerShell Użyj następującego polecenia, aby zwrócić sufiks DNS za pomocą funkcji **Get-ClusterDetail** . W przypadku korzystania z tego polecenia określ nazwę klastra HBase usługi HDInsight, nazwę administratora i hasło administratora.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     To polecenie zwraca sufiks DNS. Na przykład **yourclustername.B4.Internal.cloudapp.NET**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Aby sprawdzić, czy maszyna wirtualna może komunikować się z klastrem HBase, użyj polecenia `ping headnode0.<dns suffix>` z maszyny wirtualnej. Na przykład polecenie ping headnode0.mycluster.b1.cloudapp.net.

Aby użyć tych informacji w aplikacji Java, można wykonać kroki opisane w temacie Korzystanie z platformy [Apache Maven do kompilowania aplikacji Java, które korzystają z platformy Apache HBase z usługą HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) w celu utworzenia. Aby aplikacja łączyła się ze zdalnym serwerem HBase, należy zmodyfikować plik **HBase-site. XML** w tym przykładzie, aby użyć nazwy FQDN dla dozorcy. Na przykład:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Aby uzyskać więcej informacji na temat rozpoznawania nazw w sieciach wirtualnych platformy Azure, w tym sposobu korzystania z własnego serwera DNS, zobacz [rozpoznawanie nazw (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób tworzenia klastra Apache HBase. Aby dowiedzieć się więcej, zobacz:

* [Wprowadzenie do usługi HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Używanie pustych węzłów brzegowych w usłudze HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurowanie replikacji Apache HBase w usłudze HDInsight](apache-hbase-replication.md)
* [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Rozpoczynanie pracy z usługą Apache HBase z usługą Apache Hadoop w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Omówienie usługi Virtual Network](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

