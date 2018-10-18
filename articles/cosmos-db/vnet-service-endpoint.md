---
title: Bezpieczny dostęp do konta usługi Azure Cosmos DB przy użyciu punktu końcowego usługi Azure Virtual Network | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano kroki wymagane do instalacji punktu końcowego usługi sieci wirtualnej dla usługi Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: 868f465cc651043d3ef4b1735b4b528252572dbb
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378089"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Bezpieczny dostęp do konta usługi Azure Cosmos DB przy użyciu punktu końcowego usługi Azure Virtual Network

Konta usługi Azure cosmos DB można skonfigurować w taki sposób, aby umożliwić dostęp tylko z określonej podsieci sieci wirtualnej platformy Azure. Po włączeniu [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla usługi Azure CosmosDB z sieci wirtualnej i jej podsieci, ruch jest zapewniony optymalnego i bezpiecznego trasy do usługi Azure Cosmos DB.  

Azure Cosmos DB to usługa globalnie dystrybuowanej, wielomodelowej bazy danych. Dane na koncie usługi Azure Cosmos DB można replikować do wielu regionów. Po skonfigurowaniu usługi Azure Cosmos DB z punktem końcowym usługi sieci wirtualnej w każdej sieci wirtualnej umożliwia dostęp z adresów IP, który należy do podsieci specicifc. Na poniższej ilustracji przedstawiono ilustrację usługi Azure Cosmos DB, która ma włączonego punktu końcowego usługi sieci wirtualnej:

![Architektura punktu końcowego usługi sieci wirtualnej](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Po skonfigurowaniu konta usługi Azure Cosmos DB z punktem końcowym usługi sieci wirtualnej są dostępne tylko z określonej podsieci, publiczne/internet dostęp zostanie usunięty. Dowiedz się na szczegółowe na temat punktów końcowych usługi, zapoznaj się na platformie Azure [Omówienie punktów końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) artykułu.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Konfigurowanie punktu końcowego usługi za pomocą witryny Azure portal
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Skonfiguruj punkt końcowy usługi dla istniejącej sieci wirtualnej platformy Azure i podsieci

1. Z **wszystkie zasoby** bloku sieci wirtualnej, możesz znaleźć chcesz skonfigurować punkt końcowy usługi dla usługi Azure Cosmos DB. Przejdź do **punkty końcowe usługi** bloku i upewnij się, że podsieć sieci wirtualnej został włączony dla punktu końcowego usługi "Azure.CosmosDB".  

   ![Upewnij się, włączonego punktu końcowego usługi](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. Z **wszystkie zasoby** bloku konta usługi Azure Cosmos DB, możesz znaleźć chcesz zabezpieczyć.  

3. Przed włączeniem punkt końcowy usługi sieci wirtualnej, należy skopiować informacje zapory adresów IP skojarzonych z Twoim kontem usługi Azure Cosmos DB do użycia w przyszłości. Po skonfigurowaniu punktu końcowego usługi, można ponownie włączyć zapory adresów IP.  

4. Wybierz **zapory i sieci wirtualne** z poziomu menu ustawień i wybierz dostęp z **wybrane sieci**.  

3. Aby udzielić dostępu do istniejącej sieci wirtualnej w podsieci, w ramach sieci wirtualnej, wybierz **Dodaj istniejącą sieć wirtualną platformy Azure**.  

4. Wybierz **subskrypcji** z którego chcesz dodać sieci wirtualnej platformy Azure. Wybierz pozycję Azure **sieci wirtualne** i **podsieci** chcesz zapewnić dostęp do konta usługi Azure Cosmos DB. Następnie wybierz pozycję **Włącz** można włączyć wybranych sieci za pomocą punktów końcowych usługi dla "Microsoft.AzureCosmosDB". Po zakończeniu wybierz **Dodaj**.  

   ![Wybierz sieć wirtualną i podsieć](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Jeśli punktu końcowego usługi dla usługi Azure Cosmos DB wcześniej nie skonfigurowano dla wybranych sieci wirtualnych platformy Azure i podsieci, może zostać skonfigurowany jako część tej operacji. Włączanie dostępu potrwa do 15 minut. Jest to bardzo ważne wyłączyć zapory adresów IP po zanotowaną zawartość zapory listy ACL dla renabling je później. 

   ![sieć wirtualną i podsieć został pomyślnie skonfigurowany](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Teraz Twoje konto usługi Azure Cosmos DB będzie zezwalać tylko na ruch z tego wybrana podsieć. Jeśli wcześniej było włączone zapory adresów IP, ponownie włącz je za pomocą wcześniejszych informacji.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Skonfiguruj punkt końcowy usługi dla nowej sieci wirtualnej platformy Azure i podsieci

1. Z **wszystkie zasoby** bloku konta usługi Azure Cosmos DB, możesz znaleźć chcesz zabezpieczyć.  

> [!NOTE]
> Jeśli masz istniejące zapory adresów IP, które są skonfigurowane dla konta usługi Azure Cosmos DB, należy pamiętać, konfigurację zapory, Usuń zapory adresów IP, a następnie włączyć punkt końcowy usługi. Po włączeniu punktu końcowego usługi bez disbling zapory, ruchu z tego zakresu adresów ip, utracisz tożsamości wirtualnego adresu IP i dlatego jest porzucany IP filtr komunikatu o błędzie. Tak, aby uniknąć tego błędu zawsze należy wyłączyć reguły zapory, skopiuj je, należy włączyć punkt końcowy usługi z podsieci, a na koniec listy ACL podsieci z usługi Cosmos DB. Po skonfigurowaniu punktu końcowego usługi i dodać listy ACL można ponownie włączyć zapory adresów IP ponownie w razie potrzeby.

2. Przed włączeniem punkt końcowy usługi sieci wirtualnej, należy skopiować informacje zapory adresów IP skojarzonych z Twoim kontem usługi Azure Cosmos DB do użycia w przyszłości. Po skonfigurowaniu punktu końcowego usługi, można ponownie włączyć zapory adresów IP.  

3. Wybierz **zapory i sieci wirtualne platformy Azure** z poziomu menu ustawień i wybierz dostęp z **wybrane sieci**.  

4. Aby udzielić dostępu do nowej sieci wirtualnej platformy Azure, w ramach sieci wirtualnej, wybierz **Dodaj nową sieć wirtualną**.  

5. Podaj szczegóły wymagane do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję Utwórz. Podsieć zostanie utworzona z punktem końcowym usługi dla "Microsoft.AzureCosmosDB" włączone.

   ![Wybierz sieć wirtualną i podsieć dla nowej sieci wirtualnej](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Zezwalaj na dostęp z witryny Azure portal

Po włączeniu punktów końcowych usługi sieci wirtualnej platformy Azure dla Twojego konta bazy danych Azure Cosmos DB dostęp z portalu lub innych usług platformy Azure jest domyślnie wyłączona. Dostęp do Twojego konta bazy danych Azure Cosmos DB z maszyn spoza podsieci skonfigurowane są blokowane, łącznie z dostępem z portalu.

![Zezwalaj na dostęp z portalu](./media/vnet-service-endpoint/allow-access-from-portal.png)

Jeśli konto usługi Azure Cosmos DB jest używany przez inne usługi platformy Azure, takich jak usługa Azure Search lub dostępne z usługi Stream analytics lub Power BI, możesz zezwolić na dostęp, sprawdzając **zezwolić na dostęp do usług platformy Azure**.

Aby upewnić się, masz dostęp do metryk usługi Azure Cosmos DB z portalu, musisz włączyć **zezwolić na dostęp do witryny Azure portal** opcje. Aby dowiedzieć się więcej o tych opcjach, zobacz [połączenia z witryny Azure portal](firewall-support.md#connections-from-the-azure-portal) i [połączeń z usług PaaS platformy Azure](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services) sekcje. Po wybraniu dostępu, wybierz **Zapisz** Aby zapisać ustawienia.

## <a name="remove-a-virtual-network-or-subnet"></a>Usuwanie sieci wirtualnej lub podsieci 

1. Z **wszystkie zasoby** bloku, konto Znajdź usługa Azure Cosmos DB, dla którego jest przypisane punkty końcowe usługi.  

2. Wybierz **zapory i sieci wirtualne** z poziomu menu ustawień.  

3. Aby usunąć sieci wirtualnej lub podsieci reguły, wybierz pozycję "..." obok sieci wirtualnej lub podsieci i wybierz **Usuń**.

   ![Usuwanie sieci wirtualnej](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Kliknij przycisk **Zapisz** Aby zastosować zmiany.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Konfigurowanie punktu końcowego usługi za pomocą programu Azure PowerShell 

Aby skonfigurować punkt końcowy usługi konta usługi Azure Cosmos DB przy użyciu programu Azure PowerShell, wykonaj następujące kroki:  

1. Zainstaluj najnowszą wersję [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) i [logowania](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Upewnij się, należy pamiętać, ustawienia zapory adresów IP i całkowicie usunąć zapory adresów IP przed włączeniem punkt końcowy usługi dla konta.


> [!NOTE]
> Jeśli masz istniejące zapory adresów IP, które są skonfigurowane dla konta usługi Azure Cosmos DB, należy pamiętać, konfigurację zapory, Usuń zapory adresów IP, a następnie włączyć punkt końcowy usługi. Po włączeniu punktu końcowego usługi bez disbling zapory, ruchu z tego zakresu adresów ip, utracisz tożsamości wirtualnego adresu IP i dlatego jest porzucany IP filtr komunikatu o błędzie. Tak, aby uniknąć tego błędu zawsze należy wyłączyć reguły zapory, skopiuj je, należy włączyć punkt końcowy usługi z podsieci, a na koniec listy ACL podsieci z usługi Cosmos DB. Po skonfigurowaniu punktu końcowego usługi i dodać listy ACL można ponownie włączyć zapory adresów IP ponownie w razie potrzeby.

2. Przed włączeniem punkt końcowy usługi sieci wirtualnej, należy skopiować informacje zapory adresów IP skojarzonych z Twoim kontem usługi Azure Cosmos DB do użycia w przyszłości. Spowoduje ponowne włączenie zapory adresów IP, po skonfigurowaniu punktu końcowego usługi.  

3. Włączanie punktu końcowego usługi dla istniejącej podsieci sieci wirtualnej.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

4. Przygotuj się do włączenia listy kontroli dostępu na koncie usługi CosmosDB, upewniając się, że sieć wirtualną i podsieć ma usługi włączonego punktu końcowego usługi Azure Cosmos DB.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. Pobierz właściwości konta usługi Azure Cosmos DB, uruchamiając następujące polecenie cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Inicjowanie zmiennych do użycia w przyszłości. Skonfigurować wszystkie zmienne z istniejącej definicji konto, jeśli masz wiele lokalizacji, należy dodać je jako część tablicy. W tym kroku skonfigurujesz również punkt końcowy usługi sieci wirtualnej przez ustawienie zmiennej "accountVNETFilterEnabled" na "True". Ta wartość jest później przypisane do parametru "isVirtualNetworkFilterEnabled".  

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. Aktualizowanie właściwości konta usługi Azure Cosmos DB przy użyciu nowej konfiguracji, uruchamiając następujące polecenia cmdlet: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. Uruchom następujące polecenie, aby zweryfikować, że konto usługi Azure Cosmos DB został zaktualizowany o punkt końcowy usługi sieci wirtualnej, który został skonfigurowany w poprzednim kroku:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Dodaj punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos DB, które ma zapory adresów IP włączone

1. Najpierw wyłączyć IP zapory dostęp do konta usługi Azure Cosmos DB.  

2. Włączanie punktu końcowego sieci wirtualnej do konta usługi Azure Cosmos DB przy użyciu jednej z metod opisanych w poprzednich sekcjach.  

3. Ponownie włączyć dostęp zapory adresów IP. 

## <a name="test-the-access"></a>Testowanie dostępu

Aby sprawdzić, czy punkty końcowe usługi dla usługi Azure Cosmos DB są skonfigurowane zgodnie z oczekiwaniami, wykonaj następujące kroki:

* Skonfigurować dwie podsieci w sieci wirtualnej frontonu i zaplecza, należy włączyć punkt końcowy usługi Cosmos DB podsieci wewnętrznej bazy danych.  

* Włącz dostęp w ramach konta usługi Cosmos DB podsieci wewnętrznej bazy danych.  

* Utwórz dwie maszyny wirtualne — w podsieci wewnętrznej bazy danych, a drugi w podsieci frontonu.  

* Wypróbuj, uzyskiwanie dostępu do konta usługi Azure Cosmos DB z obu maszyn wirtualnych. Można nawiązać połączenie z maszyny wirtualnej utworzonej w podsieci wewnętrznej bazy danych, ale nie z utworzonym w podsieci frontonu. Żądanie zgłosi błąd z 404, podczas nawiązywania połączenia z podsieci frontonu, która potwierdza, że dostęp do usługi Azure Cosmos DB jest zabezpieczony przy użyciu punktu końcowego usługi sieci wirtualnej. Maszyny w podsieci wewnętrznej bazy danych będzie działać prawidłowo.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Co się stanie, gdy uzyskujesz dostęp do konta Azure Cosmos DB, która ma sieci wirtualnej, listy kontroli dostępu (ACL) jest włączona?  

HTTP 404 zostanie zwrócony błąd.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Czy na podsieci sieci wirtualnej utworzonej w różnych regionach, które mogą uzyskać dostęp do konta usługi Azure Cosmos DB w innym regionie? Na przykład jeśli konto usługi Azure Cosmos DB znajduje się w regionie zachodnie stany USA lub wschodnie stany USA i sieci wirtualnej są w wielu regionach, sieć wirtualną dostęp usługi Azure Cosmos DB?  

Tak, sieciami wirtualnymi utworzonymi w różnych regionach jest dostępna po nowe możliwości. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Konto usługi Azure Cosmos DB możliwy punkt końcowy usługi sieci wirtualnej i zapory?  

Tak, usługa sieci wirtualnych punktu końcowego i zapory mogą współistnieć. Ogólnie rzecz biorąc należy zagwarantować, że dostęp do portalu jest zawsze włączona przed skonfigurowaniem punktu końcowego usługi sieci wirtualnej umożliwia wyświetlanie metryk skojarzonych z danym kontenerem.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Można "Akceptuj połączenia z w ramach publicznych centrów danych platformy Azure" I po włączeniu dostępu do punktu końcowego usługi dla usługi Azure Cosmos DB?  

Jest to wymagane tylko, aby Twoje konto usługi Azure Cosmos DB można uzyskać dostęp przez inne platformy Azure do pierwszej strony usług takich jak usługi Azure Data factory, usługa Azure Search lub dowolnej usługi, która jest wdrażana w danym regionie platformy Azure.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Ile punktów końcowych usługi sieci wirtualnej są dozwolone w przypadku usługi Azure Cosmos DB?  

64 punkty końcowe usługi sieci wirtualnej są dozwolone dla konta usługi Azure Cosmos DB.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Jaki jest związek między regułami punktem końcowym usługi oraz grupy zabezpieczeń sieci (NSG)?  

Reguły sieciowej grupy zabezpieczeń w usłudze Azure Cosmos DB pozwalają ograniczyć dostęp do określonego zakresu adresów IP programu usługi Azure Cosmos DB. Jeśli chcesz zezwolić na dostęp do wystąpienia usługi Azure Cosmos DB, który znajduje się w konkretnym [region](https://azure.microsoft.com/global-infrastructure/regions/), możesz określić region, w następującym formacie: 

    AzureCosmosDB.<region name>

Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń tagów zobacz [tagi usługi sieci wirtualnej](../virtual-network/security-overview.md#service-tags) artykułu. 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Co to jest relacja między zapory adresów IP i możliwości punktu końcowego usługi sieci wirtualnej?  

Te dwie funkcje uzupełniają się wzajemnie w celu zapewnienia izolacji zasobów usługi Azure Cosmos DB oraz je zabezpieczyć. Przy użyciu protokołu IP zapory gwarantuje, że statyczne adresy IP mają dostęp do konta usługi Azure Cosmos DB.  

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Adres IP lokalnego urządzenia, która jest połączona za pomocą usługi Azure Virtual Network gateway(VPN) lub brama usługi Express route dostęp do usługi Azure Cosmos DB konta?  

Adres IP lub zakresu adresów IP lokalnego urządzenia należy dodać do listy statyczne adresy IP, aby uzyskać dostęp do konta usługi Azure Cosmos DB.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Co się stanie, jeśli usuniesz sieć wirtualna, zawierający ustawienia punktu końcowego usługi dla usługi Azure Cosmos DB?  

Podczas usuwania sieci wirtualnej informacje listy ACL skojarzonych z czy Azure Cosmos DB zostanie usunięty, i usuwa interakcji między siecią wirtualną, a konto usługi Azure Cosmos DB. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Co się stanie, jeśli konto usługi Azure Cosmos DB, która ma włączonego punktu końcowego usługi sieci wirtualnej zostanie usunięty?

Metadane skojarzone z określonego konta usługi Azure Cosmos DB jest usuwany z podsieci. I odpowiada za użytkownika końcowego można usunąć podsieci i sieci wirtualnej używane.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Aby utworzyć punkt końcowy usługi dla usługi Azure Cosmos DB mogą używać wirtualnej sieci równorzędnej?  

Nie tylko bezpośredniego sieci wirtualnej i ich podsieci można utworzyć punktów końcowych usługi Azure Cosmos DB.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Co stanie się źródłowy adres IP zasobu, takich jak maszyny wirtualne w podsieci, która ma włączonego punktu końcowego usługi Azure Cosmos DB?

Po włączeniu punktów końcowych usługi sieci wirtualnej, źródłowe adresy IP zasobów w podsieci sieci wirtualnej będzie przełączają się z używania publicznych adresów IPV4 na użycie prywatnych adresów sieci wirtualnej platformy Azure dla ruchu do usługi Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Usługi Azure Cosmos DB znajdują się w sieci wirtualnej platformy Azure, udostępniane przez klienta?  

Usługa Azure Cosmos DB to usługa dla wielu dzierżawców z publicznym adresem IP. Jeśli ograniczysz dostęp do podsieci sieci wirtualnej platformy Azure przy użyciu funkcji punktu końcowego usługi Dostęp jest ograniczony do konta usługi Azure Cosmos DB przy użyciu danej sieci wirtualnej platformy Azure i jej podsieci.  Konto usługi Azure Cosmos DB nie znajduje się w tej sieci wirtualnej platformy Azure. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Co zrobić, jeśli nic będą rejestrowane w Log Analytics/OMS. Jeśli jest włączone?  

Usługa Azure Cosmos DB będzie umożliwiać wypychanie powiadomień dzienników za pomocą adresu IP (bez ostatni oktet) ze stanem 403 żądanie zablokowane przez listy kontroli dostępu.  

## <a name="next-steps"></a>Kolejne kroki
Konfigurowanie zapory dla usługi Azure Cosmos DB, zobacz [zapory pomocy technicznej](firewall-support.md) artykułu.

