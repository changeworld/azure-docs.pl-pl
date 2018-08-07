---
title: Konfigurowanie zapór usługi Azure Storage i sieci wirtualne | Dokumentacja firmy Microsoft
description: Skonfiguruj zabezpieczenia sieciowe warstwowej konta magazynu.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 10/25/2017
ms.author: cbrooks
ms.component: common
ms.openlocfilehash: 9eaaaaa4cc9be661cdc2ffde2b634e062c95a404
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523261"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych
Usługa Azure Storage udostępnia model zabezpieczeń warstwowych, dzięki czemu możesz do zabezpieczania konta magazynu do określonego zestawu dozwolonych sieci.  Po skonfigurowaniu reguł sieci tylko aplikacje z dozwolonych sieci można uzyskiwać dostęp do konta magazynu.  Podczas wywoływania z dozwolonych sieci, aplikacje w dalszym ciągu wymagają właściwej zezwolenia (prawidłowy klucz dostępu lub tokenu sygnatury dostępu Współdzielonego) dostęp do konta magazynu.

> [!IMPORTANT]
> Włączenie reguły zapory dla konta usługi Storage zablokuje dostęp do żądań przychodzących danych, łącznie z innymi usługami platformy Azure.  W tym za pomocą portalu, zapisywanie dzienników itp.  Dla usług uczestniczących w programie można ponownie włączyć funkcje za pośrednictwem [wyjątki](#Exceptions) poniższej sekcji.  Aby uzyskać dostęp do portalu, trzeba to zrobić z komputera w obrębie granicy zaufanych (IP lub sieci wirtualnej), które zostały skonfigurowane.
>

## <a name="scenarios"></a>Scenariusze
Aby odmówić dostępu do ruchu sieciowego ze wszystkich sieci (w tym ruch internetowy) można skonfigurować konta magazynu domyślnie.  Można można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure, co pozwala na tworzenie bezpiecznej granicy sieci dla aplikacji.  Można również otrzymać dostęp do publicznej sieci internet zakresów adresów IP, włączenie połączenia z określonych klientów w Internecie lub w środowisku lokalnym.

Reguły sieciowych są wymuszane dla wszystkich protokołów sieciowych do usługi Azure storage, łącznie z REST i protokół SMB.  Dostęp do danych z narzędzi, takich jak witryny Azure portal, Eksploratorze usługi Storage i narzędzia AZCopy wymaga jawnego sieci reguł udzielania dostępu w przypadku reguł sieciowych znajdujących się w życie.

Reguły sieci można zastosować do istniejących kont magazynu lub mogą być stosowane podczas tworzenia nowych kont magazynu.

Po reguły sieci są stosowane, są one wymuszane dla wszystkich żądań.  Tokeny sygnatur dostępu Współdzielonego, określającymi udzielenie dostępu do określonej usługi adresu IP służą do **limit** dostęp właściciela tokenu, ale Zrób grant nowych dostępu poza reguły skonfigurowanej sieci. 

Ruch dyskowy maszyny wirtualnej (w tym instalacji i odinstalować operacje i we/wy dysku) jest **nie** wpływają reguły sieci.  Dostęp interfejsu REST do stronicowych obiektów blob jest chroniony przez zasady sieciowej.

Klasyczne konta magazynu **nie** obsługi zapory i sieci wirtualne.

Tworzenia kopii zapasowych i przywracania z maszyn wirtualnych przy użyciu dysków niezarządzanych na kontach magazynu z regułami sieciowej stosowany jest obsługiwana przy utworzeniu wyjątku, zgodnie z opisem w [wyjątki](/storage/common/storage-network-security#exceptions) dalszej części tego artykułu.  Wyjątki zapory nie są stosowane z usługą Managed Disks, ponieważ są one już zarządzane przez platformę Azure.

## <a name="change-the-default-network-access-rule"></a>Zmień domyślną regułę dostępu do sieci
Domyślnie konta magazynu akceptują połączenia z klientami za pośrednictwem sieci.  Aby ograniczyć dostęp do wybranych sieci, należy najpierw zmienić domyślnej akcji.

> [!WARNING]
> Wprowadzanie zmian do reguł sieci może wpłynąć na Twoje aplikacje możliwość łączenia z usługą Azure Storage.  Ustawienie reguły domyślnej sieci **Odmów** blokuje dostęp do danych, chyba że zasady określonej sieci *udzielanie* dostępu są również stosowane.  Pamiętaj udzielić dostępu do żadnych dozwolonych sieci przy użyciu reguł sieci, zanim będzie można zmienić domyślną regułę, aby odmówić dostępu.
>

#### <a name="azure-portal"></a>Azure Portal
1. Przejdź do konta magazynu, które chcesz zabezpieczyć.  

2. Kliknij w menu Ustawienia o nazwie **zapory i sieci wirtualne**.
3. Aby odmówić dostępu domyślnie, chce zezwolić na dostęp z "Wybranych sieci".  Aby zezwolić na ruch z wszystkich sieci, chce zezwolić na dostęp ze "Wszystkich sieci".
4. Kliknij przycisk *Zapisz* Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell
1. Zainstaluj najnowszą wersję [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) i [logowania](/powershell/azure/authenticate-azureps).

2. Wyświetl stan reguły domyślne dla konta magazynu.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Ustaw domyślną regułę w celu blokowania dostępu do sieci domyślnie.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Ustaw domyślną regułę, aby zezwolić na dostęp do sieci domyślnie.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli) i [logowania](/cli/azure/authenticate-azure-cli).
2. Wyświetl stan reguły domyślne dla konta magazynu.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Ustaw domyślną regułę w celu blokowania dostępu do sieci domyślnie.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Ustaw domyślną regułę, aby zezwolić na dostęp do sieci domyślnie.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Udzielanie dostępu z sieci wirtualnej
Konta magazynu można skonfigurować tak, aby zezwolić na dostęp tylko z określonych sieciach wirtualnych platformy Azure. 

Po włączeniu [punktu końcowego usługi](/azure/virtual-network/virtual-network-service-endpoints-overview) dla usługi Azure Storage w ramach sieci wirtualnej ruch zapewnia optymalną trasę z usługą Azure Storage. Tożsamości w sieci wirtualnej i podsieci również są przesyłane z każdym żądaniem.  Administratorzy mogą następnie skonfigurować reguły sieci konta magazynu, które zezwalanie na żądania z określonych podsieci w sieci wirtualnej.  Udzielono dostępu przez te reguły sieci muszą w dalszym ciągu spełniają wymagania autoryzacji konta magazynu można uzyskać dostęp do danych klientów.

Każde konto magazynu może obsługiwać maksymalnie 100 reguł sieci wirtualnej, które mogą być łączone z [reguł sieci IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Dostępne regiony Virtual Network
Ogólnie rzecz biorąc punkty końcowe usługi działa między sieciami wirtualnymi i wystąpieniami usług w tym samym regionie platformy Azure.  Gdy punkty końcowe usługi są używane z usługą Azure Storage, ten zakres jest rozwinięta i obejmuje [sparowanym regionie](/azure/best-practices-availability-paired-regions).  Ciągłość działania dzięki temu podczas regionalnej pracy awaryjnej, a także bezproblemowego dostępu do wystąpień tylko do odczytu magazynu geograficznie nadmiarowego (RA-GRS).  Reguły sieciowych, określającymi udzielenie dostępu z sieci wirtualnej na koncie magazynu również przyznać dostęp do dowolnego wystąpienia RA-GRS.

Podczas planowania odzyskiwania po awarii podczas regionalnej awarii, musisz aprowizować wcześniej sieciami wirtualnymi w sparowanym regionie. Powinno być włączone punkty końcowe usługi dla usługi Azure Storage, a zasady sieci, które udzielanie dostępu z tych alternatywnych sieciach wirtualnych powinny być stosowane do kont magazynu geograficznie nadmiarowego magazynu.

> [!NOTE]
> Punkty końcowe usługi nie dotyczą ruchu poza region sieci wirtualnej i pary wyznaczonym regionu.  Reguły sieciowych udzielanie dostępu z sieci wirtualnych do konta magazynu mogą być stosowane tylko dla sieci wirtualnych w regionie podstawowym konta magazynu lub w wyznaczonym sparowanym regionie.
>

### <a name="required-permissions"></a>Wymagane uprawnienia
Aby można było zastosować reguły sieci wirtualnej do konta magazynu, użytkownik musi mieć uprawnienia do *Dołącz do usługi do podsieci* dla dodawanych podsieci.  To uprawnienie jest zawarte w *Współautor konta magazynu* wbudowana rola i mogą być dodawane do definicji ról niestandardowych.

Konto magazynu i sieci wirtualnych uprawnienia **może** znajdować się w różnych subskrypcjach, ale te subskrypcje muszą być częścią tej samej dzierżawie usługi Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej
Reguł sieci wirtualnej dla kont magazynu mogą być zarządzane za pośrednictwem witryny Azure portal, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Przejdź do konta magazynu, które chcesz zabezpieczyć.  
2. Kliknij w menu Ustawienia o nazwie **zapory i sieci wirtualne**.
3. Upewnij się, czy wybrano opcję Zezwalaj na dostęp z "Wybranych sieci".
4. Aby udzielić dostępu do sieci wirtualnej za pomocą nowej reguły sieci, w obszarze "Sieci wirtualne", kliknij przycisk "Dodaj istniejące", aby wybrać istniejącą sieć wirtualną i podsieci, a następnie przycisk *Dodaj*.  Kliknij, aby utworzyć nową sieć wirtualną i przyznać jej dostęp *Dodaj nowe*, zawierają informacje niezbędne do tworzenia nowej sieci wirtualnej, a następnie kliknij przycisk *Utwórz*.

> [!NOTE]
> Jeśli punkt końcowy usługi dla usługi Azure Storage nie został wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, może zostać skonfigurowany jako część tej operacji.
>

5. Aby usunąć regułę sieci lub podsieci wirtualnej, kliknij przycisk "...", aby otworzyć menu kontekstowe dla sieci wirtualnej lub podsieci, a następnie kliknij przycisk "Usuń".
6. Kliknij przycisk *Zapisz* Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell
1. Zainstaluj najnowszą wersję [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) i [logowania](/powershell/azure/authenticate-azureps).
2. Lista reguł sieci wirtualnej
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Włączanie punktu końcowego usługi dla usługi Azure Storage w istniejącej sieci wirtualnej i podsieci
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Usuń regułę sieciowej dla sieci wirtualnej i podsieci.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) stan Odmów, lub reguł sieci nie odniesie żadnego skutku.
>

#### <a name="cliv2"></a>CLIv2
1. [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli) i [logowania](/cli/azure/authenticate-azure-cli).
2. Lista reguł sieci wirtualnej
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Włączanie punktu końcowego usługi dla usługi Azure Storage w istniejącej sieci wirtualnej i podsieci
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Usuń regułę sieciowej dla sieci wirtualnej i podsieci. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) stan Odmów, lub reguł sieci nie odniesie żadnego skutku.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Udzielanie dostępu z internetowego zakresu adresów IP
Konta magazynu można skonfigurować do Zezwalaj na dostęp z określonych publicznej sieci internet zakresów adresów IP.  Ta konfiguracja umożliwia określonych usług internetowych i lokalnej sieci, aby uzyskać dostęp podczas ogólne ruch internetowy jest zablokowany.

