---
title: 'Konfigurowanie filtrów trasy dla komunikacji równorzędnej platformy Azure ExpressRoute Microsoft: interfejs wiersza polecenia | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla Peering firmy Microsoft przy użyciu wiersza polecenia platformy Azure
documentationcenter: na
services: expressroute
author: anzaman
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anzaman
ms.openlocfilehash: a85a68393f3dc946db651791de9efff0694f9989
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850819"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurowanie filtrów trasy dla komunikacji równorzędnej firmy Microsoft: wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry trasy są sposób korzystać z podzbioru obsługiwane usługi za pomocą komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiające konfigurowanie i Zarządzanie filtrami trasy dla obwody usługi ExpressRoute.

Usługi Dynamics 365 i usługi Office 365, takich jak Exchange Online, SharePoint Online i Skype dla firm, są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodu usługi ExpressRoute, wszystkie prefiksy związane z tych usług są rozgłaszane za pośrednictwem sesji protokołu BGP, które są ustalane. Wartość społeczności protokołu BGP jest dołączony do każdego prefiksu do identyfikowania usługa, która jest dostępna za pośrednictwem prefiks. Aby uzyskać listę wartości społeczności BGP i usług, są one wykonywane na, zobacz [społeczności BGP](expressroute-routing.md#bgp).

Jeśli potrzebujesz łączności z usługami wszystkich dużej liczby prefiksów są rozgłaszane za pomocą protokołu BGP. To znacznie zwiększa rozmiar tabel tras obsługiwanego przez routery w sieci. Jeśli planujesz używać tylko podzestaw usługami oferowanymi w ramach komunikacji równorzędnej firmy Microsoft, można zmniejszyć rozmiar tabel tras na dwa sposoby. Możesz:

* Odfiltrować niechciane prefiksy, stosując filtry tras na Wspólnot protokołu BGP. To jest standardową praktyką sieci i jest powszechnie używany w wielu sieciach.

* Zdefiniuj filtry tras i zastosować je do obwodu usługi ExpressRoute. Filtr tras jest nowy zasób, który umożliwia wybranie na liście usług, której planujesz używać za pomocą komunikacji równorzędnej firmy Microsoft. Usługi routerami wysłać tylko listę prefiksów, które należą do usługi określone w filtrze trasy.

### <a name="about"></a>Informacje o filtrach trasy

W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft na obwodu ExpressRoute, routery brzegowe Microsoft ustanowić parę sesje BGP z routerami krawędzi (należy do Ciebie lub dostawcą połączenia). Nie trasy są anonsowane do sieci. Aby włączyć anonsów tras do sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które chcesz korzystać za pośrednictwem komunikacji równorzędnej firmy Microsoft obwodu usługi ExpressRoute. Jest zasadniczo białą listę wszystkich wartości społeczności protokołu BGP. Gdy zasób filtru trasa jest zdefiniowana i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy mapowane na wartości społeczności BGP są rozgłaszane do sieci.

Aby można było dołączyć filtry tras z usługami Office 365 na nich, musi mieć autoryzację do korzystania z usług Office 365 za pomocą usługi ExpressRoute. Jeśli nie masz uprawnień do korzystania z usług Office 365 za pomocą usługi ExpressRoute, operacja dołączyć filtry tras kończy się niepowodzeniem. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [Azure ExpressRoute dla usługi Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Łączności z usługami Dynamics 365 nie wymaga żadnych poprzednich autoryzacji.

> [!IMPORTANT]
> Z obwody usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 komunikacji równorzędnej firmy Microsoft ma wszystkie prefiksy usługi anonsowane przez firmę Microsoft, zaglądanie, nawet jeśli nie zdefiniowano filtrów trasy. Z obwody usługi ExpressRoute, które są skonfigurowane na lub po 1 sierpnia 2017 komunikacji równorzędnej firmy Microsoft nie będzie miał wszystkie prefiksy anonsowane do momentu filtr tras jest dołączony do obwodu.
> 
> 

### <a name="workflow"></a>Przepływ pracy

Aby móc nawiązywać połączeń z usługami za pomocą komunikacji równorzędnej firmy Microsoft, wykonaj następujące kroki konfiguracji:

* Musisz mieć aktywne obwodu usługi ExpressRoute z Microsoft równorzędna elastycznie. Wykonanie tych zadań, można użyć poniższych instrukcji:
  * [Utworzyć obwodu usługi ExpressRoute](howto-circuit-cli.md) i mieć obwodu włączane przez dostawcą połączenia, aby kontynuować. Obwód usługi expressroute musi być w stanie elastycznie i włączona.
  * [Utwórz komunikacji równorzędnej firmy Microsoft](howto-routing-cli.md) Jeśli zarządzasz bezpośrednio za pomocą sesji protokołu BGP. Lub mieć dostawcą połączenia udostępniania Microsoft komunikacji równorzędnej dla obwodu.

* Należy utworzyć i skonfigurować filtr trasy.
  * Identyfikowanie usług o użycie za pomocą komunikacji równorzędnej firmy Microsoft
  * Określenie listy wartości społeczności BGP powiązanego ze wskazanymi usługami
  * Utwórz regułę w celu zezwalania na liście prefiks zgodne wartości społeczności BGP

* Należy dołączyć filtru tras z obwodem usługi ExpressRoute.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure 2.0](/cli/azure/get-started-with-azure-cli).

* Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.

* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](howto-circuit-cli.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi expressroute musi być w stanie elastycznie i włączona.

* Musisz mieć aktywne komunikacji równorzędnej firmy Microsoft. Postępuj zgodnie z instrukcjami w [tworzenia i modyfikowania konfiguracji komunikacji równorzędnej](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wyboru subskrypcji

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Użyj poniższych przykładach umożliwiające nawiązywanie połączeń:

```azurecli
az login
```

Sprawdź subskrypcje dostępne na koncie.

```azurecli
az account list
```

Wybierz subskrypcję, dla której chcesz utworzyć obwodu usługi ExpressRoute.

```azurecli
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Krok 1: Pobierz listę prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Pobierz listę wartości społeczności BGP

Użyj następującego polecenia cmdlet można pobrać listy wartości społeczności BGP związanych z usługami dostępny za pośrednictwem komunikacji równorzędnej firmy Microsoft oraz listę prefiksów skojarzonych z nimi:

```azurecli
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Tworzenie listy wartości, które ma być używany

Tworzenie listy wartości społeczności protokołu BGP, które mają być używane w filtrze trasy. Na przykład wartość społeczności BGP dla usługi Dynamics 365 jest 12076:5040.

## <a name="filter"></a>Krok 2: Tworzenie filtr tras i regułę filtra

Filtr tras może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta zasada może mieć listy wartości społeczności BGP skojarzonych z nim.

### <a name="1-create-a-route-filter"></a>1. Utwórz filtr trasy

Najpierw należy utworzyć filtr trasy. Polecenie utworzyć filtr trasy sieciowej az tylko tworzy zasób filtr trasy. Po utworzeniu zasobu, należy utworzyć regułę i dołącz je do obiektu filtra trasy. Uruchom następujące polecenie, aby utworzyć zasób filtr trasy:

```azurecli
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Utwórz regułę filtru

Uruchom następujące polecenie, aby utworzyć nową regułę:
 
```azurecli
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Krok 3: Dołączenie filtru tras z obwodem usługi ExpressRoute

Uruchom następujące polecenie, aby dołączyć filtr trasy do obwodu ExpressRoute:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Typowe zadania

### <a name="getproperties"></a>Aby uzyskać właściwości filtru trasy

Aby uzyskać właściwości filtru trasy, użyj następującego polecenia:

```azurecli
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

Jeśli filtr trasy jest już dołączony do obwodu, aktualizacji do listy społeczności BGP automatycznie rozpropagowane prefiksu anonsu zmian za pomocą ustanowionych sesji BGP. Można zaktualizować listy społeczności BGP filtru trasy przy użyciu następującego polecenia:

```azurecli
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Aby odłączyć filtr tras z obwodu usługi ExpressRoute

Gdy filtr tras jest odłączony od obwodu ExpressRoute, prefiksy nie są rozgłaszane za pomocą sesji protokołu BGP. Można odłączyć filtr tras z obwodu usługi ExpressRoute, za pomocą następującego polecenia:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Aby usunąć filtr trasy

Filtr tras można usuwać tylko, jeśli nie jest dołączony do dowolnej obwodu. Upewnij się, że filtr tras nie jest podłączony do żadnych obwodu przed próbą usunięcia. Można usunąć filtr tras za pomocą następującego polecenia:

```azurecli
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
