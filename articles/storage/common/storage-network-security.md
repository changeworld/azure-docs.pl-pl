---
title: Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych | Dokumentacja firmy Microsoft
description: Skonfiguruj zabezpieczenia sieciowe warstwowej konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 28e3f276da231ef0186dba8ecd9c064cdd10a5a8
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281227"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych

Usługa Azure Storage oferuje model zabezpieczeń warstwowych. Ten model pozwala zabezpieczyć i kontrolować poziom dostępu do kont magazynu używanych przez aplikacje i środowiska korporacyjne na podstawie typu i podzestawu używanych sieci. W przypadku skonfigurowania reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskiwać dostęp do konta magazynu. Dostęp do konta magazynu można ograniczyć do żądań pochodzących z określonych adresów IP, zakresów adresów IP lub z listy podsieci w usłudze Azure Virtual Network (VNet).

Konta magazynu mają publiczny punkt końcowy, który jest dostępny za pomocą Internetu. Możesz również utworzyć [prywatne punkty końcowe dla konta magazynu](storage-private-endpoints.md), które przypisuje prywatny adres IP z sieci wirtualnej do konta magazynu, i zabezpiecza cały ruch między siecią wirtualną a kontem magazynu za pośrednictwem prywatnego linku. Zapora usługi Azure Storage zapewnia dostęp do kontroli dostępu do publicznego punktu końcowego konta magazynu. Możesz również użyć zapory, aby zablokować dostęp za pośrednictwem publicznego punktu końcowego podczas korzystania z prywatnych punktów końcowych. Konfiguracja zapory magazynu umożliwia także wybranie zaufanych usług platformy Azure w celu bezpiecznego uzyskiwania dostępu do konta magazynu.

Aplikacja, która uzyskuje dostęp do konta magazynu, gdy reguły sieciowe nadal obowiązują, wymagają odpowiedniej autoryzacji dla żądania. Autoryzacja jest obsługiwana przy użyciu poświadczeń usługi Azure Active Directory (Azure AD) dla obiektów blob i kolejek z prawidłowym kluczem dostępu do konta lub z tokenem SAS.

> [!IMPORTANT]
> Włączenie reguł zapory dla konta magazynu domyślnie blokuje przychodzące żądania danych, chyba że żądania pochodzą z usługi działającej w ramach platformy Azure Virtual Network (VNet) lub z dozwolonych publicznych adresów IP. Żądania, które są blokowane obejmują z innymi usługami platformy Azure w witrynie Azure portal, rejestrowania i metryk usług i tak dalej.
>
> Można udzielić dostępu do usług platformy Azure, które działają w sieci wirtualnej, zezwalając na ruch z podsieci obsługującej wystąpienie usługi. Można również włączyć ograniczoną liczbę scenariuszy za pomocą mechanizmu [wyjątków](#exceptions) opisanych poniżej. Aby uzyskać dostęp do danych z konta magazynu za pośrednictwem Azure Portal, musisz znajdować się na komputerze w ramach zaufanej granicy (adresu IP lub sieci wirtualnej), który został skonfigurowany.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenariusze

Aby zabezpieczyć konto magazynu, należy najpierw skonfigurować regułę, aby domyślnie odmówić dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego) w publicznym punkcie końcowym. Następnie należy skonfigurować reguły zezwalające na dostęp do ruchu pochodzącego z określonych sieci wirtualnych. Możesz również skonfigurować reguły, aby udzielić dostępu do ruchu z wybranych zakresów publicznych adresów IP w Internecie, włączając połączenia z określonych klientów internetowych lub lokalnych. Ta konfiguracja umożliwia tworzenie bezpiecznej granicy sieci dla aplikacji.

Można łączyć reguły zapory zezwalające na dostęp z określonych sieci wirtualnych i z zakresów publicznych adresów IP na tym samym koncie magazynu. Reguły zapory magazynu mogą być stosowane do istniejących kont magazynu lub podczas tworzenia nowych kont magazynu.

Reguły zapory magazynu dotyczą publicznego punktu końcowego konta magazynu. Nie są wymagane żadne reguły dostępu zapory, aby zezwalać na ruch dla prywatnych punktów końcowych konta magazynu. Proces zatwierdzania tworzenia prywatnego punktu końcowego przyznaje niejawny dostęp do ruchu z podsieci, która hostuje prywatny punkt końcowy.

