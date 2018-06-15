---
title: 'Konfigurowanie filtrów trasy dla komunikacji równorzędnej platformy Azure ExpressRoute Microsoft: programu PowerShell | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla Peering firmy Microsoft przy użyciu programu PowerShell
documentationcenter: na
services: expressroute
author: ganesr
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: ganesr
ms.openlocfilehash: 6e767166ecf248aa0e7fc16dc21361394e03107d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593495"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Konfigurowanie filtrów trasy dla komunikacji równorzędnej firmy Microsoft: środowiska PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry trasy są sposób korzystać z podzbioru obsługiwane usługi za pomocą komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiające konfigurowanie i Zarządzanie filtrami trasy dla obwody usługi ExpressRoute.

Usługi Dynamics 365 i usług Office 365, takich jak Exchange Online, SharePoint Online i Skype dla firm i Azure usługi publiczne, takie jak magazyn i bazy danych SQL są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. Usługi publicznej platformy Azure można wybrać na podstawie na region i nie może być zdefiniowana dla publicznej. 

Podczas komunikacji równorzędnej firmy Microsoft jest konfigurowana dla obwodu usługi ExpressRoute i jest dołączony filtr tras, za pośrednictwem sesji protokołu BGP, które są ustalane są rozgłaszane wszystkie prefiksy, które są wybrane dla tych usług. Wartość społeczności protokołu BGP jest dołączony do każdego prefiksu do identyfikowania usługa, która jest dostępna za pośrednictwem prefiks. Aby uzyskać listę wartości społeczności BGP i usług, są one wykonywane na, zobacz [społeczności BGP](expressroute-routing.md#bgp).

Jeśli potrzebujesz łączności z usługami wszystkich dużej liczby prefiksów są rozgłaszane za pomocą protokołu BGP. To znacznie zwiększa rozmiar tabel tras obsługiwanego przez routery w sieci. Jeśli planujesz używać tylko podzestaw usługami oferowanymi w ramach komunikacji równorzędnej firmy Microsoft, można zmniejszyć rozmiar tabel tras na dwa sposoby. Możesz:

- Odfiltrować niechciane prefiksy, stosując filtry tras na Wspólnot protokołu BGP. To jest standardową praktyką sieci i jest powszechnie używany w wielu sieciach.

- Zdefiniuj filtry tras i zastosować je do obwodu usługi ExpressRoute. Filtr tras jest nowy zasób, który umożliwia wybranie na liście usług, której planujesz używać za pomocą komunikacji równorzędnej firmy Microsoft. Usługi routerami wysłać tylko listę prefiksów, które należą do usługi określone w filtrze trasy.

### <a name="about"></a>Informacje o filtrach trasy

W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft na obwodu ExpressRoute, routery brzegowe Microsoft ustanowić parę sesje BGP z routerami krawędzi (należy do Ciebie lub dostawcą połączenia). Nie trasy są anonsowane do sieci. Aby włączyć anonsów tras do sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które chcesz korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft obwodu usługi ExpressRoute. Jest zasadniczo białą listę wszystkich wartości społeczności protokołu BGP. Gdy zasób filtru trasa jest zdefiniowana i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy mapowane na wartości społeczności BGP są rozgłaszane do sieci.

Aby można było dołączyć filtry tras z usługami Office 365 na nich, musi mieć autoryzację do korzystania z usług Office 365 za pomocą usługi ExpressRoute. Jeśli nie masz uprawnień do korzystania z usług Office 365 za pomocą usługi ExpressRoute, operacja dołączyć filtry tras kończy się niepowodzeniem. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [Azure ExpressRoute dla usługi Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Łączności z usługami Dynamics 365 nie wymaga żadnych poprzednich autoryzacji.

> [!IMPORTANT]
> Z obwody usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 komunikacji równorzędnej firmy Microsoft ma wszystkie prefiksy usługi anonsowane przez firmę Microsoft, zaglądanie, nawet jeśli nie zdefiniowano filtrów trasy. Z obwody usługi ExpressRoute, które są skonfigurowane na lub po 1 sierpnia 2017 komunikacji równorzędnej firmy Microsoft nie będzie miał wszystkie prefiksy anonsowane do momentu filtr tras jest dołączony do obwodu.
> 
> 

### <a name="workflow"></a>przepływ pracy

Aby móc nawiązywać połączeń z usługami za pomocą komunikacji równorzędnej firmy Microsoft, wykonaj następujące kroki konfiguracji:

- Musisz mieć aktywne obwodu usługi ExpressRoute z Microsoft równorzędna elastycznie. Wykonanie tych zadań, można użyć poniższych instrukcji:
  - [Utworzyć obwodu usługi ExpressRoute](expressroute-howto-circuit-arm.md) i mieć obwodu włączane przez dostawcą połączenia, aby kontynuować. Obwód usługi expressroute musi być w stanie elastycznie i włączona.
  - [Utwórz komunikacji równorzędnej firmy Microsoft](expressroute-circuit-peerings.md) Jeśli zarządzasz bezpośrednio za pomocą sesji protokołu BGP. Lub mieć dostawcą połączenia udostępniania Microsoft komunikacji równorzędnej dla obwodu.

-  Należy utworzyć i skonfigurować filtr trasy.
    - Identyfikowanie usług o użycie za pomocą komunikacji równorzędnej firmy Microsoft
    - Określenie listy wartości społeczności BGP powiązanego ze wskazanymi usługami
    - Utwórz regułę w celu zezwalania na liście prefiks zgodne wartości społeczności BGP

-  Należy dołączyć filtru tras z obwodem usługi ExpressRoute.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji upewnij się, że spełniają następujące kryteria:

 - Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps).

  > [!NOTE]
  > Pobierz najnowszą wersję z galerii programu PowerShell, a nie za pomocą Instalatora. Instalator nie obsługuje obecnie wymaganych poleceń cmdlet.
  > 

 - Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.

 - Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi expressroute musi być w stanie elastycznie i włączona.

 - Musisz mieć aktywne komunikacji równorzędnej firmy Microsoft. Postępuj zgodnie z instrukcjami w [tworzenia i modyfikowania konfiguracji komunikacji równorzędnej](expressroute-circuit-peerings.md)

### <a name="log-in-to-your-azure-account"></a>Zaloguj się do konta platformy Azure

Przed rozpoczęciem tej konfiguracji musisz zalogować się na koncie platformy Azure. Polecenie cmdlet wyświetla monit o podanie poświadczeń logowania dla konta platformy Azure. Po zalogowaniu pobiera ono ustawienia konta, aby były dostępne dla programu Azure PowerShell.

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzureRmAccount
```

Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

```powershell
Get-AzureRmSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>Krok 1: Pobierz listę prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Pobierz listę wartości społeczności BGP

Użyj następującego polecenia cmdlet można pobrać listy wartości społeczności BGP związanych z usługami dostępny za pośrednictwem komunikacji równorzędnej firmy Microsoft oraz listę prefiksów skojarzonych z nimi:

```powershell
Get-AzureRmBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Tworzenie listy wartości, które ma być używany

Tworzenie listy wartości społeczności protokołu BGP, które mają być używane w filtrze trasy. Na przykład wartość społeczności BGP dla usługi Dynamics 365 jest 12076:5040.

## <a name="filter"></a>Krok 2: Tworzenie filtr tras i regułę filtra

Filtr tras może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta zasada może mieć listy wartości społeczności BGP skojarzonych z nim.

### <a name="1-create-a-route-filter"></a>1. Utwórz filtr trasy

Najpierw należy utworzyć filtr trasy. Polecenie "New-AzureRmRouteFilter" tylko tworzy zasób filtr trasy. Po utworzeniu zasobu, należy utworzyć regułę i dołącz je do obiektu filtra trasy. Uruchom następujące polecenie, aby utworzyć zasób filtr trasy:

```powershell
New-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Utwórz regułę filtru

Można określić zestaw Wspólnot protokołu BGP jako listę rozdzielaną przecinkami, jak pokazano w przykładzie. Uruchom następujące polecenie, aby utworzyć nową regułę:
 
```powershell
$rule = New-AzureRmRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Dodaj regułę filtrowania trasy

Uruchom następujące polecenie, aby dodać regułę filtru do filtru tras:
 
```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>Krok 3: Dołączenie filtru tras z obwodem usługi ExpressRoute

Uruchom następujące polecenie, aby dołączyć filtr trasy do obwodu usługi expressroute, przy założeniu, że masz tylko komunikacji równorzędnej firmy Microsoft:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Typowe zadania

### <a name="getproperties"></a>Aby uzyskać właściwości filtru trasy

Aby uzyskać właściwości filtru trasy, wykonaj następujące kroki:

1. Uruchom następujące polecenie, aby uzyskać zasobu filtr trasy:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. Pobierz reguły filtrowania trasy dla zasobu filtru tras, uruchamiając następujące polecenie:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

### <a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

Jeśli filtr trasy jest już dołączony do obwodu, aktualizacji do listy społeczności BGP automatycznie rozpropagowane prefiksu anonsu zmian za pomocą ustanowionych sesji BGP. Można zaktualizować listy społeczności BGP filtru trasy przy użyciu następującego polecenia:

```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Aby odłączyć filtr tras z obwodu usługi ExpressRoute

Gdy filtr tras jest odłączony od obwodu ExpressRoute, prefiksy nie są rozgłaszane za pomocą sesji protokołu BGP. Można odłączyć filtr tras z obwodu usługi ExpressRoute, za pomocą następującego polecenia:
  
```powershell
$ckt.Peerings[0].RouteFilter = $null
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Aby usunąć filtr trasy

Filtr tras można usuwać tylko, jeśli nie jest dołączony do dowolnej obwodu. Upewnij się, że filtr tras nie jest podłączony do żadnych obwodu przed próbą usunięcia. Można usunąć filtr tras za pomocą następującego polecenia:

```powershell
Remove-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
