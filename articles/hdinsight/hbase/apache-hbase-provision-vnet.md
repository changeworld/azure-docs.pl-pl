---
title: Tworzenie klastrów HBase w sieci wirtualnej - Azure | Dokumentacja firmy Microsoft
description: Rozpoczynanie pracy z bazy danych HBase w usłudze Azure HDInsight. Informacje o sposobie tworzenia klastrów HDInsight HBase w sieci wirtualnej Azure.
keywords: ''
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 8de8e446-f818-4e61-8fad-e9d38421e80d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: edcfa47eee0f085bad415be0d9b112bbc33c3eca
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="create-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Tworzenie klastrów HBase w usłudze HDInsight w sieci wirtualnej platformy Azure
Dowiedz się, jak utworzyć klaster Azure HDInsight HBase w [sieci wirtualnej Azure][1].

Dzięki integracji sieci wirtualnej klastrów HBase można wdrożyć do tej samej sieci wirtualnej jako aplikacje, dzięki czemu aplikacje mogą komunikować się z bazą danych HBase bezpośrednio. Korzyści:

* Bezpośrednie połączenie między aplikacji sieci web do węzłów klastra HBase, co umożliwia komunikację za pomocą procedury zdalnej bazy danych HBase w języku Java wywoływania interfejsów API (RPC).
* Większa wydajność, ponieważ nie ma ruchu przejdź przez wiele bram i moduły równoważenia obciążenia.
* Możliwość przetworzenia poufne informacje w bardziej bezpieczny sposób bez narażania publiczny punkt końcowy.

