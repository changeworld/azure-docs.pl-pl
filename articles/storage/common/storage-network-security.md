---
title: Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych | Dokumenty firmy Microsoft
description: Skonfiguruj zabezpieczenia sieci warstwowe dla konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 7120ba2cf71c9af5373b830d04d0b67952922887
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113506"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych

Usługa Azure Storage udostępnia warstwowy model zabezpieczeń. Ten model umożliwia zabezpieczenie i kontrolowanie poziomu dostępu do kont magazynu, którego wymagają aplikacje i środowiska przedsiębiorstwa, na podstawie typu i podzbioru używanych sieci. Gdy skonfigurowano reguły sieciowe, tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskiwać dostęp do konta magazynu. Można ograniczyć dostęp do konta magazynu do żądań pochodzących z określonych adresów IP, zakresów adresów IP lub z listy podsieci w sieci wirtualnej platformy Azure (VNet).

Konta magazynu mają publiczny punkt końcowy, który jest dostępny za pośrednictwem Internetu. Można również utworzyć [prywatne punkty końcowe dla konta magazynu,](storage-private-endpoints.md)który przypisuje prywatny adres IP z sieci wirtualnej do konta magazynu i zabezpiecza cały ruch między siecią wirtualną a kontem magazynu za pomocą łącza prywatnego. Zapora magazynu platformy Azure zapewnia dostęp do kontroli dostępu dla publicznego punktu końcowego konta magazynu. Zaporę można również użyć, aby zablokować cały dostęp za pośrednictwem publicznego punktu końcowego podczas korzystania z prywatnych punktów końcowych. Konfiguracja zapory magazynu umożliwia również wybrane zaufane usługi platformy Azure, aby bezpiecznie uzyskać dostęp do konta magazynu.

Aplikacja, która uzyskuje dostęp do konta magazynu, gdy reguły sieciowe są nadal wymaga odpowiedniej autoryzacji dla żądania. Autoryzacja jest obsługiwana przy użyciu poświadczeń usługi Azure Active Directory (Azure AD) dla obiektów blob i kolejek, z prawidłowym kluczem dostępu do konta lub z tokenem sygnatury dostępu Współdzielonego.

> [!IMPORTANT]
> Włączenie reguł zapory dla konta magazynu domyślnie blokuje przychodzące żądania danych, chyba że żądania pochodzą z usługi działającej w sieci wirtualnej platformy Azure lub z dozwolonych publicznych adresów IP. Żądania, które są zablokowane obejmują te z innych usług platformy Azure, z witryny Azure portal, z rejestrowania i metryki usług i tak dalej.
>
> Można udzielić dostępu do usług platformy Azure, które działają z poziomu sieci wirtualnej, zezwalając na ruch z podsieci obsługującej wystąpienie usługi. Można również włączyć ograniczoną liczbę scenariuszy za pomocą mechanizmu [wyjątki](#exceptions) opisane poniżej. Aby uzyskać dostęp do danych z konta magazynu za pośrednictwem witryny Azure portal, należy być na komputerze w zaufanej granicy (IP lub sieci wirtualnej), który został skonfigurowany.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenariusze

Aby zabezpieczyć konto magazynu, należy najpierw skonfigurować regułę, aby domyślnie odmawiać dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego) w publicznym punkcie końcowym. Następnie należy skonfigurować reguły, które udzielają dostępu do ruchu z określonych sieci wirtualnych. Można również skonfigurować reguły, aby udzielić dostępu do ruchu z wybranych publicznych zakresów adresów IP w Internecie, włączając połączenia z określonymi klientami internetowymi lub lokalnymi. Ta konfiguracja umożliwia tworzenie bezpiecznej granicy sieci dla aplikacji.

Można połączyć reguły zapory, które zezwalają na dostęp z określonych sieci wirtualnych i z publicznych zakresów adresów IP na tym samym koncie magazynu. Reguły zapory magazynu można stosować do istniejących kont magazynu lub podczas tworzenia nowych kont magazynu.

