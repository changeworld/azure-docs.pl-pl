---
title: Korzystanie z prywatnych punktów końcowych dla konfiguracji aplikacji platformy Azure
description: Zabezpiecz swój sklep konfiguracji aplikacji przy użyciu prywatnych punktów końcowych
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366772"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Korzystanie z prywatnych punktów końcowych dla konfiguracji aplikacji platformy Azure

Za pomocą [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) dla usługi Azure App Configuration można zezwolić klientom w sieci wirtualnej na bezpieczny dostęp do danych za pośrednictwem łącza [prywatnego.](../private-link/private-link-overview.md) Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla magazynu konfiguracji aplikacji. Ruch sieciowy między klientami w sieci wirtualnej i magazynu konfiguracji aplikacji przechodzi przez sieć wirtualną przy użyciu łącza prywatnego w sieci szkieletowej firmy Microsoft, eliminując narażenie na publiczny Internet.

Korzystanie z prywatnych punktów końcowych w magazynie konfiguracji aplikacji umożliwia:
- Zabezpiecz szczegóły konfiguracji aplikacji, konfigurując zaporę, aby zablokować wszystkie połączenia z konfiguracją aplikacji w publicznym punkcie końcowym.
- Zwiększ bezpieczeństwo sieci wirtualnej, zapewniając, że dane nie unikną sieci wirtualnej.
- Bezpiecznie połącz się ze sklepem konfiguracji aplikacji z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [ExpressRoutes](../expressroute/expressroute-locations.md) za pomocą prywatnej komunikacji równorzędnej.

> [!NOTE]
> Usługa Azure App Configuration oferuje korzystanie z prywatnych punktów końcowych jako publicznej wersji zapoznawczej. Publiczne oferty w wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną premierą.  Publiczne funkcje i usługi w wersji zapoznawczej nie są przeznaczone do użytku w wersji produkcyjnej.

## <a name="conceptual-overview"></a>Omówienie pojęć

Prywatny punkt końcowy to specjalny interfejs sieciowy dla usługi platformy Azure w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) (VNet). Podczas tworzenia prywatnego punktu końcowego dla sklepu app config zapewnia bezpieczną łączność między klientami w sieci wirtualnej i magazynu konfiguracji. Do prywatnego punktu końcowego jest przypisywany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a magazynem konfiguracji używa bezpiecznego łącza prywatnego.

Aplikacje w sieci wirtualnej mogą łączyć się z magazynem konfiguracji za pośrednictwem prywatnego punktu końcowego **przy użyciu tych samych ciągów połączeń i mechanizmów autoryzacji, których użyłyby w przeciwnym razie.** Prywatne punkty końcowe mogą być używane ze wszystkimi protokołami obsługiwanymi przez magazyn konfiguracji aplikacji.

Konfiguracja aplikacji nie obsługuje punktów końcowych usługi, prywatne punkty końcowe można tworzyć w podsieciach [korzystających](../virtual-network/virtual-network-service-endpoints-overview.md)z punktów końcowych usługi . Klienci w podsieci można bezpiecznie połączyć się z magazynu konfiguracji aplikacji przy użyciu prywatnego punktu końcowego podczas korzystania z punktów końcowych usługi, aby uzyskać dostęp do innych.  

Podczas tworzenia prywatnego punktu końcowego dla usługi w sieci wirtualnej żądanie zgody jest wysyłane do zatwierdzenia do właściciela konta usługi. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem konta, to żądanie zgody zostanie automatycznie zatwierdzone.

