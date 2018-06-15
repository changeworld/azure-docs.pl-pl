---
title: 'Konfigurowanie filtrów trasy dla komunikacji równorzędnej platformy Azure ExpressRoute Microsoft: Portal | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla Peering firmy Microsoft przy użyciu portalu Azure
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
ms.openlocfilehash: ab0cd45334581f6f5c6dd6e86939e018df5de074
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29387731"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurowanie filtrów trasy dla komunikacji równorzędnej firmy Microsoft: Azure portal
> [!div class="op_single_selector"]
> * [Azure portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry trasy są sposób korzystać z podzbioru obsługiwane usługi za pomocą komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiające konfigurowanie i Zarządzanie filtrami trasy dla obwody usługi ExpressRoute.

Usługi Dynamics 365 i usługi Office 365, takich jak Exchange Online, SharePoint Online i Skype dla firm i usług Azure, takich jak magazyn i bazy danych SQL są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodu usługi ExpressRoute, wszystkie prefiksy związane z tych usług są rozgłaszane za pośrednictwem sesji protokołu BGP, które są ustalane. Wartość społeczności protokołu BGP jest dołączony do każdego prefiksu do identyfikowania usługa, która jest dostępna za pośrednictwem prefiks. Aby uzyskać listę wartości społeczności BGP i usług, są one wykonywane na, zobacz [społeczności BGP](expressroute-routing.md#bgp).

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
  - [Utworzyć obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i mieć obwodu włączane przez dostawcą połączenia, aby kontynuować. Obwód usługi expressroute musi być w stanie elastycznie i włączona.
  - [Utwórz komunikacji równorzędnej firmy Microsoft](expressroute-howto-routing-portal-resource-manager.md) Jeśli zarządzasz bezpośrednio za pomocą sesji protokołu BGP. Lub mieć dostawcą połączenia udostępniania Microsoft komunikacji równorzędnej dla obwodu.

-  Należy utworzyć i skonfigurować filtr trasy.
    - Identyfikowanie usług o użycie za pomocą komunikacji równorzędnej firmy Microsoft
    - Określenie listy wartości społeczności BGP powiązanego ze wskazanymi usługami
    - Utwórz regułę w celu zezwalania na liście prefiks zgodne wartości społeczności BGP

-  Należy dołączyć filtru tras z obwodem usługi ExpressRoute.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji upewnij się, że spełniają następujące kryteria:

 - Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.

 - Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi expressroute musi być w stanie elastycznie i włączona.

 - Musisz mieć aktywne komunikacji równorzędnej firmy Microsoft. Postępuj zgodnie z instrukcjami w [tworzenia i modyfikowania konfiguracji komunikacji równorzędnej](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>Krok 1: Pobierz listę prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Pobierz listę wartości społeczności BGP

BGP wartości społeczności związanych z usługami dostępny za pośrednictwem komunikacji równorzędnej firmy Microsoft jest dostępna w [wymagania dotyczące routingu usługi ExpressRoute](expressroute-routing.md) strony.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Tworzenie listy wartości, które ma być używany

Tworzenie listy wartości społeczności protokołu BGP, które mają być używane w filtrze trasy. Na przykład wartość społeczności BGP dla usługi Dynamics 365 jest 12076:5040.

## <a name="filter"></a>Krok 2: Tworzenie filtr tras i regułę filtra

Filtr tras może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta zasada może mieć listy wartości społeczności BGP skojarzonych z nim.

### <a name="1-create-a-route-filter"></a>1. Utwórz filtr trasy
Można utworzyć filtr trasy, wybierając opcję, aby utworzyć nowy zasób. Kliknij przycisk **Utwórz zasób** > **sieci** > **RouteFilter**, jak pokazano na poniższej ilustracji:

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\CreateRouteFilter1.png)

Filtr trasy należy umieścić w grupie zasobów. 

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Utwórz regułę filtru

Można dodawać i aktualizować zasady, wybierając kartę Zarządzaj reguły filtru trasy.

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\ManageRouteFilter.png)


Można wybrać usługi, którą chcesz nawiązać połączenie z listy rozwijanej i zapisać regułę po zakończeniu.

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\AddRouteFilterRule.png)


## <a name="attach"></a>Krok 3: Dołączenie filtru tras z obwodem usługi ExpressRoute

Filtr trasy do obwodu można dołączyć, wybierając przycisk "Dodaj obwodu" i wybierając obwodu usługi expressroute z listy rozwijanej.

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\AddCktToRouteFilter.png)

Jeśli dostawca połączenia służy do konfigurowania komunikacji równorzędnej z odświeżanie obwodu ExpressRoute obwodu z bloku obwodu ExpressRoute przed wybierz przycisk "Dodaj obwodu".

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Typowe zadania

### <a name="getproperties"></a>Aby uzyskać właściwości filtru trasy

Po otwarciu zasobu w portalu można wyświetlić właściwości filtru trasy.

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\ViewRouteFilter.png)


### <a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

Można zaktualizować listy wartości społeczności BGP dołączony do obwodu, wybierając przycisk "Zarządzaj regułę".


![Utwórz filtr trasy](.\media\how-to-routefilter-portal\ManageRouteFilter.png)

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\AddRouteFilterRule.png) 


### <a name="detach"></a>Aby odłączyć filtr tras z obwodu usługi ExpressRoute

Aby odłączyć obwodu z filtru trasy, kliknij prawym przyciskiem myszy w obwodzie i kliknij "Skojarzenie".

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\DetachRouteFilter.png) 


### <a name="delete"></a>Aby usunąć filtr trasy

Możesz usunąć filtr tras, wybierając przycisk Usuń. 

![Utwórz filtr trasy](.\media\how-to-routefilter-portal\DeleteRouteFilter.png) 

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).