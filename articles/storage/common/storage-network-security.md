---
title: Konfigurowanie usługi Azure Storage zapory i sieci wirtualne | Dokumentacja firmy Microsoft
description: Konfigurowanie zabezpieczeń warstwowych sieci dla konta magazynu.
services: storage
documentationcenter: ''
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/25/2017
ms.author: cbrooks
ms.openlocfilehash: 52d904e7a7e8e5d520d2abd799ef0ae7e99b9894
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurowanie usługi Azure Storage zapory i sieci wirtualnych
Magazyn Azure oferuje model zabezpieczeń warstwowych, co umożliwia Zabezpieczanie kont magazynu do określonego zestawu dozwolonych sieci.  Gdy zasady sieci są skonfigurowane, tylko aplikacje z dozwolonych sieci można uzyskać dostęp do konta magazynu.  Podczas wywoływania metody z dozwolonych sieci, aplikacje będą nadal będą musieli właściwą autoryzację (prawidłowy dostęp do klucza lub tokenu sygnatury dostępu Współdzielonego) do uzyskania dostępu do konta magazynu.

> [!IMPORTANT]
> Włączenie reguły zapory dla konta magazynu zablokuje dostęp do żądań przychodzących danych, łącznie z innymi usługami Azure.  W tym za pomocą portalu zapisywania dzienników itp.  Dla usług uczestniczących można ponownie włączyć funkcję za pomocą [wyjątki](#Exceptions) poniższej sekcji.  Dostęp do portalu będzie potrzebny w tym celu z komputera w obrębie granicy zaufanych (IP lub sieci wirtualnej), które zostały skonfigurowane.
>

## <a name="scenarios"></a>Scenariusze
Domyślnie można odmówić dostępu do ruch z wszystkich sieci (w tym ruch internetowy) można skonfigurować konta magazynu.  Może być przyznany dostęp do ruchu z określonej sieci wirtualnej Azure, co pozwala na tworzenie granicę bezpiecznej sieci dla aplikacji.  Można również przyznany dostęp do publicznej sieci internet zakresów adresów IP, włączenie połączeń z określonych klientów internetowych lub lokalnie.

Zasady sieciowe są wymuszane dla wszystkich protokołów sieciowych do magazynu Azure, w tym REST i protokołu SMB.  Portalu Azure, Eksploratora usługi Storage, takich jak dostęp do danych z narzędzia i AZCopy wymagają zasad sieci jawne udzielanie dostępu, jeśli obowiązują zasady sieci.

Zasady sieciowe można zastosować do istniejących kont magazynu lub można zastosować podczas tworzenia nowego konta magazynu.

Gdy są stosowane zasady sieci, są one stosowane dla wszystkich żądań.  Tokeny sygnatury dostępu Współdzielonego, określającymi udzielenie dostępu do określonego adresu IP usługi służą do **limit** dostępu posiadacza tokenu, ale czy Udziel nowe dostępu poza reguły skonfigurowanej sieci. 

Ruch dysku maszyny wirtualnej (w tym instalacji i Odinstaluj operacji i we/wy dysku) jest **nie** wpływ zasad sieci.  REST dostęp do stronicowe obiekty BLOB są chronione przez zasady sieci.

Klasycznych kont magazynu **nie** obsługuje zapory i sieci wirtualnych.

Kopia zapasowa i przywracanie z maszyn wirtualnych za pomocą niezarządzanych dysków na kontach magazynu przy użyciu reguł sieciowej stosowane jest obsługiwane za pośrednictwem tworzenie wyjątek, zgodnie z opisem w [wyjątki](/storage/common/storage-network-security#exceptions) sekcji tego artykułu.  Wyjątki zapory nie są stosowane w przypadku dysków zarządzanych, jak są już zarządzane przez usługę Azure.

## <a name="change-the-default-network-access-rule"></a>Zmienić domyślną regułę dostępu sieciowego
Domyślnie kont magazynu akceptowania połączeń z klientami w dowolnej sieci.  Aby ograniczyć dostęp do wybranej sieci, należy zmienić domyślne działanie.

> [!WARNING]
> Zmiany zasad sieci może wpływać na aplikacje możliwość łączenia z usługi Azure Storage.  Ustawienie reguły domyślnej sieci **odmowy** blokuje dostęp do danych, chyba że zasady określonej sieci *udzielanie* są również zastosować do nich dostęp.  Pamiętaj udzielić dostępu do żadnych sieci dozwolonych przy użyciu zasad sieci, przed wprowadzeniem zmian w regule domyślnej, aby zezwalał na dostęp.
>

#### <a name="azure-portal"></a>Azure Portal
1. Przejdź do konta magazynu, które mają zostać zabezpieczone.  

2. Kliknij menu Ustawienia o nazwie **zapory i sieci wirtualne**.
3. Aby odmówić dostępu domyślnie, chce zezwolić na dostęp z "Wybrane sieci".  Aby zezwalać na ruch z wszystkich sieci, wybierz się zezwolić na dostęp z "Wszystkie sieci".
4. Kliknij przycisk *zapisać* Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell
1. Zainstaluj najnowszą [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) i [logowania](/powershell/azure/authenticate-azureps).

2. Wyświetlanie stanu reguły domyślnej dla konta magazynu.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Ustaw domyślną regułę w celu odmawiania dostępu do sieci domyślnie.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Ustaw domyślną regułę, aby umożliwić dostęp do sieci domyślnie.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli) i [logowania](/cli/azure/authenticate-azure-cli).
2. Wyświetlanie stanu reguły domyślnej dla konta magazynu.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Ustaw domyślną regułę w celu odmawiania dostępu do sieci domyślnie.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Ustaw domyślną regułę, aby umożliwić dostęp do sieci domyślnie.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Udziel dostępu z sieci wirtualnej
Aby zezwolić na dostęp tylko w określonych sieciach wirtualnych platformy Azure można skonfigurować konta magazynu. 

Przez włączenie [punktu końcowego usługi](/azure/virtual-network/virtual-network-service-endpoints-overview) usługi Azure Storage w ramach sieci wirtualnej ruch zapewniony optymalnej trasy do usługi Magazyn Azure. Tożsamości w sieci wirtualnej i podsieci, również są przesyłane z każdym żądaniem.  Administratorzy mogą skonfigurować później zasad sieci dla konta magazynu, które Zezwalaj na żądania z określonych podsieci w sieci wirtualnej.  Klienci przyznano dostęp za pomocą tych zasad sieci należy nadal spełniają wymagania autoryzacji konta magazynu do dostępu do danych.

Każde konto magazynu może obsługiwać maksymalnie 100 zasad sieci wirtualnej, które mogą być łączone z [zasad sieci IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Dostępne regiony sieci wirtualnej
Ogólnie rzecz biorąc punktów końcowych usługi pracy między sieciami wirtualnymi i wystąpienie usługi, w tym samym regionie Azure.  W przypadku używania punktów końcowych usługi z usługą Azure Storage ten zakres jest rozszerzona w celu uwzględnienia [sparowanego region](/azure/best-practices-availability-paired-regions).  Dzięki temu ciągłości podczas regionalnej pracy awaryjnej, a także seemless dostęp do wystąpień magazynu (RA-GRS) tylko do odczytu z magazynu geograficznie reduntant.  Zasady sieci, określającymi udzielenie dostępu z sieci wirtualnej do konta magazynu również przyznać dostęp do dowolnego wystąpienia RA-GRS.

Podczas planowania odzyskiwania po awarii podczas regionalnej awarii, należy udostępnić sieci wirtualnych w regionie sparowanego z wyprzedzeniem. Punkty końcowe usługi dla usługi Azure Storage powinna być włączona, a zasady sieci przyznany dostęp z tych alternatywnych sieci wirtualnych stosuje się do kont magazynu geograficznie nadmiarowego.

> [!NOTE]
> Punkty końcowe usługi nie dotyczą ruchu poza region para wyznaczonych regionu i sieci wirtualnej.  Udzielanie dostępu z sieci wirtualnych do kont magazynu zasad sieci można stosować tylko dla sieci wirtualnych w regionie podstawowym konta magazynu lub w wyznaczonym sparowanego regionie.
>

### <a name="required-permissions"></a>Wymagane uprawnienia
Aby zastosować regułę sieci wirtualnej do konta magazynu, użytkownik musi mieć uprawnienia do *usługi przyłączyć się do podsieci* podsieci dodawany.  To uprawnienie jest uwzględniona w *współautora konta magazynu* wbudowanej roli i mogą być dodawane do definicji niestandardowej roli zabezpieczeń.

Konto magazynu i sieci wirtualnych dostęp **może** znajdować się w różnych subskrypcji, ale te subskrypcje muszą być częścią tej samej dzierżawy usługi Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Zarządzanie zasadami sieci wirtualnej
Zasady sieci wirtualnych kont magazynu mogą być zarządzane za pośrednictwem portalu Azure, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Przejdź do konta magazynu, które mają zostać zabezpieczone.  
2. Kliknij menu Ustawienia o nazwie **zapory i sieci wirtualne**.
3. Upewnij się, że wybrano opcję Zezwalaj na dostęp z "Wybrane sieci".
4. Aby udzielić dostępu do sieci wirtualnej z nową regułę sieci, w obszarze "Sieci wirtualnej", kliknij przycisk "Dodaj istniejącą" Aby wybrać istniejącej sieci wirtualnej i podsieci, a następnie przycisk *Dodaj*.  Kliknij, aby utworzyć nową sieć wirtualną i przyznać jej dostęp *Dodaj nowe*, podaj informacje niezbędne do tworzenia nowej sieci wirtualnej, a następnie kliknij przycisk *Utwórz*.

> [!NOTE]
> Jeśli punkt końcowy usługi dla usługi Azure Storage nie został wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, można skonfigurować w ramach tej operacji.
>

5. Aby usunąć regułę sieci lub podsieci wirtualnej, kliknij przycisk "..." Aby otworzyć menu kontekstowe dla sieci wirtualnej lub podsieć, a następnie kliknij przycisk Usuń.
6. Kliknij przycisk *zapisać* Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell
1. Zainstaluj najnowszą [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) i [logowania](/powershell/azure/authenticate-azureps).
2. Lista reguł sieci wirtualnej
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Włącz punkt końcowy usługi dla usługi Azure Storage w istniejącej sieci wirtualnej i podsieci
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Dodaj regułę sieciowego dla sieci wirtualnej i podsieci.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Usuń regułę sieciowego dla sieci wirtualnej i podsieci.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Upewnij się, [Ustaw domyślną regułę](#change-the-default-network-access-rule) jako Odmów, lub zasady sieciowe nie odniesie żadnego skutku.
>

#### <a name="cliv2"></a>CLIv2
1. [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli) i [logowania](/cli/azure/authenticate-azure-cli).
2. Lista reguł sieci wirtualnej
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Włącz punkt końcowy usługi dla usługi Azure Storage w istniejącej sieci wirtualnej i podsieci
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Dodaj regułę sieciowego dla sieci wirtualnej i podsieci.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Usuń regułę sieciowego dla sieci wirtualnej i podsieci. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Upewnij się, [Ustaw domyślną regułę](#change-the-default-network-access-rule) jako Odmów, lub zasady sieciowe nie odniesie żadnego skutku.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Udziel dostępu z Internetu zakres adresów IP
Konta magazynu można skonfigurować w celu Zezwól na dostęp z publicznego Internetu określonych zakresów adresów IP.  Ta konfiguracja umożliwia określonych usług internetowych i lokalnej sieci, aby uzyskać dostęp podczas ogólne ruchu internetowego jest zablokowany.

Dozwolone można podać zakresy adresów internetowych za pomocą [notacji CIDR](https://tools.ietf.org/html/rfc4632) w formularzu *16.17.18.0/24* lub jako IP poszczególnych adresów, takich jak *16.17.18.19* .

> [!NOTE]
> Zakresy adresów małych przy użyciu "/ 31" lub "/ 32" prefiks rozmiary nie są obsługiwane.  Tych zakresów powinny być skonfigurowane przy użyciu poszczególnych reguł adresu IP.
>

Zasady sieci IP są dozwolone tylko dla **publicznej sieci internet** adresów IP.  Zakresy adresów IP zarezerwowanych dla sieci prywatnej (zgodnie z definicją w dokumencie RFC 1918) są niedozwolone w regułach IP.  Prywatne sieci obejmują adresów rozpoczynających się od *10.\** , *172.16.\** , i *192.168.\** .

Obecnie obsługiwane są tylko adresy IPV4.

Każde konto magazynu może obsługiwać maksymalnie 100 zasad sieci IP, które mogą być łączone z [zasady sieci wirtualnej](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu z sieci lokalnej
Aby udzielić dostępu z sieci lokalnej do konta magazynu przy użyciu reguły sieci IP, należy zidentyfikować internetowy adresy IP używane przez sieć.  Aby uzyskać pomoc, skontaktuj się z administratorem sieci.

Jeśli sieć jest podłączony do sieci Azure za pomocą funkcji [ExpressRoute](/azure/expressroute/expressroute-introduction), każdy obwód jest skonfigurowany z dwóch publicznych adresów IP w Microsoft Edge, które są używane do nawiązania połączenia Microsoft Services takich jak usługi Azure Storage za pomocą [Azure publicznej komunikacji równorzędnej](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Aby umożliwić komunikację z obwodu do magazynu Azure, należy utworzyć zasady sieci IP dla publicznych adresów IP w sieci obwodów.  Aby można było znaleźć obwodu ExpressRoute publiczne adresy IP, [Otwórz bilet pomocy technicznej z ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) za pośrednictwem portalu Azure.


### <a name="managing-ip-network-rules"></a>Zarządzanie zasadami sieci IP
Zasady sieci IP kont magazynu mogą być zarządzane za pośrednictwem portalu Azure, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Przejdź do konta magazynu, które mają zostać zabezpieczone.  
2. Kliknij menu Ustawienia o nazwie **zapory i sieci wirtualne**.
3. Upewnij się, że wybrano opcję Zezwalaj na dostęp z "Wybrane sieci".
4. Aby przyznać dostęp do Internetu, zakres adresów IP, wprowadź adres IP lub zakres adresów (w formacie CIDR) w obszarze zapory, zakresy adresów.
5. Aby usunąć regułę sieci IP, kliknij przycisk "..." Aby otworzyć menu kontekstowe dla reguły, a następnie kliknij przycisk Usuń.
6. Kliknij przycisk *zapisać* Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell
1. Zainstaluj najnowszą [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) i [logowania](/powershell/azure/authenticate-azureps).
2. Lista reguł sieci IP.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Dodaj regułę sieciowego dla poszczególnych adresów IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Dodaj regułę sieci dla zakresu adresów IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Usuń regułę sieciowego dla poszczególnych adresów IP. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Usuń regułę sieci dla zakresu adresów IP.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Upewnij się, [Ustaw domyślną regułę](#change-the-default-network-access-rule) jako Odmów, lub zasady sieciowe nie odniesie żadnego skutku.
>

#### <a name="cliv2"></a>CLIv2
1. [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli) i [logowania](/cli/azure/authenticate-azure-cli).
2. Zasady sieci IP listy
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Dodaj regułę sieciowego dla poszczególnych adresów IP.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Dodaj regułę sieci dla zakresu adresów IP.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Usuń regułę sieciowego dla poszczególnych adresów IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Usuń regułę sieci dla zakresu adresów IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Upewnij się, [Ustaw domyślną regułę](#change-the-default-network-access-rule) jako Odmów, lub zasady sieciowe nie odniesie żadnego skutku.
>

## <a name="exceptions"></a>Wyjątki
Gdy zasady sieci można włączyć konfiguracji bezpiecznej sieci dla większości scenariuszy, istnieją przypadki, której wyjątki musi otrzymać Aby włączyć pełną funkcjonalność.  Konta magazynu można skonfigurować za pomocą wyjątków dla usług Microsoft zaufane i uzyskać dostęp do danych analizy magazynu.

### <a name="trusted-microsoft-services"></a>Zaufany usług firmy Microsoft
Działanie pewnych usług firmy Microsoft, które współdziałają z kontami magazynu z sieci, które nie można udzielić dostępu za pomocą zasad sieci. 

Aby zezwolić na ten typ usługi, aby działać zgodnie z oczekiwaniami, można zezwolić na zestaw zaufanych usług firmy Microsoft, aby ominąć zasady sieci. Te usługi będzie używać silnego uwierzytelniania dostępu do konta magazynu.

Po włączeniu wyjątek "Zaufanych usług firmy Microsoft" następujących usług (jeśli jest zarejestrowany w ramach subskrypcji) udzielany jest dostęp do konta magazynu:

|Usługa|Nazwa dostawcy zasobów|Przeznaczenie|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Wykonywanie kopii zapasowych i przywracania niezarządzane dysków w przypadku maszyn wirtualnych IAAS. (nie wymaga do zarządzanych dysków). [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|Tworzenie i artefaktu instalacji obrazu niestandardowego.  [Dowiedz się więcej](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Włączanie publikowania zdarzenia magazynu obiektów Blob.  [Dowiedz się więcej](https://docs.microsoft.com/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Archiwizowanie danych z przechwytywania centrów zdarzeń.  [Dowiedz się więcej](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Sieci systemu Azure|Microsoft.Networking|Przechowywanie i analizowanie dzienników ruchu sieciowego.  [Dowiedz się więcej](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
||||

### <a name="storage-analytics-data-access"></a>Dostęp do danych analizy magazynu
W niektórych przypadkach dostęp do odczytu dzienników diagnostycznych i metryki z jest wymagana poza granicami sieci.  Zezwalaj na dostęp do odczytu do przechowywania plików dziennika konta, metryki tabel lub obu może zostać przydzielony wyjątków od reguł sieci. [Dowiedz się więcej o pracy z analityka magazynu.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Zarządzanie wyjątkami
Wyjątki reguł sieci mogą być zarządzane za pośrednictwem portalu Azure, programu PowerShell lub interfejsu wiersza polecenia Azure w wersji 2.

#### <a name="azure-portal"></a>Azure Portal
1. Przejdź do konta magazynu, które mają zostać zabezpieczone.  
2. Kliknij menu Ustawienia o nazwie **zapory i sieci wirtualne**.
3. Upewnij się, że wybrano opcję Zezwalaj na dostęp z "Wybrane sieci".
4. W obszarze wyjątki wybierz wyjątki, które chcesz udzielić.
5. Kliknij przycisk *zapisać* Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell
1. Zainstaluj najnowszą [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) i [logowania](/powershell/azure/authenticate-azureps).
2. Wyświetl wyjątki dla zasad sieci konta magazynu.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Skonfiguruj wyjątki zasad sieci konta magazynu.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Usuń wyjątki reguł sieciowej konta magazynu.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Upewnij się, [Ustaw domyślną regułę](#change-the-default-network-access-rule) jako Odmów, lub usuwanie wyjątków nie odniesie żadnego skutku.
>

#### <a name="cliv2"></a>CLIv2
1. [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli) i [logowania](/cli/azure/authenticate-azure-cli).
2. Wyświetl wyjątki dla zasad sieci konta magazynu.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Skonfiguruj wyjątki zasad sieci konta magazynu.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Usuń wyjątki reguł sieciowej konta magazynu.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Upewnij się, [Ustaw domyślną regułę](#change-the-default-network-access-rule) jako Odmów, lub usuwanie wyjątków nie odniesie żadnego skutku.
>

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat punktów końcowych usługi sieci Azure w [punktów końcowych usługi](/azure/virtual-network/virtual-network-service-endpoints-overview).

Zapoznanie się z zabezpieczeń usługi Azure Storage w Dig [przewodnik zabezpieczeń magazynu Azure](storage-security-guide.md).
