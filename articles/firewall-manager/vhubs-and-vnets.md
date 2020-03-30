---
title: Jakie są opcje architektury usługi Azure Firewall Manager?
description: Porównaj i kontrast przy użyciu sieci wirtualnej centrum lub zabezpieczonych architektur koncentratora wirtualnego z usługą Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444578"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Jakie są opcje architektury usługi Azure Firewall Manager?

Usługa Azure Firewall Manager może zapewnić zarządzanie zabezpieczeniami dla dwóch typów architektury sieci:

- **zabezpieczony koncentrator wirtualny**

   [Usługa Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) to zasób zarządzany przez firmę Microsoft, który umożliwia łatwe tworzenie architektur koncentratora i szprychy. Gdy z takim koncentratorem są skojarzone zasady zabezpieczeń i routingu, jest on określany jako *[zabezpieczone centrum wirtualne](secured-virtual-hub.md)*. 
- **sieć wirtualna koncentratora**

   Jest to standardowa sieć wirtualna platformy Azure, którą tworzysz i zarządzasz samodzielnie. Gdy zasady zabezpieczeń są skojarzone z takim koncentratorem, jest on określany jako *sieć wirtualna koncentratora*. W tej chwili obsługiwane są tylko zasady zapory platformy Azure. Można równorzędne sieci wirtualne, które zawierają serwery obciążenia i usługi. Można również zarządzać zapory w autonomicznych sieciach wirtualnych, które nie są równorzędne z żadnym szprychy.

## <a name="comparison"></a>Porównanie

W poniższej tabeli porównano te dwie opcje architektury i możesz pomóc w podjęciu decyzji, która z nich jest odpowiednia dla wymagań dotyczących zabezpieczeń organizacji:


|  |**Sieć wirtualna centrum**|**Zabezpieczone centrum wirtualne**  |
|---------|---------|---------|
|**Zasób bazowy**     |Sieć wirtualna|Wirtualne centrum WAN|
|**Szprycha & piasty**     |Używa komunikacji równorzędnej sieci wirtualnej|Automatyczne korzystanie z połączenia sieci wirtualnej koncentratora|
|**Łączność on-prem**     |Brama SIECI VPN do 10 Gb/s i 30 połączeń S2S; Expressroute|Bardziej skalowalna brama sieci VPN do połączeń 20 Gb/s i 1000 S2S; Trasa ekspresowa|
|**Zautomatyzowana łączność w oddziale przy użyciu funkcji SDWAN**      |Nieobsługiwane|Obsługiwane|
|**Koncentratory na region**     |Wiele sieci wirtualnych na region|Pojedyncze centrum wirtualne na region. Możliwość wielu koncentratorów z wieloma wirtualnymi sieciami WAN|
|**Zapora azure — wiele publicznych adresów IP**      |Klient pod warunkiem,|Automatycznie wygenerowany. Ma być dostępny przez GA.|
|**Strefy dostępności zapory platformy Azure**     |Obsługiwane|Niedostępne w wersji zapoznawczej. Do udostępnienia przez GA|
|**Zaawansowane zabezpieczenia internetowe z zewnętrznymi partnerami security as a Service**     |Ustalona i zarządzana łączność VPN z wybranym partnerem|Zautomatyzowane za pośrednictwem przepływu zaufanego partnera zabezpieczeń i środowiska zarządzania partnerem|
|**Scentralizowane zarządzanie trasą do kierowania ruchu do węzła**     |Trasa zdefiniowana przez użytkownika zarządzana przez klienta|Obsługiwane przy użyciu protokołu BGP|
|**Zapora aplikacji internetowej w usłudze Application Gateway** |Obsługiwane w sieci wirtualnej|Obecnie obsługiwane w sieci szprych|
|**Wirtualne urządzenie sieciowe**|Obsługiwane w sieci wirtualnej|Obecnie obsługiwane w sieci szprych|

## <a name="next-steps"></a>Następne kroki

- Przegląd [wdrażania usługi Azure Firewall Manager Preview](deployment-overview.md)
- Dowiedz się więcej o [zabezpieczonych centrach wirtualnych](secured-virtual-hub.md).