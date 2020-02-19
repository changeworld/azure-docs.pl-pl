---
title: Co to jest wersja zapoznawcza usługi Azure firewall Manager?
description: Informacje o funkcjach Menedżera zapory platformy Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 0ba2ce30cee3ff7e3a9f71b4f1b0928fa84e775d
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443157"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Co to jest wersja zapoznawcza usługi Azure firewall Manager?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Menedżer zapory Azure w wersji zapoznawczej to usługa zarządzania zabezpieczeniami, która zapewnia centralne zasady zabezpieczeń i Zarządzanie trasami dla obwodów zabezpieczeń opartych na chmurze. 

Menedżer zapory może zapewnić zarządzanie zabezpieczeniami dla dwóch typów architektury sieci:

- **bezpieczny koncentrator wirtualny**

   [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) to zasób zarządzany przez firmę Microsoft, który umożliwia łatwe tworzenie architektur Hub i szprych. Gdy zasady zabezpieczeń i routingu są skojarzone z takim centrum, jest ono nazywane *[bezpiecznym koncentratorem wirtualnym](secured-virtual-hub.md)* . 
- **Sieć wirtualna centrum**

   Jest to standardowa Sieć wirtualna platformy Azure, która jest tworzona i zarządzana. Gdy zasady zabezpieczeń są skojarzone z takim centrum, jest ono określane jako *centralny sieci wirtualnej*. W tej chwili obsługiwane są tylko zasady zapory platformy Azure. Można połączyć sieci wirtualne równorzędne, które zawierają serwery obciążeń i usługi. Można także zarządzać zaporami w autonomicznych sieciach wirtualnych, które nie są połączone z żadną szprychą.

Aby zapoznać się ze szczegółowym porównaniem bezpiecznych architektur wirtualnych *centrów wirtualnych* i *koncentratorów* , zobacz [co to są opcje architektury usługi Azure firewall Manager?](vhubs-and-vnets.md).

