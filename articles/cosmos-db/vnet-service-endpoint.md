---
title: Bezpieczny dostęp do konta bazy danych rozwiązania Cosmos Azure przy użyciu punktu końcowego usługi Azure Virtual Network | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano kroki wymagane do instalacji punkt końcowy usługi sieci wirtualnej dla bazy danych Azure rozwiązania Cosmos.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: e6b263c1eb9fe3b151f0a51b5da9a92b8ced4549
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109746"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Bezpieczny dostęp do konta bazy danych rozwiązania Cosmos Azure przy użyciu punktu końcowego usługi Azure Virtual Network

Aby zezwolić na dostęp tylko z określonej podsieci sieci wirtualnej platformy Azure można skonfigurować konta CosmosDB Azure. Przez włączenie [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla CosmosDB Azure z sieci wirtualnej i jej podsieci, ruch jest zapewniona optymalnego i bezpiecznego trasy do bazy danych rozwiązania Cosmos Azure.  

Azure Cosmos DB to usługa globalnie dystrybuowanej, wielomodelowej bazy danych. Dane konta bazy danych rozwiązania Cosmos Azure można replikować do wielu regionach. Po skonfigurowaniu bazy danych Azure rozwiązania Cosmos z punktem końcowym usługi sieci wirtualnej każdej sieci wirtualnej zezwala na dostęp z adresów IP, który należy do podsieci specicifc. Na poniższej ilustracji przedstawiono ilustrację bazy danych rozwiązania Cosmos platformy Azure, która ma włączony punkt końcowy usługi sieci wirtualnej:

![Architektura punkt końcowy usługi sieci wirtualnej](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Po skonfigurowaniu konta bazy danych Azure rozwiązania Cosmos punkt końcowy usługi sieci wirtualnej, jest możliwy tylko z określonej podsieci, dostęp do wszystkich publicznych/Internetu jest usuwany. Aby dowiedzieć się w szczegółowych o punktów końcowych usługi, odwoływać się do platformy Azure [Omówienie punktów końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) artykułu.

> [!NOTE]
> Obecnie można skonfigurować dla interfejsu API Azure rozwiązania Cosmos bazy danych SQL lub kont funkcji Mongo API punktów końcowych usługi sieci wirtualnej. Możliwość konfigurowania punktów końcowych usługi dla innych interfejsów API i suwerennych chmury, takich jak platformy Azure w Niemczech lub Azure dla instytucji rządowych będzie wkrótce dostępna. Jeśli masz istniejące zapory IP skonfigurowanych dla tego konta bazy danych Azure rozwiązania Cosmos Uwaga konfigurację zapory, Usuń zapory IP, a następnie skonfiguruj listy ACL punktu końcowego usługi. Po skonfigurowaniu punktu końcowego usługi można ponownie włączyć zapory IP, jeśli to konieczne.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Konfigurowanie punktu końcowego usługi za pomocą portalu Azure
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Skonfiguruj punkt końcowy usługi dla istniejącej sieci wirtualnej platformy Azure i podsieci

1. Z **wszystkie zasoby** bloku sieci wirtualnej można znaleźć chcesz skonfigurować punkt końcowy usługi dla bazy danych Azure rozwiązania Cosmos. Przejdź do **punkty końcowe usługi** bloku i upewnij się, że podsieci sieci wirtualnej została włączona dla punktu końcowego usługi "Azure.CosmosDB".  

   ![Potwierdź włączony punkt końcowy usługi](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. Z **wszystkie zasoby** bloku Azure DB rozwiązania Cosmos konta można znaleźć chcesz zabezpieczyć.  

3. Przed włączeniem punkt końcowy usługi sieci wirtualnej, skopiuj informacje o zaporze IP skojarzonych z Twoim kontem platformy Azure DB rozwiązania Cosmos do użytku w przyszłości. IP zapory można ponownie włączyć po skonfigurowaniu punktu końcowego usługi.  

4. Wybierz **zapory i sieci wirtualne** z menu ustawień i wybierz pozycję Zezwalaj na dostęp z **wybrane sieci**.  

3. Aby udzielić dostępu do istniejącej sieci wirtualnej dla podsieci w sieci wirtualnej, zaznacz **Dodaj istniejącej sieci wirtualnej platformy Azure**.  

4. Wybierz **subskrypcji** z której chcesz dodać sieci wirtualnej platformy Azure. Wybierz platformy Azure **sieci wirtualnych** i **podsieci** chcesz zapewnić dostęp do Twojego konta bazy danych Azure rozwiązania Cosmos. Następnie wybierz **włączyć** włączyć wybranych sieci z punktów końcowych usługi dla "Microsoft.AzureCosmosDB". Po zakończeniu wybierz **Dodaj**.  

   ![Wybierz sieć wirtualną i podsieć](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Jeśli punkt końcowy usługi dla bazy danych Azure rozwiązania Cosmos wcześniej nie jest skonfigurowany dla wybranej sieci wirtualnych platformy Azure i podsieci, można skonfigurować w ramach tej operacji. Włączanie dostępu potrwa do 15 minut. Jest bardzo ważne, aby wyłączyć zapory IP po zauważyć w dół zawartość zapory ACL renabling je później. 

   ![sieć wirtualna i podsieć został pomyślnie skonfigurowany](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Teraz konto bazy danych Azure rozwiązania Cosmos tylko będzie zezwalać na ruch z to wybrany podsieci. Jeśli wcześniej miała włączone IP zapory, należy ponownie włączyć za pomocą wcześniejszych informacji.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Skonfiguruj punkt końcowy usługi dla nowej sieci wirtualnej platformy Azure i podsieci

1. Z **wszystkie zasoby** bloku Azure DB rozwiązania Cosmos konta można znaleźć chcesz zabezpieczyć.  

> [!NOTE]
> Jeśli masz istniejące zapory IP skonfigurowanych dla tego konta bazy danych rozwiązania Cosmos Azure należy pamiętać, konfigurację zapory, Usuń zapory IP, a następnie włącz punktu końcowego usługi. Po włączeniu punktu końcowego usługi bez disbling zapory ruchu z tego zakresu ip spowoduje utratę tożsamość wirtualnego adresu IP i zostanie usunięte z komunikatem o błędzie filtru IP. Tak, aby uniknąć tego błędu zawsze należy wyłączyć reguły zapory, skopiuj je, włączyć punkt końcowy usługi z podsieci i na koniec listy ACL podsieci z rozwiązania Cosmos bazy danych. Po skonfigurowaniu punktu końcowego usługi i Dodaj listę kontroli dostępu można ponownie włączyć zapory IP ponownie w razie potrzeby.

2. Przed włączeniem punkt końcowy usługi sieci wirtualnej, skopiuj informacje o zaporze IP skojarzonych z Twoim kontem platformy Azure DB rozwiązania Cosmos do użytku w przyszłości. IP zapory można ponownie włączyć po skonfigurowaniu punktu końcowego usługi.  

3. Wybierz **zapory i sieci wirtualnych platformy Azure** z menu ustawień i wybierz pozycję Zezwalaj na dostęp z **wybrane sieci**.  

4. Aby udzielić dostępu do nowych Azure sieci wirtualnej, w sieci wirtualnej, wybierz **Dodaj nową sieć wirtualną**.  

5. Podaj szczegóły wymagane do utworzenia nowej sieci wirtualnej, a następnie wybierz opcję Utwórz. Podsieć zostanie utworzona z punktem końcowym usługi dla "Microsoft.AzureCosmosDB" jest włączona.

   ![Wybierz sieć wirtualną i podsieć dla nowej sieci wirtualnej](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Zezwalaj na dostęp z portalu Azure

Po włączeniu punktów końcowych usługi sieci wirtualnej platformy Azure dla konta bazy danych Azure DB rozwiązania Cosmos dostęp z portalu lub innych usług platformy Azure jest domyślnie wyłączona. Dostęp do bazy danych Azure rozwiązania Cosmos konta bazy danych z maszyn poza skonfigurowanym podsieci są zablokowane, łącznie z dostępem z portalu.

![Zezwalaj na dostęp z portalu](./media/vnet-service-endpoint/allow-access-from-portal.png)

Jeśli konta bazy danych Azure rozwiązania Cosmos jest używane przez inne usługi Azure, takich jak usługi Azure Search lub uzyskać dostępu do usługi analiza strumienia lub usługi Power BI, zezwolisz na dostęp przez sprawdzenie **zezwolić na dostęp do usług Azure**.

Aby upewnić się, masz dostęp do bazy danych Azure rozwiązania Cosmos metryki z portalu, należy włączyć **zezwolić na dostęp do portalu Azure** opcje. Aby dowiedzieć się więcej o tych opcjach, zobacz [połączenia z portalu Azure](firewall-support.md#connections-from-the-azure-portal) i [połączeń z usług Azure PaaS](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services) sekcje. Po wybraniu dostępu, wybierz **zapisać** Aby zapisać ustawienia.

## <a name="remove-a-virtual-network-or-subnet"></a>Usuwanie podsieci lub sieci wirtualnej 

1. Z **wszystkie zasoby** bloku, Znajdź Azure DB rozwiązania Cosmos konto, dla którego jest przypisane punktów końcowych usługi.  

2. Wybierz **zapory i sieci wirtualne** z menu ustawień.  

3. Aby usunąć reguły podsieci lub sieci wirtualnej, wybierz pozycję "..." obok sieci wirtualnej lub podsieci i wybierz **Usuń**.

   ![Usuń sieć wirtualną](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Kliknij przycisk **zapisać** Aby zastosować zmiany.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Konfigurowanie punktu końcowego usługi za pomocą programu Azure PowerShell 

Aby skonfigurować punkt końcowy usługi do konta bazy danych Azure rozwiązania Cosmos przy użyciu programu Azure PowerShell, wykonaj następujące kroki:  

1. Zainstaluj najnowszą [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) i [logowania](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Upewnij się, należy pamiętać, ustawienia zapory IP i całkowicie usunąć zapory IP przed włączeniem punkt końcowy usługi dla konta.


> [!NOTE]
> Jeśli masz istniejące zapory IP skonfigurowanych dla tego konta bazy danych rozwiązania Cosmos Azure należy pamiętać, konfigurację zapory, Usuń zapory IP, a następnie włącz punktu końcowego usługi. Po włączeniu punktu końcowego usługi bez disbling zapory ruchu z tego zakresu ip spowoduje utratę tożsamość wirtualnego adresu IP i zostanie usunięte z komunikatem o błędzie filtru IP. Tak, aby uniknąć tego błędu zawsze należy wyłączyć reguły zapory, skopiuj je, włączyć punkt końcowy usługi z podsieci i na koniec listy ACL podsieci z rozwiązania Cosmos bazy danych. Po skonfigurowaniu punktu końcowego usługi i Dodaj listę kontroli dostępu można ponownie włączyć zapory IP ponownie w razie potrzeby.

2. Przed włączeniem punkt końcowy usługi sieci wirtualnej, skopiuj informacje o zaporze IP skojarzonych z Twoim kontem platformy Azure DB rozwiązania Cosmos do użytku w przyszłości. Zapora IP będzie ponownie włączyć po skonfigurowaniu punktu końcowego usługi.  

3. Włącz punkt końcowy usługi na istniejącą podsieć sieci wirtualnej.  

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

4. Przygotowanie do włączenia listy kontroli dostępu na koncie CosmosDB upewniając się, że sieć wirtualna i podsieć mają włączone dla bazy danych Azure rozwiązania Cosmos punktu końcowego usługi.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. Pobierz właściwości konta bazy danych Azure rozwiązania Cosmos, uruchamiając następujące polecenie cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Inicjowanie zmiennych do użycia w przyszłości. Skonfigurować wszystkie zmienne z istniejącej definicji konta, jeśli masz wiele lokalizacji, należy dodać je jako część tablicy. W tym kroku należy również skonfigurować punkt końcowy usługi sieci wirtualnej przez ustawienie zmiennej "accountVNETFilterEnabled" na wartość "True". Ta wartość jest później przypisane do parametru "isVirtualNetworkFilterEnabled".  

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

7. Aktualizowanie właściwości konta bazy danych rozwiązania Cosmos Azure przy użyciu nowej konfiguracji, uruchamiając następujące polecenia cmdlet: 

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

8. Uruchom następujące polecenie, aby sprawdzić, czy Twoje konto bazy danych Azure rozwiązania Cosmos jest zaktualizowane z punktem końcowym usługi sieci wirtualnej, skonfigurowanym w poprzednim kroku:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Dodaj punkt końcowy usługi sieci wirtualnej jest włączona Zapora IP konta bazy danych Azure rozwiązania Cosmos

1. Należy najpierw wyłączyć IP zapory dostęp do konta bazy danych Azure rozwiązania Cosmos.  

2. Włącz konto bazy danych Azure rozwiązania Cosmos punktu końcowego sieci wirtualnej przy użyciu jednej z metod opisanych w poprzednich sekcjach.  

3. Ponownie włączyć dostęp zapory IP. 

## <a name="test-the-access"></a>Testowanie dostępu

Aby sprawdzić, czy punkty końcowe usługi dla bazy danych Azure rozwiązania Cosmos są skonfigurowane zgodnie z oczekiwaniami, wykonaj następujące kroki:

* Skonfigurować dwie podsieci w sieci wirtualnej frontonu i wewnętrznej bazy danych, należy włączyć punkt końcowy usługi DB rozwiązania Cosmos podsieci wewnętrznej bazy danych.  

* Włączenie dostępu w ramach konta bazy danych rozwiązania Cosmos podsieci wewnętrznej bazy danych.  

* Utwórz dwie maszyny wirtualne-do-jednego w podsieci wewnętrznej bazy danych i drugą w podsieci frontonu.  

* Spróbuj dostęp do konta bazy danych Azure rozwiązania Cosmos z obu maszyn wirtualnych. Można się połączyć z maszyną wirtualną utworzoną w podsieci wewnętrznej bazy danych, ale nie z utworzonym w podsieci frontonu. Żądanie zostanie błąd 404 się podczas próby połączenia z podsieci frontonu, który potwierdza, że dostęp do bazy danych Azure rozwiązania Cosmos jest zabezpieczone przy użyciu punkt końcowy usługi sieci wirtualnej. Maszyny w podsieci wewnętrznej bazy danych będzie funkcjonować prawidłowo.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Co się stanie, gdy uzyskujesz dostęp do konta z bazy danych Azure rozwiązania Cosmos sieć wirtualną, której włączono listy kontroli dostępu (ACL)?  

HTTP 404 zwracany jest błąd.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Czy podsieci sieci wirtualnej utworzonej w różnych regionach, mogą uzyskiwać dostęp do konta bazy danych Azure rozwiązania Cosmos w innym regionie? Na przykład jeśli konto bazy danych Azure rozwiązania Cosmos zachodnie stany USA, wschodnie stany USA a sieci wirtualnej są w wielu regionach, można sieci wirtualnej dostęp do bazy danych rozwiązania Cosmos Azure?  

Tak, sieci wirtualne utworzone w różnych regionach dostęp można uzyskać nową funkcję. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Konto bazy danych Azure rozwiązania Cosmos mają zarówno punkt końcowy usługi sieci wirtualnej i zapory?  

Tak, punkt końcowy usługi sieci wirtualnej i zapory mogą współistnieć. Ogólnie rzecz biorąc upewnij się, że dostęp do portalu jest zawsze włączona przed skonfigurowaniem punktu końcowego sieci wirtualnej do umożliwiają wyświetlanie metryki skojarzone z kontenera.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>I "umożliwiają dostęp do innych usług platformy Azure w danym regionie Azure" podczas dostępu do punktu końcowego usługi jest włączona dla bazy danych rozwiązania Cosmos Azure?  

Jest to wymagane, tylko kiedy zechcesz, konto bazy danych Azure rozwiązania Cosmos być dostępne dla innych Azure pierwszej strony usług takich jak fabryki danych Azure, Azure Search lub dowolnej usługi, które zostało wdrożone w podany region platformy Azure.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Jak wiele punktów końcowych usługi sieci wirtualnej są dozwolone w przypadku bazy danych rozwiązania Cosmos Azure?  

64 punktów końcowych usługi sieci wirtualnej są dozwolone dla konta bazy danych Azure rozwiązania Cosmos.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Co to jest relacja między reguły punktu końcowego usługi i grupy zabezpieczeń sieci (NSG)?  

Reguły NSG w usłudze Azure DB rozwiązania Cosmos pozwalają ograniczyć dostęp do określonego zakresu adresów IP DB rozwiązania Cosmos platformy Azure. Jeśli chcesz zezwolić na dostęp do wystąpienia bazy danych Azure rozwiązania Cosmos, który znajduje się w określonej [region](https://azure.microsoft.com/global-infrastructure/regions/), można określić region w następującym formacie: 

    AzureCosmosDB.<region name>

Aby dowiedzieć się więcej na temat NSG tagów, zobacz [tagi usługi sieci wirtualnej](../virtual-network/security-overview.md#service-tags) artykułu. 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Co to jest relacja między IP zapory i możliwości punkt końcowy usługi sieci wirtualnej?  

Te dwie funkcje uzupełniają do zapewnienia izolacji bazy danych Azure rozwiązania Cosmos zasoby i zabezpieczenia. Przy użyciu protokołu IP zapory zapewnia, że statyczne adresy IP dostęp do konta bazy danych Azure rozwiązania Cosmos.  

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Adres IP urządzenia lokalnego, który jest połączony za pośrednictwem sieci wirtualnej Azure gateway(VPN) lub Express route bramy masz dostęp do konta platformy Azure DB rozwiązania Cosmos?  

Adres IP lub zakres adresów IP lokalnego urządzenia powinien można dodać do listy statyczne adresy IP w celu uzyskania dostępu do konta bazy danych Azure rozwiązania Cosmos.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Co się stanie, jeśli usuniesz siecią wirtualną, która ma ustawienia punktu końcowego usługi dla bazy danych rozwiązania Cosmos Azure?  

Usunięcie sieci wirtualnej informacje listy ACL skojarzone z bazy danych Azure rozwiązania Cosmos jest usuwany, a usuwa interakcji między siecią wirtualną i konto bazy danych Azure rozwiązania Cosmos. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Co się stanie w przypadku bazy danych Azure rozwiązania Cosmos konta, które ma włączone punkt końcowy usługi sieci wirtualnej jest usuwany?

Metadane skojarzone z określonego konta bazy danych Azure rozwiązania Cosmos jest usuwany z podsieci. I jest odpowiedzialny za użytkownika końcowego można usunąć podsieci i sieci wirtualne używane.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Aby utworzyć punkt końcowy usługi dla bazy danych rozwiązania Cosmos Azure można używać peered sieci wirtualnej?  

Nie tylko bezpośredniego sieci wirtualnej i ich podsieci można utworzyć punktami końcowymi usług Azure DB rozwiązania Cosmos.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Co się dzieje źródłowy adres IP zasobów, takich jak maszyny wirtualnej w podsieci, która ma włączony punkt końcowy usługi bazy danych rozwiązania Cosmos Azure?

Po włączeniu punktów końcowych usługi sieci wirtualnej źródłowych adresów IP zasobów w podsieci sieci wirtualnej zostanie przełączona z używania publicznych adresów IPV4 do prywatnych adresów sieci wirtualnej platformy Azure dla ruchu do bazy danych Azure rozwiązania Cosmos.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Bazy danych rozwiązania Cosmos Azure znajdują się w sieci wirtualnej platformy Azure, podana przez klienta?  

Azure DB rozwiązania Cosmos jest usługę wielodostępną z publicznym adresem IP. Jeśli ograniczysz dostęp do podsieci sieci wirtualnej platformy Azure przy użyciu funkcji punktu końcowego usługi Dostęp jest ograniczony dla Twojego konta bazy danych rozwiązania Cosmos Azure za pośrednictwem danej sieci wirtualnej platformy Azure i jej podsieci.  Konto platformy Azure DB rozwiązania Cosmos nie znajduje się w tej sieci wirtualnej platformy Azure. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Co zrobić, jeśli niczego będą rejestrowane w dzienniku analiza/OMS. Jeśli jest włączone?  

Azure DB rozwiązania Cosmos przeprowadzi wypychanie dzienniki z adresu IP (bez ostatni oktet) ze stanem 403 dla żądania zablokowana z powodu listy ACL.  

## <a name="next-steps"></a>Kolejne kroki
Aby skonfigurować zaporę bazy danych Azure rozwiązania Cosmos znajduje się w temacie [zapory Obsługa](firewall-support.md) artykułu.

