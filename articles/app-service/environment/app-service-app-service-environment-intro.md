---
title: Wprowadzenie do aplikacji usługi v1 środowiska — platformy Azure
description: Więcej informacji na temat funkcji v1 środowiska App Service Environment, która zawiera jednostki skalowania bezpieczne, przyłączone do sieci wirtualnej i dedykowane do uruchamiania wszystkich aplikacji.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 2bb1a9c3922f435b6be78614aacff6e85bf475ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130742"
---
# <a name="introduction-to-app-service-environment-v1"></a>Wprowadzenie do aplikacji App Service Environment w wersji 1

> [!NOTE]
> Ten artykuł dotyczy środowiska App Service Environment w wersji 1.  Istnieje nowsza wersja usługi App Service Environment jest łatwiejszy w obsłudze, która działa na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej na temat nowej wersji rozpoczynać [wprowadzenie do usługi App Service Environment](intro.md).

## <a name="overview"></a>Omówienie

Środowisko usługi App Service jest [Premium] [ PremiumTier] opcji planu usługi [usługi Azure App Service](../overview.md) zapewniający pełni izolowane, dedykowane środowisko do bezpiecznego uruchamiania Aplikacje platformy Azure App Service w dużej skali, w tym aplikacji Web Apps, Mobile Apps oraz API Apps.  

App Service Environment to idealne rozwiązanie w przypadku obciążeń aplikacji wymagających:

* Bardzo dużej skali
* Izolacja i bezpiecznego dostępu do sieci

Klienci mogą tworzyć wiele środowisk App Service Environment w jednym regionie platformy Azure, a także w wielu regionach platformy Azure.  Dzięki temu środowisk usługi App Service idealne rozwiązanie w przypadku warstw aplikacji bez stanu w odniesieniu do wysokiej RPS obciążeń skalowanych w poziomie.

Środowiska usługi App Service są izolowane w celu uruchamiania tylko jednego klienta aplikacji i są zawsze wdrażane w sieci wirtualnej.  Klienci mają precyzyjną kontrolę nad zarówno aplikacji dla ruchu przychodzącego i wychodzącego ruchu sieciowego i aplikacje mogą nawiązywać bezpieczne szybkie połączenia za pośrednictwem wirtualnych sieci lokalnych zasobów firmowych.

Omówienie środowiska usługi App Service Włącz dużej skali oraz bezpieczny dostęp do sieci, zobacz [AzureCon Deep Dive] [ AzureConDeepDive] w środowiskach usługi App Service!

Aby uzyskać szczegółowe na temat skalowania w poziomie, za pomocą wielu środowisk usługi App Service zobacz artykuł na temat sposobu instalacji [zasięgu aplikacji rozproszonej geograficznie][GeodistributedAppFootprint].

Aby dowiedzieć się, jak skonfigurowano architekturę zabezpieczeń przedstawioną w tym filmie AzureCon Deep Dive, zobacz artykuł we wdrażaniu [warstwie architekturę zabezpieczeń](app-service-app-service-environment-layered-security.md) przy użyciu środowisk usługi App Service.

Aplikacje działające w środowiskach usługi App Service może mieć dostęp uzyskiwany przez nadrzędne urządzenia, takich jak zapory aplikacji sieci web (WAF).  Artykuł dotyczący [Konfigurowanie zapory aplikacji sieci Web dla środowiska usługi App Service](app-service-app-service-environment-web-application-firewall.md) opisano w tym scenariuszu.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Zasoby obliczeniowe dedykowane

Wszystkie zasoby obliczeniowe w środowisku usługi App Service są przeznaczone wyłącznie dla jednej subskrypcji, a środowisko App Service Environment można skonfigurować maksymalnie 50 (50) zasobów obliczeniowych do wyłącznego użytku przez jedną aplikację.

Środowisko usługi App Service składa się z puli zasobów obliczeniowych frontonu, jak również pule zasobów obliczeniowych procesów roboczych jednego do trzech.

Pula frontonu zawiera zasoby obliczeniowe są odpowiedzialne za kończenia żądań SSL, jak również automatyczne równoważenie obciążenia żądań aplikacji w środowisku usługi App Service.

