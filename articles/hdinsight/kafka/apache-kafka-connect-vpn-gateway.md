---
title: Nawiązywanie połączenia przy użyciu sieci wirtualnych — Azure HDInsight Kafka
description: Dowiedz się, jak łączyć się bezpośrednio do platformy Kafka w HDInsight przy użyciu usługi Azure Virtual Network. Dowiedz się, jak połączyć się z platformy Kafka z klientami programowania przy użyciu bramy sieci VPN lub klientów w sieci lokalnej za pomocą urządzenia bramy sieci VPN.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: ddff9ffb00f4167cb8f64a75b129711467de739d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297061"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Nawiązać połączenie z platformy Apache Kafka w HDInsight przy użyciu sieci wirtualnej platformy Azure

Dowiedz się, jak łączyć się bezpośrednio do platformy Apache Kafka w HDInsight przy użyciu usługi Azure Virtual Network. Ten dokument zawiera informacje dotyczące nawiązywania połączenia platformy Kafka za pomocą następujących konfiguracji:

* Z zasobów w sieci lokalnej. To połączenie jest nawiązywane za pomocą urządzenia sieci VPN (oprogramowania lub sprzętu) w sieci lokalnej.
* Z poziomu środowiska projektowego, przy użyciu oprogramowania klienta VPN.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Planowanie i architektura

HDInsight nie zezwala na bezpośrednie połączenie z platformą Kafka za pośrednictwem publicznej sieci internet. Zamiast tego klienci platformy Kafka (producenci i konsumenci) muszą używać jednej z następujących metod połączenia:

* W tej samej sieci wirtualnej jako Kafka HDInsight uruchomić klienta. Ta konfiguracja jest używana w [rozpoczynać platformy Apache Kafka w HDInsight](apache-kafka-get-started.md) dokumentu. Klient uruchamia bezpośrednio na węzły klastra HDInsight lub na inną maszynę wirtualną w tej samej sieci.

