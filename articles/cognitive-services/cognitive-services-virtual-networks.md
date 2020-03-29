---
title: Sieci wirtualne
titleSuffix: Azure Cognitive Services
description: Skonfiguruj zabezpieczenia sieci warstwowe dla zasobów usług Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371226"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Konfigurowanie sieci wirtualnych usług Azure Cognitive Services

Usługa Azure Cognitive Services udostępnia warstwowy model zabezpieczeń. Ten model umożliwia zabezpieczenie kont usług Cognitive Services do określonego podzbioru sieci. Gdy skonfigurowano reguły sieciowe, tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskać dostęp do konta. Można ograniczyć dostęp do zasobów za pomocą filtrowania żądań. Zezwalanie tylko na żądania pochodzące z określonych adresów IP, zakresów adresów IP lub z listy podsieci w [sieciach wirtualnych platformy Azure](../virtual-network/virtual-networks-overview.md). Jeśli jesteś zainteresowany tą ofertą, musisz [poprosić o dostęp do wersji zapoznawczej.](https://aka.ms/cog-svc-vnet-signup)

Aplikacja, która uzyskuje dostęp do zasobu usług Cognitive Services, gdy reguły sieciowe są w mocy wymaga autoryzacji. Autoryzacja jest obsługiwana przy użyciu poświadczeń [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) lub z prawidłowym kluczem interfejsu API.

> [!IMPORTANT]
> Włączenie reguł zapory dla konta usług Cognitive Services domyślnie blokuje przychodzące żądania danych. Aby umożliwić składanie wniosków, należy spełnić jeden z następujących warunków:
> * Żądanie powinno pochodzić z usługi działającej w sieci wirtualnej platformy Azure (VNet) na liście dozwolonej podsieci docelowego konta usług Cognitive Services. Punkt końcowy w żądaniach pochodzących z sieci wirtualnej musi być ustawiony jako [niestandardowa poddomena](cognitive-services-custom-subdomains.md) konta usług Cognitive Services.
> * Lub żądanie powinno pochodzić z listy dozwolonych adresów IP.
>
> Żądania, które są zablokowane obejmują te z innych usług platformy Azure, z witryny Azure portal, z rejestrowania i metryki usług i tak dalej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenariusze

Aby zabezpieczyć zasób usług Cognitive Services, należy najpierw skonfigurować regułę, aby domyślnie odmawiać dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego). Następnie należy skonfigurować reguły, które udzielają dostępu do ruchu z określonych sieci wirtualnych. Ta konfiguracja umożliwia tworzenie bezpiecznej granicy sieci dla aplikacji. Można również skonfigurować reguły, aby udzielić dostępu do ruchu z wybranych publicznych zakresów adresów IP w Internecie, włączając połączenia z określonymi klientami internetowymi lub lokalnymi.

Reguły sieciowe są wymuszane we wszystkich protokołach sieciowych do usług Azure Cognitive Services, w tym REST i WebSocket. Aby uzyskać dostęp do danych przy użyciu narzędzi, takich jak konsole testowe platformy Azure, należy skonfigurować jawne reguły sieciowe. Reguły sieciowe można stosować do istniejących zasobów usług Cognitive Services lub podczas tworzenia nowych zasobów usług Cognitive Services. Po zastosowaniu reguł sieciowych są one wymuszane dla wszystkich żądań.

## <a name="supported-regions-and-service-offerings"></a>Obsługiwane regiony i oferty usług

Obsługa sieci wirtualnej dla usług Cognitive Services wymienionych poniżej jest ograniczona do *regionów Central US EUAP*, *South Central US*, *East US*, West *US 2*, Europa *Północna, Republika Południowej Afryki Północnej,* Europa *Zachodnia, Indie Środkowe,* Australia *Wschodnia,* *Zachodnie stany USA*i Us *Gov Virginia* Azure. *North Europe* *West Europe* Jeśli oferta usługi nie jest wymieniona w tym miejscu, nie obsługuje sieci wirtualnych.

