---
title: 'Usługa ExpressRoute: filtry marszruty — komunikacja równorzędna firmy Microsoft:narzędzie interfejsu wiersza polecenia platformy Azure'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu interfejsu wiersza polecenia platformy Azure
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033411"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: narzędzie interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiają konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi.

Usługi Office 365, takie jak Exchange Online, SharePoint Online i Skype dla firm, są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. Gdy komunikacja równorzędna firmy Microsoft jest skonfigurowana w obwodzie usługi ExpressRoute, wszystkie prefiksy związane z tymi usługami są anonsowane za pośrednictwem sesji BGP, które są ustanawiane. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby uzyskać listę wartości społeczności BGP i usług, które mapują, zobacz [społeczności BGP](expressroute-routing.md#bgp).

Jeśli wymagana jest łączność ze wszystkimi usługami, duża liczba prefiksów są anonsowane za pośrednictwem protokołu BGP. Znacznie zwiększa to rozmiar tabel tras obsługiwanych przez routery w sieci. Jeśli planujesz korzystać tylko z podzbioru usług oferowanych za pośrednictwem komunikacji równorzędnej firmy Microsoft, można zmniejszyć rozmiar tabel tras na dwa sposoby. Możesz:

* Odfiltruj niechciane prefiksy, stosując filtry tras w społecznościach BGP. Jest to standardowa praktyka sieciowa i jest powszechnie używana w wielu sieciach.

* Zdefiniuj filtry tras i zastosuj je do obwodu usługi ExpressRoute. Filtr marszruty to nowy zasób, który umożliwia wybranie listy usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft. Routery usługi ExpressRoute wysyłają tylko listę prefiksów, które należą do usług określonych w filtrze trasy.

### <a name="about-route-filters"></a><a name="about"></a>Informacje o filtrach tras

Gdy komunikacja równorzędna firmy Microsoft jest skonfigurowana w obwodzie usługi ExpressRoute, routery krawędzi firmy Microsoft ustanawiają parę sesji BGP z routerami brzegowymi (twój lub twój dostawca łączności). Żadne trasy nie są ogłaszane w sieci. Aby włączyć ogłaszanie tras w sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Jest to zasadniczo lista wszystkich dozwolonych wartości atrybutu Community protokołu BGP. Gdy zasób filtru tras jest zdefiniowany i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy zamapowane do wartości atrybutu Community protokołu BGP są ogłaszane w sieci.

Aby móc dołączać filtry tras z usługami Office 365, musisz mieć autoryzację korzystania z usług Office 365 za pośrednictwem usługi ExpressRoute. Jeśli nie masz autoryzacji do korzystania z usług Office 365 za pośrednictwem usługi ExpressRoute, operacja dołączania filtrów marszruty nie powiedzie się. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [Azure ExpressRoute for Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana przed 1 sierpnia 2017 r. będzie miała wszystkie prefiksy usługi anonsowane za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana 1 sierpnia 2017 r. lub później nie będzie miała żadnych prefiksów anonsowanych, dopóki filtr trasy nie zostanie dołączony do obwodu.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Aby można było pomyślnie połączyć się z usługami za pośrednictwem komunikacji równorzędnej firmy Microsoft, należy wykonać następujące kroki konfiguracji:

* Musisz mieć aktywny obwód usługi ExpressRoute, który ma aprowizowaną komunikację równorzędną firmy Microsoft. Aby wykonać następujące zadania, można użyć następujących instrukcji:
  * [Utwórz obwód usługi ExpressRoute](howto-circuit-cli.md) i włącz obwód przez dostawcę łączności przed kontynuowaniem. Obwód usługi ExpressRoute musi być w stanie aprowidywizacji i włączone.
  * [Utwórz komunikację równorzędną firmy Microsoft,](howto-routing-cli.md) jeśli bezpośrednio zarządzasz sesją BGP. Możesz też udostępnić dostawcę łączności Microsoft w komunikacji równorzędnej dla obwodu.

* Należy utworzyć i skonfigurować filtr trasy.
  * Identyfikowanie usług, z których możesz korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft
  * Identyfikowanie listy wartości społeczności BGP skojarzonych z usługami
  * Tworzenie reguły zezwalającej na listę prefiksów pasującą do wartości społeczności BGP

* Filtr trasy należy dołączyć do obwodu usługi ExpressRoute.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

* Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](howto-circuit-cli.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie aprowidywizacji i włączone.

* Musisz mieć aktywną komunikację równorzędną firmy Microsoft. Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia i modyfikowania konfiguracji komunikacji równorzędnej](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Jeśli korzystasz z "Wypróbuj", zalogujesz się automatycznie i możesz pominąć krok logowania. Skorzystaj z poniższych przykładów, aby połączyć się:

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

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1: Pobierz listę prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Uzyskaj listę wartości społeczności BGP

Użyj następującego polecenia cmdlet, aby uzyskać listę wartości społeczności BGP skojarzonych z usługami dostępnymi za pośrednictwem komunikacji równorzędnej firmy Microsoft oraz listę skojarzonych z nimi prefiksów:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Zrób listę wartości, których chcesz użyć

Poskonuj listę wartości społeczności BGP, których chcesz użyć w filtrze trasy.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2: Tworzenie filtru trasy i reguły filtru

Filtr trasy może mieć tylko jedną regułę, a reguła musi mieć typ "Zezwalaj". Ta reguła może mieć listę wartości społeczności BGP skojarzonych z nią.

### <a name="1-create-a-route-filter"></a>1. Tworzenie filtru trasy

Najpierw utwórz filtr trasy. Polecenie `az network route-filter create` tworzy tylko zasób filtru trasy. Po utworzeniu zasobu należy utworzyć regułę i dołączyć ją do obiektu filtru trasy. Uruchom następujące polecenie, aby utworzyć zasób filtru trasy:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Tworzenie reguły filtrowania

Uruchom następujące polecenie, aby utworzyć nową regułę:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3: Podłącz filtr trasy do obwodu usługi ExpressRoute

Uruchom następujące polecenie, aby dołączyć filtr trasy do obwodu usługi ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Typowe zadania

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Aby uzyskać właściwości filtru trasy

Aby uzyskać właściwości filtru trasy, użyj następującego polecenia:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aby zaktualizować właściwości filtru trasy

Jeśli filtr trasy jest już dołączony do obwodu, aktualizacje listy społeczności BGP automatycznie propagują odpowiednie zmiany anonsu prefiksów za pośrednictwem ustalonych sesji BGP. Listę społeczności BGP filtru trasy można zaktualizować za pomocą następującego polecenia:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Aby odłączyć filtr trasy od obwodu usługi ExpressRoute

Po odłączeniu filtru trasy od obwodu usługi ExpressRoute nie są anonsowane przez sesję BGP. Filtr trasy można odłączyć od obwodu usługi ExpressRoute za pomocą następującego polecenia:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Aby usunąć filtr trasy

Filtr trasy można usunąć tylko wtedy, gdy nie jest on dołączony do żadnego obwodu. Upewnij się, że filtr trasy nie jest dołączony do żadnego obwodu przed próbą jego usunięcia. Filtr trasy można usunąć za pomocą następującego polecenia:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).
