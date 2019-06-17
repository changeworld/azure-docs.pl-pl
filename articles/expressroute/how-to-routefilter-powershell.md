---
title: 'Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft — ExpressRoute: Program PowerShell: Azure | Microsoft Docs'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla Peering firmy Microsoft przy użyciu programu PowerShell
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: e5d94fad5ddcfd0b34e36cb96727cff48b62ea0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730218"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki, które w tym artykule ułatwiają konfigurowanie i zarządzanie nimi filtrów tras dla obwodów usługi ExpressRoute.

Usługi Dynamics 365 i usługami Office 365, takich jak Exchange Online, SharePoint Online i Skype dla firm i usługi publiczne platformy Azure, takie jak storage i bazy danych SQL są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. Usługi publiczne platformy Azure można wybrać na podstawie poszczególnych regionów i nie może być zdefiniowana dla usługi publicznej.

Podczas komunikacji równorzędnej firmy Microsoft jest skonfigurowany na obwód usługi ExpressRoute, a następnie filtr tras jest dołączona, wszystkie prefiksy, które są wybrane dla tych usług są anonsowane za pośrednictwem sesji protokołu BGP, które są ustanowione. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby uzyskać listę wartości społeczności BGP i usług mapowania ich na zobacz [społeczności BGP](expressroute-routing.md#bgp).

Jeśli potrzebujesz łączności z wszystkich usług, dużej liczby prefiksy są anonsowane za pośrednictwem protokołu BGP. To znacznie zwiększa rozmiar tabel tras, obsługiwane przez routery w sieci. Jeśli planujesz korzystanie z podzestawu usługi oferowane za pośrednictwem komunikacji równorzędnej firmy Microsoft, można zmniejszyć rozmiar tabelach tras na dwa sposoby. Możesz:

- Odfiltruj prefiksy niepożądane, stosując filtry tras na społeczności BGP. To jest standardową praktyką sieci i jest często używany w wielu sieciach.

- Zdefiniuj filtry tras i zastosować je na obwód usługi ExpressRoute. Filtr trasy jest nowy zasób, który pozwala wybrać listę usług, które planujesz korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft. Usługa ExpressRoute routery przesyłają tylko listę prefiksów, które należą do usług określonych w filtrze trasy.

### <a name="about"></a>Informacje o filtrach trasy

W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft na obwód usługi ExpressRoute, routery graniczne firmy Microsoft ustanowić pary sesji protokołu BGP z routerami granicznymi (należy do Ciebie lub dostawcą połączenia). Żadne trasy nie są ogłaszane w sieci. Aby włączyć ogłaszanie tras w sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Jest to zasadniczo lista wszystkich wartości atrybutu Community protokołu BGP. Gdy zasób filtru tras jest zdefiniowany i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy zamapowane do wartości atrybutu Community protokołu BGP są ogłaszane w sieci.

Aby móc Dołącz filtry tras z usługami Office 365 na nich musi mieć zezwolenie na korzystanie z usług Office 365 za pośrednictwem usługi ExpressRoute. Jeśli nie masz uprawnień do korzystania z usług Office 365 za pośrednictwem usługi ExpressRoute, Dołącz filtry tras operacja kończy się niepowodzeniem. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [usługi Azure ExpressRoute dla usługi Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Łączność z usługi Dynamics 365 nie wymaga dowolnego pozwoleniu.

> [!IMPORTANT]
> Obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 r. komunikacji równorzędnej firmy Microsoft będzie miał wszystkie prefiksy usługi anonsowanego za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli nie zdefiniowano filtry tras. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, skonfigurowanych po 1 sierpnia 2017 r. nie będzie miał wszelkie prefiksy anonsowane do czasu podłączenia filtru tras do obwodu.
> 
> 

### <a name="workflow"></a>Przepływ pracy

Aby móc nawiązywać połączeń z usługami za pośrednictwem komunikacji równorzędnej firmy Microsoft, należy wykonać poniższe czynności konfiguracyjne:

- Musisz mieć aktywny obwód usługi ExpressRoute, zawierającej komunikacji równorzędnej elastycznie firmy Microsoft. Można użyć zgodnie z poniższymi instrukcjami, aby wykonać te zadania:
  - [Utwórz obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md) i który powinien zostać włączony przez dostawcę połączenia przed kontynuowaniem. Obwód usługi ExpressRoute musi być w stanie zainicjowany i włączony.
  - [Tworzenie komunikacji równorzędnej firmy Microsoft](expressroute-circuit-peerings.md) Jeśli zarządzasz bezpośrednio za pomocą sesji protokołu BGP. Lub Poproś dostawcę połączenia aprowizacji komunikację równorzędną Microsoft dla obwodu.

-  Należy utworzyć i skonfigurować filtr tras.
    - Zidentyfikuj usługi Ci korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft
    - Identyfikowanie listę wartości społeczności BGP skojarzone z usługami
    - Utwórz regułę zezwalającą na listę prefiksów pasujących wartości społeczności BGP

-  Należy dołączyć filtru tras do obwodu usługi ExpressRoute.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji upewnij się, że spełniają następujące kryteria:

 - Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

 - Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie zainicjowany i włączony.

 - Konieczne jest posiadanie aktywnej komunikacji równorzędnej firmy Microsoft. Postępuj zgodnie z instrukcjami w [tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-circuit-peerings.md) artykułu.


### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Zaloguj się do konta platformy Azure

Przed rozpoczęciem tej konfiguracji musisz zalogować się na koncie platformy Azure. Polecenie cmdlet wyświetla monit o podanie poświadczeń logowania dla konta platformy Azure. Po zalogowaniu pobiera ono ustawienia konta, aby były dostępne dla programu Azure PowerShell.

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Skorzystaj z następującego przykładu w celu łatwiejszego nawiązania połączenia. Jeśli używasz usługi Azure Cloud Shell, nie trzeba uruchomić to polecenie cmdlet, jak użytkownik zostanie automatycznie zarejestrowany w.

```azurepowershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

```azurepowershell-interactive
Get-AzSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>Krok 1. Pobierz listę prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Pobierz listę wartości społeczności BGP

Aby uzyskać listę wartości społeczności BGP skojarzone z usługami za pośrednictwem komunikacji równorzędnej firmy Microsoft i listę prefiksów skojarzonych z nimi, należy użyć następującego polecenia cmdlet:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Tworzenie listy wartości, które chcesz użyć

Tworzenie listy wartości społeczności BGP, którego chcesz użyć do filtru trasy. Na przykład wartość społeczności BGP dla usługi Dynamics 365 jest 12076:5040.

## <a name="filter"></a>Krok 2. Tworzenie filtru tras i regułę filtru

Filtr trasy może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta zasada może mieć listę wartości społeczności BGP skojarzonych z nim.

### <a name="1-create-a-route-filter"></a>1. Tworzenie filtru tras

Najpierw utwórz filtr tras. Polecenie "New-AzRouteFilter" tylko tworzy zasób filtru trasy. Po utworzeniu zasobu, należy utworzyć regułę i dołącz je do obiektu filtru trasy. Uruchom następujące polecenie, aby utworzyć zasób filtru trasy:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Utwórz regułę filtru

Można określić zbiór społeczności BGP jako listę rozdzielaną przecinkami, jak pokazano w przykładzie. Uruchom następujące polecenie, aby utworzyć nową regułę:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Dodaj regułę do filtru trasy

Uruchom następujące polecenie, aby dodać regułę filtru do filtru trasy:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>Krok 3. Dołącz filtru tras do obwodu usługi ExpressRoute

Uruchom następujące polecenie, aby dołączyć filtru tras do obwodu usługi ExpressRoute, przy założeniu, że tylko komunikacji równorzędnej firmy Microsoft:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Typowe zadania

### <a name="getproperties"></a>Aby uzyskać właściwości filtru tras

Można pobrać właściwości filtru tras, wykonaj następujące kroki:

1. Uruchom następujące polecenie, aby pobrać zasób filtru trasy:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Pobierz reguły filtru trasy dla zasobu filtru tras, uruchamiając następujące polecenie:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

Jeśli filtr tras jest już dołączony do obwodu, aktualizacje do listy społeczności BGP automatycznie rozpropagowane zmiany anonsowania prefiksu za pośrednictwem ustanowionych sesji protokołu BGP. Można zaktualizować listy społeczności BGP filtru tras za pomocą następującego polecenia:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Aby odłączyć filtr tras z obwodem usługi ExpressRoute

Gdy filtr tras jest odłączona od obwodu usługi ExpressRoute, nie prefiksy są anonsowane za pośrednictwem sesji BGP. Możesz odłączyć filtr tras z obwodem usługi ExpressRoute, korzystając z następującego polecenia:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Można usunąć filtru tras

Filtr tras można usunąć tylko wtedy, jeśli nie jest dołączony do dowolnego obwodu. Upewnij się, że filtr tras nie jest dołączony do dowolnego obwodu przed próbą usunięcia go. Można usunąć filtru tras, używając następującego polecenia:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