Reguły zapory magazynu mają zastosowanie do publicznego punktu końcowego konta magazynu. Nie potrzebujesz żadnych reguł dostępu do zapory, aby zezwolić na ruch dla prywatnych punktów końcowych konta magazynu. Proces zatwierdzania tworzenia prywatnego punktu końcowego udziela niejawnego dostępu do ruchu z podsieci, która obsługuje prywatny punkt końcowy.

Reguły sieciowe są wymuszane we wszystkich protokołach sieciowych do magazynu platformy Azure, w tym REST i SMB. Aby uzyskać dostęp do danych przy użyciu narzędzi, takich jak Portal Azure, Eksplorator magazynu i AZCopy, należy skonfigurować jawne reguły sieciowe.

Po zastosowaniu reguł sieciowych są one wymuszane dla wszystkich żądań. Tokeny sygnatury dostępu współdzielonego, które udzielają dostępu do określonego adresu IP, służą do ograniczania dostępu posiadacza tokenu, ale nie udzielają nowego dostępu poza skonfigurowane reguły sieciowe.

Reguły sieciowe nie mają wpływu na ruch dysku maszyny wirtualnej (w tym operacje instalacji i odinstalowywania oraz we/wy dysku). DOSTĘP REST do stronicowych obiektów blob jest chroniony przez reguły sieciowe.

Klasyczne konta magazynu nie obsługują zapór i sieci wirtualnych.