### <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Stacja robocza z programem Azure PowerShell**. Zobacz [instalacja i używanie programu Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-hbase-cluster-into-virtual-network"></a>Tworzenie klastra HBase w sieci wirtualnej
W tej sekcji, utworzyć klaster HBase opartych na systemie Linux z zależne konto magazynu Azure w sieci wirtualnej platformy Azure przy użyciu [szablonu usługi Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Inne metody tworzenia klastrów i opis ustawień, zobacz [Tworzenie klastrów usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji na temat przy użyciu szablonu do tworzenia klastrów Hadoop w usłudze HDInsight, zobacz [klastrów utworzyć Hadoop w HDInsight przy użyciu szablonów usługi Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]
> Niektóre właściwości są zakodowane na stałe do szablonu. Na przykład:
>
> * **Lokalizacja**: wschodnie stany USA 2
> * **Wersja klastra**: 3.6
> * **Liczba węzłów procesu roboczego klastra**: 2
> * **Domyślne konto magazynu**: unikatowy ciąg
> * **Nazwa sieci wirtualnej**: &lt;nazwa klastra >-Sieć wirtualna
> * **Przestrzeń adresową sieci wirtualnej**: 10.0.0.0/16
> * **Nazwa podsieci**: podsieć1
> * **Zakres adresów podsieci**: 10.0.0.0/24
>
> &lt;Nazwa klastra > jest zastępowany nazwą klastra, podaj przy użyciu szablonu.
>
>

1. Kliknij poniższy obraz, aby otworzyć szablon w usłudze Azure Portal. Szablon znajduje się w [szablonów Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Z **wdrożenie niestandardowe** bloku, wprowadź następujące właściwości:

   * **Subskrypcja**: Wybierz subskrypcję platformy Azure, używany do tworzenia klastra usługi HDInsight, zależne konto magazynu i sieci wirtualnej platformy Azure.
   * **Grupa zasobów**: Wybierz **Utwórz nowy**i określ nazwę nowej grupy zasobów.
   * **Lokalizacja**: Wybierz lokalizację dla grupy zasobów.
   * **ClusterName**: Wprowadź nazwę klastra usługi Hadoop, która ma zostać utworzony.
   * **Nazwa logowania i hasło klastra**: domyślna nazwa logowania to **admin**.
   * **Nazwa użytkownika i hasło SSH**: domyślna nazwa użytkownika to **sshuser**.  Tę nazwę można zmienić.
   * **Akceptuję warunki i postanowienia, o których wspomniano**: (Wybierz)
3. Kliknij pozycję **Kup**. Utworzenie klastra trwa około 20 minut. Po utworzeniu klastra można kliknąć bloku klastra w portalu, aby go otworzyć.

Po ukończeniu samouczka możesz usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Instrukcje dotyczące usuwania klastra znajdują się [klastrów zarządzania Hadoop w usłudze HDInsight przy użyciu portalu Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

Aby rozpocząć pracę z nowego klastra HBase, można użyć procedur w [rozpocząć korzystanie z platformy Hadoop w usłudze HDInsight HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Połącz się z klastrem HBase przy użyciu interfejsów API RPC Java HBase
1. Tworzenie infrastruktury jako usługi (IaaS) maszyny wirtualnej w tej samej sieci wirtualnej platformy Azure i tej samej podsieci. Aby uzyskać instrukcje dotyczące tworzenia nowej maszyny wirtualnej IaaS, zobacz [tworzenia maszyny wirtualnej systemem Windows Server](../../virtual-machines/windows/quick-create-portal.md). Gdy czynności opisane w tym dokumencie, należy użyć następujących wartości dla konfiguracji sieci:

   * **Sieć wirtualna**: &lt;nazwa klastra >-Sieć wirtualna
   * **Podsieci**: podsieć1

   > [!IMPORTANT]
   > Zastąp &lt;nazwa klastra > o nazwie używane podczas tworzenia klastra usługi HDInsight w poprzednich krokach.
   >
   >

   Używając tych wartości, maszyna wirtualna jest umieszczona w tej samej sieci wirtualnej i podsieci co klaster usługi HDInsight. Ta konfiguracja pozwala na bezpośrednio komunikować się ze sobą. Istnieje możliwość tworzenia klastra usługi HDInsight z węzłem krawędzi puste. Węzeł brzegowy może służyć do zarządzania klastrem.  Aby uzyskać więcej informacji, zobacz [użycia węzłów pusty krawędzi w usłudze HDInsight](../hdinsight-apps-use-edge-node.md).

2. Nawiązać HBase zdalnie za pomocą aplikacji Java, należy użyć w pełni kwalifikowaną nazwę (FQDN). Aby to ustalić, należy uzyskać sufiks DNS konkretnego połączenia klastra HBase. Aby to zrobić, użyj jednej z następujących metod:

   * Korzystanie z przeglądarki sieci Web do wywoływania Ambari:

     Przejdź do https://&lt;ClusterName >.azurehdinsight.net/api/v1/clusters/&lt;ClusterName > / hostuje? minimal_response = true. Monitor przechodzi w stan to plik JSON o sufiksów DNS.
   * Za pomocą witryny sieci Web Ambari:

     1. Przejdź do https://&lt;ClusterName >. azurehdinsight.net.
     2. Kliknij przycisk **hostów** z górnego menu.
   * Użyj Curl w celu wykonywania wywołań REST:

    ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
    ```

     W danych JavaScript Object Notation (JSON) zwrócone Znajdź pozycję "host_name". Zawiera nazwę FQDN dla węzłów w klastrze. Na przykład:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Część nazwy domeny rozpoczynający się od nazwy klastra jest sufiks DNS. Na przykład mycluster.b1.cloudapp.net.
   * Korzystanie z programu Azure PowerShell

     Użyj następującego skryptu programu Azure PowerShell do rejestrowania **Get ClusterDetail** funkcja, która może służyć do zwrócenia sufiks DNS:

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

     Po uruchomieniu skryptu programu Azure PowerShell, użyj następującego polecenia do zwrócenia sufiks DNS przy użyciu **Get ClusterDetail** funkcji. Określ nazwę klastra HDInsight HBase, nazwę administratora i hasło administratora kopii, korzystając z tego polecenia.

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

Aby sprawdzić, czy maszyny wirtualne mogą komunikować się z klastra HBase, użyj polecenia `ping headnode0.<dns suffix>` z maszyny wirtualnej. Na przykład headnode0.mycluster.b1.cloudapp.net ping.

Aby użyć tych informacji w aplikacji Java, można wykonać kroki opisane w [Użyj Maven do tworzenia aplikacji Java korzystających z bazy danych HBase w usłudze HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) do tworzenia aplikacji. Aby połączyć się ze zdalnym serwerem bazy danych HBase aplikacji, należy zmodyfikować **hbase-site.xml** pliku w tym przykładzie do zastosowania nazwy FQDN dla dozorcy. Na przykład:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> Aby uzyskać więcej informacji na temat rozpoznawania nazw w usłudze Azure sieci wirtualnych oraz o sposobie używania własnych serwera DNS, zobacz [rozpoznawania nazw (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób utworzyć klaster HBase. Aby dowiedzieć się więcej, zobacz:

* [Rozpoczynanie pracy z usługą HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Użyj węzłami pusty krawędzi w usłudze HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurowanie replikacji bazy danych HBase w usłudze HDInsight](apache-hbase-replication.md)
* [Tworzenie klastrów Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Rozpoczęcie korzystania z bazy danych HBase z użyciem usługi Hadoop w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Omówienie usługi Virtual Network](../../virtual-network/virtual-networks-overview.md)

[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

