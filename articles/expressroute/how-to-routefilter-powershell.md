---
title: 'Usługa ExpressRoute: filtry marszruty — komunikacja równorzędna firmy Microsoft:program Azure PowerShell'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu programu PowerShell
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3fa53258321b22e1683122edca1816f6d4c291b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618616"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: Program PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiają konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi.

Usługi Office 365, takie jak Exchange Online, SharePoint Online i Skype dla firm oraz usługi publiczne platformy Azure, takie jak magazyn i baza danych SQL, są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. Usługi publiczne platformy Azure można wybierać na podstawie regionu i nie można ich zdefiniować dla usługi publicznej.

Gdy komunikacja równorzędna firmy Microsoft jest skonfigurowana w obwodzie usługi ExpressRoute i jest dołączony filtr trasy, wszystkie prefiksy wybrane dla tych usług są anonsowane za pośrednictwem sesji BGP, które są ustanawiane. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby uzyskać listę wartości społeczności BGP i usług, które mapują, zobacz [społeczności BGP](expressroute-routing.md#bgp).

Jeśli wymagana jest łączność ze wszystkimi usługami, duża liczba prefiksów są anonsowane za pośrednictwem protokołu BGP. Znacznie zwiększa to rozmiar tabel tras obsługiwanych przez routery w sieci. Jeśli planujesz korzystać tylko z podzbioru usług oferowanych za pośrednictwem komunikacji równorzędnej firmy Microsoft, można zmniejszyć rozmiar tabel tras na dwa sposoby. Możesz:

- Odfiltruj niechciane prefiksy, stosując filtry tras w społecznościach BGP. Jest to standardowa praktyka sieciowa i jest powszechnie używana w wielu sieciach.

- Zdefiniuj filtry tras i zastosuj je do obwodu usługi ExpressRoute. Filtr marszruty to nowy zasób, który umożliwia wybranie listy usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft. Routery usługi ExpressRoute wysyłają tylko listę prefiksów, które należą do usług określonych w filtrze trasy.

### <a name="about-route-filters"></a><a name="about"></a>Informacje o filtrach tras

Gdy komunikacja równorzędna firmy Microsoft jest skonfigurowana w obwodzie usługi ExpressRoute, routery brzegowe sieci firmy Microsoft ustanawiają parę sesji BGP z routerami brzegowymi (twoimi lub dostawcą łączności). Żadne trasy nie są ogłaszane w sieci. Aby włączyć ogłaszanie tras w sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Jest to zasadniczo lista dozwolonych wszystkich wartości społeczności BGP. Gdy zasób filtru tras jest zdefiniowany i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy zamapowane do wartości atrybutu Community protokołu BGP są ogłaszane w sieci.

Aby móc dołączać filtry tras z usługami Office 365, musisz mieć autoryzację korzystania z usług Office 365 za pośrednictwem usługi ExpressRoute. Jeśli nie masz autoryzacji do korzystania z usług Office 365 za pośrednictwem usługi ExpressRoute, operacja dołączania filtrów marszruty nie powiedzie się. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [Azure ExpressRoute for Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana przed 1 sierpnia 2017 r. będzie miała wszystkie prefiksy usługi anonsowane za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana 1 sierpnia 2017 r. lub później nie będzie miała żadnych prefiksów anonsowanych, dopóki filtr trasy nie zostanie dołączony do obwodu.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Aby można było pomyślnie połączyć się z usługami za pośrednictwem komunikacji równorzędnej firmy Microsoft, należy wykonać następujące kroki konfiguracji:

- Musisz mieć aktywny obwód usługi ExpressRoute, który ma aprowizowaną komunikację równorzędną firmy Microsoft. Aby wykonać następujące zadania, można użyć następujących instrukcji:
  - [Utwórz obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md) i włącz obwód przez dostawcę łączności przed kontynuowaniem. Obwód usługi ExpressRoute musi być w stanie aprowidywizacji i włączone.
  - [Utwórz komunikację równorzędną firmy Microsoft,](expressroute-circuit-peerings.md) jeśli bezpośrednio zarządzasz sesją BGP. Możesz też udostępnić dostawcę łączności Microsoft w komunikacji równorzędnej dla obwodu.

-  Należy utworzyć i skonfigurować filtr trasy.
    - Identyfikowanie usług, z których możesz korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft
    - Identyfikowanie listy wartości społeczności BGP skojarzonych z usługami
    - Tworzenie reguły zezwalającej na listę prefiksów pasującą do wartości społeczności BGP

-  Filtr trasy należy dołączyć do obwodu usługi ExpressRoute.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji upewnij się, że spełniasz następujące kryteria:

 - Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

 - Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie aprowidywizacji i włączone.

 - Musisz mieć aktywną komunikację równorzędną firmy Microsoft. Postępuj zgodnie z instrukcjami w [artykule Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej.](expressroute-circuit-peerings.md)


### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Zaloguj się do konta platformy Azure

Przed rozpoczęciem tej konfiguracji musisz zalogować się na koncie platformy Azure. Polecenie cmdlet wyświetla monit o podanie poświadczeń logowania dla konta platformy Azure. Po zalogowaniu pobiera ono ustawienia konta, aby były dostępne dla programu Azure PowerShell.

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu, aby pomóc ci się połączyć. Jeśli używasz usługi Azure Cloud Shell, nie musisz uruchamiać tego polecenia cmdlet, ponieważ zostaniesz automatycznie zalogowany.

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

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1: Pobierz listę prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Uzyskaj listę wartości społeczności BGP

Użyj następującego polecenia cmdlet, aby uzyskać listę wartości społeczności BGP skojarzonych z usługami dostępnymi za pośrednictwem komunikacji równorzędnej firmy Microsoft oraz listę skojarzonych z nimi prefiksów:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Zrób listę wartości, których chcesz użyć

Poskonuj listę wartości społeczności BGP, których chcesz użyć w filtrze trasy.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2: Tworzenie filtru trasy i reguły filtru

Filtr trasy może mieć tylko jedną regułę, a reguła musi mieć typ "Zezwalaj". Ta reguła może mieć listę wartości społeczności BGP skojarzonych z nią.

### <a name="1-create-a-route-filter"></a>1. Tworzenie filtru trasy

Najpierw utwórz filtr trasy. Polecenie "New-AzRouteFilter" tworzy tylko zasób filtru trasy. Po utworzeniu zasobu należy utworzyć regułę i dołączyć ją do obiektu filtru trasy. Uruchom następujące polecenie, aby utworzyć zasób filtru trasy:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Tworzenie reguły filtrowania

Można określić zestaw społeczności BGP jako listę oddzieloną przecinkami, jak pokazano w przykładzie. Uruchom następujące polecenie, aby utworzyć nową regułę:
 
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

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3: Podłącz filtr trasy do obwodu usługi ExpressRoute

Uruchom następujące polecenie, aby dołączyć filtr trasy do obwodu usługi ExpressRoute, przy założeniu, że masz tylko komunikację równorzędną firmy Microsoft:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Typowe zadania

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Aby uzyskać właściwości filtru trasy

Aby uzyskać właściwości filtru trasy, należy wykonać następujące czynności:

1. Uruchom następujące polecenie, aby uzyskać zasób filtru trasy:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Pobierz reguły filtru trasy dla zasobu filtru trasy, uruchamiając następujące polecenie:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aby zaktualizować właściwości filtru trasy

Jeśli filtr trasy jest już dołączony do obwodu, aktualizacje listy społeczności BGP automatycznie propagują odpowiednie zmiany anonsu prefiksów za pośrednictwem ustalonych sesji BGP. Listę społeczności BGP filtru trasy można zaktualizować za pomocą następującego polecenia:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Aby odłączyć filtr trasy od obwodu usługi ExpressRoute

Po odłączeniu filtru trasy od obwodu usługi ExpressRoute nie są anonsowane przez sesję BGP. Filtr trasy można odłączyć od obwodu usługi ExpressRoute za pomocą następującego polecenia:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Aby usunąć filtr trasy

Filtr trasy można usunąć tylko wtedy, gdy nie jest on dołączony do żadnego obwodu. Upewnij się, że filtr trasy nie jest dołączony do żadnego obwodu przed próbą jego usunięcia. Filtr trasy można usunąć za pomocą następującego polecenia:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).