Dysków niezarządzanych można używać na kontach magazynu z regułami sieciowymi zastosowanymi do tworzenia kopii zapasowych i przywracania maszyn wirtualnych przez utworzenie wyjątku. Ten proces jest udokumentowany w sekcji [Wyjątki](#exceptions) tego artykułu. Wyjątki zapory nie mają zastosowania w przypadku dysków zarządzanych, ponieważ są już zarządzane przez platformę Azure.

## <a name="change-the-default-network-access-rule"></a>Zmienianie domyślnej reguły dostępu do sieci

Domyślnie konta magazynu akceptują połączenia od klientów w dowolnej sieci. Aby zawęzić dostęp do grupy wybranych sieci, należy najpierw zmienić akcję domyślną.

> [!WARNING]
> Zmiany reguł sieci mogą mieć wpływ na możliwość łączenia się aplikacji z usługą Azure Storage. Ustawienie domyślnej reguły sieci **na odmowę** blokuje cały dostęp do danych, chyba że stosowane są również określone reguły sieciowe, które **udzielają** dostępu. Należy pamiętać o udzieleniu dostępu do wszystkich dozwolonych sieci przy użyciu reguł sieci, zanim reguła domyślna zostanie zmieniona na odmawianie dostępu.

### <a name="managing-default-network-access-rules"></a>Zarządzanie domyślnymi regułami dostępu sieciowego

Domyślne reguły dostępu do sieci dla kont magazynu można zarządzać za pośrednictwem witryny Azure portal, powershell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij menu ustawień o nazwie **Zapory i sieci wirtualne**.

1. Aby domyślnie odmówić dostępu, wybierz opcję zezwalania na dostęp z **wybranych sieci**. Aby zezwolić na ruch ze wszystkich sieci, wybierz opcję zezwalającą na dostęp ze **wszystkich sieci**.

1. Aby zastosować zmiany, kliknij pozycję **Zapisz**.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [program Azure PowerShell](/powershell/azure/install-Az-ps) i [zaloguj się](/powershell/azure/authenticate-azureps).

1. Wyświetl stan reguły domyślnej dla konta magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Ustaw domyślną regułę domyślnie odmawiając dostęp do sieci.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Ustaw domyślną regułę, aby domyślnie zezwalać na dostęp do sieci.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>Okręg wyborczy CLIv2

1. Zainstaluj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [zaloguj się](/cli/azure/authenticate-azure-cli).

1. Wyświetl stan reguły domyślnej dla konta magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Ustaw domyślną regułę domyślnie odmawiając dostęp do sieci.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Ustaw domyślną regułę, aby domyślnie zezwalać na dostęp do sieci.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Udzielanie dostępu z sieci wirtualnej

Konta magazynu można skonfigurować tak, aby zezwalać na dostęp tylko z określonych podsieci. Dozwolone podsieci mogą należeć do sieci wirtualnej w tej samej subskrypcji lub w innej subskrypcji, w tym subskrypcji należących do innej dzierżawy usługi Azure Active Directory.

Włącz [punkt końcowy usługi](/azure/virtual-network/virtual-network-service-endpoints-overview) dla usługi Azure Storage w sieci wirtualnej. Punkt końcowy usługi kieruje ruch z sieci wirtualnej za pośrednictwem optymalnej ścieżki do usługi Azure Storage. Tożsamości podsieci i sieci wirtualnej są również przesyłane z każdym żądaniem. Administratorzy mogą następnie skonfigurować reguły sieciowe dla konta magazynu, które umożliwiają odbieranie żądań z określonych podsieci w sieci wirtualnej. Klienci, którym udzielono dostępu za pośrednictwem tych reguł sieciowych, muszą nadal spełniać wymagania dotyczące autoryzacji konta magazynu, aby uzyskać dostęp do danych.

Każde konto magazynu obsługuje do 100 reguł sieci wirtualnej, które mogą być łączone z [regułami sieci IP.](#grant-access-from-an-internet-ip-range)

### <a name="available-virtual-network-regions"></a>Dostępne regiony sieci wirtualnej

Ogólnie rzecz biorąc punkty końcowe usługi działają między sieciami wirtualnymi i wystąpieniami usługi w tym samym regionie platformy Azure. Podczas korzystania z punktów końcowych usługi z usługą Azure Storage, ten zakres rośnie do [włączenia sparowanego regionu](/azure/best-practices-availability-paired-regions). Punkty końcowe usługi umożliwiają ciągłość podczas regionalnej pracy awaryjnej i dostęp do wystąpień magazynu geograficznego tylko do odczytu (RA-GRS). Reguły sieciowe, które udzielają dostępu z sieci wirtualnej do konta magazynu, również udzielają dostępu do dowolnego wystąpienia RA-GRS.

Podczas planowania odzyskiwania po awarii podczas awarii regionalnej, należy utworzyć sieci wirtualne w regionie sparowane z wyprzedzeniem. Włącz punkty końcowe usługi dla usługi Azure Storage, z regułami sieciowymi przyznającymi dostęp z tych alternatywnych sieci wirtualnych. Następnie zastosuj te reguły do kont magazynu geograficznie nadmiarowego.

> [!NOTE]
> Punkty końcowe usługi nie mają zastosowania do ruchu poza regionem sieci wirtualnej i wyznaczonej pary regionów. Reguły sieciowe przyznające dostęp z sieci wirtualnych do kont magazynu w regionie podstawowym konta magazynu lub w wyznaczonym regionie sparowanym.

### <a name="required-permissions"></a>Wymagane uprawnienia

Aby zastosować regułę sieci wirtualnej do konta magazynu, użytkownik musi mieć odpowiednie uprawnienia dla dodawanych podsieci. Potrzebne uprawnienie to *Dołącz usługę do podsieci* i jest uwzględnione w roli wbudowanego *współautora konta magazynu.* Można go również dodać do niestandardowych definicji ról.

Konto magazynu i sieci wirtualne udzielone dostęp może być w różnych subskrypcji, w tym subskrypcji, które są częścią innej dzierżawy usługi Azure AD.

> [!NOTE]
> Konfiguracja reguł, które udzielają dostępu do podsieci w sieciach wirtualnych, które są częścią innej dzierżawy usługi Azure Active Directory, jest obecnie obsługiwana tylko za pośrednictwem interfejsów API programu Powershell, interfejsu wiersza polecenia i restu. Takich reguł nie można skonfigurować za pośrednictwem witryny Azure portal, chociaż mogą być wyświetlane w portalu.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej

Reguły sieci wirtualnej dla kont magazynu można zarządzać za pośrednictwem witryny Azure portal, powershell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij menu ustawień o nazwie **Zapory i sieci wirtualne**.

1. Sprawdź, czy wybrano zezwolenie na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do sieci wirtualnej z nową regułą sieci, w obszarze **Sieci wirtualne**kliknij pozycję **Dodaj istniejącą sieć wirtualną**, wybierz opcje **Sieci wirtualne** i **podsieci,** a następnie kliknij przycisk **Dodaj**. Aby utworzyć nową sieć wirtualną i udzielić jej dostępu, kliknij pozycję **Dodaj nową sieć wirtualną**. Podaj informacje niezbędne do utworzenia nowej sieci wirtualnej, a następnie kliknij przycisk **Utwórz**.

    > [!NOTE]
    > Jeśli punkt końcowy usługi dla usługi Azure Storage nie został wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, można skonfigurować go w ramach tej operacji.
    >
    > Obecnie tylko sieci wirtualne należące do tej samej dzierżawy usługi Azure Active Directory są wyświetlane do wyboru podczas tworzenia reguły. Aby udzielić dostępu do podsieci w sieci wirtualnej należącej do innej dzierżawy, użyj interfejsów API programu Powershell, CLI lub REST.

1. Aby usunąć regułę sieci wirtualnej lub podsieci, kliknij przycisk **...** aby otworzyć menu kontekstowe dla sieci wirtualnej lub podsieci, a następnie kliknij przycisk **Usuń**.

1. Aby zastosować zmiany, kliknij pozycję **Zapisz**.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [program Azure PowerShell](/powershell/azure/install-Az-ps) i [zaloguj się](/powershell/azure/authenticate-azureps).

1. Wyświetl listę reguł sieci wirtualnej.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usługi Azure Storage w istniejącej sieci wirtualnej i podsieci.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Dodaj regułę sieci dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Aby dodać regułę sieci dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, należy użyć w pełni kwalifikowanego parametru **VirtualNetworkResourceId** w postaci "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

1. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić domyślną regułę](#change-the-default-network-access-rule) **odrzucania**lub reguły sieciowe nie mają wpływu.

#### <a name="cliv2"></a>Okręg wyborczy CLIv2

1. Zainstaluj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [zaloguj się](/cli/azure/authenticate-azure-cli).

1. Wyświetl listę reguł sieci wirtualnej.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usługi Azure Storage w istniejącej sieci wirtualnej i podsieci.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Dodaj regułę sieci dla sieci wirtualnej i podsieci.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Aby dodać regułę dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, użyj w pełni kwalifikowanego identyfikatora podsieci w postaci\<"/subscriptions/ subscription-ID\>\</resourceGroups/ resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/vNet-name\<\>/subnets/\<subnet-name\>".
    >
    > Parametr **subskrypcji** służy do pobierania identyfikatora podsieci dla sieci wirtualnej należącej do innej dzierżawy usługi Azure AD.

1. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić domyślną regułę](#change-the-default-network-access-rule) **odrzucania**lub reguły sieciowe nie mają wpływu.

## <a name="grant-access-from-an-internet-ip-range"></a>Udzielanie dostępu z internetowego zakresu adresów IP

Konta magazynu można skonfigurować tak, aby zezwalać na dostęp z określonych publicznych zakresów adresów IP. Ta konfiguracja zapewnia dostęp do określonych usług internetowych i sieci lokalnych oraz blokuje ogólny ruch internetowy.

Podaj dozwolone zakresy adresów internetowych za pomocą [notacji CIDR](https://tools.ietf.org/html/rfc4632) w formie *16.17.18.0/24* lub jako indywidualne adresy IP, takie jak *16.17.18.19*.

   > [!NOTE]
   > Małe zakresy adresów przy użyciu rozmiarów prefiksów "/31" lub "/32" nie są obsługiwane. Zakresy te powinny być konfigurowane przy użyciu reguł poszczególnych adresów IP.

Reguły sieci IP są dozwolone tylko dla **publicznych internetowych** adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nie są dozwolone w regułach IP. Sieci prywatne obejmują adresy zaczynające się od _10.*_, _172.16.*_ - _172.31.*_ i _192.168.*_.

   > [!NOTE]
   > Reguły sieci IP nie mają wpływu na żądania pochodzące z tego samego regionu platformy Azure co konto magazynu. Użyj [reguł sieci wirtualnej,](#grant-access-from-a-virtual-network) aby zezwolić na żądania tego samego regionu.

  > [!NOTE]
  > Usługi wdrożone w tym samym regionie co konto magazynu używają prywatnych adresów IP platformy Azure do komunikacji. W związku z tym nie można ograniczyć dostęp do określonych usług platformy Azure na podstawie ich zakresu publicznego wychodzącego adresu IP.

Tylko adresy IPV4 są obsługiwane dla konfiguracji reguł zapory magazynu.

Każde konto magazynu obsługuje do 100 reguł sieci IP.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu z sieci lokalnych

Aby udzielić dostępu z sieci lokalnych do konta magazynu z regułą sieci IP, należy zidentyfikować adresy IP skierowane do Internetu używane przez sieć. Aby uzyskać pomoc, skontaktuj się z administratorem sieci.

jeśli korzystasz z usługi [ExpressRoute](/azure/expressroute/expressroute-introduction) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP nat są dostarczane przez klienta lub są dostarczane przez usługodawcę. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP

Reguły sieci IP dla kont magazynu można zarządzać za pośrednictwem witryny Azure portal, powershell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij menu ustawień o nazwie **Zapory i sieci wirtualne**.

1. Sprawdź, czy wybrano zezwolenie na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do internetowego zakresu adresów IP, wprowadź zakres adresów IP lub adresów (w formacie CIDR) w obszarze**Zakres adresów** **zapory** > .

1. Aby usunąć regułę sieci IP, kliknij ikonę kosza obok zakresu adresów.

1. Aby zastosować zmiany, kliknij pozycję **Zapisz**.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [program Azure PowerShell](/powershell/azure/install-Az-ps) i [zaloguj się](/powershell/azure/authenticate-azureps).

1. Lista reguł sieci IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Dodaj regułę sieciową dla pojedynczego adresu IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Dodaj regułę sieciową dla zakresu adresów IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Usuń regułę sieciową dla pojedynczego adresu IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Usuń regułę sieciową dla zakresu adresów IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić domyślną regułę](#change-the-default-network-access-rule) **odrzucania**lub reguły sieciowe nie mają wpływu.

#### <a name="cliv2"></a>Okręg wyborczy CLIv2

1. Zainstaluj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [zaloguj się](/cli/azure/authenticate-azure-cli).

1. Lista reguł sieci IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Dodaj regułę sieciową dla pojedynczego adresu IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Dodaj regułę sieciową dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Usuń regułę sieciową dla pojedynczego adresu IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Usuń regułę sieciową dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić domyślną regułę](#change-the-default-network-access-rule) **odrzucania**lub reguły sieciowe nie mają wpływu.

## <a name="exceptions"></a>Wyjątki

Reguły sieciowe pomagają tworzyć bezpieczne środowisko dla połączeń między aplikacjami a danymi w większości scenariuszy. Jednak niektóre aplikacje zależą od usług platformy Azure, których nie można jednoznacznie odizolować za pomocą reguł sieci wirtualnej lub adresu IP. Ale takie usługi muszą być przyznane do magazynu, aby włączyć pełną funkcjonalność aplikacji. W takich sytuacjach można użyć ustawienia ***Zezwalaj na zaufane usługi firmy Microsoft...*** w celu umożliwienia takim usługom dostępu do danych, dzienników lub analiz.

### <a name="trusted-microsoft-services"></a>Zaufane usługi firmy Microsoft

Niektóre usługi firmy Microsoft działają z sieci, których nie można uwzględnić w regułach sieciowych. Można udzielić podzbioru takich zaufanych usług firmy Microsoft dostępu do konta magazynu, przy jednoczesnym zachowaniu reguł sieciowych dla innych aplikacji. Te zaufane usługi będą następnie używać silnego uwierzytelniania, aby bezpiecznie połączyć się z kontem magazynu. Włączyliśmy dwa tryby zaufanego dostępu dla usług firmy Microsoft.

- Zasoby niektórych usług, **po zarejestrowaniu się w subskrypcji,** mogą uzyskać dostęp do konta magazynu **w tej samej subskrypcji** dla wybranych operacji, takich jak pisanie dzienników lub tworzenie kopii zapasowej.
- Zasoby niektórych usług mogą uzyskać jawny dostęp do konta **magazynu, przypisując rolę RBAC** do jego tożsamości zarządzanej przypisanej do systemu.


Po włączeniu ustawienia **Zezwalaj na zaufane usługi firmy Microsoft...** zasoby następujących usług, które są zarejestrowane w tej samej subskrypcji co konto magazynu, są uzyskiwały dostęp dla ograniczonego zestawu operacji zgodnie z opisem:

| Usługa                  | Nazwa dostawcy zasobów     | Dozwolone operacje                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Usługi Microsoft.RecoveryServices | Uruchamianie kopii zapasowych i przywracanie dysków niezarządzanych na maszynach wirtualnych usługi IAAS. (nie jest wymagane dla dysków zarządzanych). [Dowiedz się więcej](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Skrzynka z danymi firmy Microsoft          | Umożliwia importowanie danych na platformę Azure przy użyciu pola danych. [Dowiedz się więcej](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Niestandardowe tworzenie obrazów i instalacja artefaktów. [Dowiedz się więcej](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Włącz publikowanie zdarzeń magazynu obiektów blob i zezwalaj na publikowanie w kolejce magazynu w usłudze Event Grid. Dowiedz się więcej o [zdarzeniach magazynu obiektów blob](/azure/event-grid/event-sources) i [publikowaniu w kolejkach](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft.EventHub         | Archiwizuj dane za pomocą usługi Event Hubs Capture. [Dowiedz się więcej](/azure/event-hubs/event-hubs-capture-overview). |
| Azure File Sync          | Microsoft.StorageSync      | Umożliwia przekształcenie serwera plików przy prem do pamięci podręcznej dla udziałów plików azure. Umożliwia synchronizację wielu lokacji, szybkie odzyskiwanie po awarii i tworzenie kopii zapasowych po stronie chmury. [Dowiedz się więcej](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Aprowizuj początkową zawartość domyślnego systemu plików dla nowego klastra HDInsight. [Dowiedz się więcej](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Eksport importu platformy Azure      | Microsoft.ImportEksport     | Umożliwia importowanie danych na platformę Azure i eksportowanie danych z platformy Azure przy użyciu usługi Import/Eksport. [Dowiedz się więcej](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Umożliwia zapisywanie danych monitorowania na koncie bezpiecznego magazynu, w tym dzienników diagnostycznych zasobów, logowania i inspekcji usługi Azure Active Directory oraz dzienników usługi Microsoft Intune. [Dowiedz się więcej](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Tworzenie sieci na platformie Azure         | Microsoft.Network          | Przechowywanie i analizowanie dzienników ruchu sieciowego. [Dowiedz się więcej](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Włącz replikację do odzyskiwania po awarii maszyn wirtualnych usługi Azure IaaS podczas korzystania z kont pamięci podręcznej, źródła lub magazynu docelowego z włączoną zaporą.  [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

Ustawienie **Zezwalaj na zaufane usługi firmy Microsoft...** umożliwia również określone wystąpienie poniższych usług w celu uzyskania dostępu do konta magazynu, jeśli [jawnie przypisano rolę RBAC](storage-auth-aad.md#assign-rbac-roles-for-access-rights) do [tożsamości zarządzanej przypisanej przez system](../../active-directory/managed-identities-azure-resources/overview.md) dla tego wystąpienia zasobu. W takim przypadku zakres dostępu dla wystąpienia odpowiada roli RBAC przypisanej do tożsamości zarządzanej.

| Usługa                        | Nazwa dostawcy zasobów                 | Przeznaczenie            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure Cognitive Search         | Microsoft.Search/searchSługs        | Umożliwia usługom wyszukiwania funkcji Cognitive Search dostęp do kont magazynu w celu indeksowania, przetwarzania i wykonywania zapytań. |
| Usługa Azure Container Registry Tasks | Microsoft.ContainerRegistry/rejestry | Zadania usługi ACR mogą uzyskiwać dostęp do kont magazynu podczas tworzenia obrazów kontenerów. |
| Azure Data Factory             | Microsoft.DataFactory/fabryki        | Umożliwia dostęp do kont magazynu za pośrednictwem środowiska wykonawczego ADF. |
| Azure Data Share               | Microsoft.DataShare/konta           | Umożliwia dostęp do kont magazynu za pośrednictwem udostępniania danych. |
| Azure Logic Apps               | Microsoft.Logic/przepływy pracy              | Umożliwia aplikacjom logiki dostęp do kont magazynu. [Dowiedz się więcej](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Usługa Azure Machine Learning | Microsoft.MachineLearningServices      | Autoryzowane obszary robocze usługi Azure Machine Learning zapisują dane wyjściowe eksperymentu, modele i dzienniki do magazynu obiektów Blob i odczytują dane. [Dowiedz się więcej](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Umożliwia importowanie i eksportowanie danych z określonych wystąpień bazy danych SQL przy użyciu polybase. [Dowiedz się więcej](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Usługa Azure Stream Analytics         | Microsoft.StreamAnalityka             | Umożliwia zapisywanie danych z zadania przesyłania strumieniowego w magazynie obiektów Blob. Ta funkcja jest obecnie dostępna w wersji zapoznawczej. [Dowiedz się więcej](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft.Synapse/obszary robocze          | Umożliwia dostęp do danych w usłudze Azure Storage z usługi Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Dostęp do danych analizy pamięci masowej

W niektórych przypadkach wymagany jest dostęp do odczytu dzienników diagnostycznych i metryk spoza granicy sieci. Podczas konfigurowania dostępu zaufanych usług do konta magazynu można zezwolić na dostęp do odczytu plików dziennika, tabel metryk lub obu tych metryk. [Dowiedz się więcej o pracy z analizą pamięci masowej.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Zarządzanie wyjątkami

Można zarządzać wyjątkami reguł sieciowych za pośrednictwem witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure w wersji 2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij menu ustawień o nazwie **Zapory i sieci wirtualne**.

1. Sprawdź, czy wybrano zezwolenie na dostęp z **wybranych sieci**.

1. W **obszarze Wyjątki**wybierz wyjątki, które chcesz przyznać.

1. Aby zastosować zmiany, kliknij pozycję **Zapisz**.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [program Azure PowerShell](/powershell/azure/install-Az-ps) i [zaloguj się](/powershell/azure/authenticate-azureps).

1. Wyświetl wyjątki dla reguł sieciowych kont magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Skonfiguruj wyjątki od reguł sieciowych kont magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Usuń wyjątki od reguł sieciowych kont magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić domyślną regułę](#change-the-default-network-access-rule) **odrzucać**lub usuwanie wyjątków nie ma wpływu.

#### <a name="cliv2"></a>Okręg wyborczy CLIv2

1. Zainstaluj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [zaloguj się](/cli/azure/authenticate-azure-cli).

1. Wyświetl wyjątki dla reguł sieciowych kont magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Skonfiguruj wyjątki od reguł sieciowych kont magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Usuń wyjątki od reguł sieciowych kont magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić domyślną regułę](#change-the-default-network-access-rule) **odrzucać**lub usuwanie wyjątków nie ma wpływu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o punktach końcowych usługi Azure Network w [punktach końcowych usługi.](/azure/virtual-network/virtual-network-service-endpoints-overview)

Zagłębij się w bezpieczeństwo usługi Azure Storage w [przewodniku po zabezpieczeniach usługi Azure Storage.](../blobs/security-recommendations.md)
