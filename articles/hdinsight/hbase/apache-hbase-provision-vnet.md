---
title: Tworzenie klastrów HBase w sieci wirtualnej — Azure
description: Wprowadzenie do korzystania z bazy danych HBase w usłudze Azure HDInsight. Dowiedz się, jak utworzyć klastry HDInsight HBase w sieci wirtualnej platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: e4e15d1c6554fc567f668b2033bff5b5664db918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972794"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Tworzenie klastrów Apache HBase w usłudze HDInsight w sieci wirtualnej platformy Azure

Dowiedz się, jak utworzyć klastry usługi Azure HDInsight Apache HBase w [sieci wirtualnej platformy Azure.](https://azure.microsoft.com/services/virtual-network/)

Dzięki integracji z siecią wirtualną klastry Apache HBase można wdrożyć w tej samej sieci wirtualnej co aplikacje, dzięki czemu aplikacje mogą komunikować się bezpośrednio z usługą HBase. Korzyści:

* Bezpośrednia łączność aplikacji sieci web z węzłami klastra HBase, która umożliwia komunikację za pośrednictwem interfejsów API zdalnego wywołania procedury (RPC) firmy HBase Java.
* Zwiększona wydajność dzięki nieposiedzeniu ruchu przez wiele bram i modułów równoważenia obciążenia.
* Możliwość przetwarzania poufnych informacji w bezpieczniejszy sposób bez narażania publicznego punktu końcowego.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Tworzenie klastra Apache HBase w sieci wirtualnej

W tej sekcji utworzysz klaster Apache HBase oparty na systemie Linux z zależnym kontem usługi Azure Storage w sieci wirtualnej platformy Azure przy użyciu [szablonu usługi Azure Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). Aby uzyskać inne metody tworzenia klastra i zrozumienie ustawień, zobacz [Tworzenie klastrów HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji na temat tworzenia klastrów Apache Hadoop w usłudze Apache Hadoop, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów usługi Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Niektóre właściwości są zakodowane na czas w szablonie. Przykład:
>
> * **Lokalizacja**: Wschodnie stany USA 2
> * **Wersja klastra**: 3.6
> * **Liczba węzłów procesu roboczego klastra**: 2
> * **Domyślne konto magazynu:** unikatowy ciąg znaków
> * **Nazwa sieci wirtualnej**: CLUSTERNAME-vnet
> * **Przestrzeń adresowa sieci wirtualnej**: 10.0.0.0/16
> * **Nazwa podsieci**: podsieć1
> * **Zakres adresów podsieci:** 10.0.0.0/24
>
> `CLUSTERNAME`zostanie zastąpiona nazwą klastra podajona podczas korzystania z szablonu.

1. Wybierz następujący obraz, aby otworzyć szablon w witrynie Azure portal. Szablon znajduje się w [szablonach szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. W oknie **dialogowym wdrażania niestandardowego** wybierz pozycję **Edytuj szablon**.

1. W wierszu 165 `Standard_A3` `Standard_A4_V2`zmień wartość na . Następnie wybierz pozycję **Zapisz**.

1. Wypełnij pozostały szablon następującymi informacjami:

    |Właściwość |Wartość |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję platformy Azure używaną do utworzenia klastra HDInsight, zależnego konta magazynu i sieci wirtualnej platformy Azure.|
    Grupa zasobów|Wybierz **pozycję Utwórz nowy**i określ nową nazwę grupy zasobów.|
    |Lokalizacja|Wybierz lokalizację dla grupy zasobów.|
    |Nazwa klastra|Wprowadź nazwę klastra Hadoop, który ma zostać utworzony.|
    |Nazwa użytkownika logowania do klastra i hasło|Domyślną nazwą użytkownika jest **admin**. Podaj hasło.|
    |Ssh Nazwa użytkownika i hasło|Domyślna nazwa użytkownika **to sshuser**.  Podaj hasło.|

    Wybierz **zgadzam się z warunkami podanymi powyżej.**

1. Wybierz pozycję **Kup**. Utworzenie klastra trwa około 20 minut. Po utworzeniu klastra można wybrać klaster w portalu, aby go otworzyć.

Po zakończeniu tego artykułu można usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Aby uzyskać instrukcje usuwania klastra, zobacz [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu portalu Azure.](../hdinsight-administer-use-portal-linux.md#delete-clusters)

Aby rozpocząć pracę z nowym klastrem HBase, można użyć procedur znalezionych w [Wprowadzenie do pracy przy użyciu Apache HBase z Apache Hadoop w HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Łączenie się z klastrem Apache HBase przy użyciu interfejsów API Apache HBase Java RPC

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz infrastrukturę jako maszynę wirtualną usługi (IaaS) w tej samej sieci wirtualnej platformy Azure i tej samej podsieci. Aby uzyskać instrukcje dotyczące tworzenia nowej maszyny wirtualnej IaaS, zobacz [Tworzenie maszyny wirtualnej z systemem Windows Server](../../virtual-machines/windows/quick-create-portal.md). Wykonując czynności opisane w tym dokumencie, należy użyć następujących wartości dla konfiguracji sieci:

* **Sieć wirtualna**: CLUSTERNAME-vnet
* **Podsieć**: podsieć1

> [!IMPORTANT]  
> Zamień `CLUSTERNAME` nazwę używaną podczas tworzenia klastra HDInsight w poprzednich krokach.

Przy użyciu tych wartości maszyna wirtualna jest umieszczana w tej samej sieci wirtualnej i podsieci co klaster HDInsight. Ta konfiguracja umożliwia im bezpośrednią komunikację ze sobą. Istnieje sposób tworzenia klastra HDInsight z pustym węzłem krawędzi. Węzeł krawędzi może służyć do zarządzania klastrem.  Aby uzyskać więcej informacji, zobacz [Używanie pustych węzłów krawędzi w aplikacji HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Uzyskiwanie w pełni kwalifikowanej nazwy domeny

W przypadku zdalnego łączenia się z usługą HBase przy użyciu aplikacji Java należy użyć w pełni kwalifikowanej nazwy domeny (FQDN). Aby to ustalić, należy uzyskać sufiks DNS specyficzny dla połączenia klastra HBase. Aby to zrobić, można użyć jednej z następujących metod:

* Użyj przeglądarki sieci Web, aby nawiązać połączenie [Apache Ambari:](https://ambari.apache.org/)

    Przejdź do `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Zwraca plik JSON z sufiksami DNS.

* Skorzystaj ze strony internetowej Ambari:

    1. Przejdź do `https://CLUSTERNAME.azurehdinsight.net`.
    2. Wybierz **hosty** z górnego menu.

* Użyj Curl do wykonywania połączeń REST:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

W zwróconych danych notacji obiektu JavaScript (JSON) znajdź wpis "host_name". Zawiera numer FQDN dla węzłów w klastrze. Przykład:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

Część nazwy domeny rozpoczynająca się od nazwy klastra jest sufiksem DNS. Na przykład `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

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

### <a name="verify-communication-inside-virtual-network"></a>Weryfikowanie komunikacji wewnątrz sieci wirtualnej

Aby sprawdzić, czy maszyna wirtualna może komunikować się `ping headnode0.<dns suffix>` z klastrem HBase, użyj polecenia z maszyny wirtualnej. Na przykład `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Aby użyć tych informacji w aplikacji Java, można wykonać kroki w [użyj Apache Maven do tworzenia aplikacji Java, które używają Apache HBase z HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) do tworzenia aplikacji. Aby aplikacja połączyła się ze zdalnym serwerem HBase, zmodyfikuj plik **hbase-site.xml** w tym przykładzie, aby użyć nazwy FQDN dla zookeeper. Przykład:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Aby uzyskać więcej informacji na temat rozpoznawania nazw w sieciach wirtualnych platformy Azure, w tym sposobu korzystania z własnego serwera DNS, zobacz [Rozpoznawanie nazw (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak utworzyć klaster Apache HBase. Aby dowiedzieć się więcej, zobacz:

* [Rozpoczynanie pracy z usługą HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Używanie pustych węzłów krawędziowych w umiań HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurowanie replikacji Apache HBase w udziale usługi HDInsight](apache-hbase-replication.md)
* [Tworzenie klastrów Apache Hadoop w udziale HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Zacznij korzystać z Apache HBase z Apache Hadoop w HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Omówienie usługi Virtual Network](../../virtual-network/virtual-networks-overview.md)
