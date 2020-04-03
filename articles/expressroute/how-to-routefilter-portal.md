---
title: 'Usługa ExpressRoute: filtry marszruty — komunikacja równorzędna firmy Microsoft:witryna Azure portal'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu witryny Azure portal.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: f2be9b4e7152c61885b1a41e94ebd328059d437b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618562"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: witryna Azure portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiają konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi.

Usługi Office 365, takie jak Exchange Online, SharePoint Online i Skype dla firm, oraz usługi platformy Azure, takie jak magazyn i baza danych SQL, są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. Gdy komunikacja równorzędna firmy Microsoft jest skonfigurowana w obwodzie usługi ExpressRoute, wszystkie prefiksy związane z tymi usługami są anonsowane za pośrednictwem sesji BGP, które są ustanawiane. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby uzyskać listę wartości społeczności BGP i usług, które mapują, zobacz [społeczności BGP](expressroute-routing.md#bgp).

Jeśli wymagana jest łączność ze wszystkimi usługami, duża liczba prefiksów są anonsowane za pośrednictwem protokołu BGP. Znacznie zwiększa to rozmiar tabel tras obsługiwanych przez routery w sieci. Jeśli planujesz korzystać tylko z podzbioru usług oferowanych za pośrednictwem komunikacji równorzędnej firmy Microsoft, można zmniejszyć rozmiar tabel tras na dwa sposoby. Możesz:

- Odfiltruj niechciane prefiksy, stosując filtry tras w społecznościach BGP. Jest to standardowa praktyka sieciowa i jest powszechnie używana w wielu sieciach.

- Zdefiniuj filtry tras i zastosuj je do obwodu usługi ExpressRoute. Filtr marszruty to nowy zasób, który umożliwia wybranie listy usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft. Routery usługi ExpressRoute wysyłają tylko listę prefiksów, które należą do usług określonych w filtrze trasy.

### <a name="about-route-filters"></a><a name="about"></a>Informacje o filtrach tras

Gdy komunikacja równorzędna firmy Microsoft jest skonfigurowana w obwodzie usługi ExpressRoute, routery krawędzi firmy Microsoft ustanawiają parę sesji BGP z routerami brzegowymi (twój lub twój dostawca łączności). Żadne trasy nie są ogłaszane w sieci. Aby włączyć ogłaszanie tras w sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Jest to zasadniczo lista wszystkich wartości społeczności BGP, które chcesz zezwolić. Gdy zasób filtru tras jest zdefiniowany i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy zamapowane do wartości atrybutu Community protokołu BGP są ogłaszane w sieci.

Aby móc dołączać filtry tras z usługami Office 365, musisz mieć autoryzację korzystania z usług Office 365 za pośrednictwem usługi ExpressRoute. Jeśli nie masz autoryzacji do korzystania z usług Office 365 za pośrednictwem usługi ExpressRoute, operacja dołączania filtrów marszruty nie powiedzie się. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [Azure ExpressRoute for Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana przed 1 sierpnia 2017 r. będzie miała wszystkie prefiksy usługi anonsowane za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana 1 sierpnia 2017 r. lub później nie będzie miała żadnych prefiksów anonsowanych, dopóki filtr trasy nie zostanie dołączony do obwodu.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Aby można było pomyślnie połączyć się z usługami za pośrednictwem komunikacji równorzędnej firmy Microsoft, należy wykonać następujące kroki konfiguracji:

- Musisz mieć aktywny obwód usługi ExpressRoute, który ma aprowizowaną komunikację równorzędną firmy Microsoft. Aby wykonać następujące zadania, można użyć następujących instrukcji:
  - [Utwórz obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i włącz obwód przez dostawcę łączności przed kontynuowaniem. Obwód usługi ExpressRoute musi być w stanie aprowidywizacji i włączone.
  - [Utwórz komunikację równorzędną firmy Microsoft,](expressroute-howto-routing-portal-resource-manager.md) jeśli bezpośrednio zarządzasz sesją BGP. Możesz też udostępnić dostawcę łączności Microsoft w komunikacji równorzędnej dla obwodu.

-  Należy utworzyć i skonfigurować filtr trasy.
    - Identyfikowanie usług, z których możesz korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft
    - Identyfikowanie listy wartości społeczności BGP skojarzonych z usługami
    - Tworzenie reguły zezwalającej na listę prefiksów pasującą do wartości społeczności BGP

-  Filtr trasy należy dołączyć do obwodu usługi ExpressRoute.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji upewnij się, że spełniasz następujące kryteria:

 - Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

 - Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie aprowidywizacji i włączone.

 - Musisz mieć aktywną komunikację równorzędną firmy Microsoft. Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia i modyfikowania konfiguracji komunikacji równorzędnej](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1: Pobierz listę prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Uzyskaj listę wartości społeczności BGP

Wartości społeczności BGP skojarzone z usługami dostępnymi za pośrednictwem komunikacji równorzędnej firmy Microsoft są dostępne na stronie [Wymagania dotyczące routingu usługi ExpressRoute.](expressroute-routing.md)

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Zrób listę wartości, których chcesz użyć

Poskonuj listę [wartości społeczności BGP,](expressroute-routing.md#bgp) których chcesz użyć w filtrze trasy. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2: Tworzenie filtru trasy i reguły filtru

Filtr trasy może mieć tylko jedną regułę, a reguła musi mieć typ "Zezwalaj". Ta reguła może mieć listę wartości społeczności BGP skojarzonych z nią.

### <a name="1-create-a-route-filter"></a>1. Tworzenie filtru trasy
Filtr trasy można utworzyć, wybierając opcję utworzenia nowego zasobu. Kliknij **pozycję Utwórz filtr** > **trasy****sieciowej** > , jak pokazano na poniższej ilustracji:

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Filtr trasy należy umieścić w grupie zasobów. 

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Tworzenie reguły filtrowania

Reguły można dodawać i aktualizować, wybierając kartę zarządzaj regułą dla filtru trasy.

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Możesz wybrać usługi, z którymi chcesz się połączyć, z listy rozwijanej i zapisać regułę po zakończeniu.

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3: Podłącz filtr trasy do obwodu usługi ExpressRoute

Filtr trasy można dołączyć do obwodu, wybierając przycisk "Dodaj obwód" i wybierając obwód usługi ExpressRoute z listy rozwijanej.

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Jeśli dostawca łączności konfiguruje komunikację równorzędną dla obwodu usługi ExpressRoute odświeżyć obwód z bloku obwodu usługi ExpressRoute przed wybraniem przycisku "Dodaj obwód".

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Typowe zadania

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Aby uzyskać właściwości filtru trasy

Właściwości filtru marszruty można wyświetlić po otwarciu zasobu w portalu.

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aby zaktualizować właściwości filtru trasy

Listę wartości społeczności BGP dołączonych do obwodu można zaktualizować, wybierając przycisk "Zarządzaj regułą".


![Tworzenie filtru trasy](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Aby odłączyć filtr trasy od obwodu usługi ExpressRoute

Aby odłączyć obwód od filtra trasy, kliknij prawym przyciskiem myszy na obwód i kliknij "odłącz".

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Aby usunąć filtr trasy

Filtr trasy można usunąć, wybierając przycisk usuń. 

![Tworzenie filtru trasy](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).

* Aby uzyskać informacje na temat przykładów konfiguracji routera, zobacz [Przykłady konfiguracji routera, aby skonfigurować routing i zarządzać nim](expressroute-config-samples-routing.md). 
