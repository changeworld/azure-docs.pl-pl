---
title: Nawiązać połączenie przy użyciu sieci wirtualnych - Azure HDInsight Kafka | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać bezpośrednie Kafka w usłudze HDInsight przy użyciu sieci wirtualnej platformy Azure. Dowiedz się, jak połączyć się z Kafka z klientów Programowanie przy użyciu bramy sieci VPN lub klienci w sieci lokalnej za pomocą urządzenia bramy sieci VPN.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/02/2018
ms.author: larryfr
ms.openlocfilehash: 2740b5cf483fe3fbc2644510461863b939ffaae3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="connect-to-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Nawiązać Kafka w usłudze HDInsight przy użyciu sieci wirtualnej platformy Azure

Dowiedz się, jak nawiązać bezpośrednie Kafka w usłudze HDInsight przy użyciu sieci wirtualnej platformy Azure. Ten dokument zawiera informacje dotyczące nawiązywania połączenia Kafka przy użyciu następujących konfiguracji:

* Z zasobów w sieci lokalnej. To połączenie zostanie nawiązane za pomocą urządzenia sieci VPN (oprogramowania lub sprzętu) w sieci lokalnej.
* Środowiska programowania przy użyciu oprogramowania klienta sieci VPN.

## <a name="architecture-and-planning"></a>Planowanie i architektura

HDInsight nie zezwala na bezpośrednie połączenie Kafka za pośrednictwem publicznej sieci internet. Zamiast tego Kafka klientów (producenci i konsumenci) muszą używać jednej z następujących metod połączenia:

* Uruchom klienta w tej samej sieci wirtualnej, jak Kafka w usłudze HDInsight. Ta konfiguracja jest używana w [rozpoczynać Kafka Apache na HDInsight](apache-kafka-get-started.md) dokumentu. Klient uruchamia bezpośrednio na węzłach klastra usługi HDInsight lub inną maszynę wirtualną w tej samej sieci.

