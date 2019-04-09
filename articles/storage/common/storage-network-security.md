---
title: Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych | Dokumentacja firmy Microsoft
description: Skonfiguruj zabezpieczenia sieciowe warstwowej konta magazynu.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: 6d6ca1fe1256f1571079027ebd299492bfa62f41
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057391"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych

Usługa Azure Storage oferuje model zabezpieczeń warstwowych. Ten model umożliwia Zabezpieczanie kont magazynu do określonego zestawu sieci, obsługiwane. Po skonfigurowaniu reguł sieci tylko te aplikacje, które wnioskujące o udostępnienie danych z ponad określonego zestawu sieci mają dostęp do konta magazynu.

Aplikacja, która uzyskuje dostęp do konta magazynu, gdy zasady sieci wymaga prawidłowego autoryzacji w żądaniu. Autoryzacja jest obsługiwana przy użyciu poświadczeń usługi Azure Active Directory (Azure AD) dla kolejek i obiektów blob, prawidłowe konto dostępu do klucza lub tokenu sygnatury dostępu Współdzielonego.

> [!IMPORTANT]
> Włączenie reguły zapory dla konta magazynu domyślnie blokuje żądań przychodzących danych, chyba, że żądania pochodzą z usługi, która działa w ramach usługi Azure Virtual Network (VNet). Żądania, które są blokowane obejmują z innymi usługami platformy Azure w witrynie Azure portal, rejestrowania i metryk usług i tak dalej.
>
> Możesz udzielić dostępu do usług platformy Azure, które działają z w ramach sieci wirtualnej, umożliwiając podsieci wystąpienia usługi. Włącz ograniczonej liczbie scenariuszy za pomocą [wyjątki](#exceptions) mechanizm opisane w poniższej sekcji. Aby uzyskać dostęp do witryny Azure portal, należałoby znajdować się na komputerze w ramach zaufanej (IP lub sieci wirtualnej) ustawiony.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenariusze

Konfigurowanie kont magazynu, aby odmówić dostępu do ruchu sieciowego ze wszystkich sieci (w tym ruch z Internetu), domyślnie. Następnie przyznać dostęp do ruchu z określonych sieci wirtualnych. Ta konfiguracja umożliwia tworzenie bezpiecznej granicy sieci dla aplikacji. Można również przyznać dostęp do publicznej sieci internet zakresów adresów IP, włączenie połączenia z określonych klientów w Internecie lub w środowisku lokalnym.

Reguły sieciowych są wymuszane dla wszystkich protokołów sieciowych do usługi Azure storage, łącznie z REST i protokół SMB. Dostęp do danych za pomocą narzędzi takich jak witryny Azure portal, Eksploratorze usługi Storage i narzędzia AZCopy, wymagane są jawne sieci reguły.

Reguły sieci można zastosować do istniejących kont magazynu lub podczas tworzenia nowych kont magazynu.

Po reguły sieci są stosowane, są one wymuszane dla wszystkich żądań. Tokeny sygnatur dostępu Współdzielonego, określającymi udzielenie dostępu do określonego adresu IP służą do ograniczania dostępu posiadacza tokenu, ale nie może udzielać nowych dostępu poza reguł skonfigurowanych sieci.

Ruch dyskowy maszyny wirtualnej (w tym instalowanie i odinstalowywanie operacje i we/wy dysku) nie ma wpływu zasad sieci. Dostęp interfejsu REST do stronicowych obiektów blob jest chroniony przez zasady sieciowej.

Klasyczne konta magazynu nie obsługują zapory i sieci wirtualne.

Usługa unmanaged disks w ramach kont magazynu służy za pomocą reguł sieci stosowane do tworzenia kopii zapasowych i przywracanie maszyn wirtualnych, tworząc wyjątek. Ten proces jest udokumentowany w [wyjątki](#exceptions) dalszej części tego artykułu. Wyjątki zapory nie są stosowane w przypadku dysków zarządzanych jako są już zarządzane przez platformę Azure.

## <a name="change-the-default-network-access-rule"></a>Zmień domyślną regułę dostępu do sieci

Domyślnie konta magazynu akceptują połączenia z klientami za pośrednictwem sieci. Aby ograniczyć dostęp do wybranych sieci, należy najpierw zmienić domyślnej akcji.

> [!WARNING]
> Wprowadzanie zmian do reguł sieci może wpłynąć na Twoje aplikacje możliwość łączenia z usługą Azure Storage. Ustawienie reguły domyślnej sieci **Odmów** blokuje dostęp do danych, chyba że określonej sieci reguły do **udzielić** dostępu są również stosowane. Pamiętaj udzielić dostępu do żadnych dozwolonych sieci przy użyciu reguł sieci, zanim będzie można zmienić domyślną regułę, aby odmówić dostępu.

### <a name="managing-default-network-access-rules"></a>Reguły dostępu do sieci w usłudze zarządzania domyślne

Możesz zarządzać domyślnej reguły dostępu do sieci dla kont magazynu w witrynie Azure portal, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij w menu Ustawienia o nazwie **zapory i sieci wirtualne**.

1. Aby odmówić dostępu domyślnie, chce zezwolić na dostęp z **wybrane sieci**. Aby zezwolić na ruch z wszystkich sieci, chce zezwolić na dostęp z **wszystkie sieci**.

1. Kliknij przycisk **Zapisz** Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [programu Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj](/powershell/azure/authenticate-azureps).

1. Wyświetl stan reguły domyślne dla konta magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Ustaw domyślną regułę w celu blokowania dostępu do sieci domyślnie.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Ustaw domyślną regułę, aby zezwolić na dostęp do sieci domyślnie.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj](/cli/azure/authenticate-azure-cli).

1. Wyświetl stan reguły domyślne dla konta magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Ustaw domyślną regułę w celu blokowania dostępu do sieci domyślnie.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Ustaw domyślną regułę, aby zezwolić na dostęp do sieci domyślnie.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Udzielanie dostępu z sieci wirtualnej

Można skonfigurować konta magazynu, aby zezwolić na dostęp tylko z określonej sieci wirtualnych.

Włącz [punktu końcowego usługi](/azure/virtual-network/virtual-network-service-endpoints-overview) dla usługi Azure Storage w obrębie sieci wirtualnej. Ten punkt końcowy umożliwia ruch optymalną trasę do usługi Azure Storage. Tożsamości w sieci wirtualnej i podsieci również są przesyłane z każdym żądaniem. Administratorzy mogą następnie skonfigurować reguły sieci konta magazynu, które zezwalanie na żądania z określonych podsieci w sieci wirtualnej. Udzielono dostępu przez te reguły sieci muszą w dalszym ciągu spełniają wymagania autoryzacji konta magazynu można uzyskać dostęp do danych klientów.

Każde konto magazynu obsługuje maksymalnie 100 reguł sieci wirtualnej, które mogą być łączone z [reguł sieci IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiony dostępne sieci wirtualnej

Ogólnie rzecz biorąc punkty końcowe usługi działa między sieciami wirtualnymi i wystąpieniami usług w tym samym regionie platformy Azure. Korzystając z punktów końcowych usługi za pomocą usługi Azure Storage, ten zakres rozszerza się, aby uwzględnić [sparowanym regionie](/azure/best-practices-availability-paired-regions). Punkty końcowe usługi umożliwiają ciągłość działalności biznesowej podczas regionalnej pracy awaryjnej i dostępu do wystąpień tylko do odczytu magazynu geograficznie nadmiarowego (RA-GRS). Reguły sieciowych, określającymi udzielenie dostępu z sieci wirtualnej na koncie magazynu również przyznać dostęp do dowolnego wystąpienia RA-GRS.

Podczas planowania odzyskiwania po awarii podczas regionalnej awarii, należy wcześniej utworzyć sieci wirtualne w sparowanym regionie. Włączanie punktów końcowych usługi dla usługi Azure Storage z regułami sieciowej udzielanie dostępu z tych alternatywnych sieciach wirtualnych. Te reguły można następnie zastosować do kont magazynu geograficznie nadmiarowego magazynu.

> [!NOTE]
> Punkty końcowe usługi nie mają zastosowania do ruchu poza region sieci wirtualnej i parze regionów wyznaczonym. Można zastosować tylko reguł sieci udzielanie dostępu z sieci wirtualnych do konta magazynu w regionie podstawowym konta magazynu lub w wyznaczonym sparowanym regionie.

### <a name="required-permissions"></a>Wymagane uprawnienia

Aby zastosować regułę sieci wirtualnej do konta magazynu, użytkownik musi mieć odpowiednie uprawnienia dla dodawanych podsieci. Jest wymagane uprawnienie *Dołącz do usługi do podsieci* i wchodzi w skład *Współautor konta magazynu* wbudowana rola. Mogą być również dodawane do definicji ról niestandardowych.

Konto magazynu i sieci wirtualnych udzielić dostępu może być w różnych subskrypcjach, ale te subskrypcje muszą być częścią tej samej dzierżawie usługi Azure AD.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej

Możesz zarządzać reguł sieci wirtualnej dla konta magazynu za pośrednictwem witryny Azure portal, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij w menu Ustawienia o nazwie **zapory i sieci wirtualne**.

1. Sprawdź, czy zostało wybrane, aby zezwolić na dostęp z **wybrane sieci**.

1. Aby udzielić dostępu do sieci wirtualnej z nową regułę sieci, w obszarze **sieci wirtualne**, kliknij przycisk **Dodaj istniejącą sieć wirtualną**, wybierz opcję **sieci wirtualne** i **Podsieci** opcje, a następnie kliknij **Dodaj**. Kliknij, aby utworzyć nową sieć wirtualną i przyznać jej dostęp **Dodaj nową sieć wirtualną**. Podaj informacje niezbędne do tworzenia nowej sieci wirtualnej, a następnie kliknij przycisk **Utwórz**.

    > [!NOTE]
    > Jeśli punkt końcowy usługi dla usługi Azure Storage nie został wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, możesz go skonfigurować jako część tej operacji.

1. Aby usunąć sieci wirtualnej lub podsieci regułę, kliknij przycisk **...**  Otwórz menu kontekstowe dla sieci wirtualnej lub podsieci, a następnie kliknij przycisk **Usuń**.

1. Kliknij przycisk **Zapisz** Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [programu Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj](/powershell/azure/authenticate-azureps).

1. Lista reguł sieci wirtualnej.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usługi Azure Storage w istniejącej sieci wirtualnej lub podsieci.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. Usuń regułę sieciowej dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) do **Odmów**, lub reguł sieci nie mają wpływu.

#### <a name="cliv2"></a>CLIv2

1. Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj](/cli/azure/authenticate-azure-cli).

