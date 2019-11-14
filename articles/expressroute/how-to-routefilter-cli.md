---
title: 'ExpressRoute: filtry tras — Komunikacja równorzędna firmy Microsoft: interfejs wiersza polecenia platformy Azure'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla Peering firmy Microsoft przy użyciu wiersza polecenia platformy Azure
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033411"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki, które w tym artykule ułatwiają konfigurowanie i zarządzanie nimi filtrów tras dla obwodów usługi ExpressRoute.

Usługi Office 365, takie jak Exchange Online, SharePoint Online i Skype dla firm, są dostępne za pomocą komunikacji równorzędnej firmy Microsoft. W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute, wszystkie prefiksy, które dotyczą te usługi są anonsowane za pośrednictwem sesji protokołu BGP, które są ustalane. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby uzyskać listę wartości społeczności BGP i usług mapowania ich na zobacz [społeczności BGP](expressroute-routing.md#bgp).

Jeśli potrzebujesz łączności z wszystkich usług, dużej liczby prefiksy są anonsowane za pośrednictwem protokołu BGP. To znacznie zwiększa rozmiar tabel tras, obsługiwane przez routery w sieci. Jeśli planujesz korzystanie z podzestawu usługi oferowane za pośrednictwem komunikacji równorzędnej firmy Microsoft, można zmniejszyć rozmiar tabelach tras na dwa sposoby. Możesz:

* Odfiltruj prefiksy niepożądane, stosując filtry tras na społeczności BGP. To jest standardową praktyką sieci i jest często używany w wielu sieciach.

* Zdefiniuj filtry tras i zastosować je na obwód usługi ExpressRoute. Filtr trasy jest nowy zasób, który pozwala wybrać listę usług, które planujesz korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft. Usługa ExpressRoute routery przesyłają tylko listę prefiksów, które należą do usług określonych w filtrze trasy.

### <a name="about"></a>Informacje o filtrach trasy

W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft na obwód usługi ExpressRoute, routery graniczne firmy Microsoft ustanowić pary sesji protokołu BGP z routerami granicznymi (należy do Ciebie lub dostawcą połączenia). Żadne trasy nie są ogłaszane w sieci. Aby włączyć ogłaszanie tras w sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Jest to zasadniczo lista wszystkich wartości atrybutu Community protokołu BGP. Gdy zasób filtru tras jest zdefiniowany i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy zamapowane do wartości atrybutu Community protokołu BGP są ogłaszane w sieci.

Aby móc Dołącz filtry tras z usługami Office 365 na nich musi mieć zezwolenie na korzystanie z usług Office 365 za pośrednictwem usługi ExpressRoute. Jeśli nie masz uprawnień do korzystania z usług Office 365 za pośrednictwem usługi ExpressRoute, Dołącz filtry tras operacja kończy się niepowodzeniem. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [usługi Azure ExpressRoute dla usługi Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 r. komunikacji równorzędnej firmy Microsoft będzie miał wszystkie prefiksy usługi anonsowanego za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli nie zdefiniowano filtry tras. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, skonfigurowanych po 1 sierpnia 2017 r. nie będzie miał wszelkie prefiksy anonsowane do czasu podłączenia filtru tras do obwodu.
> 
> 

### <a name="workflow"></a>Przepływ pracy

Aby móc nawiązywać połączeń z usługami za pośrednictwem komunikacji równorzędnej firmy Microsoft, należy wykonać poniższe czynności konfiguracyjne:

* Musisz mieć aktywny obwód usługi ExpressRoute, zawierającej komunikacji równorzędnej elastycznie firmy Microsoft. Można użyć zgodnie z poniższymi instrukcjami, aby wykonać te zadania:
  * [Utwórz obwód usługi ExpressRoute](howto-circuit-cli.md) i który powinien zostać włączony przez dostawcę połączenia przed kontynuowaniem. Obwód usługi ExpressRoute musi być w stanie zainicjowany i włączony.
  * [Tworzenie komunikacji równorzędnej firmy Microsoft](howto-routing-cli.md) Jeśli zarządzasz bezpośrednio za pomocą sesji protokołu BGP. Lub Poproś dostawcę połączenia aprowizacji komunikację równorzędną Microsoft dla obwodu.

* Należy utworzyć i skonfigurować filtr tras.
  * Zidentyfikuj usługi Ci korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft
  * Identyfikowanie listę wartości społeczności BGP skojarzone z usługami
  * Utwórz regułę zezwalającą na listę prefiksów pasujących wartości społeczności BGP

* Należy dołączyć filtru tras do obwodu usługi ExpressRoute.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

* Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](howto-circuit-cli.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie zainicjowany i włączony.

* Konieczne jest posiadanie aktywnej komunikacji równorzędnej firmy Microsoft. Postępuj zgodnie z instrukcjami zawartymi w sekcji [tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Jeśli używasz "Try It" jest zalogowany automatycznie i można pominąć krok logowania. Poniższe przykłady umożliwiają łatwiejszego nawiązania połączenia:

```azurecli
az login
```

Sprawdź subskrypcje dostępne na koncie.

```azurecli-interactive
az account list
```

Wybierz subskrypcję, dla której chcesz utworzyć obwód usługi ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Krok 1: Pobierz listę prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Pobierz listę wartości społeczności BGP

Aby uzyskać listę wartości społeczności BGP skojarzone z usługami za pośrednictwem komunikacji równorzędnej firmy Microsoft i listę prefiksów skojarzonych z nimi, należy użyć następującego polecenia cmdlet:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Utwórz listę wartości, których chcesz użyć

Tworzenie listy wartości społeczności BGP, którego chcesz użyć do filtru trasy.

## <a name="filter"></a>Krok 2: Tworzenie filtru tras i regułę filtru

Filtr trasy może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta zasada może mieć listę wartości społeczności BGP skojarzonych z nim.

### <a name="1-create-a-route-filter"></a>1. Tworzenie filtru tras

Najpierw utwórz filtr tras. Polecenie `az network route-filter create` tworzy tylko zasób filtru tras. Po utworzeniu zasobu, należy utworzyć regułę i dołącz je do obiektu filtru trasy. Uruchom następujące polecenie, aby utworzyć zasób filtru trasy:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Utwórz regułę filtru

Uruchom następujące polecenie, aby utworzyć nową regułę:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Krok 3: Dołącz filtru tras do obwodu usługi ExpressRoute

Uruchom następujące polecenie, aby dołączyć filtru tras do obwodu usługi ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Typowe zadania

### <a name="getproperties"></a>Aby uzyskać właściwości filtru tras

Aby uzyskać właściwości filtru tras, użyj następującego polecenia:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

Jeśli filtr tras jest już dołączony do obwodu, aktualizacje do listy społeczności BGP automatycznie rozpropagowane zmiany anonsowania prefiksu za pośrednictwem ustanowionych sesji protokołu BGP. Można zaktualizować listy społeczności BGP filtru tras za pomocą następującego polecenia:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Aby odłączyć filtr tras z obwodem usługi ExpressRoute

Gdy filtr tras jest odłączona od obwodu usługi ExpressRoute, nie prefiksy są anonsowane za pośrednictwem sesji BGP. Możesz odłączyć filtr tras z obwodem usługi ExpressRoute, korzystając z następującego polecenia:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Można usunąć filtru tras

Filtr tras można usunąć tylko wtedy, jeśli nie jest dołączony do dowolnego obwodu. Upewnij się, że filtr tras nie jest dołączony do dowolnego obwodu przed próbą usunięcia go. Można usunąć filtru tras, używając następującego polecenia:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