![Zapora — Menedżer](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Funkcje w wersji zapoznawczej Menedżera zapory platformy Azure

Menedżer zapory Azure w wersji zapoznawczej oferuje następujące funkcje:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Wdrażanie i konfiguracja centralnej zapory platformy Azure

Można centralnie wdrażać i konfigurować wiele wystąpień zapory platformy Azure, które obejmują różne regiony i subskrypcje platformy Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Zasady hierarchiczne (globalne i lokalne)

Za pomocą narzędzia Menedżer zapory Azure w wersji zapoznawczej można centralnie zarządzać zasadami zapory platformy Azure w wielu zabezpieczonych koncentratorach wirtualnych. Centralne zespoły IT mogą tworzyć globalne zasady zapory w celu wymuszenia zasad zapory dla całej organizacji w zespołach. Lokalne zasady zapory umożliwiają DevOps samoobsługowego modelu w celu zwiększenia elastyczności.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integracja z zabezpieczeniami jako usługą innych firm w celu zapewnienia bezpieczeństwa zaawansowanego

Oprócz zapory platformy Azure można zintegrować dostawców usługi Security AS (SECaaS) innych firm w celu zapewnienia dodatkowej ochrony sieci dla sieci wirtualnej i połączeń internetowych oddziałów.

Ta funkcja jest dostępna tylko w przypadku bezpiecznych wdrożeń koncentratora wirtualnego.

- Filtrowanie ruchu między sieciami wirtualnymi (V2I)

   - Filtrowanie ruchu wychodzącego z sieci wirtualnej za pomocą preferowanego dostawcy zabezpieczeń innej firmy.
   - Korzystaj z zaawansowanej, opartej na użytkownikach internetowej ochrony obciążeń w chmurze działających na platformie Azure.

- Filtrowanie ruchu z gałęzi do Internetu (B2I)

   Korzystaj z łączności z platformą Azure i globalnej dystrybucji, aby łatwo dodawać filtrowanie oddziałów dla gałęzi do scenariuszy internetowych.

Aby uzyskać więcej informacji na temat zaufanych dostawców zabezpieczeń, zobacz [co to są partnerzy zaufanych zabezpieczeń programu Azure firewall Manager (wersja zapoznawcza)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Scentralizowane zarządzanie trasami

Łatwo kieruj ruch do bezpiecznego centrum w celu filtrowania i rejestrowania bez konieczności ręcznego konfigurowania tras zdefiniowanych przez użytkownika (UDR) w sieciach wirtualnych szprych. 

Ta funkcja jest dostępna tylko w przypadku bezpiecznych wdrożeń koncentratora wirtualnego.

Dostawców innych firm można używać do filtrowania ruchu z Internetu (B2I), obok siebie przy użyciu zapory platformy Azure dla gałęzi do sieci wirtualnej (B2V), sieci wirtualnej z siecią wirtualną (V2V) i Sieć wirtualna do Internetu (V2I). Dostawców innych firm można także używać do filtrowania ruchu V2I, o ile nie jest wymagana Zapora platformy Azure dla B2V lub V2V. 

## <a name="region-availability"></a>Dostępność w danym regionie

Zasady zapory platformy Azure mogą być używane w różnych regionach. Można na przykład utworzyć zasady w regionie zachodnie stany USA i używać ich w regionach Wschodnie stany USA. 

## <a name="known-issues"></a>Znane problemy

Wersja zapoznawcza Menedżera zapory platformy Azure obejmuje następujące znane problemy:

|Problem  |Opis  |Środki zaradcze  |
|---------|---------|---------|
|Ograniczenia filtrowania innych firm|Filtrowanie ruchu V2I z dostawcami innych firm nie jest obsługiwane przez usługę Azure firewall B2V i V2V.|Obecnie trwa badanie.|
|Dzielenie ruchu nie jest obecnie obsługiwane|Dzielenie pakietów Office 365 i Public PaaS nie jest obecnie obsługiwane. W związku z tym wybranie dostawcy innej firmy dla usługi V2I lub B2I spowoduje również wysłanie wszystkich usług Azure Public PaaS i Office 365 przez usługę partnera.|Obecnie badanie podziału ruchu w centrum.
|Jeden bezpieczny koncentrator wirtualny na region|Nie można mieć więcej niż jednego zabezpieczonego koncentratora wirtualnego na region|Utwórz wiele wirtualnych sieci WAN w regionie.|
|Zasady podstawowe muszą znajdować się w tym samym regionie co zasady lokalne|Utwórz wszystkie zasady lokalne w tym samym regionie co zasady podstawowe. Można nadal stosować zasady, które zostały utworzone w jednym regionie w zabezpieczonym centrum z innego regionu.|Obecnie trwa badanie.|
|Komunikacja między centrami nie działa z bezpiecznym koncentratorem wirtualnym|Zabezpieczona wirtualna koncentrator do bezpiecznej komunikacji z koncentratorem wirtualnym nie jest jeszcze obsługiwana.|Obecnie trwa badanie.|
|Wszystkie zabezpieczone centra wirtualne współużytkujące tę samą wirtualną sieć WAN muszą znajdować się w tej samej grupie zasobów.|To zachowanie jest wyrównane z koncentratorami wirtualnych sieci WAN już dziś.|Utwórz wiele wirtualnych sieci WAN, aby umożliwić tworzenie zabezpieczonych koncentratorów wirtualnych w różnych grupach zasobów.|
|Grupy adresów IP nie są obsługiwane w zasadach zapory|Grupy IP znajdują się w publicznej wersji zapoznawczej i obecnie są obsługiwane tylko przy użyciu tradycyjnych reguł zapory|Trwa naprawianie

## <a name="next-steps"></a>Następne kroki

- Przegląd [wdrożenia usługi Azure firewall Manager w wersji zapoznawczej](deployment-overview.md)
- Poznaj [bezpieczne centra wirtualne](secured-virtual-hub.md).