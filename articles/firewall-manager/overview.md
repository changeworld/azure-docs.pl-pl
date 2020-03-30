---
title: Co to jest usługa Azure Firewall Manager Preview?
description: Dowiedz się więcej o funkcjach Usługi Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/13/2020
ms.author: victorh
ms.openlocfilehash: 149782f627d586e927c828506a7d4f1b5437b987
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79366278"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Co to jest usługa Azure Firewall Manager Preview?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Usługa Azure Firewall Manager Preview to usługa zarządzania zabezpieczeniami, która zapewnia centralne zasady zabezpieczeń i zarządzanie trasami dla obszarów zabezpieczeń opartych na chmurze. 

Menedżer zapór może zapewnić zarządzanie zabezpieczeniami dla dwóch typów architektury sieci:

- **zabezpieczony koncentrator wirtualny**

   [Usługa Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) to zasób zarządzany przez firmę Microsoft, który umożliwia łatwe tworzenie architektur koncentratora i szprychy. Gdy z takim koncentratorem są skojarzone zasady zabezpieczeń i routingu, jest on określany jako *[zabezpieczony koncentrator wirtualny](secured-virtual-hub.md)*. 
- **sieć wirtualna koncentratora**

   Jest to standardowa sieć wirtualna platformy Azure, którą tworzysz i zarządzasz samodzielnie. Gdy zasady zabezpieczeń są skojarzone z takim koncentratorem, jest on określany jako *sieć wirtualna koncentratora*. W tej chwili obsługiwane są tylko zasady zapory platformy Azure. Można równorzędne sieci wirtualne, które zawierają serwery obciążenia i usługi. Można również zarządzać zapory w autonomicznych sieciach wirtualnych, które nie są równorzędne z żadnym szprychy.

Aby uzyskać szczegółowe porównanie *zabezpieczonych* architektur sieci wirtualnych i *centrów,* zobacz [Jakie są opcje architektury usługi Azure Firewall Manager?](vhubs-and-vnets.md).

