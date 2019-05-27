---
title: Konfigurowanie sieci wirtualnej i podsieci na podstawie dostępu dla konta usługi Azure Cosmos DB
description: W tym dokumencie opisano kroki wymagane do skonfigurowania punktu końcowego usługi sieci wirtualnej dla usługi Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: c0b1b415db9d8a530a495e09805ad9788c1edfbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66153649"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurowanie dostępu z sieci wirtualnych (VNet)

Można skonfigurować konta usługi Azure Cosmos DB, aby zezwolić na dostęp z określonej podsieci sieci wirtualnej platformy Azure. Ograniczanie dostępu do konta usługi Azure Cosmos DB z połączeniami z podsieci w sieci wirtualnej:
 
1. Włącz podsieci, aby wysłać do usługi Azure Cosmos DB podsieci i tożsamość sieci wirtualnej. Można to osiągnąć przez włączenie punktu końcowego usługi dla usługi Azure Cosmos DB w określonej podsieci.

1. Dodaj regułę w ramach konta usługi Azure Cosmos DB do określenia podsieci jako źródło, z którego może mieć dostęp do konta.

> [!NOTE]
> Po włączeniu punktu końcowego usługi dla konta usługi Azure Cosmos DB w podsieci źródła ruchu, który dociera do usługi Azure Cosmos DB jest przełączany z publicznym adresem IP na sieć wirtualną i podsieć. Przełączanie ruchu ma zastosowanie do dowolnego konta usługi Azure Cosmos DB, który jest dostępny z tą podsiecią. Jeśli Twoje konta usługi Azure Cosmos DB zaporę opartego na protokole IP, aby umożliwić tej podsieci, żądania z włączoną obsługą usługi podsieci nie są już zgodne reguły zapory IP, a następnie są one odrzucone. 
>
> Aby dowiedzieć się więcej, zobacz kroki opisane w temacie [migrowania reguła zapory adresów IP do listy kontroli dostępu w sieci wirtualnej](#migrate-from-firewall-to-vnet) dalszej części tego artykułu. 

Poniżej opisano sposób konfigurowania punktu końcowego usługi sieci wirtualnej dla konta usługi Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Konfigurowanie punktu końcowego usługi za pomocą witryny Azure portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Skonfiguruj punkt końcowy usługi dla istniejącej sieci wirtualnej platformy Azure i podsieć

1. Z **wszystkie zasoby** bloku, należy znaleźć konta usługi Azure Cosmos DB, który chcesz zabezpieczyć.

1. Wybierz **zapory i sieci wirtualne** z menu Ustawienia i zezwolić na dostęp z **wybrane sieci**.

1. Aby udzielić dostępu do istniejącej sieci wirtualnej w podsieci, w obszarze **sieci wirtualne**, wybierz opcję **Dodaj istniejącą sieć wirtualną platformy Azure**.

1. Wybierz **subskrypcji** z którego chcesz dodać siecią wirtualną platformy Azure. Wybierz pozycję Azure **sieci wirtualne** i **podsieci** chcesz zapewnić dostęp do konta usługi Azure Cosmos DB. Następnie wybierz pozycję **Włącz** można włączyć wybranych sieci za pomocą punktów końcowych usługi dla "Microsoft.AzureCosmosDB". Po zakończeniu wybierz **Dodaj**. 

   ![Wybierz sieć wirtualną i podsieć](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Po włączeniu dostępu z sieci wirtualnej usługi Azure Cosmos DB umożliwi ruchu z tego wybrana podsieć. Sieć wirtualną i podsieć, która zostanie dodana powinien pojawić się, jak pokazano na poniższym zrzucie ekranu:

   ![sieć wirtualną i podsieć został pomyślnie skonfigurowany](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Aby włączyć punkty końcowe usługi sieci wirtualnej, potrzebne są następujące uprawnienia subskrypcji:
>   * Subskrypcja z siecią wirtualną: Współautor sieci
>   * Subskrypcja z kontem usługi Azure Cosmos DB: Współautor konta usługi DocumentDB
>   * Jeśli sieć wirtualna i konta usługi Azure Cosmos DB znajdują się w różnych subskrypcjach, upewnij się, że subskrypcję, która ma sieci wirtualnej ma również `Microsoft.DocumentDB` dostawcy zasobów zarejestrowany. Aby zarejestrować dostawcę zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/resource-manager-supported-services.md) artykułu. 

Poniżej przedstawiono wskazówki dotyczące rejestrowania subskrypcji za pomocą dostawcy zasobów.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Skonfiguruj punkt końcowy usługi dla nowej sieci wirtualnej platformy Azure i podsieć

1. Z **wszystkie zasoby** bloku, należy znaleźć konta usługi Azure Cosmos DB, który chcesz zabezpieczyć.  

1. Wybierz **zapory i sieci wirtualne platformy Azure** z menu Ustawienia i zezwolić na dostęp z **wybrane sieci**.  

1. Aby udzielić dostępu do nowej sieci wirtualnej platformy Azure, w obszarze **sieci wirtualne**, wybierz opcję **Dodaj nową sieć wirtualną**.  

1. Podaj szczegóły wymagane do tworzenia nowej sieci wirtualnej, a następnie wybierz **Utwórz**. Podsieć zostanie utworzona z punktem końcowym usługi dla "Microsoft.AzureCosmosDB" włączone.

   ![Wybierz sieć wirtualną i podsieć dla nowej sieci wirtualnej](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Jeśli konto usługi Azure Cosmos DB jest używany przez inne usługi platformy Azure, takich jak usługa Azure Search lub uzyskiwania dostępu do usługi Stream analytics lub usługi Power BI, możesz zezwolić na dostęp, wybierając **akceptowania połączeń z w ramach publicznych centrów danych platformy Azure**.

Aby upewnić się, że masz dostęp do metryk usługi Azure Cosmos DB z portalu, musisz włączyć **zezwolić na dostęp z witryny Azure portal** opcje. Aby dowiedzieć się więcej o tych opcjach, zobacz [skonfigurowanie zapory IP](how-to-configure-firewall.md) artykułu. Po włączeniu dostępu, wybierz **Zapisz** Aby zapisać ustawienia.

## <a id="remove-vnet-or-subnet"></a>Usuwanie sieci wirtualnej lub podsieci 

1. Z **wszystkie zasoby** bloku, konto Znajdź usługa Azure Cosmos DB, dla którego jest przypisane punkty końcowe usługi.  

2. Wybierz **zapory i sieci wirtualne** z poziomu menu ustawień.  

3. Aby usunąć sieć wirtualną lub podsiecią, reguły, wybierz **...**  obok sieci wirtualnej lub podsieci i wybierz **Usuń**.

   ![Usuwanie sieci wirtualnej](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Wybierz **Zapisz** Aby zastosować zmiany.

## <a id="configure-using-powershell"></a>Konfigurowanie punktu końcowego usługi za pomocą programu Azure PowerShell

> [!NOTE]
> Podczas korzystania z programu PowerShell lub interfejsu wiersza polecenia platformy Azure, należy określić pełną listę filtrów IP i sieci wirtualnej list ACL w parametrach nie tylko te, które mają zostać dodane.

Aby skonfigurować punkt końcowy usługi dla konta usługi Azure Cosmos DB przy użyciu programu Azure PowerShell, wykonaj następujące kroki:  

1. Zainstaluj [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) i [Zaloguj](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Włączanie punktu końcowego usługi dla istniejącej podsieci sieci wirtualnej.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Uzyskaj informacje o sieci wirtualnej.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Pobierz właściwości konta usługi Azure Cosmos DB, uruchamiając następujące polecenie cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicjowanie zmiennych do użycia w przyszłości. Skonfiguruj wszystkie zmienne z istniejącej definicji konta.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aktualizowanie właściwości konta usługi Azure Cosmos DB przy użyciu nowej konfiguracji, uruchamiając następujące polecenia cmdlet: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Uruchom następujące polecenie, aby zweryfikować, że konto usługi Azure Cosmos DB został zaktualizowany o punkt końcowy usługi sieci wirtualnej, który został skonfigurowany w poprzednim kroku:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Konfigurowanie punktu końcowego usługi za pomocą wiersza polecenia platformy Azure 

1. Włącz punkt końcowy usługi dla podsieci w sieci wirtualnej.

1. Zaktualizuj istniejące konto usługi Azure Cosmos DB przy użyciu list kontroli dostępu do podsieci (ACL).

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Utwórz nowe konto usługi Azure Cosmos DB przy użyciu list ACL podsieci.

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

## <a id="migrate-from-firewall-to-vnet"></a>Migrowanie z reguła zapory adresów IP do listy ACL sieci wirtualnej 

Tylko dla kont usługi Azure Cosmos DB przy użyciu istniejącej reguły zapory IP zezwalające podsieć, jeśli chcesz użyć sieci wirtualnej i list ACL opartych na podsieci zamiast reguła zapory adresów IP, wykonaj następujące kroki.

Po włączeniu punktu końcowego usługi dla konta usługi Azure Cosmos DB dla podsieci, żądania są wysyłane ze źródłem informacjami wirtualnej sieci i podsieci zamiast publicznego adresu IP. Te żądania nie są zgodne filtr adresów IP. Dla wszystkich kont usługi Azure Cosmos DB, uzyskać dostęp z podsieci z punktu końcowego usługi obsługującego odbywa się to przełącznik źródła. Aby uniknąć przestoju, wykonaj następujące kroki:

1. Pobierz właściwości konta usługi Azure Cosmos DB, uruchamiając następujące polecenie cmdlet:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicjowanie zmiennych do użycia w przyszłości. Skonfiguruj wszystkie zmienne z istniejącej definicji konta. Dodaj sieć wirtualną listy ACL do wszystkich kont usługi Azure Cosmos DB, którego uzyskiwany jest dostęp z podsieci za pomocą `ignoreMissingVNetServiceEndpoint` flagi.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aktualizowanie właściwości konta usługi Azure Cosmos DB przy użyciu nowej konfiguracji, uruchamiając następujące polecenia cmdlet:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Powtórz kroki 1 – 3 dla wszystkich kont usługi Azure Cosmos DB, do których dostęp z podsieci.

1.  Poczekaj 15 minut, a następnie zaktualizuj podsieci, aby włączyć punkt końcowy usługi.

1.  Włączanie punktu końcowego usługi dla istniejącej podsieci sieci wirtualnej.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Usuń regułę zapory adresów IP w podsieci.

## <a name="next-steps"></a>Kolejne kroki

* Aby skonfigurować zaporę usługi Azure Cosmos DB, zobacz [zapory pomocy technicznej](firewall-support.md) artykułu.
