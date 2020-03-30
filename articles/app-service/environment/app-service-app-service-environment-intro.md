---
title: Wprowadzenie do ASE v1
description: Dowiedz się więcej o funkcjach środowiska usługi app service w wersji 1. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f655b7793bfbb5bbeddfc2f1f8e7bc973dabeb4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687359"
---
# <a name="introduction-to-app-service-environment-v1"></a>Wprowadzenie do środowiska usługi app service w wersji 1

> [!NOTE]
> Ten artykuł dotyczy środowiska usługi app service w wersji 1.  Istnieje nowsza wersja środowiska usługi app service, która jest łatwiejsza w użyciu i działa na bardziej zaawansowanej infrastruktury. Aby dowiedzieć się więcej o nowej wersji, zacznij od [wprowadzenia do środowiska usługi app service](intro.md).

## <a name="overview"></a>Omówienie

Środowisko usługi aplikacji to opcja planu usługi [Premium][PremiumTier] [usługi usługi Azure App Service,](../overview.md) która zapewnia w pełni izolowane i dedykowane środowisko do bezpiecznego uruchamiania aplikacji usługi Azure App Service na dużą skalę, w tym aplikacji sieci Web, aplikacji mobilnych i aplikacji interfejsu API.  

Środowiska usługi app service są idealne dla obciążeń aplikacji wymagających:

* Bardzo wysoka skala
* Izolacja i bezpieczny dostęp do sieci

Klienci mogą tworzyć wiele środowisk usługi app service w jednym regionie platformy Azure, a także w wielu regionach platformy Azure.  Dzięki temu środowiska usługi app service idealnie nadają się do skalowania poziomego warstw aplikacji bez stanu w celu obsługi wysokich obciążeń RPS.

Środowiska usługi app service są izolowane do uruchamiania tylko aplikacji jednego klienta i są zawsze wdrażane w sieci wirtualnej.  Klienci mają szczegółową kontrolę nad ruchem sieciowym aplikacji przychodzących i wychodzących, a aplikacje mogą nawiązywać szybkie bezpieczne połączenia za pośrednictwem sieci wirtualnych z lokalnymi zasobami firmowymi.

Aby uzyskać omówienie sposobu, w jaki środowiska usługi app service umożliwiają wysoki i bezpieczny dostęp do sieci, zobacz [AzureCon Deep Dive][AzureConDeepDive] w środowiskach usługi app service!

Aby uzyskać szczegółowe informacje na temat skalowania poziomego przy użyciu wielu środowisk usługi app service, zobacz artykuł dotyczący konfigurowania [geograficznie rozproszonego śladu aplikacji][GeodistributedAppFootprint].

Aby zobaczyć, jak architektura zabezpieczeń pokazano w AzureCon Deep Dive został skonfigurowany, zobacz artykuł na temat implementacji [architektury zabezpieczeń warstwowych](app-service-app-service-environment-layered-security.md) ze środowiskami usługi App Service.

Aplikacje działające w środowiskach usługi app service mogą mieć dostęp bramki dla urządzeń nadrzędnych, takich jak zapory aplikacji sieci web (WAF).  Artykuł dotyczący [konfigurowania środowiska usługi WAF dla usługi app service](app-service-app-service-environment-web-application-firewall.md) obejmuje ten scenariusz.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedykowane zasoby obliczeniowe

Wszystkie zasoby obliczeniowe w środowisku usługi app service są przeznaczone wyłącznie do pojedynczej subskrypcji, a środowisko usługi app service można skonfigurować z maksymalnie pięćdziesiąt (50) zasobów obliczeniowych do wyłącznego użytku przez jedną aplikację.

Środowisko usługi aplikacji składa się z frontonu puli zasobów obliczeniowych, a także od jednej do trzech pul zasobów obliczeniowych procesu roboczego.

Pula front-end zawiera zasoby obliczeniowe odpowiedzialne za zakończenie SSL, a także automatyczne równoważenie obciążenia żądań aplikacji w środowisku usługi aplikacji.