Każdej puli procesów roboczych zawiera zasoby obliczeniowe przydzielone do [planów usługi App Service][AppServicePlan], który z kolei zawiera co najmniej jedną aplikację usługi Azure App Service.  Ponieważ w środowisku usługi App Service może być maksymalnie trzy pule innego procesu roboczego, masz swobodę wyboru różnych zasobów obliczeniowych dla każdej puli procesów roboczych.  

Na przykład dzięki temu można utworzyć jedną pulę procesów roboczych przy użyciu mniej wydajnych zasoby obliczeniowe dla planów usługi App Service przeznaczonego dla projektowania lub testowania aplikacji.  Pula procesów roboczych drugi (lub nawet trzeci) użyć bardziej zaawansowanych zasobów obliczeniowych, przeznaczony dla planów usługi App Service uruchamianie aplikacji produkcyjnych.

Aby uzyskać szczegółowe informacje na temat ilości zasobów obliczeniowych dostępnych do pul frontonu i procesu roboczego, zobacz [jak skonfigurować środowisko App Service Environment][HowToConfigureanAppServiceEnvironment].  

Aby uzyskać więcej informacji na temat rozmiarów zasobów obliczeniowych dostępnych obsługiwane w środowisku usługi App Service, należy się zapoznać [App Service — ceny] [ AppServicePricing] strony, a następnie przejrzyj opcje dostępne dla środowisk usługi App Service w Warstwy cenowej Premium.

## <a name="virtual-network-support"></a>Obsługa sieci wirtualnej

Środowisko usługi App Service mogą być tworzone w **albo** z siecią wirtualną usługi Azure Resource Manager **lub** sieć wirtualną modelu wdrażania klasycznego ([więcej informacji o sieciach wirtualnych] [MoreInfoOnVirtualNetworks]).  Ponieważ środowisko App Service zawsze istnieje w sieci wirtualnej, a dokładniej w podsieci sieci wirtualnej, mogą korzystać z funkcji zabezpieczeń sieci wirtualnych do sterowania zarówno dla ruchu przychodzącego i wychodzącą komunikacją sieciową.  

Środowisko usługi App Service może być albo połączenie z Internetem za pomocą publicznego adresu IP lub wewnętrznym, połączonego z przy użyciu adresu wewnętrznego modułu równoważenia obciążenia (ILB) platformy Azure.

Możesz użyć [sieciowe grupy zabezpieczeń] [ NetworkSecurityGroups] do ograniczają komunikację sieciową przychodzącą do podsieci, w której znajduje się środowisko App Service.  Dzięki temu można uruchamiać aplikacje za urządzeniami i usługami, takie jak zapory aplikacji sieci web i dostawcy SaaS.

Aplikacje często muszą również uzyskiwać dostęp do zasobów firmy, takich jak wewnętrzne bazy danych i usługi internetowe.  Typowym podejściem jest zapewnienie tych punktów końcowych dostępne tylko dla ruchu w sieci wewnętrznej przepływu w ramach sieci wirtualnej platformy Azure.  Po środowisko App Service jest przyłączony do tej samej sieci wirtualnej jako wewnętrzny usługi, aplikacje działające w środowisku można uzyskiwać do nich dostęp, nieosiągalny za pośrednictwem punktów końcowych w tym [Site-to-Site] [ SiteToSite] i [Usługi azure ExpressRoute] [ ExpressRoute] połączeń.

Aby uzyskać więcej informacji na temat środowisk usługi App Service z sieciami wirtualnymi i sieciami lokalnymi konsultować się następujące artykuły w [architektury sieci][NetworkArchitectureOverview], [kontrolowanie ruchu przychodzącego Ruch][ControllingInboundTraffic], i [bezpieczne połączenie z Zapleczami][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć pracę przy użyciu środowisk usługi App Service, zobacz [jak do utworzyć środowisko App Service Environment][HowToCreateAnAppServiceEnvironment]

Aby uzyskać omówienie architektury sieci środowiska App Service Environment, zobacz [omówienie architektury sieci] [ NetworkArchitectureOverview] artykułu.

Szczegółowe informacje na temat używania środowiska usługi App Service przy użyciu usługi ExpressRoute, zobacz następujący artykuł na [Expressroute i środowiska usługi App Service][NetworkConfigDetailsForExpressRoute].

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