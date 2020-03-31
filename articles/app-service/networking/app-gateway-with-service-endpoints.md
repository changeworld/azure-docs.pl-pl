---
title: Integracja bramy aplikacji z punktami końcowymi usługi — usługa Azure App Service | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak brama aplikacji integruje się z usługą Azure App Service zabezpieczona za pomocą punktów końcowych usługi.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980064"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integracja bramy aplikacji z punktami końcowymi usługi
Istnieją trzy odmiany usługi App Service, które wymagają nieco innej konfiguracji integracji z usługą Azure Application Gateway. Odmiany obejmują regularne App Service — znany również jako wielodostępne, wewnętrzny moduł równoważenia obciążenia (ILB) App Service Environment (ASE) i zewnętrznego środowiska ASE. W tym artykule omówimy sposób konfigurowania go za pomocą usługi App Service (wielu dzierżawców) i omówi zagadnienia dotyczące równoważenia obciążenia sieciowego i zewnętrznego programu ASE.

## <a name="integration-with-app-service-multi-tenant"></a>Integracja z usługą App Service (wielodostępna)
Usługa app service (wielodostępna) ma publiczny punkt końcowy skierowany do Internetu. Za pomocą punktów końcowych usługi można zezwolić na ruch tylko z określonej [podsieci](../../virtual-network/virtual-network-service-endpoints-overview.md) w ramach sieci wirtualnej platformy Azure i zablokować wszystko inne. W poniższym scenariuszu użyjemy tej funkcji, aby upewnić się, że wystąpienie usługi App Service może odbierać ruch tylko z określonego wystąpienia bramy aplikacji.

