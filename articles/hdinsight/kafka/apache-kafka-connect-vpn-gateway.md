---
title: Łączenie się z platformą Kafka przy użyciu sieci wirtualnych — Azure HDInsight
description: Dowiedz się, jak bezpośrednio łączyć się z platformą Kafka w usłudze HDInsight za pośrednictwem sieci wirtualnej platformy Azure. Dowiedz się, jak łączyć się z platformą Kafka z klientami deweloperskimi przy użyciu bramy sieci VPN lub od klientów w sieci lokalnej przy użyciu urządzenia bramy sieci VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 36ff0d5f1fc96b2013555d37a869ebf629a22be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272125"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Nawiązywanie połączenia z platformą Apache Kafka w usłudze HDInsight przy użyciu sieci wirtualnej platformy Azure

Dowiedz się, jak bezpośrednio łączyć się z apache kafka na usługi HDInsight za pośrednictwem sieci wirtualnej platformy Azure. Ten dokument zawiera informacje na temat łączenia się z platformą Kafka przy użyciu następujących konfiguracji:

* Z zasobów w sieci lokalnej. To połączenie jest nawiązywało przy użyciu urządzenia sieci VPN (oprogramowania lub sprzętu) w sieci lokalnej.
* Ze środowiska programistycznego przy użyciu klienta oprogramowania VPN.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Architektura i planowanie

Usługa HDInsight nie zezwala na bezpośrednie połączenie z platformą Kafka za pośrednictwem publicznego internetu. Zamiast tego klienci platformy Kafka (producenci i konsumenci) muszą stosować jedną z następujących metod połączenia:

* Uruchom klienta w tej samej sieci wirtualnej co kafka na HDInsight. Ta konfiguracja jest używana w dokumencie [Start with Apache Kafka on HDInsight.](apache-kafka-get-started.md) Klient jest uruchamiany bezpośrednio w węzłach klastra HDInsight lub na innej maszynie wirtualnej w tej samej sieci.

