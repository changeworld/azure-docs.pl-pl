---
title: 'Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: Usługa Azure ExpressRoute — Portal | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu witryny Azure portal.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0515b5e85c3bcf56f1f238620d6036d1be0bec7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839277"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki, które w tym artykule ułatwiają konfigurowanie i zarządzanie nimi filtrów tras dla obwodów usługi ExpressRoute.

Usługi Dynamics 365 i usługi Office 365, takich jak Exchange Online, SharePoint Online i Skype dla firm i usług platformy Azure, takie jak storage i bazy danych SQL są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute, wszystkie prefiksy, które dotyczą te usługi są anonsowane za pośrednictwem sesji protokołu BGP, które są ustalane. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby uzyskać listę wartości społeczności BGP i usług mapowania ich na zobacz [społeczności BGP](expressroute-routing.md#bgp).

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
  - [Utwórz obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i który powinien zostać włączony przez dostawcę połączenia przed kontynuowaniem. Obwód usługi ExpressRoute musi być w stanie zainicjowany i włączony.
  - [Tworzenie komunikacji równorzędnej firmy Microsoft](expressroute-howto-routing-portal-resource-manager.md) Jeśli zarządzasz bezpośrednio za pomocą sesji protokołu BGP. Lub Poproś dostawcę połączenia aprowizacji komunikację równorzędną Microsoft dla obwodu.

-  Należy utworzyć i skonfigurować filtr tras.
    - Zidentyfikuj usługi Ci korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft
    - Identyfikowanie listę wartości społeczności BGP skojarzone z usługami
    - Utwórz regułę zezwalającą na listę prefiksów pasujących wartości społeczności BGP

-  Należy dołączyć filtru tras do obwodu usługi ExpressRoute.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji upewnij się, że spełniają następujące kryteria:

 - Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

 - Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie zainicjowany i włączony.

 - Konieczne jest posiadanie aktywnej komunikacji równorzędnej firmy Microsoft. Postępuj zgodnie z instrukcjami zawartymi w sekcji [tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>Krok 1. Pobierz listę prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Pobierz listę wartości społeczności BGP

Wartości społeczności BGP skojarzone z usługami za pośrednictwem komunikacji równorzędnej firmy Microsoft jest dostępna w [wymagania dotyczące routingu usługi ExpressRoute](expressroute-routing.md) strony.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Tworzenie listy wartości, które chcesz użyć

Tworzenie listy wartości społeczności BGP, którego chcesz użyć do filtru trasy. Na przykład wartość społeczności BGP dla usługi Dynamics 365 jest 12076:5040.

## <a name="filter"></a>Krok 2. Tworzenie filtru tras i regułę filtru

Filtr trasy może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta zasada może mieć listę wartości społeczności BGP skojarzonych z nim.

### <a name="1-create-a-route-filter"></a>1. Tworzenie filtru tras
Można utworzyć filtr tras, wybierając opcję utworzenia nowego zasobu. Kliknij przycisk **Utwórz zasób** > **sieć** > **filtr trasy**, jak pokazano na poniższej ilustracji:

![Tworzenie filtru tras](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Filtr trasy należy umieścić w grupie zasobów. 

![Tworzenie filtru tras](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Utwórz regułę filtru

Można dodawać i zaktualizuj zasady, wybierając kartę Zarządzaj reguły filtru trasy.

![Tworzenie filtru tras](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Można wybrać usługi, który chcesz nawiązać połączenie z listy rozwijanej i zapisać regułę po zakończeniu.

![Tworzenie filtru tras](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>Krok 3. Dołącz filtru tras do obwodu usługi ExpressRoute

Można dołączyć filtru tras do obwodu, wybierając przycisk "Dodaj obwód" i wybierając obwód usługi ExpressRoute z listy rozwijanej.

![Tworzenie filtru tras](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Jeśli dostawca połączenia skonfiguruje, komunikację równorzędną dla odświeżanie obwodu usługi ExpressRoute obwodu z bloku obwodu usługi ExpressRoute można wybrać przycisk "Dodaj obwód".

![Tworzenie filtru tras](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Typowe zadania

### <a name="getproperties"></a>Aby uzyskać właściwości filtru tras

Podczas otwierania zasobu w portalu, można wyświetlić właściwości filtru tras.

![Tworzenie filtru tras](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

Możesz zaktualizować listę wartości społeczności BGP dołączone do obwodu, wybierając przycisk "Reguła Zarządzaj".


![Tworzenie filtru tras](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Tworzenie filtru tras](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="detach"></a>Aby odłączyć filtr tras z obwodem usługi ExpressRoute

Aby odłączyć obwodu z filtru tras, kliknij prawym przyciskiem myszy w ramach obwodu, a następnie kliknij pozycję "Usuń skojarzenie".

![Tworzenie filtru tras](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>Można usunąć filtru tras

Możesz usunąć filtru tras, wybierając przycisk Usuń. 

![Tworzenie filtru tras](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).