![Integracja bramy aplikacji z usługą App Service](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Istnieją dwie części tej konfiguracji oprócz tworzenia usługi app service i bramy aplikacji. Pierwsza część włącza punkty końcowe usługi w podsieci sieci wirtualnej, w której wdrożono bramę aplikacji. Punkty końcowe usługi zapewni cały ruch sieciowy opuszczający podsieć w kierunku usługi App Service zostaną oznaczone określonym identyfikatorem podsieci. Drugą częścią jest ustawienie ograniczenia dostępu określonej aplikacji sieci web, aby upewnić się, że dozwolony jest tylko ruch oznaczony tym identyfikatorem określonej podsieci. Można go skonfigurować przy użyciu różnych narzędzi w zależności od preferencji.

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Za pomocą witryny Azure Portal wykonaj cztery kroki, aby aprowizować i skonfigurować konfigurację. Jeśli masz istniejące zasoby, możesz pominąć pierwsze kroki.
1. Tworzenie usługi app service przy użyciu jednego z przewodników Szybki start w dokumentacji usługi App Service, na przykład [.Net Core Quickstart](../../app-service/app-service-web-get-started-dotnet.md)
2. Utwórz bramę aplikacji przy użyciu [programu Szybki start portalu](../../application-gateway/quick-create-portal.md), ale pomiń sekcję Dodaj obiekty docelowe wewnętrznej bazy danych.
3. Skonfiguruj [usługę App Service jako wewnętrznej bazy danych w bramie aplikacji](../../application-gateway/configure-web-app-portal.md), ale pomiń sekcję Ogranicz dostęp.
4. Na koniec utwórz [ograniczenie dostępu przy użyciu punktów końcowych usługi](../../app-service/app-service-ip-restrictions.md#service-endpoints).

Teraz można uzyskać dostęp do usługi App Service za pośrednictwem bramy aplikacji, ale jeśli spróbujesz uzyskać dostęp bezpośrednio do usługi App Service, powinien pojawić się błąd HTTP 403 wskazujący, że witryna sieci Web została zatrzymana.

![Integracja bramy aplikacji z usługą App Service](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
[Szablon wdrażania Menedżera zasobów][template-app-gateway-app-service-complete] będzie aprowizować pełny scenariusz. Scenariusz składa się z wystąpienia usługi App Service zablokowane z punktów końcowych usługi i ograniczenia dostępu do odbierania tylko ruch z bramy aplikacji. Szablon zawiera wiele ustawień domyślnych inteligentnych i unikatowe poprawki dodane do nazw zasobów, aby były proste. Aby je zastąpić, musisz sklonować repozytorium lub pobrać szablon i edytować go. 

Aby zastosować szablon, można użyć przycisku Wdrażanie na platformie Azure znajdującego się w opisie szablonu lub użyć odpowiedniego programu PowerShell/CLI.

## <a name="using-azure-command-line-interface"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Przykład [interfejsu wiersza polecenia platformy Azure](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) będzie aprowizować usługę app service zablokowane z punktów końcowych usługi i ograniczenia dostępu do odbierania tylko ruch z bramy aplikacji. Jeśli wystarczy wyizolować ruch do istniejącej usługi App Service z istniejącej bramy aplikacji, następujące polecenie jest wystarczające.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

W konfiguracji domyślnej polecenie zapewni zarówno konfigurację konfiguracji punktu końcowego usługi w podsieci, jak i ograniczenie dostępu w usłudze app service.

## <a name="considerations-for-ilb-ase"></a>Zagadnienia dotyczące ASE ILB
Środowisko ASE równoważenia obciążenia sieciowego nie jest narażony na dostęp do Internetu, a ruch między wystąpieniem a bramą aplikacji jest już izolowany w sieci wirtualnej. Poniższy [przewodnik instrukcje](../environment/integrate-with-application-gateway.md) konfiguruje ase równoważenia obciążenia i integruje go z bramą aplikacji przy użyciu witryny Azure portal. 

Jeśli chcesz upewnić się, że tylko ruch z podsieci bramy aplikacji dociera do ase, można skonfigurować grupę zabezpieczeń sieci (NSG), które mają wpływ na wszystkie aplikacje sieci web w ASE. W przypadku sieciowej sieciowej można określić zakres adresów IP podsieci i opcjonalnie porty (80/443). Upewnij się, że nie zastąpisz [wymaganych reguł sieciowej sieciowej sieciowej dla](../environment/network-info.md#network-security-groups) ase działać poprawnie.

Aby wyizolować ruch do pojedynczej aplikacji sieci web, musisz użyć ograniczeń dostępu opartych na protokãoła ip, ponieważ punkty końcowe usługi nie będą działać dla usługi ASE. Adres IP powinien być prywatnym adresem IP wystąpienia bramy aplikacji.

## <a name="considerations-for-external-ase"></a>Zagadnienia dotyczące zewnętrznego ASE
Zewnętrzna usługa ASE ma publiczny moduł równoważenia obciążenia, taki jak usługa aplikacji z wieloma dzierżawcami. Punkty końcowe usługi nie działają dla ase i dlatego trzeba będzie używać ograniczeń dostępu opartego na adresie IP przy użyciu publicznego adresu IP wystąpienia bramy aplikacji. Aby utworzyć zewnętrzny ase przy użyciu portalu Azure, możesz śledzić ten [szybki start](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Szablon usługi Azure Resource Manager dla pełnego scenariusza"

## <a name="considerations-for-kuduscm-site"></a>Uwagi dotyczące strony kudu/scm
Witryna scm, znana również jako kudu, jest witryną administratora, która istnieje dla każdej aplikacji internetowej. Nie można odwrócić serwera proxy witryny scm i najprawdopodobniej chcesz również zablokować ją na poszczególne adresy IP lub określoną podsieć.

Jeśli chcesz używać tych samych ograniczeń dostępu co lokacja główna, możesz dziedziczyć ustawienia za pomocą następującego polecenia.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Jeśli chcesz ustawić indywidualne ograniczenia dostępu dla witryny scm, możesz dodać ograniczenia dostępu, korzystając z flagi --scm-site, jak pokazano poniżej.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat środowiska usługi app service, zobacz [dokumentację środowiska usługi app service](https://docs.microsoft.com/azure/app-service/environment).

Aby jeszcze bardziej zabezpieczyć aplikację sieci Web, informacje o Zaporze aplikacji sieci Web w bramie aplikacji można znaleźć w [dokumentacji zapory aplikacji sieci Web platformy Azure](../../web-application-firewall/ag/ag-overview.md).