* Podłącz sieć prywatną, taką jak sieć lokalna, do sieci wirtualnej. Ta konfiguracja umożliwia klientom w sieci lokalnej bezpośrednią pracę z platformą Kafka. Aby włączyć tę konfigurację, wykonaj następujące zadania:

  1. Utwórz sieć wirtualną.
  2. Utwórz bramę sieci VPN, która używa konfiguracji lokacja lokacja. Konfiguracja używana w tym dokumencie łączy się z urządzeniem bramy sieci VPN w sieci lokalnej.
  3. Utwórz serwer DNS w sieci wirtualnej.
  4. Skonfiguruj przekazywanie dalej między serwerem DNS w każdej sieci.
  5. Utwórz kafkę w klastrze HDInsight w sieci wirtualnej.

     Aby uzyskać więcej informacji, zobacz [Połącz z apache kafka z](#on-premises) sieci lokalnej sekcji.

* Połącz poszczególne maszyny z siecią wirtualną za pomocą bramy sieci VPN i klienta sieci VPN. Aby włączyć tę konfigurację, wykonaj następujące zadania:

  1. Utwórz sieć wirtualną.
  2. Utwórz bramę sieci VPN, która używa konfiguracji typu punkt-lokacja. Tej konfiguracji można używać zarówno z klientami systemu Windows, jak i MacOS.
  3. Utwórz kafkę w klastrze HDInsight w sieci wirtualnej.
  4. Konfigurowanie platformy Kafka do reklamy IP. Ta konfiguracja umożliwia klientowi łączenie się przy użyciu adresów IP brokera zamiast nazw domen.
  5. Pobierz i korzystaj z klienta SIECI VPN w systemie dewelopera.

     Aby uzyskać więcej informacji, zobacz [sekcję Połącz z platformą Apache Kafka z klientem sieci VPN.](#vpnclient)

     > [!WARNING]  
     > Ta konfiguracja jest zalecana tylko do celów programisty ze względu na następujące ograniczenia:
     >
     > * Każdy klient musi połączyć się za pomocą klienta oprogramowania sieci VPN.
     > * Klient sieci VPN nie przekazuje żądań rozpoznawania nazw do sieci wirtualnej, dlatego do komunikowania się z platformą Kafka należy używać adresowania IP. Komunikacja IP wymaga dodatkowej konfiguracji w klastrze platformy Kafka.

Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight w sieci wirtualnej, zobacz [Planowanie sieci wirtualnej dla klastrów usługi Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a>Łączenie się z apache kafka z sieci lokalnej

Aby utworzyć klaster platformy Kafka, który komunikuje się z siecią lokalną, wykonaj kroki opisane w [pliku Połącz HDInsight z lokalnym dokumentem sieciowym.](./../connect-on-premises-network.md)

> [!IMPORTANT]  
> Podczas tworzenia klastra HDInsight wybierz typ __klastra platformy Kafka.__

W tych krokach utworzy się następującą konfigurację:

* Azure Virtual Network
* Brama sieci VPN między lokacjami
* Konto usługi Azure Storage (używane przez usługę HDInsight)
* Usługa Kafka w usłudze HDInsight

Aby sprawdzić, czy klient platformy Kafka może łączyć się z klastrem z lokalnego, należy wykonać kroki opisane w sekcji [Klientu Programu Example: Python.](#python-client)

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a>Połącz się z apache kafka za pomocą klienta sieci VPN

Aby utworzyć następującą konfigurację, należy wykonać kroki opisane w tej sekcji:

* Azure Virtual Network
* Brama sieci VPN typu punkt-lokacja
* Konto usługi Azure Storage (używane przez usługę HDInsight)
* Usługa Kafka w usłudze HDInsight

1. Wykonaj kroki opisane w dokumencie [Praca z certyfikatami z podpisem własnym dla połączeń typu punkt-lokacja.](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) Ten dokument tworzy certyfikaty potrzebne dla bramy.

2. Otwórz monit programu PowerShell i użyj następującego kodu, aby zalogować się do subskrypcji platformy Azure:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Aby utworzyć zmienne zawierające informacje o konfiguracji, użyj następującego kodu:

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

4. Użyj następującego kodu, aby utworzyć grupę zasobów platformy Azure i sieć wirtualną:

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
    > Może upłynąć kilka minut, aby ten proces został ukończony.

5. Użyj następującego kodu, aby utworzyć konto usługi Azure Storage i kontener obiektów blob:

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

6. Użyj następującego kodu, aby utworzyć klaster HDInsight:

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

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurowanie platformy Kafka do reklamy IP

Domyślnie Apache Zookeeper zwraca nazwę domeny brokerów platformy Kafka do klientów. Ta konfiguracja nie działa z klientem oprogramowania sieci VPN, ponieważ nie może używać rozpoznawania nazw dla jednostek w sieci wirtualnej. W przypadku tej konfiguracji należy wykonać następujące czynności, aby skonfigurować platformę Kafka do anonsowania adresów IP zamiast nazw domen:

1. Korzystając z przeglądarki internetowej, przejdź do adresu `https://CLUSTERNAME.azurehdinsight.net`. Zamień `CLUSTERNAME` na nazwę kafki w klastrze HDInsight.

    Po wyświetleniu monitu użyj nazwy użytkownika i hasła HTTPS dla klastra. Zostanie wyświetlony interfejs użytkownika sieci Web Ambari dla klastra.

2. Aby wyświetlić informacje o platformie Kafka, wybierz pozycję __Kafka__ z listy po lewej stronie.

    ![Lista usług z wyróżnioną poka](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Aby wyświetlić konfigurację platformy Kafka, wybierz __configs__ z górnego środka.

    ![Konfiguracja usług Apache Ambari](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. Aby znaleźć konfigurację __kafka-env,__ wprowadź `kafka-env` w polu __Filtr__ w prawym górnym rogu.

    ![Konfiguracja platformy Kafka dla kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Aby skonfigurować platformę Kafka do anonsowania adresów IP, dodaj następujący tekst u dołu pola __szablonu kafka-env:__

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Aby skonfigurować interfejs, na który nasłuchuje program Kafka, wprowadź `listeners` pole __Filtr__ w prawym górnym rogu.

7. Aby skonfigurować platformę Kafka do nasłuchiwać na wszystkich `PLAINTEXT://0.0.0.0:9092`interfejsach sieciowych, zmień wartość w polu __detektory__ na .

8. Aby zapisać zmiany konfiguracji, użyj przycisku __Zapisz.__ Wprowadź wiadomość tekstową opisującą zmiany. Po zapisaniu zmian wybierz __przycisk OK.__

    ![Apache Ambari zapisać konfigurację](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. Aby zapobiec błędom podczas ponownego uruchamiania platformy Kafka, użyj przycisku __Akcje usługi__ i wybierz opcję __Włącz tryb konserwacji__. Wybierz przycisk OK, aby wykonać tę operację.

    ![Akcje serwisowe z podświetlenia włączenia konserwacji](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Aby ponownie uruchomić platformę Kafka, użyj przycisku __Uruchom ponownie__ i wybierz __pozycję Uruchom ponownie wszystkie, których dotyczy problem__. Potwierdź ponowne uruchomienie, a następnie użyj przycisku __OK__ po zakończeniu operacji.

    ![Przycisk Uruchom ponownie z podświetlenym ponownym uruchomieniem wszystkich wyróżnionych](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. Aby wyłączyć tryb konserwacji, użyj przycisku __Akcje serwisu__ i wybierz __opcję Wyłącz tryb konserwacji__. Wybierz **przycisk OK,** aby wykonać tę operację.

### <a name="connect-to-the-vpn-gateway"></a>Łączenie się z bramą sieci VPN

Aby połączyć się z bramą sieci VPN, użyj sekcji __Połącz z platformą Azure__ dokumentu [konfiguruj połączenie typu punkt-lokacja.](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect)

## <a name="example-python-client"></a><a id="python-client"></a>Przykład: Klient języka Python

Aby sprawdzić poprawność łączności z platformą Kafka, należy wykonać następujące kroki, aby utworzyć i uruchomić producenta i konsumenta języka Python:

1. Użyj jednej z następujących metod, aby pobrać w pełni kwalifikowaną nazwę domeny (FQDN) i adresy IP węzłów w klastrze platformy Kafka:

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

    Ten skrypt zakłada, że `$resourceGroupName` jest to nazwa grupy zasobów platformy Azure, która zawiera sieć wirtualną.

    Zapisz zwrócone informacje do użycia w następnych krokach.

2. Aby zainstalować klienta [języka kafka-python,](https://kafka-python.readthedocs.io/) należy użyć następujących czynności:

    ```bash
    pip install kafka-python
    ```

3. Aby wysłać dane do platformy Kafka, użyj następującego kodu języka Python:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Zastąp `'kafka_broker'` wpisy adresami zwróconymi z kroku 1 w tej sekcji:

   * Jeśli używasz __klienta sieci VPN oprogramowania,__ zastąp `kafka_broker` wpisy adresem IP węzłów procesu roboczego.

   * Jeśli __włączono rozpoznawanie nazw za pośrednictwem niestandardowego serwera DNS,__ zastąp `kafka_broker` wpisy nazwą FQDN węzłów procesu roboczego.

     > [!NOTE]
     > Ten kod wysyła `test message` ciąg `testtopic`do tematu . Domyślną konfiguracją platformy Kafka w programie HDInsight jest utworzenie tematu, jeśli nie istnieje.

4. Aby pobrać wiadomości z platformy Kafka, użyj następującego kodu języka Python:

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

    Zastąp `'kafka_broker'` wpisy adresami zwróconymi z kroku 1 w tej sekcji:

    * Jeśli używasz __klienta sieci VPN oprogramowania,__ zastąp `kafka_broker` wpisy adresem IP węzłów procesu roboczego.

    * Jeśli __włączono rozpoznawanie nazw za pośrednictwem niestandardowego serwera DNS,__ zastąp `kafka_broker` wpisy nazwą FQDN węzłów procesu roboczego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight z siecią wirtualną, zobacz [Planowanie wdrożenia sieci wirtualnej dla klastrów usługi Azure HDInsight.](../hdinsight-plan-virtual-network-deployment.md)

Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej platformy Azure z bramą sieci VPN typu punkt-lokacja, zobacz następujące dokumenty:

* [Konfigurowanie połączenia typu "punkt-lokacja" przy użyciu portalu Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurowanie połączenia typu punkt-lokacja przy użyciu programu Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Aby uzyskać więcej informacji na temat pracy z apache kafka na HDInsight, zobacz następujące dokumenty:

* [Wprowadzenie do apache Kafka w programie HDInsight](apache-kafka-get-started.md)
* [Użyj dublowania z Apache Kafka na HDInsight](apache-kafka-mirroring.md)