1. Lista reguł sieci wirtualnej.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usługi Azure Storage w istniejącej sieci wirtualnej lub podsieci.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. Usuń regułę sieciowej dla sieci wirtualnej i podsieci.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) do **Odmów**, lub reguł sieci nie mają wpływu.

## <a name="grant-access-from-an-internet-ip-range"></a>Udzielanie dostępu z internetowego zakresu adresów IP

Można skonfigurować konta magazynu, aby zezwolić na dostęp z określonych publicznej sieci internet zakresów adresów IP. Ta konfiguracja daje dostęp do określonych usług internetowych i sieciami lokalnymi i blokuje ruch internetowy ogólne.

Podaj internet dozwolonych zakresów adresów, za pomocą [notacji CIDR](https://tools.ietf.org/html/rfc4632) w formie *16.17.18.0/24* lub jako adres IP poszczególnych adresów, takich jak *16.17.18.19*.

   > [!NOTE]
   > Zakresy adresów małe, za pomocą "/ 31" lub "/ 32" prefiks rozmiary nie są obsługiwane. Tych zakresów powinny być skonfigurowane przy użyciu reguły dotyczące poszczególnych adresów IP.

Reguły sieciowych adresów IP są dozwolone tylko dla **publicznej sieci internet** adresów IP. Zakresy adresów zarezerwowanych dla sieci prywatnych adresów IP (zgodnie z definicją w [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nie są dozwolone w regułach adresów IP. Prywatne sieci obejmują adresów rozpoczynających się _zapis 10.*_, _172.16. *_ - _172.31. *_, i _192.168. *_.

   > [!NOTE]
   > Reguły sieciowych adresów IP nie mają wpływu na żądania pochodzące z tym samym regionie platformy Azure jako konto magazynu. Użyj [reguł sieci wirtualnej](#grant-access-from-a-virtual-network) Aby zezwolić na żądania z tego samego regionu.

W tej chwili obsługiwane są tylko adresy IPV4.

Każde konto magazynu obsługuje maksymalnie 100 adresów IP sieci zasad, które mogą być łączone z [reguł sieci wirtualnej](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu do sieci lokalnej

Aby udzielić dostępu z sieci lokalnej do swojego konta magazynu przy użyciu reguły sieci IP, należy określić adresy IP używane przez sieć z Internetem. Aby uzyskać pomoc, skontaktuj się z administratorem sieci.

jeśli korzystasz z usługi [ExpressRoute](/azure/expressroute/expressroute-introduction) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP

Możesz zarządzać reguł sieci IP dla konta magazynu za pośrednictwem witryny Azure portal, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij w menu Ustawienia o nazwie **zapory i sieci wirtualne**.

1. Sprawdź, czy zostało wybrane, aby zezwolić na dostęp z **wybrane sieci**.

1. Aby udzielić dostępu do Internetu, zakres adresów IP, wprowadź adres IP lub zakres adresów (w formacie CIDR) mocy **zapory** > **zakres adresów**.

1. Aby usunąć regułę sieci IP, kliknij ikonę Kosza obok zakresu adresów.

1. Kliknij przycisk **Zapisz** Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [programu Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj](/powershell/azure/authenticate-azureps).

1. Lista reguł sieci IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Dodaj regułę sieciowej dla indywidualnego adresu IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Dodaj regułę sieciowej dla zakresu adresów IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Usuń regułę sieci dla indywidualnego adresu IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Usuń regułę sieci dla zakresu adresów IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) do **Odmów**, lub reguł sieci nie mają wpływu.

#### <a name="cliv2"></a>CLIv2

1. Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj](/cli/azure/authenticate-azure-cli).

1. Lista reguł sieci IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Dodaj regułę sieciowej dla indywidualnego adresu IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Dodaj regułę sieciowej dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Usuń regułę sieci dla indywidualnego adresu IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Usuń regułę sieci dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) do **Odmów**, lub reguł sieci nie mają wpływu.

## <a name="exceptions"></a>Wyjątki

Reguły sieciowych można włączyć konfiguracji bezpiecznej sieci w przypadku większości scenariuszy. Jednakże istnieją przypadki, gdzie wyjątki musi otrzymać Aby włączyć pełną funkcjonalność. Wyjątki dla zaufanych usług firmy Microsoft i uzyskać dostęp do danych analizy magazynu można skonfigurować konta magazynu.

### <a name="trusted-microsoft-services"></a>Zaufanych usług firmy Microsoft

Niektóre usługi firmy Microsoft, które współdziałają z kontami magazynu działają z sieci, których nie można udzielić dostępu za pomocą reguł sieci.

Aby ułatwić tego typu elementu roboczego usług, zgodnie z oczekiwaniami, zezwala zestaw zaufanych usług firmy Microsoft w celu obejścia zasad sieci. Te usługi użyje silnego uwierzytelniania dostępu do konta magazynu.

Po włączeniu **dozwolonych zaufanych usług firmy Microsoft...**  wyjątku, następujące usługi (jeśli jest zarejestrowany w ramach subskrypcji), mają prawo dostępu konta magazynu:

|Usługa|Nazwa dostawcy zasobów|Przeznaczenie|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Wykonywanie kopii zapasowych i przywracanie dysków niezarządzanych na maszynach wirtualnych IAAS. (niewymagane w przypadku dysków zarządzanych). [Dowiedz się więcej](/azure/backup/backup-introduction-to-azure-backup).|
|Azure Data Box|Microsoft.DataBox|Umożliwia importowanie danych na platformę Azure za pomocą urządzenia Data Box. [Dowiedz się więcej](/azure/databox/data-box-overview).|
|Azure DevTest Labs|Microsoft.DevTestLab|Tworzenie i artefaktu instalacji obrazu niestandardowego. [Dowiedz się więcej](/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Włączanie publikowania zdarzeń usługi Blob Storage i umożliwiają usługi Event Grid opublikować w kolejkach usługi storage. Dowiedz się więcej o [zdarzenia usługi blob storage](/azure/event-grid/event-sources) i [publikowania w kolejkach](/azure/event-grid/event-handlers).|
|Azure Event Hubs|Microsoft.EventHub|Archiwizowanie danych za pomocą usługi Event Hubs Capture. [Dowiedz się więcej](/azure/event-hubs/event-hubs-capture-overview).|
|Azure HDInsight|Microsoft.HDInsight|Aprowizacja początkowa zawartość domyślnego systemu plików dla nowego klastra HDInsight. [Dowiedz się więcej](https://azure.microsoft.com/en-us/blog/enhance-hdinsight-security-with-service-endpoints/).|
|Azure Monitor|Microsoft.Insights|Umożliwia zapisywanie danych do konta magazynu zabezpieczone monitorowania [więcej](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security).|
|Sieci systemu Azure|Microsoft.Networking|Store i analizowanie dzienników ruchu sieciowego. [Dowiedz się więcej](/azure/network-watcher/network-watcher-packet-capture-overview).|
|Azure Site Recovery|Microsoft.SiteRecovery |Konfigurowanie odzyskiwania po awarii, należy włączyć replikację dla maszyn wirtualnych IaaS platformy Azure. Jest to wymagane, jeśli używasz konta magazynu pamięci podręcznej włączona jest Zapora lub źródłowego konta magazynu lub docelowe konto magazynu.  [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication).|
|Azure SQL Data Warehouse|Microsoft.Sql|Umożliwia importowanie i eksportowanie scenariuszy przy użyciu technologii PolyBase. [Dowiedz się więcej](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).|

### <a name="storage-analytics-data-access"></a>Dostęp do danych analizy magazynu

W niektórych przypadkach dostęp do odczytu, dzienniki diagnostyczne i metryki z jest wymagana poza granicami sieci. Można przyznać wyjątki od zasad sieci, aby zezwolić na dostęp do odczytu do plików dziennika konta magazynu i/lub metryki tabel. [Dowiedz się więcej na temat pracy z usługą storage analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Zarządzanie wyjątkami

Możesz zarządzać wyjątki od reguły sieci za pośrednictwem witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure w wersji 2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij w menu Ustawienia o nazwie **zapory i sieci wirtualne**.

1. Sprawdź, czy zostało wybrane, aby zezwolić na dostęp z **wybrane sieci**.

1. W obszarze **wyjątki**, zaznacz wyjątki chcesz przydzielić.

1. Kliknij przycisk **Zapisz** Aby zastosować zmiany.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [programu Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj](/powershell/azure/authenticate-azureps).

1. Wyświetl wyjątków dla reguły sieci konta magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Skonfiguruj wyjątki od reguły sieci konta magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Usuń wyjątki reguły sieci konta magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) do **Odmów**, lub usunąć wyjątki nie mają wpływu.

#### <a name="cliv2"></a>CLIv2

1. Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj](/cli/azure/authenticate-azure-cli).

1. Wyświetl wyjątków dla reguły sieci konta magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Skonfiguruj wyjątki od reguły sieci konta magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Usuń wyjątki reguły sieci konta magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [Ustaw domyślną regułę](#change-the-default-network-access-rule) do **Odmów**, lub usunąć wyjątki nie mają wpływu.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat punktów końcowych usługi sieci platformy Azure w [punkty końcowe usługi](/azure/virtual-network/virtual-network-service-endpoints-overview).

Dokładniejsze informacje dotyczące zabezpieczeń usługi Azure Storage w [Przewodnik po zabezpieczeniach usługi Azure Storage](storage-security-guide.md).