* Łączenie sieci prywatnej, np. z siecią lokalną z siecią wirtualną. Ta konfiguracja umożliwia klientom w sieci lokalnej, aby bezpośrednio pracować z platformą Kafka. Aby włączyć tę konfigurację, wykonaj następujące czynności:

  1. Utwórz sieć wirtualną.
  2. Tworzenie bramy sieci VPN, który korzysta z konfiguracji lokacja lokacja. Konfiguracja używana w tym dokumencie nawiązuje połączenie z urządzeniem bramy sieci VPN w sieci lokalnej.
  3. Tworzenie serwera DNS w sieci wirtualnej.
  4. Konfigurowanie składnika przesyłanie dalej między serwerem DNS w każdej sieci.
  5. Utwórz platformy Kafka w klastrze HDInsight w sieci wirtualnej.

     Aby uzyskać więcej informacji, zobacz [Connect do platformy Apache Kafka z siecią lokalną](#on-premises) sekcji. 

* Połącz poszczególnych maszyn w sieci wirtualnej przy użyciu bramy sieci VPN i klienta sieci VPN. Aby włączyć tę konfigurację, wykonaj następujące czynności:

  1. Utwórz sieć wirtualną.
  2. Utwórz bramę sieci VPN, która używa konfiguracji punkt lokacja. Ta konfiguracja może służyć z klientami z systemem MacOS i Windows.
  3. Utwórz platformy Kafka w klastrze HDInsight w sieci wirtualnej.
  4. Do celów reklamowych adresów IP, należy skonfigurować platformy Kafka. Ta konfiguracja umożliwia klientom nawiązywanie połączenia przy użyciu brokera adresów IP zamiast nazwy domeny.
  5. Pobierz i korzystania z klienta sieci VPN w systemie programistycznym.

     Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z platformy Apache Kafka za pomocą klienta sieci VPN](#vpnclient) sekcji.

     > [!WARNING]  
     > Ta konfiguracja jest zalecana tylko w celach programistycznych, ze względu na następujące ograniczenia:
     >
     > * Każdy klient musi połączyć przy użyciu oprogramowania klienta VPN.
     > * Klient sieci VPN nie zostały spełnione żądań rozpoznawania nazw do sieci wirtualnej, dlatego należy używać adresu IP, odnoszący się do komunikowania się z platformą Kafka. Komunikacji z pakietem Integracyjnym wymaga dodatkowej konfiguracji klastra Kafka.

Aby uzyskać więcej informacji na temat używania HDInsight w sieci wirtualnej, zobacz [rozszerzyć HDInsight przy użyciu usługi Azure Virtual Networks](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Łączenie do platformy Apache Kafka z sieci lokalnej

Aby utworzyć klaster platformy Kafka, która komunikuje się z Twoją siecią lokalną, wykonaj kroki opisane w [Connect HDInsight z siecią lokalną](./../connect-on-premises-network.md) dokumentu.

> [!IMPORTANT]  
> Podczas tworzenia klastra HDInsight wybierz __Kafka__ typ klastra.

Te kroki służą utworzeniu następującej konfiguracji:

* Azure Virtual Network
* Bramy sieci VPN typu lokacja lokacja
* Konto usługi Azure Storage (wykorzystywane przez HDInsight)
* Usługa Kafka w usłudze HDInsight

Aby sprawdzić, czy klient platformy Kafka łączy się z klastrem ze środowiska lokalnego, wykonaj kroki w [przykładu: Klient Python](#python-client) sekcji.

## <a id="vpnclient"></a> Łączenie z platformy Apache Kafka przy użyciu klienta sieci VPN

Wykonaj kroki w tej sekcji, aby utworzyć następującą konfigurację:

* Azure Virtual Network
* Brama sieci VPN typu punkt lokacja
* Konto usługi Azure Storage (wykorzystywane przez HDInsight)
* Usługa Kafka w usłudze HDInsight

1. Postępuj zgodnie z instrukcjami w [Praca z certyfikatami z podpisem własnym dla połączeń punkt lokacja](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) dokumentu. W tym dokumencie tworzy certyfikaty potrzebny dla bramy.

2. Otwórz wiersz polecenia programu PowerShell i użyj poniższego kodu, aby zarejestrować się w Twojej subskrypcji platformy Azure:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Użyj poniższego kodu, aby utworzyć zmienne, które zawierają informacje o konfiguracji:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Aby utworzyć grupę zasobów platformy Azure i sieci wirtualnej, użyj następującego kodu:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > Może potrwać kilka minut na zakończenie tego procesu.

5. Użyj poniższego kodu, aby utworzyć kontener konta usługi Azure Storage i blob:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Użyj poniższego kodu, aby utworzyć klaster HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Ten proces trwa około 15 minut.

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurowanie platformy Kafka do celów reklamowych adresu IP

Domyślnie Apache Zookeeper zwraca nazwę domeny brokerów platformy Kafka na klientach. Ta konfiguracja nie działa przy użyciu oprogramowania klienckiego sieci VPN, zgodnie z rozpoznawania nazw nie może używać w przypadku jednostek w sieci wirtualnej. W przypadku tej konfiguracji umożliwia skonfigurowanie platformy Kafka anonsowanie adresów IP zamiast nazwy domeny następujące czynności:

1. Korzystając z przeglądarki internetowej przejdź do `https://CLUSTERNAME.azurehdinsight.net`. Zastąp `CLUSTERNAME` o nazwie platformy Kafka w klastrze HDInsight.

    Po wyświetleniu monitu użyj protokołu HTTPS, nazwę użytkownika i hasło dla klastra. Interfejs użytkownika sieci Web Ambari klastra jest wyświetlana.

2. Aby wyświetlić informacje o platformie Kafka, wybierz pozycję __Kafka__ z listy po lewej stronie.

    ![Wyróżnioną listę usług z platformą Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Zaznacz, aby wyświetlić konfigurację platformy Kafka __Configs__ z górnej środkowej.

    ![Konfiguracje łączy dla platformy Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Aby znaleźć __env platformy kafka__ konfiguracji, wprowadź `kafka-env` w __filtru__ w prawym górnym rogu.

    ![Konfiguracja platformy Kafka, środowisko platformy kafka](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Aby skonfigurować Kafka anonsowanie adresów IP, Dodaj następujący tekst do dołu __kafka env szablonów__ pola:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Aby skonfigurować interfejs, który nasłuchuje platformy Kafka, wprowadź `listeners` w __filtru__ w prawym górnym rogu.

7. Aby skonfigurować Kafka do nasłuchiwania na wszystkich interfejsach sieciowych, zmień wartość w __odbiorników__ pole `PLAINTEXT://0.0.0.0:9092`.

8. Aby zapisać zmiany konfiguracji, należy użyć __Zapisz__ przycisku. Wprowadź wiadomość tekstową, opisująca zmiany. Wybierz __OK__ po zapisaniu zmian.

    ![Konfiguracja przycisk Zapisz](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Aby zapobiec błędom przy ponownym uruchamianiu platformy Kafka, użyj __działania usługi__ i wybrać __włączyć w trybie konserwacji__. Wybierz przycisk OK, aby ukończyć tę operację.

    ![Włączanie obsługi wyróżnione akcje usługi](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Ponowne uruchomienie platformy Kafka, użyj __ponowne uruchomienie__ i wybrać __ponowne uruchomienie wszystkich wpływ__. Potwierdź ponowne uruchomienie, a następnie użyj __OK__ przycisku po ukończeniu tej operacji.

    ![Uruchom ponownie wyróżnionym z ponownym uruchomieniem komputera wszystkie których to dotyczy](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Aby wyłączyć tryb konserwacji, użyj __działania usługi__ i wybrać __Włącz Wyłącz tryb konserwacji__. Wybierz **OK** do ukończenia tej operacji.

### <a name="connect-to-the-vpn-gateway"></a>Łączenie z bramą sieci VPN

Aby nawiązać połączenie z bramą sieci VPN, należy użyć __Połącz z platformą Azure__ części [Konfigurowanie połączenia typu punkt-lokacja](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) dokumentu.

## <a id="python-client"></a> Przykład: Klienta języka Python

Aby zweryfikować połączenie do platformy Kafka, należy użyć do tworzenia i uruchamiania Python producentów i konsumentów następujące czynności:

1. Pobieranie w pełni kwalifikowaną nazwę domeny (FQDN) i adresy IP z węzłów w klastrze Kafka, użyj jednej z następujących metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Ten skrypt założono, że `$resourceGroupName` to nazwa grupy zasobów platformy Azure, która zawiera sieć wirtualną.

    Zapisz informacje zwrócone do użycia w następnych krokach.

2. Poniższa tabela zainstalować [języka python na platformie kafka](https://kafka-python.readthedocs.io/) klienta:

    ```bash
    pip install kafka-python
    ```

3. Aby wysyłać dane do platformy Kafka, użyj następującego kodu języka Python:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Zastąp `'kafka_broker'` wpisów z adresami zwrócony z kroku 1 w tej sekcji:

   * Jeśli używasz __klienta sieci VPN oprogramowania__, Zastąp `kafka_broker` wpisów z adresu IP węzłów procesu roboczego.

   * Jeśli masz __włączone rozpoznawanie nazw za pomocą niestandardowego serwera DNS__, Zastąp `kafka_broker` zapisów o FQDN węzłów procesu roboczego.

     > [!NOTE]
     > Ten kod wysyła ciąg `test message` do tematu `testtopic`. Jest domyślna konfiguracja platformy kafka w HDInsight w celu utworzenia tematu, jeśli nie istnieje.

4. Można pobrać komunikaty z platformy Kafka, użyj następującego kodu języka Python:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Zastąp `'kafka_broker'` wpisów z adresami zwrócony z kroku 1 w tej sekcji:

    * Jeśli używasz __klienta sieci VPN oprogramowania__, Zastąp `kafka_broker` wpisów z adresu IP węzłów procesu roboczego.

    * Jeśli masz __włączone rozpoznawanie nazw za pomocą niestandardowego serwera DNS__, Zastąp `kafka_broker` zapisów o FQDN węzłów procesu roboczego.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat używania HDInsight z siecią wirtualną, zobacz [rozszerzyć usłudze Azure HDInsight przy użyciu usługi Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentu.

Aby uzyskać więcej informacji na temat tworzenia usługi Azure Virtual Network za pomocą bramy sieci VPN typu punkt-lokacja zobacz następujące dokumenty:

* [Konfigurowanie połączenia typu punkt-lokacja przy użyciu witryny Azure portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurowanie połączenia typu punkt-lokacja przy użyciu programu Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Aby uzyskać więcej informacji na temat pracy z platformą Apache Kafka w HDInsight zobacz następujące dokumenty:

* [Wprowadzenie do platformy Apache Kafka w HDInsight](apache-kafka-get-started.md)
* [Użyj funkcji dublowania przy użyciu platformy Apache Kafka w HDInsight](apache-kafka-mirroring.md)