Dozwolone zakresy adresów internetowych można określić za pomocą [notacji CIDR](https://tools.ietf.org/html/rfc4632) w formie *16.17.18.0/24* lub jako adres IP poszczególnych adresów, takich jak *16.17.18.19* .

> [!NOTE]
> Zakresy adresów małe, za pomocą "/ 31" lub "/ 32" prefiks rozmiary nie są obsługiwane.  Tych zakresów powinny być skonfigurowane przy użyciu reguły dotyczące poszczególnych adresów IP.
>

Reguły sieciowych adresów IP są dozwolone tylko dla **publicznej sieci internet** adresów IP.  Zakresy adresów IP zarezerwowanych dla sieci prywatnych (zgodnie z definicją w dokumencie RFC 1918) są niedozwolone w regułach adresów IP.  Prywatne sieci obejmują adresów rozpoczynających się *10.\** , *172.16.\** , i *192.168.\** .

W tej chwili obsługiwane są tylko adresy IPV4.

Każde konto magazynu może obsługiwać maksymalnie 100 adresów IP reguł sieciowych, które mogą być łączone z [reguł sieci wirtualnej](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu do sieci lokalnej
Aby udzielić dostępu z sieci lokalnej do swojego konta magazynu przy użyciu reguły sieci IP, należy określić adresy IP używane przez sieć z Internetem.  Aby uzyskać pomoc, skontaktuj się z administratorem sieci.

Jeśli sieć jest podłączony do sieci platformy Azure za pomocą funkcji [ExpressRoute](/azure/expressroute/expressroute-introduction), każdy obwód skonfigurowano dwa publiczne adresy IP w Microsoft Edge, które są używane nawiązać połączenia z programem Microsoft Services takich jak Azure Storage przy użyciu [Publicznej komunikacji równorzędnej azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Aby zezwalać na komunikację z obwodu do usługi Azure Storage, należy utworzyć zasady sieci IP publiczne adresy IP obwodów usługi.  Aby można było znaleźć publiczne adresy IP obwodów usługi ExpressRoute [Otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) za pośrednictwem witryny Azure portal.


### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP
Reguły sieciowych adresów IP dla kont magazynu mogą być zarządzane za pośrednictwem witryny Azure portal, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Przejdź do konta magazynu, które chcesz zabezpieczyć.  
2. Kliknij w menu Ustawienia o nazwie **zapory i sieci wirtualne**.
3. Upewnij się, czy wybrano opcję Zezwalaj na dostęp z "Wybranych sieci".
4. Aby udzielić dostępu do Internetu, zakres adresów IP, należy wprowadzić adres IP lub zakres adresów (w formacie CIDR) mocy zapory, zakresy adresów.
5. Aby usunąć regułę sieci IP, kliknij przycisk "...", aby otworzyć menu kontekstowe dla tej reguły, a następnie kliknij przycisk "Usuń".
6. Kliknij przycisk *Zapisz* Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell
1. Zainstaluj najnowszą wersję [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) i [logowania](/powershell/azure/authenticate-azureps).
2. Lista reguł sieci IP.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Dodaj regułę sieciowej dla indywidualnego adresu IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Dodaj regułę sieciowej dla zakresu adresów IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Usuń regułę sieci dla indywidualnego adresu IP. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Usuń regułę sieci dla zakresu adresów IP.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) stan Odmów, lub reguł sieci nie odniesie żadnego skutku.
>

#### <a name="cliv2"></a>CLIv2
1. [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli) i [logowania](/cli/azure/authenticate-azure-cli).
2. Reguły sieci IP list
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Dodaj regułę sieciowej dla indywidualnego adresu IP.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Dodaj regułę sieciowej dla zakresu adresów IP.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Usuń regułę sieci dla indywidualnego adresu IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Usuń regułę sieci dla zakresu adresów IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) stan Odmów, lub reguł sieci nie odniesie żadnego skutku.
>

## <a name="exceptions"></a>Wyjątki
Gdy zasady sieci można włączyć konfiguracji bezpiecznej sieci w przypadku większości scenariuszy, istnieją przypadki, gdzie wyjątki musi otrzymać Aby włączyć pełną funkcjonalność.  Konta magazynu można skonfigurować za pomocą wyjątków dla usług zaufanych firmy Microsoft i uzyskać dostęp do danych analizy magazynu.

### <a name="trusted-microsoft-services"></a>Zaufanych usług firmy Microsoft
Niektóre usługi firmy Microsoft, które współdziałają z kontami magazynu działają z sieci, których nie można udzielić dostępu za pomocą reguł sieci. 

Aby zezwolić na ten typ usługi, której ma działać zgodnie z oczekiwaniami, zezwala na zestaw zaufanych usług firmy Microsoft w celu obejścia zasad sieci. Te usługi użyje silnego uwierzytelniania dostępu do konta magazynu.

Gdy jest włączony wyjątek "Zaufanych usług firmy Microsoft", następujące usługi (jeśli jest zarejestrowany w ramach subskrypcji) uzyskają dostęp do konta magazynu:

|Usługa|Nazwa dostawcy zasobów|Przeznaczenie|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Umożliwia wykonywanie kopii zapasowych i przywracanie dysków niezarządzanych na maszynach wirtualnych IAAS. (niewymagane w przypadku dysków zarządzanych). [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|Tworzenie i artefaktu instalacji obrazu niestandardowego.  [Dowiedz się więcej](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Włączanie publikowania zdarzeń usługi Blob Storage.  [Dowiedz się więcej](https://docs.microsoft.com/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Archiwizowanie danych za pomocą usługi Event Hubs Capture.  [Dowiedz się więcej](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Sieci systemu Azure|Microsoft.Networking|Store i analizowanie dzienników ruchu sieciowego.  [Dowiedz się więcej](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
||||

### <a name="storage-analytics-data-access"></a>Dostęp do danych analizy magazynu
W niektórych przypadkach dostęp do odczytu, dzienniki diagnostyczne i metryki z jest wymagana poza granicami sieci.  Aby zezwolić na dostęp do odczytu do przechowywania plików dziennika konta, tabulky metrik lub oba można udzielić wyjątki od zasad sieci. [Dowiedz się więcej na temat pracy z usługą storage analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Zarządzanie wyjątkami
Wyjątki od reguły sieci można zarządzać za pośrednictwem witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure w wersji 2.

#### <a name="azure-portal"></a>Azure Portal
1. Przejdź do konta magazynu, które chcesz zabezpieczyć.  
2. Kliknij w menu Ustawienia o nazwie **zapory i sieci wirtualne**.
3. Upewnij się, czy wybrano opcję Zezwalaj na dostęp z "Wybranych sieci".
4. W obszarze wyjątki wybierz wyjątki, którą chcesz przydzielić.
5. Kliknij przycisk *Zapisz* Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell
1. Zainstaluj najnowszą wersję [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) i [logowania](/powershell/azure/authenticate-azureps).
2. Wyświetl wyjątków dla reguły sieci konta magazynu.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Skonfiguruj wyjątki od reguły sieci konta magazynu.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Usuń wyjątki reguły sieci konta magazynu.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) stan Odmów, lub usuwanie wyjątków odniesie żadnego skutku.
>

#### <a name="cliv2"></a>CLIv2
1. [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli) i [logowania](/cli/azure/authenticate-azure-cli).
2. Wyświetl wyjątków dla reguły sieci konta magazynu.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Skonfiguruj wyjątki od reguły sieci konta magazynu.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Usuń wyjątki reguły sieci konta magazynu.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) stan Odmów, lub usuwanie wyjątków odniesie żadnego skutku.
>

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat punktów końcowych usługi sieci platformy Azure w [punktów końcowych usługi](/azure/virtual-network/virtual-network-service-endpoints-overview).

Dokładniejsze informacje dotyczące zabezpieczeń usługi Azure Storage w [Przewodnik po zabezpieczeniach magazynu Azure](storage-security-guide.md).