![firewall-manager](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Funkcje Usługi Azure Firewall Manager w wersji preview

Usługa Azure Firewall Manager Preview oferuje następujące funkcje:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Wdrożenie i konfiguracja centralnej zapory platformy Azure

Można centralnie wdrażać i konfigurować wiele wystąpień Zapory platformy Azure, które obejmują różne regiony i subskrypcje platformy Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Zasady hierarchiczne (globalne i lokalne)

Za pomocą usługi Azure Firewall Manager Preview można centralnie zarządzać zasadami Zapory platformy Azure w wielu zabezpieczonych centrach wirtualnych. Centralne zespoły IT mogą tworzyć globalne zasady zapory, aby wymusić zasady dotyczące całej zapory w całej organizacji w różnych zespołach. Lokalnie autorstwa zasady zapory umożliwiają devops modelu samoobsługi dla lepszej elastyczności.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Zintegrowana z zabezpieczeniami innych firm jako usługą w celu uzyskania zaawansowanych zabezpieczeń

Oprócz zapory platformy Azure można zintegrować dostawców zabezpieczeń innych firm jako usługi (SECaaS), aby zapewnić dodatkową ochronę sieci wirtualnej i połączeń internetowych w oddziale.

Ta funkcja jest dostępna tylko w przypadku zabezpieczonych wdrożeń koncentratora wirtualnego.

- Filtrowanie ruchu sieci wirtualnej do Internetu (V2I)

   - Filtruj wychodzący ruch sieci wirtualnej z preferowanym dostawcą zabezpieczeń innej firmy.
   - Korzystaj z zaawansowanej ochrony internetu z uwzględnieniem użytkownika dla obciążeń w chmurze działających na platformie Azure.

- Filtrowanie ruchu między gałęziami a Internetem (B2I)

   Wykorzystaj łączność platformy Azure i dystrybucję globalną, aby łatwo dodać filtrowanie innych firm w scenariuszach branżowych do Internetu.

Aby uzyskać więcej informacji na temat zaufanych dostawców zabezpieczeń, zobacz [Co to są zaufani partnerzy zabezpieczeń usługi Azure Firewall Manager (wersja zapoznawcza)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Scentralizowane zarządzanie trasami

Łatwe kierowanie ruchu do zabezpieczonego koncentratora w celu filtrowania i rejestrowania bez konieczności ręcznego konfigurowania tras zdefiniowanych przez użytkownika (UDR) w sieciach wirtualnych szprych. 

Ta funkcja jest dostępna tylko w przypadku zabezpieczonych wdrożeń koncentratora wirtualnego.

Można użyć dostawców innych firm do filtrowania ruchu usługi Branch to Internet (B2I), obok usługi Azure Firewall for Branch to VNet (B2V), VNet to VNet (V2V) i V2I. Można również użyć dostawców innych firm do filtrowania ruchu w wersji 2I, o ile zapora azure nie jest wymagana dla B2V lub V2V. 

## <a name="region-availability"></a>Dostępność w danym regionie

Zasady zapory azure mogą być używane w różnych regionach. Na przykład można utworzyć zasady w zachodnie stany USA i używać jej we wschodnich stanach USA. 

## <a name="known-issues"></a>Znane problemy

Usługa Azure Firewall Manager Preview ma następujące znane problemy:

|Problem  |Opis  |Środki zaradcze  |
|---------|---------|---------|
|Ograniczenia filtrowania innych firm.|Filtrowanie ruchu w wersji 2I z dostawcami zewnętrznymi nie jest obsługiwane przez zaporę B2V i V2V platformy Azure.|Badanie|
|Podział ruchu nie jest obecnie obsługiwany.|Podział ruchu usługi Office 365 i usługi Azure Public PaaS nie jest obecnie obsługiwany. W związku z tym wybranie zewnętrznego dostawcy dla systemu V2I lub B2I również wysyła wszystkie usługi Azure Public PaaS i usługi Office 365 ruchu za pośrednictwem usługi partnera.|Badanie podziału ruchu w centrum.
|Jeden zabezpieczony koncentrator wirtualny na region.|Nie można mieć więcej niż jednego zabezpieczonego centrum wirtualnego na region.|Utwórz wiele wirtualnych sieci WAN w regionie.|
|Podstawowe zasady muszą znajdować się w tym samym regionie co zasady lokalne.|Utwórz wszystkie lokalne zasady w tym samym regionie co zasady podstawowe. Nadal można zastosować zasady, które zostały utworzone w jednym regionie w zabezpieczonym centrum z innego regionu.|Badanie|
|Komunikacja między koncentratorami nie działa z zabezpieczonym koncentratorem wirtualnym|Zabezpieczona komunikacja z centrum wirtualnym do zabezpieczonego centrum wirtualnego nie jest jeszcze obsługiwana.|Badanie|
|Wszystkie zabezpieczone koncentratory wirtualne współużytkowane w tej samej wirtualnej sieci WAN muszą znajdować się w tej samej grupie zasobów.|To zachowanie jest obecnie wyrównane z wirtualnymi centrami sieci WAN.|Utwórz wiele wirtualnych sieci WAN, aby umożliwić tworzenie zabezpieczonych centrów wirtualnych w różnych grupach zasobów.|
|Grupy IP nie są obsługiwane w zasadach zapory.|Grupy ADRESÓW IP są w publicznej wersji zapoznawczej i obecnie są obsługiwane tylko z tradycyjnymi regułami zapory.|Poprawka w toku.
|Subskrypcje dostawcy rozwiązań w chmurze (CSP) nie są obsługiwane.|Obecnie [subskrypcje usługi CSP](https://azure.microsoft.com/offers/ms-azr-0145p/) nie są obsługiwane.|Badanie

## <a name="next-steps"></a>Następne kroki

- Przegląd [wdrażania usługi Azure Firewall Manager Preview](deployment-overview.md)
- Dowiedz się więcej o [zabezpieczonych centrach wirtualnych](secured-virtual-hub.md).