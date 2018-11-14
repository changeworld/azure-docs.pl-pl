---
title: Jak skonfigurować sieć wirtualną i dostępu dla konta usługi Azure Cosmos oparta na podsieci
description: W tym dokumencie opisano kroki wymagane do instalacji punktu końcowego usługi sieci wirtualnej dla usługi Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: b788490d588c217d97786f9306baad3083a9c03f
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629385"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>Jak uzyskać dostęp do zasobów usługi Azure Cosmos DB z sieciami wirtualnymi

Konta usługi Azure cosmos DB można skonfigurować w taki sposób, aby umożliwić dostęp tylko z określonej podsieci sieci wirtualnej platformy Azure. Istnieją dwa kroki wymagane w celu ograniczenia dostępu do konta usługi Azure Cosmos z połączeniami z podsieci w sieci wirtualnej (VNET).
 
1. Włącz podsieci, aby wysłać do usługi Azure Cosmos DB podsieci i tożsamość sieci Wirtualnej. Można to osiągnąć przez włączenie punktu końcowego usługi dla usługi Azure Cosmos DB w określonej podsieci.

1. Dodaj regułę na koncie usługi Azure Cosmos Określanie podsieci jako źródło, z którego, dostęp do konta.

> [!NOTE]
> Raz punktu końcowego usługi Cosmos Azure, Twoje konto jest włączone w podsieci, źródła ruchu docieranie do usługi Azure Cosmos DB przechodzi z publicznego adresu IP do sieci Wirtualnej i podsieci. Przełączanie ruchu ma zastosowanie do dowolnego konta usługi Azure Cosmos, którego uzyskiwany jest dostęp do tej podsieci. Jeśli konta usługi Azure Cosmos opartych na adresie IP zaporę, aby umożliwić tej podsieci, żądań z włączoną usługą podsieci nie będzie odpowiadać reguły zapory IP, a pliki zostały odrzucone. Aby dowiedzieć się więcej, zobacz kroki opisane w temacie [migrowany reguła zapory adresów IP do listy kontroli dostępu do sieci Wirtualnej](#migrate-from-firewall-to-vnet) dalszej części tego artykułu. 

Poniżej opisano sposób konfigurowania punktu końcowego usługi sieci Wirtualnej dla konta usługi Azure Cosmos DB.

## <a id="configure-using-portal"></a>Konfigurowanie punktu końcowego usługi za pomocą witryny Azure portal

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Skonfiguruj punkt końcowy usługi dla istniejącej sieci wirtualnej platformy Azure i podsieci

1. Z **wszystkie zasoby** bloku sieci wirtualnej, możesz znaleźć chcesz zabezpieczyć.

1. Wybierz **zapory i sieci wirtualne** z poziomu menu ustawień i wybierz dostęp z **wybrane sieci**.

1. Aby udzielić dostępu do istniejącej sieci wirtualnej w podsieci, w obszarze **sieci wirtualne**, wybierz opcję **Dodaj istniejącą sieć wirtualną platformy Azure**.

1. Wybierz **subskrypcji** z którego chcesz dodać sieci wirtualnej platformy Azure. Wybierz pozycję Azure **sieci wirtualne** i **podsieci** chcesz zapewnić dostęp do konta usługi Azure Cosmos. Następnie wybierz pozycję **Włącz** można włączyć wybranych sieci za pomocą punktów końcowych usługi dla "Microsoft.AzureCosmosDB". Po zakończeniu wybierz **Dodaj**. 

   ![Wybierz sieć wirtualną i podsieć](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Po włączeniu na koncie usługi Azure Cosmos zezwala tylko ruchu z tego wybrana podsieć. Sieć wirtualną i podsieć, którą dodałeś powinien pojawić się, jak pokazano na poniższym zrzucie ekranu:

   ![sieć wirtualną i podsieć został pomyślnie skonfigurowany](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Aby włączyć punkty końcowe usługi sieci wirtualnej, będziesz potrzebować następujących uprawnień subskrypcji:
  * Subskrypcja z siecią Wirtualną: Współautor sieci
  * Subskrypcji przy użyciu konta usługi Azure Cosmos: Współautor konta usługi DocumentDB

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Skonfiguruj punkt końcowy usługi dla nowej sieci wirtualnej platformy Azure i podsieci

1. Z **wszystkie zasoby** bloku konta usługi Azure Cosmos DB, możesz znaleźć chcesz zabezpieczyć.  

2. Przed włączeniem punkt końcowy usługi sieci wirtualnej, należy skopiować informacje zapory adresów IP skojarzonych z Twoim kontem usługi Azure Cosmos DB do użycia w przyszłości. Po skonfigurowaniu punktu końcowego usługi, można ponownie włączyć zapory adresów IP.  

3. Wybierz **zapory i sieci wirtualne platformy Azure** z poziomu menu ustawień i wybierz dostęp z **wybrane sieci**.  

4. Aby udzielić dostępu do nowej sieci wirtualnej platformy Azure, w ramach sieci wirtualnych wybierz **Dodaj nową sieć wirtualną**.  

5. Podaj szczegóły wymagane do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję Utwórz. Podsieć zostanie utworzona z punktem końcowym usługi dla "Microsoft.AzureCosmosDB" włączone.

   ![Wybierz sieć wirtualną i podsieć dla nowej sieci wirtualnej](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Jeśli Twoje konto usługi Azure Cosmos jest używany przez inne usługi platformy Azure, takich jak usługa Azure Search lub uzyskiwać dostęp z usługi Stream analytics lub Power BI, zezwalasz na dostęp, sprawdzając Zezwalaj na dostęp do usług platformy Azure.

Aby upewnić się, masz dostęp do metryk usługi Azure Cosmos DB z portalu, musisz włączyć **zezwolić na dostęp do witryny Azure portal** opcje. Aby dowiedzieć się więcej o tych opcjach, zobacz żądania z witryny Azure portal i żądanie z usług PaaS platformy Azure na części konfiguracji [zapory adresów IP](how-to-configure-firewall.md) artykułu. Po wybraniu dostępu, wybierz **Zapisz** Aby zapisać ustawienia.

## <a id="remove-vnet-or-subnet"></a>Usuwanie sieci wirtualnej lub podsieci 

1. Z **wszystkie zasoby** bloku, konto Znajdź usługa Azure Cosmos DB, dla którego jest przypisane punkty końcowe usługi.  

2. Wybierz **zapory i sieci wirtualne** z poziomu menu ustawień.  

3. Aby usunąć sieci wirtualnej lub podsieci reguły, wybierz pozycję "..." obok sieci wirtualnej lub podsieci i wybierz **Usuń**.

   ![Usuwanie sieci wirtualnej](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Kliknij przycisk **Zapisz** Aby zastosować zmiany.

## <a id="configure-using-powershell"></a>Konfigurowanie punktu końcowego usługi za pomocą programu Azure PowerShell 

Aby skonfigurować punkt końcowy usługi do konta usługi Azure Cosmos DB przy użyciu programu Azure PowerShell, wykonaj następujące kroki:  

1. Zainstaluj najnowszą wersję [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) i [logowania](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Włączanie punktu końcowego usługi dla istniejącej podsieci sieci wirtualnej.  

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

1. Przygotuj się do włączenia listy kontroli dostępu na koncie usługi Azure Cosmos, upewniając się, że sieć wirtualną i podsieć ma punkt końcowy usługi włączany dla konta.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

1. Pobierz właściwości konta usługi Azure Cosmos DB, uruchamiając następujące polecenie cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicjowanie zmiennych do użycia w przyszłości. Skonfigurować wszystkie zmienne z istniejącej definicji konto, jeśli masz wiele lokalizacji, należy dodać je jako część tablicy. W tym kroku skonfigurujesz również punkt końcowy usługi sieci wirtualnej przez ustawienie zmiennej "accountVNETFilterEnabled" na "True". Ta wartość jest później przypisane do parametru "isVirtualNetworkFilterEnabled". 

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

1. Aktualizowanie właściwości konta usługi Azure Cosmos DB przy użyciu nowej konfiguracji, uruchamiając następujące polecenia cmdlet: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

1. Uruchom następujące polecenie, aby zweryfikować, że konto usługi Azure Cosmos DB został zaktualizowany o punkt końcowy usługi sieci wirtualnej, który został skonfigurowany w poprzednim kroku:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Konfigurowanie punktu końcowego usługi za pomocą wiersza polecenia platformy Azure 

1. Włącz punkt końcowy usługi dla podsieci w sieci wirtualnej.

1. Aktualizowanie istniejącego konta usługi Azure Cosmos z podsiecią list ACL

   ```azurecli-interactive
   az cosmosdb update \
      --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Utwórz nowe konto usługi Azure Cosmos z podsiecią list ACL

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>Migrowanie z reguła zapory adresów IP do listy ACL sieci Wirtualnej 

Poniższe kroki są wymagane tylko dla konta usługi Azure Cosmos z istniejące reguły zapory IP, dzięki czemu podsieci i chcesz użyć sieci Wirtualnej i list ACL opartych na podsieci zamiast reguła zapory adresów IP.

Po włączeniu punktu końcowego usługi dla konta usługi Azure Cosmos w podsieci żądania są wysyłane ze źródłem zawierający informacje o sieci Wirtualnej i podsieci zamiast publicznego adresu IP. Dlatego takie żądania nie są zgodne filtr adresów IP. Ten przełącznik źródła odbywa się dla wszystkich kont usługi Azure Cosmos dostęp z podsieci za pomocą włączonego punktu końcowego usługi. Aby uniknąć przestoju, wykonaj następujące czynności:

1. Pobierz właściwości konta usługi Azure Cosmos, uruchamiając następujące polecenie cmdlet:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicjowanie zmiennych do użycia w przyszłości. Skonfiguruj wszystkie zmienne z istniejącej definicji konta. Dodawanie listy ACL sieci Wirtualnej do usługi Cosmos Azure wszystkie konta, którego uzyskiwany jest dostęp z podsieci za pomocą `ignoreMissingVNetServiceEndpoint` flagi.  

   Jeśli masz wiele lokalizacji, należy dodać je jako część tablicy. W tym kroku skonfigurujesz również punkt końcowy usługi sieci wirtualnej przez ustawienie zmiennej "accountVNETFilterEnabled" na "True". Ta wartość jest później przypisane do parametru "isVirtualNetworkFilterEnabled".

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
   $subnetID = “Subnet ARM URL” e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{"id"=$subnetID, "ignoreMissingVNetServiceEndpoint"="True"})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Aktualizowanie właściwości konta usługi Azure Cosmos przy użyciu nowej konfiguracji, uruchamiając następujące polecenia cmdlet:

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ApiVersion $apiVersion `
    -ResourceGroupName $rgName `
    -Name $acctName -Properties $CosmosDBProperties
   ```

1. Powtórz kroki 1 – 3 dla wszystkich kont w usłudze Azure Cosmos, których dostęp z podsieci.

1.  Poczekaj 15 minut, a następnie zaktualizuj podsieci, aby włączyć punkt końcowy usługi.

1.  Włączanie punktu końcowego usługi dla istniejącej podsieci sieci wirtualnej.

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Usuń regułę zapory adresów IP w podsieci.

## <a name="next-steps"></a>Kolejne kroki

* Aby skonfigurować zaporę usługi Azure Cosmos DB, zobacz [zapory pomocy technicznej](firewall-support.md) artykułu.

