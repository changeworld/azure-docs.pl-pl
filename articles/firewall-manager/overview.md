---
title: Co to jest wersja zapoznawcza usługi Azure firewall Manager?
description: Informacje o funkcjach Menedżera zapory platformy Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 11/21/2019
ms.author: victorh
ms.openlocfilehash: 897819928ab0bcf48b58428014c03aea6b2145fd
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267950"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Co to jest wersja zapoznawcza usługi Azure firewall Manager?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager (wersja zapoznawcza) to usługa do zarządzania zabezpieczeniami, która umożliwia centralne zarządzanie zasadami zabezpieczeń i trasami dla obwodów zabezpieczeń opartych na chmurze. Współpracuje z [Centrum sieci wirtualnej platformy Azure](../virtual-wan/virtual-wan-about.md#resources), zasobem zarządzanym przez firmę Microsoft, który umożliwia łatwe tworzenie architektur Hub i szprych. Gdy zasady zabezpieczeń i routingu są skojarzone z takim centrum, jest ono nazywane *[bezpiecznym koncentratorem wirtualnym](secured-virtual-hub.md)* . 

![Zapora — Menedżer](media/overview/firewallmanagerv3.png)

## <a name="azure-firewall-manager-preview-features"></a>Funkcje w wersji zapoznawczej Menedżera zapory platformy Azure

Menedżer zapory Azure w wersji zapoznawczej oferuje następujące funkcje:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Wdrażanie i konfiguracja centralnej zapory platformy Azure

Można centralnie wdrażać i konfigurować wiele wystąpień zapory platformy Azure, które obejmują różne regiony i subskrypcje platformy Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Zasady hierarchiczne (globalne i lokalne)

Za pomocą narzędzia Menedżer zapory Azure w wersji zapoznawczej można centralnie zarządzać zasadami zapory platformy Azure w wielu zabezpieczonych koncentratorach wirtualnych. Centralne zespoły IT mogą tworzyć globalne zasady zapory w celu wymuszenia zasad zapory dla całej organizacji w zespołach. Lokalne zasady zapory umożliwiają DevOps samoobsługowego modelu w celu zwiększenia elastyczności.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integracja z zabezpieczeniami jako usługą innych firm w celu zapewnienia bezpieczeństwa zaawansowanego

Oprócz zapory platformy Azure można zintegrować dostawców usługi Security AS (SECaaS) innych firm w celu zapewnienia dodatkowej ochrony sieci dla sieci wirtualnej i połączeń internetowych oddziałów.

- Filtrowanie ruchu między sieciami wirtualnymi (V2I)

   - Filtrowanie ruchu wychodzącego z sieci wirtualnej za pomocą preferowanego dostawcy zabezpieczeń innej firmy.
   - Korzystaj z zaawansowanej, opartej na użytkownikach internetowej ochrony obciążeń w chmurze działających na platformie Azure.

- Filtrowanie ruchu z gałęzi do Internetu (B2I)

   Korzystaj z łączności z platformą Azure i globalnej dystrybucji, aby łatwo dodawać filtrowanie oddziałów dla gałęzi do scenariuszy internetowych.

Aby uzyskać więcej informacji na temat zaufanych dostawców zabezpieczeń, zobacz [co to są partnerzy zaufanych zabezpieczeń programu Azure firewall Manager (wersja zapoznawcza)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Scentralizowane zarządzanie trasami

Łatwo kieruj ruch do bezpiecznego centrum w celu filtrowania i rejestrowania bez konieczności ręcznego konfigurowania tras zdefiniowanych przez użytkownika (UDR) w sieciach wirtualnych szprych. Dostawców innych firm można używać do filtrowania ruchu z Internetu (B2I), obok siebie przy użyciu zapory platformy Azure dla gałęzi do sieci wirtualnej (B2V), sieci wirtualnej z siecią wirtualną (V2V) i Sieć wirtualna do Internetu (V2I). Dostawców innych firm można także używać do filtrowania ruchu V2I, o ile nie jest wymagana Zapora platformy Azure dla B2V lub V2V. 

## <a name="region-availability"></a>Dostępność w danym regionie

W publicznej wersji zapoznawczej są obsługiwane następujące regiony:

- Europa Zachodnia, Europa Północna, Francja środkowa, Francja Południowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo
- Australia Wschodnia, Australia Środkowa, Australia Środkowa 2, Australia Południowo-Wschodnia
- Kanada Środkowa
- Wschodnie stany USA, zachodnie stany USA, Wschodnie stany USA 2, Południowo-środkowe stany USA, zachodnie stany USA 2, środkowe stany USA, Północno-środkowe stany USA, zachodnie stany USA

Zasady zapory platformy Azure można tworzyć tylko w tych regionach, ale mogą one być używane w różnych regionach. Można na przykład utworzyć zasady w regionie zachodnie stany USA i używać ich w regionach Wschodnie stany USA. 

## <a name="known-issues"></a>Znane problemy

Wersja zapoznawcza Menedżera zapory platformy Azure obejmuje następujące znane problemy:

|Problem  |Opis  |Środki zaradcze  |
|---------|---------|---------|
|Ręcznie utworzone sieci wirtualnych centralne nie są obsługiwane|Obecnie Menedżer zapory platformy Azure obsługuje sieci utworzone za pomocą koncentratorów wirtualnych. Używanie własnej ręcznie utworzonej sieci wirtualnej centrum nie jest jeszcze obsługiwane.|Na razie użyj Menedżera zapory platformy Azure z sieciami piasty i szprych utworzonych przy użyciu centrów wirtualnych.<br>Obecnie trwa badanie.
|Ograniczenia filtrowania innych firm|Filtrowanie ruchu V2I z dostawcami innych firm nie jest obsługiwane przez usługę Azure firewall B2V i V2V.|Obecnie trwa badanie.|
|Dzielenie ruchu nie jest obecnie obsługiwane|Dzielenie pakietów Office 365 i Public PaaS nie jest obecnie obsługiwane. W związku z tym wybranie dostawcy innej firmy dla usługi V2I lub B2I spowoduje również wysłanie wszystkich usług Azure Public PaaS i Office 365 przez usługę partnera.|Obecnie badanie podziału ruchu w centrum.
|Jeden koncentrator na region|Nie można mieć więcej niż jednego centrum na region|Utwórz wiele wirtualnych sieci WAN w regionie.|
|Zasady podstawowe muszą znajdować się w tym samym regionie co zasady lokalne|Utwórz wszystkie zasady lokalne w tym samym regionie co zasady podstawowe. Można nadal stosować zasady, które zostały utworzone w jednym regionie w zabezpieczonym centrum z innego regionu.|Obecnie trwa badanie.|
|Komunikacja między centrami nie działa z bezpiecznym koncentratorem wirtualnym|Zabezpieczona wirtualna koncentrator do bezpiecznej komunikacji z koncentratorem wirtualnym nie jest jeszcze obsługiwana.|Obecnie trwa badanie.|

## <a name="next-steps"></a>Następne kroki

- Przegląd [wdrożenia usługi Azure firewall Manager w wersji zapoznawczej](deployment-overview.md)
- Poznaj [bezpieczne centra wirtualne](secured-virtual-hub.md).