Właściciele kont usług mogą zarządzać żądaniami zgody `Private Endpoints` i prywatnymi punktami końcowymi za pomocą karty magazynu konfiguracji w [witrynie Azure portal](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Prywatne punkty końcowe konfiguracji aplikacji 

Podczas tworzenia prywatnego punktu końcowego, należy określić magazyn konfiguracji aplikacji, z którym łączy. Jeśli masz wiele wystąpień konfiguracji aplikacji w ramach konta, potrzebujesz oddzielnego prywatnego punktu końcowego dla każdego sklepu.

### <a name="connecting-to-private-endpoints"></a>Łączenie się z prywatnymi punktami końcowymi

Platforma Azure opiera się na rozpoznawaniu dns do kierowania połączeń z sieci wirtualnej do magazynu konfiguracji za pośrednictwem łącza prywatnego. Ciągi połączeń można szybko znaleźć w portalu Azure, wybierając sklep konfiguracji aplikacji, a następnie wybierając **pozycję Ustawienia** > **kluczy dostępu**.  

> [!IMPORTANT]
> Użyj tego samego ciągu połączenia, aby połączyć się z magazynem konfiguracji aplikacji przy użyciu prywatnych punktów końcowych, jak można użyć dla publicznego punktu końcowego. Nie łącz się z kontem magazynu przy użyciu adresu `privatelink` URL poddomeny.

## <a name="dns-changes-for-private-endpoints"></a>Zmiany DNS dla prywatnych punktów końcowych

Podczas tworzenia prywatnego punktu końcowego rekord zasobu CNAME DNS dla magazynu konfiguracji jest aktualizowany do `privatelink`aliasu w poddomenie z prefiksem . Platforma Azure tworzy również [prywatną strefę DNS](../dns/private-dns-overview.md) odpowiadającą `privatelink` poddomenie z rekordami zasobów DNS A dla prywatnych punktów końcowych.

Po rozpoznaniu adresu URL punktu końcowego spoza sieci wirtualnej, jest rozpoznawany do publicznego punktu końcowego magazynu. Po rozwiązaniu z poziomu sieci wirtualnej obsługującej prywatny punkt końcowy adres URL punktu końcowego jest rozpoznawany dla prywatnego punktu końcowego.

Można kontrolować dostęp dla klientów spoza sieci wirtualnej za pośrednictwem publicznego punktu końcowego przy użyciu usługi Zapora azure.

Takie podejście umożliwia dostęp do magazynu **przy użyciu tego samego ciągu połączenia** dla klientów w sieci wirtualnej obsługujących prywatnych punktów końcowych, a także klientów spoza sieci wirtualnej.

Jeśli używasz niestandardowego serwera DNS w sieci, klienci muszą mieć możliwość rozpoznania w pełni kwalifikowanej nazwy domeny (FQDN) dla punktu końcowego usługi do adresu IP prywatnego punktu końcowego. Skonfiguruj serwer DNS, aby delegować poddomenę łącza prywatnego do prywatnej `AppConfigInstanceA.privatelink.azconfig.io` strefy DNS sieci wirtualnej, lub skonfiguruj rekordy A przy połącz adres IP prywatnego punktu końcowego.

> [!TIP]
> W przypadku korzystania z niestandardowego lub lokalnego serwera DNS należy skonfigurować serwer `privatelink` DNS w taki sposób, aby rozpoznał nazwę sklepu w poddomenie na prywatny adres IP punktu końcowego. Można to zrobić, delegując `privatelink` poddomenę do prywatnej strefy DNS sieci wirtualnej lub konfigurując strefę DNS na serwerze DNS i dodając rekordy DNS A.

## <a name="pricing"></a>Cennik

Włączanie prywatnych punktów końcowych wymaga magazynu konfiguracji aplikacji [warstwy standardowej.](https://azure.microsoft.com/pricing/details/app-configuration/)  Aby dowiedzieć się więcej o szczegółach cenowania łączy prywatnych, zobacz [Cennik łącza prywatnego platformy Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tworzeniu prywatnego punktu końcowego dla sklepu konfiguracji aplikacji, zapoznaj się z następującymi artykułami:

- [Tworzenie prywatnego punktu końcowego przy użyciu Centrum łączy prywatnych w witrynie Azure portal](../private-link/create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](../private-link/create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu programu Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Dowiedz się, jak skonfigurować serwer DNS z prywatnymi punktami końcowymi:

- [Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Konfiguracja DNS dla prywatnych punktów końcowych](/azure/private-link/private-endpoint-overview#dns-configuration)