Reguły sieciowych są wymuszane dla wszystkich protokołów sieciowych do usługi Azure storage, łącznie z REST i protokół SMB. Aby uzyskać dostęp do danych przy użyciu narzędzi, takich jak Azure Portal, Eksplorator usługi Storage i AZCopy, należy skonfigurować jawne reguły sieciowe.

Po reguły sieci są stosowane, są one wymuszane dla wszystkich żądań. Tokeny sygnatur dostępu Współdzielonego, określającymi udzielenie dostępu do określonego adresu IP służą do ograniczania dostępu posiadacza tokenu, ale nie może udzielać nowych dostępu poza reguł skonfigurowanych sieci.

Ruch dyskowy maszyny wirtualnej (w tym instalowanie i odinstalowywanie operacje i we/wy dysku) nie ma wpływu zasad sieci. Dostęp interfejsu REST do stronicowych obiektów blob jest chroniony przez zasady sieciowej.

Klasyczne konta magazynu nie obsługują zapory i sieci wirtualne.

Usługa unmanaged disks w ramach kont magazynu służy za pomocą reguł sieci stosowane do tworzenia kopii zapasowych i przywracanie maszyn wirtualnych, tworząc wyjątek. Ten proces jest udokumentowany w [wyjątki](#exceptions) dalszej części tego artykułu. Wyjątki zapory nie są stosowane w przypadku dysków zarządzanych jako są już zarządzane przez platformę Azure.

## <a name="change-the-default-network-access-rule"></a>Zmień domyślną regułę dostępu do sieci

Domyślnie konta magazynu akceptują połączenia z klientami za pośrednictwem sieci. Aby ograniczyć dostęp do wybranych sieci, należy najpierw zmienić domyślnej akcji.

> [!WARNING]
> Wprowadzanie zmian do reguł sieci może wpłynąć na Twoje aplikacje możliwość łączenia z usługą Azure Storage. Ustawienie domyślnej reguły sieci na **Odmów** uniemożliwia dostęp do danych, chyba że stosowane są również określone reguły **sieciowe zezwalające na dostęp.** Pamiętaj udzielić dostępu do żadnych dozwolonych sieci przy użyciu reguł sieci, zanim będzie można zmienić domyślną regułę, aby odmówić dostępu.

### <a name="managing-default-network-access-rules"></a>Reguły dostępu do sieci w usłudze zarządzania domyślne

Możesz zarządzać domyślnej reguły dostępu do sieci dla kont magazynu w witrynie Azure portal, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Portal Azure

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

Konta magazynu można skonfigurować tak, aby zezwalały na dostęp tylko z określonych podsieci. Dozwolone podsieci mogą należeć do sieci wirtualnej w tej samej subskrypcji lub w ramach innej subskrypcji, w tym subskrypcji należących do innej dzierżawy Azure Active Directory.

Włącz [punktu końcowego usługi](/azure/virtual-network/virtual-network-service-endpoints-overview) dla usługi Azure Storage w obrębie sieci wirtualnej. Punkt końcowy usługi kieruje ruch z sieci wirtualnej przez optymalną ścieżkę do usługi Azure Storage. Tożsamości podsieci i sieci wirtualnej są również przesyłane z każdym żądaniem. Administratorzy mogą następnie skonfigurować reguły sieci dla konta magazynu, które zezwalają na odbieranie żądań z określonych podsieci w sieci wirtualnej. Udzielono dostępu przez te reguły sieci muszą w dalszym ciągu spełniają wymagania autoryzacji konta magazynu można uzyskać dostęp do danych klientów.

Każde konto magazynu obsługuje maksymalnie 100 reguł sieci wirtualnej, które mogą być łączone z [reguł sieci IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiony dostępne sieci wirtualnej

Ogólnie rzecz biorąc punkty końcowe usługi działa między sieciami wirtualnymi i wystąpieniami usług w tym samym regionie platformy Azure. Korzystając z punktów końcowych usługi za pomocą usługi Azure Storage, ten zakres rozszerza się, aby uwzględnić [sparowanym regionie](/azure/best-practices-availability-paired-regions). Punkty końcowe usługi umożliwiają ciągłość działalności biznesowej podczas regionalnej pracy awaryjnej i dostępu do wystąpień tylko do odczytu magazynu geograficznie nadmiarowego (RA-GRS). Reguły sieciowych, określającymi udzielenie dostępu z sieci wirtualnej na koncie magazynu również przyznać dostęp do dowolnego wystąpienia RA-GRS.

Podczas planowania odzyskiwania po awarii podczas regionalnej awarii, należy wcześniej utworzyć sieci wirtualne w sparowanym regionie. Włączanie punktów końcowych usługi dla usługi Azure Storage z regułami sieciowej udzielanie dostępu z tych alternatywnych sieciach wirtualnych. Te reguły można następnie zastosować do kont magazynu geograficznie nadmiarowego magazynu.

> [!NOTE]
> Punkty końcowe usługi nie mają zastosowania do ruchu poza region sieci wirtualnej i parze regionów wyznaczonym. Można zastosować tylko reguł sieci udzielanie dostępu z sieci wirtualnych do konta magazynu w regionie podstawowym konta magazynu lub w wyznaczonym sparowanym regionie.

### <a name="required-permissions"></a>Wymagane uprawnienia

Aby zastosować regułę sieci wirtualnej do konta magazynu, użytkownik musi mieć odpowiednie uprawnienia dla dodawanych podsieci. Jest wymagane uprawnienie *Dołącz do usługi do podsieci* i wchodzi w skład *Współautor konta magazynu* wbudowana rola. Mogą być również dodawane do definicji ról niestandardowych.

Konto magazynu i dostępne sieci wirtualne mogą znajdować się w różnych subskrypcjach, w tym subskrypcje, które są częścią innej dzierżawy usługi Azure AD.

> [!NOTE]
> Konfiguracja reguł, które udzielają dostępu do podsieci w sieciach wirtualnych, które są częścią innej dzierżawy Azure Active Directory, są obecnie obsługiwane tylko za pomocą programu PowerShell, interfejsu wiersza polecenia i interfejsów API REST. Takich reguł nie można skonfigurować za pomocą Azure Portal, ale mogą one być wyświetlane w portalu.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej

Możesz zarządzać reguł sieci wirtualnej dla konta magazynu za pośrednictwem witryny Azure portal, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Portal Azure

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij w menu Ustawienia o nazwie **zapory i sieci wirtualne**.

1. Sprawdź, czy zostało wybrane, aby zezwolić na dostęp z **wybrane sieci**.

1. Aby udzielić dostępu do sieci wirtualnej z nową regułę sieci, w obszarze **sieci wirtualne**, kliknij przycisk **Dodaj istniejącą sieć wirtualną**, wybierz opcję **sieci wirtualne** i **Podsieci** opcje, a następnie kliknij **Dodaj**. Kliknij, aby utworzyć nową sieć wirtualną i przyznać jej dostęp **Dodaj nową sieć wirtualną**. Podaj informacje niezbędne do tworzenia nowej sieci wirtualnej, a następnie kliknij przycisk **Utwórz**.

    > [!NOTE]
    > Jeśli punkt końcowy usługi dla usługi Azure Storage nie został wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, możesz go skonfigurować jako część tej operacji.
    >
    > Obecnie do wyboru podczas tworzenia reguły są wyświetlane tylko sieci wirtualne należące do tej samej dzierżawy Azure Active Directory. Aby udzielić dostępu do podsieci w sieci wirtualnej należącej do innej dzierżawy, użyj programu PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.

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

    > [!TIP]
    > Aby dodać regułę sieciową dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, należy użyć w pełni kwalifikowanego parametru **VirtualNetworkResourceId** w postaci "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-name".

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

    > [!TIP]
    > Aby dodać regułę dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, użyj w pełni kwalifikowanego identyfikatora podsieci w postaci "/subscriptions/\<Subscription-ID\>/resourceGroups/\<resourceName-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-Name\>/Subnets/\<subnet-name\>".
    >
    > Możesz użyć parametru **subskrypcji** , aby pobrać identyfikator podsieci dla sieci wirtualnej należącej do innej dzierżawy usługi Azure AD.

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

Reguły sieciowych adresów IP są dozwolone tylko dla **publicznej sieci internet** adresów IP. Zakresy adresów zarezerwowanych dla sieci prywatnych adresów IP (zgodnie z definicją w [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nie są dozwolone w regułach adresów IP. Prywatne sieci obejmują adresów rozpoczynających się _zapis 10.*_ , _172.16. *_  - _172.31. *_ , i _192.168. *_ .

   > [!NOTE]
   > Reguły sieciowych adresów IP nie mają wpływu na żądania pochodzące z tym samym regionie platformy Azure jako konto magazynu. Użyj [reguł sieci wirtualnej](#grant-access-from-a-virtual-network) Aby zezwolić na żądania z tego samego regionu.

  > [!NOTE]
  > Usługi wdrożone w tym samym regionie co konto magazynu używają prywatnych adresów IP platformy Azure do komunikacji. Z tego względu nie można ograniczyć dostępu do określonych usług platformy Azure w oparciu o ich publiczny zakres adresów IP ruchu przychodzącego.

Dla konfiguracji reguł zapory magazynu są obsługiwane tylko adresy IPV4.

Każde konto magazynu obsługuje do 100 reguł sieci adresów IP.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu do sieci lokalnej

Aby udzielić dostępu z sieci lokalnej do swojego konta magazynu przy użyciu reguły sieci IP, należy określić adresy IP używane przez sieć z Internetem. Aby uzyskać pomoc, skontaktuj się z administratorem sieci.

jeśli korzystasz z usługi [ExpressRoute](/azure/expressroute/expressroute-introduction) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP

Możesz zarządzać reguł sieci IP dla konta magazynu za pośrednictwem witryny Azure portal, programu PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Portal Azure

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

Reguły sieciowe pomagają w tworzeniu bezpiecznego środowiska dla połączeń między aplikacjami i danymi w większości scenariuszy. Niektóre aplikacje są jednak zależne od usług platformy Azure, których nie można jednoznacznie odizolować za pomocą sieci wirtualnej lub reguł adresów IP. Jednak te usługi muszą zostać przyznane do magazynu, aby umożliwić pełne działanie aplikacji. W takich sytuacjach można użyć ustawienia Zezwalaj na ***Zaufane usługi firmy Microsoft..*** ., aby umożliwić tym usługom dostęp do danych, dzienników lub analiz.

### <a name="trusted-microsoft-services"></a>Zaufanych usług firmy Microsoft

Niektóre usługi firmy Microsoft działają z sieci, które nie mogą być uwzględnione w regułach sieci. Można przyznać podzbiór takich zaufanych usług firmy Microsoft dostęp do konta magazynu przy zachowaniu reguł sieci dla innych aplikacji. Te zaufane usługi będą następnie używać silnego uwierzytelniania do bezpiecznego łączenia się z kontem magazynu. Włączono dwa tryby zaufanego dostępu dla usług firmy Microsoft.

- Zasoby niektórych usług, które są **rejestrowane w ramach subskrypcji**, mogą uzyskiwać dostęp do konta magazynu **w ramach tej samej subskrypcji** dla operacji wybierania, takich jak zapisywanie dzienników lub kopia zapasowa.
- Do zasobów niektórych usług można uzyskać jawny dostęp do konta magazynu przez **przypisanie roli RBAC** do przypisanej do niej tożsamości zarządzanej.


Po włączeniu ustawienia **Zezwalaj na zaufane usługi firmy Microsoft..** . dla zasobów następujących usług, które są zarejestrowane w ramach tej samej subskrypcji, co konto magazynu, zostanie udzielony dostęp do ograniczonego zestawu operacji zgodnie z opisem:

| Usługa                  | Nazwa dostawcy zasobów     | Dozwolone operacje                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Usługa Azure Backup             | Microsoft.RecoveryServices | Wykonywanie kopii zapasowych i przywracanie dysków niezarządzanych na maszynach wirtualnych IAAS. (niewymagane w przypadku dysków zarządzanych). [Dowiedz się więcej](/azure/backup/backup-introduction-to-azure-backup). |
| Urządzenie Azure Data Box           | Microsoft.DataBox          | Umożliwia importowanie danych na platformę Azure przy użyciu urządzenie Data Box. [Dowiedz się więcej](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Tworzenie i artefaktu instalacji obrazu niestandardowego. [Dowiedz się więcej](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Włączanie publikowania zdarzeń usługi Blob Storage i umożliwiają usługi Event Grid opublikować w kolejkach usługi storage. Dowiedz się więcej o [zdarzenia usługi blob storage](/azure/event-grid/event-sources) i [publikowania w kolejkach](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft.EventHub         | Archiwizowanie danych za pomocą usługi Event Hubs Capture. [Dowiedz się więcej](/azure/event-hubs/event-hubs-capture-overview). |
| Usługa Azure File Sync          | Microsoft.StorageSync      | Umożliwia przekształcenie serwera plików Premium na pamięć podręczną dla udziałów plików platformy Azure. Umożliwienie synchronizacji obejmujących wiele witryn, szybkie odzyskiwanie po awarii i tworzenie kopii zapasowej po stronie chmury. [Dowiedz się więcej](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Zainicjuj wstępną zawartość domyślnego systemu plików dla nowego klastra usługi HDInsight. [Dowiedz się więcej](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Eksportowanie importu platformy Azure      | Microsoft.ImportExport     | Umożliwia importowanie danych na platformę Azure i eksportowanie danych z platformy Azure za pomocą usługi Import/Export. [Dowiedz się więcej](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Umożliwia zapisywanie danych do konta magazynu zabezpieczone monitorowania [więcej](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Sieci systemu Azure         | Microsoft.Network          | Store i analizowanie dzienników ruchu sieciowego. [Dowiedz się więcej](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Włącz replikację na potrzeby odzyskiwania po awarii maszyn wirtualnych platformy Azure IaaS w przypadku korzystania z pamięci podręcznej z włączoną zaporą, źródła lub docelowego konta magazynu.  [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

Ustawienie **Zezwalaj na zaufane usługi firmy Microsoft...** umożliwia również określonym wystąpieniu poniższych usług uzyskanie dostępu do konta magazynu, jeśli jawnie [przypiszesz rolę RBAC](storage-auth-aad.md#assign-rbac-roles-for-access-rights) do [zarządzanej tożsamości przypisanej do systemu](../../active-directory/managed-identities-azure-resources/overview.md) dla tego wystąpienia zasobu. W takim przypadku zakres dostępu dla wystąpienia odpowiada roli RBAC przypisanej do zarządzanej tożsamości.

| Usługa                        | Nazwa dostawcy zasobów          | Przeznaczenie            |
| :----------------------------- | :------------------------------------- | :---------- |
| Usługa Azure Container Registry Tasks | Microsoft.ContainerRegistry/registries | Zadania ACR mogą uzyskać dostęp do kont magazynu podczas kompilowania obrazów kontenerów. |
| Azure Data Factory             | Microsoft.DataFactory/factories        | Zezwala na dostęp do kont magazynu za pomocą środowiska uruchomieniowego ADF. |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Umożliwia aplikacjom logiki dostęp do kont magazynu. [Dowiedz się więcej](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning | Microsoft.MachineLearningServices      | Autoryzowane Azure Machine Learning obszary robocze zapisują dane wyjściowe eksperymentu, modele i dzienniki w usłudze BLOB Storage. [Dowiedz się więcej](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). |
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Umożliwia importowanie i eksportowanie danych z określonych wystąpień SQL Database przy użyciu bazy. [Dowiedz się więcej](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Umożliwia zapisanie danych z zadania przesyłania strumieniowego w usłudze BLOB Storage. Ta funkcja jest obecnie dostępna w wersji zapoznawczej. [Dowiedz się więcej](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft. Synapse/obszary robocze          | Umożliwia dostęp do danych w usłudze Azure Storage z Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Dostęp do danych analizy magazynu

W niektórych przypadkach dostęp do odczytu, dzienniki diagnostyczne i metryki z jest wymagana poza granicami sieci. Podczas konfigurowania dostępu do zaufanych usług do konta magazynu można zezwolić na dostęp do odczytu dla plików dziennika, metryk tabel lub obu. [Dowiedz się więcej na temat pracy z usługą storage analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Zarządzanie wyjątkami

Możesz zarządzać wyjątki od reguły sieci za pośrednictwem witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure w wersji 2.

#### <a name="azure-portal"></a>Portal Azure

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

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat punktów końcowych usługi sieci platformy Azure w [punkty końcowe usługi](/azure/virtual-network/virtual-network-service-endpoints-overview).

Dokładniejsze informacje dotyczące zabezpieczeń usługi Azure Storage w [Przewodnik po zabezpieczeniach usługi Azure Storage](../blobs/security-recommendations.md).
