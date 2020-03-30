---
title: Konfigurowanie dostępu opartego na sieci wirtualnej dla konta usługi Azure Cosmos
description: W tym dokumencie opisano kroki wymagane do skonfigurowania punktu końcowego usługi sieci wirtualnej dla usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366232"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurowanie dostępu z sieci wirtualnej

Konta usługi Azure Cosmos DB można skonfigurować tak, aby zezwalać na dostęp tylko z określonej podsieci sieci wirtualnej platformy Azure. Aby ograniczyć dostęp do konta usługi Azure Cosmos DB z połączeniami z podsieci w sieci wirtualnej:

1. Włącz podsieć, aby wysłać podsieci i tożsamości sieci wirtualnej do usługi Azure Cosmos DB. Można to osiągnąć, włączając punkt końcowy usługi dla usługi Azure Cosmos DB w określonej podsieci.

1. Dodaj regułę na koncie usługi Azure Cosmos DB, aby określić podsieć jako źródło, z którego można uzyskać dostęp do konta.

> [!NOTE]
> Gdy punkt końcowy usługi dla konta usługi Azure Cosmos DB jest włączony w podsieci, źródło ruchu, który dociera do usługi Azure Cosmos DB przełącza się z publicznego adresu IP do sieci wirtualnej i podsieci. Przełączanie ruchu dotyczy dowolnego konta usługi Azure Cosmos DB, do którego jest dostęp z tej podsieci. Jeśli konta usługi Azure Cosmos DB mają zaporę opartą na protokona z protokołu IP, aby zezwolić na tę podsieci, żądania z podsieci obsługującej usługę nie są już zgodne z regułami zapory IP i są odrzucane.
>
> Aby dowiedzieć się więcej, zobacz kroki opisane w [sekcji Migrowanie z zapory IP do listy kontroli dostępu do sieci wirtualnej](#migrate-from-firewall-to-vnet) w tym artykule.

W poniższych sekcjach opisano sposób konfigurowania punktu końcowego usługi sieci wirtualnej dla konta usługi Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurowanie punktu końcowego usługi przy użyciu portalu Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurowanie punktu końcowego usługi dla istniejącej sieci wirtualnej i podsieci platformy Azure

1. W bloku **Wszystkie zasoby** znajdź konto usługi Azure Cosmos DB, które chcesz zabezpieczyć.

1. Wybierz **zapory i sieci wirtualne** z menu ustawień i wybierz opcję zezwalania na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do istniejącej **podsieci**sieci wirtualnej, w obszarze Sieci wirtualne wybierz pozycję **Dodaj istniejącą sieć wirtualną platformy Azure**.

1. Wybierz **subskrypcję,** z której chcesz dodać sieć wirtualną platformy Azure. Wybierz **sieci wirtualne** platformy Azure i **podsieci,** które chcesz zapewnić dostęp do konta usługi Azure Cosmos DB. Następnie **wybierz** włącz, aby włączyć wybrane sieci z punktami końcowymi usługi dla "Microsoft.AzureCosmosDB". Po jego zakończeniu wybierz pozycję **Dodaj**.

   ![Wybieranie sieci wirtualnej i podsieci](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Po włączeniu konta usługi Azure Cosmos DB dla dostępu z sieci wirtualnej, umożliwi ruch tylko z tej wybranej podsieci. Dodana sieć wirtualna i podsieć powinny być wyświetlane w sposób pokazany na poniższym zrzucie ekranu:

   ![Sieć wirtualna i podsieć skonfigurowane pomyślnie](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Aby włączyć punkty końcowe usługi sieci wirtualnej, potrzebne są następujące uprawnienia subskrypcji:
>   * Subskrypcja z siecią wirtualną: współautor sieci
>   * Subskrypcja za pomocą konta usługi Azure Cosmos DB: współautor konta DocumentDB
>   * Jeśli twoja sieć wirtualna i konto usługi Azure Cosmos DB znajdują się w `Microsoft.DocumentDB` różnych subskrypcjach, upewnij się, że subskrypcja, która ma sieć wirtualną, również ma zarejestrowanego dostawcę zasobów. Aby zarejestrować dostawcę zasobów, zobacz [dostawców zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md) artykułu.

Oto wskazówki dotyczące rejestrowania subskrypcji u dostawcy zasobów.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurowanie punktu końcowego usługi dla nowej sieci wirtualnej i podsieci platformy Azure

1. W bloku **Wszystkie zasoby** znajdź konto usługi Azure Cosmos DB, które chcesz zabezpieczyć.  

1. Wybierz **zapory i sieci wirtualne platformy Azure** z menu ustawień i wybierz, aby zezwolić na dostęp z wybranych **sieci**.  

1. Aby udzielić dostępu do nowej sieci wirtualnej platformy Azure, w obszarze **Sieci wirtualne**wybierz pozycję **Dodaj nową sieć wirtualną**.  

1. Podaj szczegóły wymagane do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję **Utwórz**. Podsieć zostanie utworzona z włączonym punktem końcowym usługi dla "Microsoft.AzureCosmosDB".

   ![Wybieranie sieci wirtualnej i podsieci dla nowej sieci wirtualnej](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Jeśli twoje konto usługi Azure Cosmos DB jest używane przez inne usługi platformy Azure, takie jak Usługa Azure Cognitive Search, lub jest dostępne z analizy strumienia lub usługi Power BI, zezwalasz na dostęp, wybierając **opcję Akceptuj połączenia z globalnych centrów danych platformy Azure.**

Aby upewnić się, że masz dostęp do metryki usługi Azure Cosmos DB z portalu, należy włączyć zezwalaj na dostęp z opcji **witryny Azure portal.** Aby dowiedzieć się więcej o tych opcjach, zobacz artykuł [Konfigurowanie zapory IP.](how-to-configure-firewall.md) Po włączeniu dostępu wybierz pozycję **Zapisz,** aby zapisać ustawienia.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Usuwanie sieci wirtualnej lub podsieci

1. W bloku **Wszystkie zasoby** znajdź konto usługi Azure Cosmos DB, do którego przypisano punkty końcowe usługi.  

1. Z menu ustawień wybierz **zapory i sieci wirtualne.**  

1. Aby usunąć regułę sieci wirtualnej lub podsieci, wybierz **...** obok sieci wirtualnej lub podsieci, a następnie wybierz pozycję **Usuń**.

   ![Usuwanie sieci wirtualnej](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurowanie punktu końcowego usługi przy użyciu programu Azure PowerShell

> [!NOTE]
> Podczas korzystania z programu PowerShell lub interfejsu wiersza polecenia platformy Azure należy określić pełną listę filtrów IP i list ACL sieci wirtualnej w parametrach, a nie tylko te, które należy dodać.

Aby skonfigurować punkt końcowy usługi do konta usługi Azure Cosmos DB przy użyciu programu Azure PowerShell, należy wykonać następujące kroki:  

1. Zainstaluj [program Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) i [zaloguj się](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Włącz punkt końcowy usługi dla istniejącej podsieci sieci wirtualnej.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. Uzyskaj informacje o sieci wirtualnej.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Przygotowywanie reguły sieci wirtualnej usługi Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Zaktualizuj właściwości konta usługi Azure Cosmos DB za pomocą nowej konfiguracji punktu końcowego sieci wirtualnej: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Uruchom następujące polecenie, aby sprawdzić, czy twoje konto usługi Azure Cosmos DB jest aktualizowane o punkt końcowy usługi sieci wirtualnej skonfigurowany w poprzednim kroku:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Konfigurowanie punktu końcowego usługi przy użyciu interfejsu wiersza polecenia platformy Azure

Konta usługi Azure Cosmos można skonfigurować dla punktów końcowych usługi, gdy są tworzone lub aktualizowane w późniejszym czasie, jeśli podsieć jest już skonfigurowana dla nich. Punkty końcowe usługi można również włączyć na koncie usługi Cosmos, gdzie podsieć nie jest jeszcze skonfigurowany dla nich, a następnie rozpocznie pracę, gdy podsieć jest skonfigurowany później. Ta elastyczność umożliwia administratorom, którzy nie mają dostępu do konta usługi Cosmos i zasobów sieci wirtualnej, aby ich konfiguracje były niezależne od siebie.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Utwórz nowe konto usługi Cosmos i połącz je z podsiecią zaplecza dla nowej sieci wirtualnej

W tym przykładzie sieci wirtualnej i podsieci jest tworzony z punktów końcowych usługi włączone dla obu podczas ich tworzenia.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Niezależne łączenie i konfigurowanie konta usługi Cosmos do podsieci zaplecza

Ten przykład ma na celu pokazanie, jak połączyć konto usługi Azure Cosmos z istniejącą nową siecią wirtualną, w której podsieć nie jest jeszcze skonfigurowana dla punktów końcowych usługi. Odbywa się to `--ignore-missing-vnet-service-endpoint` za pomocą parametru. Dzięki temu konfiguracja konta usługi Cosmos zakończyć bez błędu przed zakończeniem konfiguracji do podsieci sieci wirtualnej. Po zakończeniu konfiguracji podsieci konto usługi Cosmos będzie dostępne za pośrednictwem skonfigurowanych podsieci.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrowanie z reguły zapory IP do listy ACL sieci wirtualnej

Aby przeprowadzić migrację konta usługi Azure Cosmos DB z używania reguł zapory IP do korzystania z punktów końcowych usługi sieci wirtualnej, należy wykonać następujące kroki.

Po skonfigurowaniu konta usługi Azure Cosmos DB dla punktu końcowego usługi dla podsieci żądania z tej podsieci są wysyłane do usługi Azure Cosmos DB z informacjami o sieci wirtualnej i podsieci zamiast źródłowego publicznego adresu IP. Te żądania nie będą już zgodne z filtrem IP skonfigurowanym na koncie usługi Azure Cosmos DB, dlatego poniższe kroki są niezbędne, aby uniknąć przestojów.

Przed kontynuowaniem włącz punkt końcowy usługi Usługi Azure Cosmos DB w sieci wirtualnej i podsieci, wykonując krok pokazany powyżej w części "Włącz punkt końcowy usługi dla istniejącej podsieci sieci wirtualnej".

1. Uzyskaj informacje o sieci wirtualnej i podsieci:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Przygotuj nowy obiekt reguły sieci wirtualnej dla konta usługi Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Zaktualizuj konto usługi Azure Cosmos DB, aby włączyć dostęp do punktu końcowego usługi z podsieci:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Powtórz poprzednie kroki dla wszystkich kont usługi Azure Cosmos DB dostępnych z podsieci.

1. Usuń regułę zapory IP dla podsieci z reguł zapory konta usługi Azure Cosmos DB.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować zaporę dla usługi Azure Cosmos DB, zobacz artykuł [pomocy technicznej zapory.](firewall-support.md)