> [!div class="checklist"]
> * [Narzędzie do wykrywania anomalii](./anomaly-detector/index.yml)
> * [Wizja komputerowa](./computer-vision/index.yml)
> * [Moderator zawartości](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Rozpoznawanie twarzy](./face/index.yml)
> * [Rozpoznawanie formularzy](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizacja](./personalizer/index.yml)
> * [Analiza tekstu](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

Obsługa sieci wirtualnej dla usług Cognitive Services wymienionych poniżej jest ograniczona do regionów *Central US EUAP*, *South Central US*, East *US,* *West US 2*, *Global*i US *Gov Virginia* Azure regiony.
> [!div class="checklist"]
> * [Tekst tłumacza](./translator/index.yml)

## <a name="service-tags"></a>Tagi usługi
Oprócz obsługi punktów końcowych usługi sieci wirtualnej dla powyższych usług, usługi Cognitive Services obsługuje również tag usługi dla konfiguracji reguł sieci wychodzącej. Następujące usługi są zawarte w CognitiveServicesManagement service tag.
> [!div class="checklist"]
> * [Narzędzie do wykrywania anomalii](./anomaly-detector/index.yml)
> * [Wizja komputerowa](./computer-vision/index.yml)
> * [Moderator zawartości](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Rozpoznawanie twarzy](./face/index.yml)
> * [Rozpoznawanie formularzy](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizacja](./personalizer/index.yml)
> * [Analiza tekstu](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Tekst tłumacza](./translator/index.yml)
> * [Usługa mowy](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Zmienianie domyślnej reguły dostępu do sieci

Domyślnie zasoby usług Cognitive Services akceptują połączenia od klientów w dowolnej sieci. Aby zawęzić dostęp do grupy wybranych sieci, należy najpierw zmienić akcję domyślną.

> [!WARNING]
> Wprowadzanie zmian w regułach sieciowych może mieć wpływ na zdolność aplikacji do łączenia się z usługami Azure Cognitive Services. Ustawienie domyślnej reguły sieci **na odmowę** blokuje cały dostęp do danych, chyba że stosowane są również określone reguły sieciowe, które **udzielają** dostępu. Należy pamiętać o udzieleniu dostępu do wszystkich dozwolonych sieci przy użyciu reguł sieci, zanim reguła domyślna zostanie zmieniona na odmawianie dostępu. Jeśli zezwalasz na wystawianie adresów IP dla sieci lokalnej, należy dodać wszystkie możliwe wychodzące publiczne adresy IP z sieci lokalnej.

### <a name="managing-default-network-access-rules"></a>Zarządzanie domyślnymi regułami dostępu sieciowego

Domyślne reguły dostępu do sieci dla zasobów usług Cognitive Services można zarządzać za pośrednictwem witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Portal Azure](#tab/portal)

1. Przejdź do zasobu usług Cognitive Services, który chcesz zabezpieczyć.

1. Wybierz menu **ZARZĄDZANIE ZASOBAMI** o nazwie **Sieć wirtualna**.

   ![Opcja sieci wirtualnej](media/vnet/virtual-network-blade.png)

1. Aby domyślnie odmówić dostępu, wybierz opcję zezwalania na dostęp z **wybranych sieci**. Przy samym ustawieniu **Wybrane sieci,** bez opieki skonfigurowanych **sieci wirtualnych** lub **zakresów adresów** — cały dostęp jest skutecznie odrzucany. Gdy odmówiono dostępu, żądania próbujące korzystać z zasobu usług Cognitive Services nie są dozwolone. Witryna Azure portal, azure powershell lub azure cli nadal mogą być używane do konfigurowania zasobu usług Cognitive Services.
1. Aby zezwolić na ruch ze wszystkich sieci, wybierz opcję zezwalającą na dostęp ze **wszystkich sieci**.

   ![Sieci wirtualne odmawiają](media/vnet/virtual-network-deny.png)

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Zainstaluj [program Azure PowerShell](/powershell/azure/install-az-ps) i [zaloguj się](/powershell/azure/authenticate-azureps)lub wybierz pozycję **Wypróbuj**.

1. Wyświetlanie stanu reguły domyślnej dla zasobu usług Cognitive Services.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Ustaw domyślną regułę domyślnie odmawiając dostęp do sieci.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Ustaw domyślną regułę, aby domyślnie zezwalać na dostęp do sieci.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [zaloguj się](/cli/azure/authenticate-azure-cli)lub wybierz pozycję **Wypróbuj**.

1. Wyświetlanie stanu reguły domyślnej dla zasobu usług Cognitive Services.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Ustaw domyślną regułę domyślnie odmawiając dostęp do sieci.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Ustaw domyślną regułę, aby domyślnie zezwalać na dostęp do sieci.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Udzielanie dostępu z sieci wirtualnej

Zasoby usług Cognitive Services można skonfigurować tak, aby zezwalać na dostęp tylko z określonych podsieci. Dozwolone podsieci mogą należeć do sieci wirtualnej w tej samej subskrypcji lub w innej subskrypcji, w tym subskrypcji należących do innej dzierżawy usługi Azure Active Directory.

Włącz [punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla usług Azure Cognitive Services w sieci wirtualnej. Punkt końcowy usługi kieruje ruch z sieci wirtualnej za pośrednictwem optymalnej ścieżki do usługi Azure Cognitive Services. Tożsamości podsieci i sieci wirtualnej są również przesyłane z każdym żądaniem. Administratorzy mogą następnie skonfigurować reguły sieci dla zasobu usług Cognitive Services, które umożliwiają odbieranie żądań z określonych podsieci w sieci wirtualnej. Klienci, którym udzielono dostępu za pośrednictwem tych reguł sieciowych, muszą nadal spełniać wymagania dotyczące autoryzacji zasobu usług Cognitive Services, aby uzyskać dostęp do danych.

Każdy zasób usług Cognitive Services obsługuje do 100 reguł sieci wirtualnej, które mogą być łączone z [regułami sieci IP.](#grant-access-from-an-internet-ip-range)

### <a name="required-permissions"></a>Wymagane uprawnienia

Aby zastosować regułę sieci wirtualnej do zasobu usług Cognitive Services, użytkownik musi mieć odpowiednie uprawnienia dla dodawanych podsieci. Wymagane uprawnienie jest domyślną rolą *współautora* lub rolą *współautora usług Cognitive Services.* Wymagane uprawnienia można również dodać do definicji ról niestandardowych.

Zasób usług Cognitive Services i sieci wirtualne, którym udzielono dostępu, mogą znajdować się w różnych subskrypcjach, w tym subskrypcje, które są częścią innej dzierżawy usługi Azure AD.

> [!NOTE]
> Konfiguracja reguł, które udzielają dostępu do podsieci w sieciach wirtualnych, które są częścią innej dzierżawy usługi Azure Active Directory, jest obecnie obsługiwana tylko za pośrednictwem interfejsów API programu Powershell, interfejsu wiersza polecenia i restu. Takich reguł nie można skonfigurować za pośrednictwem witryny Azure portal, chociaż mogą być wyświetlane w portalu.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej

Reguły sieci wirtualnej dla zasobów usług Cognitive Services można zarządzać za pośrednictwem witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Portal Azure](#tab/portal)

1. Przejdź do zasobu usług Cognitive Services, który chcesz zabezpieczyć.

1. Wybierz menu **ZARZĄDZANIE ZASOBAMI** o nazwie **Sieć wirtualna**.

1. Sprawdź, czy wybrano zezwolenie na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do sieci wirtualnej z istniejącą regułą sieci, w obszarze **Sieci wirtualne**wybierz pozycję **Dodaj istniejącą sieć wirtualną**.

   ![Dodawanie istniejącej sieci vNet](media/vnet/virtual-network-add-existing.png)

1. Wybierz opcje **Sieci wirtualne** i **podsieci,** a następnie wybierz pozycję **Włącz**.

   ![Dodawanie istniejących szczegółów sieci vNet](media/vnet/virtual-network-add-existing-details.png)

1. Aby utworzyć nową sieć wirtualną i udzielić jej dostępu, wybierz pozycję **Dodaj nową sieć wirtualną**.

   ![Dodawanie nowej sieci vNet](media/vnet/virtual-network-add-new.png)

1. Podaj informacje niezbędne do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję **Utwórz**.

   ![Tworzenie sieci vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Jeśli punkt końcowy usługi dla usług Azure Cognitive Services nie został wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, można skonfigurować go w ramach tej operacji.
    >
    > Obecnie tylko sieci wirtualne należące do tej samej dzierżawy usługi Azure Active Directory są wyświetlane do wyboru podczas tworzenia reguły. Aby udzielić dostępu do podsieci w sieci wirtualnej należącej do innej dzierżawy, użyj interfejsów API programu Powershell, CLI lub REST.

1. Aby usunąć regułę sieci wirtualnej lub podsieci, wybierz **polecenie ...** aby otworzyć menu kontekstowe dla sieci wirtualnej lub podsieci, a następnie wybierz pozycję **Usuń**.

   ![Usuń vNet](media/vnet/virtual-network-remove.png)

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Zainstaluj [program Azure PowerShell](/powershell/azure/install-az-ps) i [zaloguj się](/powershell/azure/authenticate-azureps)lub wybierz pozycję **Wypróbuj**.

1. Wyświetl listę reguł sieci wirtualnej.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usług Azure Cognitive Services w istniejącej sieci wirtualnej i podsieci.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Dodaj regułę sieci dla sieci wirtualnej i podsieci.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Aby dodać regułę sieci dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, należy użyć w pełni kwalifikowanego parametru **VirtualNetworkResourceId** w postaci "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

1. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [zaloguj się](/cli/azure/authenticate-azure-cli)lub wybierz pozycję **Wypróbuj**.

1. Wyświetl listę reguł sieci wirtualnej.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usług Azure Cognitive Services w istniejącej sieci wirtualnej i podsieci.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Dodaj regułę sieci dla sieci wirtualnej i podsieci.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Aby dodać regułę dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, użyj w pełni kwalifikowanego identyfikatora podsieci w formularzu "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Parametr **subskrypcji** służy do pobierania identyfikatora podsieci dla sieci wirtualnej należącej do innej dzierżawy usługi Azure AD.

1. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Pamiętaj, aby [ustawić domyślną regułę](#change-the-default-network-access-rule) **odrzucania**lub reguły sieciowe nie mają wpływu.

## <a name="grant-access-from-an-internet-ip-range"></a>Udzielanie dostępu z internetowego zakresu adresów IP

Zasoby usług Cognitive Services można skonfigurować tak, aby zezwalać na dostęp z określonych publicznych zakresów adresów IP. Ta konfiguracja zapewnia dostęp do określonych usług i sieci lokalnych, skutecznie blokując ogólny ruch internetowy.

Podaj dozwolone zakresy adresów internetowych przy `16.17.18.0/24` użyciu [notacji CIDR](https://tools.ietf.org/html/rfc4632) w formularzu lub jako indywidualne adresy IP, takie jak `16.17.18.19`.

   > [!Tip]
   > Małe zakresy adresów przy użyciu rozmiarów prefiksów "/31" lub "/32" nie są obsługiwane. Zakresy te powinny być konfigurowane przy użyciu reguł poszczególnych adresów IP.

Reguły sieci IP są dozwolone tylko dla **publicznych internetowych** adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nie są dozwolone w regułach IP. Sieci prywatne obejmują adresy `10.*` `172.16.*`  -  `172.31.*`rozpoczynające `192.168.*`się od , i .

   > [!NOTE]
   > Reguły sieci IP nie mają wpływu na żądania pochodzące z tego samego regionu platformy Azure co zasób usług Cognitive Services. Użyj [reguł sieci wirtualnej,](#grant-access-from-a-virtual-network) aby zezwolić na żądania tego samego regionu.

W tej chwili obsługiwane są tylko adresy IPV4. Każdy zasób usług Cognitive Services obsługuje do 100 reguł sieci IP, które mogą być łączone z [regułami sieci wirtualnej.](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu z sieci lokalnych

Aby udzielić dostępu z sieci lokalnych do zasobu usług Cognitive Services z regułą sieci IP, należy zidentyfikować adresy IP skierowane do Internetu używane przez sieć. Aby uzyskać pomoc, skontaktuj się z administratorem sieci.

Jeśli używasz [usługi ExpressRoute](../expressroute/expressroute-introduction.md) lokalnie do komunikacji równorzędnej publicznej lub komunikacji równorzędnej firmy Microsoft, musisz zidentyfikować adresy IP TRANSLATORA. W przypadku komunikacji równorzędnej publicznej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP TRANSLATORA. Każdy z nich jest stosowany do ruchu usługi platformy Azure, gdy ruch wchodzi w szkielet sieci Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft adresy IP nat, które są używane, są dostarczane przez klienta lub są dostarczane przez usługodawcę. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP

Reguły sieci IP dla zasobów usług Cognitive Services można zarządzać za pośrednictwem portalu Azure, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Portal Azure](#tab/portal)

1. Przejdź do zasobu usług Cognitive Services, który chcesz zabezpieczyć.

1. Wybierz menu **ZARZĄDZANIE ZASOBAMI** o nazwie **Sieć wirtualna**.

1. Sprawdź, czy wybrano zezwolenie na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do internetowego zakresu adresów IP, wprowadź zakres adresów IP lub adresów (w [formacie CIDR)](https://tools.ietf.org/html/rfc4632)w obszarze**Zakres adresów** **zapory** > . Akceptowane są tylko prawidłowe publiczne adresy IP (niezarezerwowane).

   ![Dodaj zakres adresów IP](media/vnet/virtual-network-add-ip-range.png)

1. Aby usunąć regułę sieci IP, <span class="docon docon-delete x-hidden-focus"></span> wybierz ikonę kosza obok zakresu adresów.

   ![Usuń zakres adresów IP](media/vnet/virtual-network-delete-ip-range.png)

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Zainstaluj [program Azure PowerShell](/powershell/azure/install-az-ps) i [zaloguj się](/powershell/azure/authenticate-azureps)lub wybierz pozycję **Wypróbuj**.

1. Lista reguł sieci IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Dodaj regułę sieciową dla pojedynczego adresu IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Dodaj regułę sieciową dla zakresu adresów IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Usuń regułę sieciową dla pojedynczego adresu IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Usuń regułę sieciową dla zakresu adresów IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [zaloguj się](/cli/azure/authenticate-azure-cli)lub wybierz pozycję **Wypróbuj**.

1. Lista reguł sieci IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Dodaj regułę sieciową dla pojedynczego adresu IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Dodaj regułę sieciową dla zakresu adresów IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Usuń regułę sieciową dla pojedynczego adresu IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Usuń regułę sieciową dla zakresu adresów IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Pamiętaj, aby [ustawić domyślną regułę](#change-the-default-network-access-rule) **odrzucania**lub reguły sieciowe nie mają wpływu.

## <a name="next-steps"></a>Następne kroki

* Poznaj różne usługi [Azure Cognitive Services](welcome.md)
* Dowiedz się więcej o [punktach końcowych usługi sieci wirtualnej platformy Azure](../virtual-network/virtual-network-service-endpoints-overview.md)