Każda pula procesów roboczych zawiera zasoby obliczeniowe przydzielone do [planów usługi app service,][AppServicePlan]które z kolei zawierają jedną lub więcej aplikacji usługi Azure App Service.  Ponieważ w środowisku usługi app service mogą istnieć maksymalnie trzy różne pule procesów, można wybrać różne zasoby obliczeniowe dla każdej puli procesów roboczych.  

Na przykład pozwala to utworzyć jedną pulę procesów roboczych z mniej zaawansowanymi zasobami obliczeniowymi dla planów usługi app service przeznaczonych do tworzenia lub testowania aplikacji.  Druga (lub nawet trzecia) pula procesów procesowych może używać bardziej zaawansowanych zasobów obliczeniowych przeznaczonych dla planów usługi app service z uruchomionymi aplikacjami produkcyjnymi.

Aby uzyskać więcej informacji na temat ilości zasobów obliczeniowych dostępnych dla pule front-end i pracowników, zobacz [Jak skonfigurować środowisko usługi app service][HowToConfigureanAppServiceEnvironment].  

Aby uzyskać szczegółowe informacje na temat dostępnych rozmiarów zasobów obliczeniowych obsługiwanych w środowisku usługi app service, odwiedź stronę [Cennik usługi app service][AppServicePricing] i zapoznaj się z dostępnymi opcjami dla środowisk usługi app service w warstwie cenowej Premium.

## <a name="virtual-network-support"></a>Obsługa sieci wirtualnej

Środowisko usługi aplikacji można utworzyć **w** sieci wirtualnej usługi Azure Resource Manager **lub** w klasycznej sieci wirtualnej modelu wdrażania[(więcej informacji o sieciach wirtualnych).][MoreInfoOnVirtualNetworks]  Ponieważ środowisko usługi aplikacji zawsze istnieje w sieci wirtualnej, a dokładniej w podsieci sieci wirtualnej, można korzystać z funkcji zabezpieczeń sieci wirtualnych do kontrolowania zarówno przychodzącej, jak i wychodzącej komunikacji sieciowej.  

Środowisko usługi aplikacji może mieć dostęp do Internetu z publicznym adresem IP lub z widokiem wewnętrznym tylko z adresem równoważenia obciążenia wewnętrznego platformy Azure (ILB).

Za pomocą [sieciowych grup zabezpieczeń][NetworkSecurityGroups] można ograniczyć przychodzące komunikaty sieciowe do podsieci, w której znajduje się środowisko usługi app service.  Dzięki temu można uruchamiać aplikacje za urządzeniami i usługami nadrzędnymi, takimi jak zapory aplikacji sieci web i dostawcy SaaS sieci.

Aplikacje często muszą również uzyskiwać dostęp do zasobów firmy, takich jak wewnętrzne bazy danych i usługi internetowe.  Typowym podejściem jest udostępnienie tych punktów końcowych tylko do wewnętrznego ruchu sieciowego przepływającego w sieci wirtualnej platformy Azure.  Gdy środowisko usługi aplikacji jest przyłączone do tej samej sieci wirtualnej co usługi wewnętrzne, aplikacje działające w środowisku mogą uzyskiwać do nich dostęp, w tym punkty końcowe osiągalne za pośrednictwem połączeń [lokacja-lokacja][SiteToSite] i [usługi Azure ExpressRoute.][ExpressRoute]

Aby uzyskać więcej informacji na temat sposobu pracy środowisk usługi app service z sieciami wirtualnymi i sieciami lokalnymi, zapoznaj się z poniższymi artykułami dotyczącymi [architektury sieci][NetworkArchitectureOverview], kontrolowania [ruchu przychodzącego][ControllingInboundTraffic]i [bezpiecznego łączenia się z zapleczem.][SecurelyConnectingToBackends] 

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć korzystanie ze środowisk usługi App Service, zobacz [Jak utworzyć środowisko usługi aplikacji][HowToCreateAnAppServiceEnvironment]

Aby zapoznać się z omówieniem architektury sieci środowiska usługi app service, zobacz [omówienie architektury sieci.][NetworkArchitectureOverview]

Aby uzyskać szczegółowe informacje na temat korzystania ze środowiska usługi App Service w usłudze ExpressRoute, zobacz następujący artykuł dotyczący [środowiska tras ekspresowych i aplikacji][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: https://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->