* Połączenie sieci prywatnej, takich jak sieci lokalnej do sieci wirtualnej. Ta konfiguracja pozwala klientom w sieci lokalnej pracować bezpośrednio z Kafka. Aby włączyć tę konfigurację, wykonaj następujące czynności:

    1. Utwórz sieć wirtualną.
    2. Tworzenie bramy sieci VPN, która używa konfiguracji lokacja lokacja. Konfiguracja użyta w tym dokumencie łączy się urządzenie bramy sieci VPN w sieci lokalnej.
    3. Utwórz serwer DNS w sieci wirtualnej.
    4. Konfiguruj przekazywanie między serwer DNS w każdej sieci.
    5. Zainstaluj Kafka w usłudze HDInsight w sieci wirtualnej.

    Aby uzyskać więcej informacji, zobacz [nawiązywanie Kafka z sieci lokalnej](#on-premises) sekcji. 

* Połączenie poszczególnych maszyn z sieci wirtualnej przy użyciu bramy sieci VPN i klienta sieci VPN. Aby włączyć tę konfigurację, wykonaj następujące czynności:

    1. Utwórz sieć wirtualną.
    2. Tworzenie bramy sieci VPN, która używa konfiguracji punkt lokacja. Ta konfiguracja może służyć z systemami Windows i MacOS klientów.
    3. Zainstaluj Kafka w usłudze HDInsight w sieci wirtualnej.
    4. Skonfiguruj Kafka IP reklamy. Ta konfiguracja umożliwia klientowi łączyć się przy użyciu adresów IP zamiast nazwy domeny.
    5. Pobranie i użycie klienta sieci VPN w systemie deweloperskim.

    Aby uzyskać więcej informacji, zobacz [nawiązywanie Kafka za pomocą klienta VPN](#vpnclient) sekcji.

    > [!WARNING]
    > Ta konfiguracja jest zalecana tylko do celów programistycznych, ze względu na następujące ograniczenia:
    >
    > * Każdy klient musi połączyć za pomocą oprogramowania klienta sieci VPN.
    > * Klient sieci VPN nie zostały spełnione żądań rozpoznawania nazw do sieci wirtualnej, należy użyć do komunikacji z Kafka o adresowaniu IP. Komunikacja IP wymaga dodatkowej konfiguracji klastra Kafka.

Aby uzyskać więcej informacji na temat używania usługi HDInsight w sieci wirtualnej, zobacz [rozszerzyć HDInsight przy użyciu sieci wirtualnych Azure](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Nawiązywanie połączenia z siecią lokalną do Kafka

Aby utworzyć klaster Kafka, który komunikuje się z sieci lokalnej, postępuj zgodnie z instrukcjami [HDInsight połączyć się z siecią lokalną](./../connect-on-premises-network.md) dokumentu.

> [!IMPORTANT]
> Podczas tworzenia klastra usługi HDInsight, wybierz __Kafka__ typ klastra.

Te kroki Utwórz następującą konfigurację:

* Azure Virtual Network
* Brama VPN lokacja lokacja
* Konto usługi Azure Storage (używane przez usługi HDInsight)
* Usługa Kafka w usłudze HDInsight

Aby sprawdzić, czy klient Kafka łączy do klastra z lokalnych, wykonaj czynności w [przykład: Python klienta](#python-client) sekcji.

## <a id="vpnclient"></a> Połączenie z Kafka z klienta sieci VPN

Wykonaj kroki w tej sekcji, aby utworzyć następującej konfiguracji:

* Azure Virtual Network
* Brama sieci VPN punkt lokacja
* Konto usługi Azure Storage (używane przez usługi HDInsight)
* Usługa Kafka w usłudze HDInsight

1. Postępuj zgodnie z instrukcjami [Praca z certyfikatów z podpisem własnym dla połączenia punkt lokacja](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) dokumentu. Ten dokument tworzy certyfikaty wymagane dla bramy.

2. Otwórz wiersz programu PowerShell i użyć poniższego kodu, aby zalogować się do subskrypcji platformy Azure:

    ```powershell
    Connect-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Aby utworzyć zmienne, które zawierają informacje o konfiguracji, należy użyć poniższego kodu:

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

4. Aby utworzyć grupy zasobów platformy Azure i siecią wirtualną, należy użyć poniższego kodu:

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
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
    > Może upłynąć kilka minut na zakończenie danego procesu.

5. Aby utworzyć kontener konto magazynu Azure i obiektów blob, należy użyć poniższego kodu:

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Poniższy kod umożliwia utworzenie klastra usługi HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
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
  > Ten proces trwa około 15 minut do wykonania.

### <a name="configure-kafka-for-ip-advertising"></a>Skonfiguruj Kafka do celów reklamowych IP

Domyślnie dozorcy zwraca nazwę domeny brokerzy Kafka do klientów. Ta konfiguracja nie działa z oprogramowania klienta sieci VPN, ponieważ nie może używać rozpoznawania nazw dla jednostek w sieci wirtualnej. W przypadku tej konfiguracji umożliwia skonfigurowanie Kafka anonsowanie adresów IP zamiast nazwy domeny następujące czynności:

1. Przy użyciu przeglądarki sieci web, przejdź do https://CLUSTERNAME.azurehdinsight.net. Zastąp __CLUSTERNAME__ o nazwie Kafka w klastrze usługi HDInsight.

    Po wyświetleniu monitu użyj protokołu HTTPS, nazwę użytkownika i hasło dla klastra. Zostanie wyświetlony interfejs użytkownika sieci Web Ambari dla klastra.

2. Aby wyświetlić informacje o Kafka, wybierz __Kafka__ na liście z lewej strony.

    ![Lista usług z Kafka wyróżnione](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Zaznacz, aby wyświetlić konfigurację Kafka __Configs__ ze środka top.

    ![Łącza Configs Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Aby znaleźć __kafka env__ konfiguracji, wprowadź `kafka-env` w __filtru__ w prawym górnym rogu.

    ![Konfiguracja Kafka, kafka env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Aby skonfigurować Kafka anonsowanie adresów IP, Dodaj poniższy tekst do dołu __kafka env szablonów__ pola:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Aby skonfigurować interfejs, który nasłuchuje Kafka, wprowadź `listeners` w __filtru__ w prawym górnym rogu.

7. Aby skonfigurować Kafka do nasłuchiwania na wszystkich interfejsach sieciowych, zmień wartość w __odbiorników__ do `PLAINTEXT://0.0.0.0:9092`.

8. Aby zapisać zmiany konfiguracji, użyj __zapisać__ przycisku. Wprowadź wiadomość tekstową opisujące zmiany. Wybierz __OK__ po zapisaniu zmian.

    ![Konfiguracja przyciskiem Zapisz](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Aby zapobiec błędom podczas ponownego uruchamiania Kafka, należy użyć __akcji usługi__ i wybrać __włączyć w trybie konserwacji__. Wybierz przycisk OK, aby wykonać tę operację.

    ![Włącz funkcję obsługi wyróżnione akcje usługi](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Aby ponownie uruchomić Kafka, należy użyć __Uruchom ponownie__ i wybrać __ponowne uruchomienie wszystkich wpływ__. Upewnij się, ponowne uruchomienie, a następnie użyj __OK__ przycisku po ukończeniu tej operacji.

    ![Uruchom ponownie przycisk o ponowne uruchomienie wszystkich wpływ wyróżnione](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Aby wyłączyć tryb konserwacji, użyj __akcji usługi__ i wybrać __włączyć Wyłącz tryb konserwacji__. Wybierz **OK** do ukończenia tej operacji.

### <a name="connect-to-the-vpn-gateway"></a>Łączenie się z bramą sieci VPN

Aby połączyć się z bramą sieci VPN, należy użyć __Connect Azure__ sekcji [skonfigurować połączenie punkt-lokacja](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) dokumentu.

## <a id="python-client"></a> Przykład: Python klienta

Do sprawdzania poprawności łączności Kafka, należy użyć do tworzenia i uruchamiania producent Python i konsumentów następujące czynności:

1. Można pobrać w pełni kwalifikowanej nazwy domeny (FQDN) i adresy IP w węzłach klastra Kafka, użyj jednej z następujących metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

    Ten skrypt, przy założeniu, że `$resourceGroupName` to nazwa grupy zasobów platformy Azure, która zawiera sieci wirtualnej.

    Zapisz informacje zwrócone do użycia w następnych krokach.

2. Należy zainstalować następujące [kafka python](http://kafka-python.readthedocs.io/) klienta:

        pip install kafka-python

3. Aby wysłać dane do Kafka, należy użyć poniższego kodu Python:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  # NOTE: you don't need the full list of worker nodes, just one or two.
  producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Zastąp `'kafka_broker'` wpisów z adresami zwrócony z kroku 1 w tej sekcji:

    * Jeśli używasz __klienta VPN oprogramowania__, Zastąp `kafka_broker` wpisów z adresu IP z węzłami procesów roboczych.

    * Jeśli masz __włączone rozpoznawanie nazw za pomocą niestandardowy serwer DNS__, Zastąp `kafka_broker` wpisów z nazwą FQDN węzłów procesu roboczego.

    > [!NOTE]
    > Ten kod wysyła ciąg `test message` do tematu `testtopic`. Domyślna konfiguracja Kafka w usłudze HDInsight jest tworzenie tematu, jeśli nie istnieje.

4. Aby pobrać komunikaty z Kafka, należy użyć poniższego kodu Python:

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

    * Jeśli używasz __klienta VPN oprogramowania__, Zastąp `kafka_broker` wpisów z adresu IP z węzłami procesów roboczych.

    * Jeśli masz __włączone rozpoznawanie nazw za pomocą niestandardowy serwer DNS__, Zastąp `kafka_broker` wpisów z nazwą FQDN węzłów procesu roboczego.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o korzystaniu z usługi HDInsight z sieci wirtualnej, zobacz [rozszerzenie Azure HDInsight przy użyciu sieci wirtualnej platformy Azure](../hdinsight-extend-hadoop-virtual-network.md) dokumentu.

Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej platformy Azure z bramą sieci VPN typu punkt-lokacja można znaleźć w następujących dokumentach:

* [Skonfiguruj połączenie punkt-lokacja za pomocą portalu Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Skonfiguruj połączenie punkt-lokacja za pomocą programu Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Więcej informacji na temat pracy z klastrem Kafka w usłudze HDInsight można znaleźć w następujących dokumentach:

* [Wprowadzenie do platformy Kafka w usłudze HDInsight](apache-kafka-get-started.md)
* [Użyj funkcji dublowania z Kafka w usłudze HDInsight](apache-kafka-mirroring.md)
