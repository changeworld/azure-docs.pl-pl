---
title: Kim są zaufani partnerzy zabezpieczeń usługi Azure Firewall Manager (wersja zapoznawcza)
description: Dowiedz się więcej o zaufanych partnerach zabezpieczeń usługi Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436769"
---
# <a name="what-are-trusted-security-partners-preview"></a>Co to są zaufani partnerzy w zakresie zabezpieczeń (wersja zapoznawcza)?

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Zaufani partnerzy zabezpieczeń (wersja zapoznawcza)* w usłudze Azure Firewall Manager umożliwiają korzystanie ze znanych, najlepszych w swojej klasie zabezpieczeń innych firm jako usługi (SECaaS) w celu ochrony dostępu do Internetu dla użytkowników.

Dzięki szybkiej konfiguracji można zabezpieczyć koncentrator za pomocą obsługiwanego partnera zabezpieczeń oraz kierować i filtrować ruch internetowy z sieci wirtualnych (sieci wirtualnych) lub lokalizacji oddziałów w regionie. Odbywa się to przy użyciu zautomatyzowanego zarządzania trasami, bez konfigurowania tras zdefiniowanych przez użytkownika (UDR) i zarządzania nimi.

Zabezpieczonych koncentratorów skonfigurowanych z wybranym partnerem zabezpieczeń w wielu regionach platformy Azure można wdrożyć, aby uzyskać łączność i zabezpieczenia dla użytkowników z dowolnego miejsca na całym świecie w tych regionach. Dzięki możliwości korzystania z oferty partnera zabezpieczeń dla ruchu aplikacji Internet/SaaS i zapory azure dla ruchu prywatnego w zabezpieczonych centrach można teraz rozpocząć tworzenie krawędzi zabezpieczeń na platformie Azure, która znajduje się w pobliżu użytkowników i aplikacji rozproszonych globalnie.

W przypadku tej wersji zapoznawczej obsługiwanymi partnerami zabezpieczeń są **ZScaler** i **iboss**. Obsługiwane regiony to WestCentralUS, NorthCentralUS, WestUS, WestUS2 i EastUS.

![Zaufani partnerzy w zakresie zabezpieczeń](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Kluczowe scenariusze

Partnerów zabezpieczeń można używać do filtrowania ruchu internetowego w następujących scenariuszach:

- Sieć wirtualna (VNet) do Internetu

   Korzystaj z zaawansowanej ochrony internetu z uwzględnieniem użytkownika dla obciążeń w chmurze działających na platformie Azure.

- Odgałęzić do Internetu

   Wykorzystaj łączność platformy Azure i dystrybucję globalną, aby łatwo dodać filtrowanie NSaaS innych firm w scenariuszach międzygałęzień i internetu. Za pomocą wirtualnej sieci WAN platformy Azure można utworzyć globalną sieć tranzytową i krawędź zabezpieczeń.

Obsługiwane są następujące scenariusze:
-   VNet do Internetu za pośrednictwem oferty partnera innej firmy.
-   Odgałęzić się do Internetu za pośrednictwem oferty partnera zewnętrznego.
-   Odgałęzić do Internetu za pośrednictwem oferty partnera innej firmy, reszta ruchu prywatnego (Spoke-to-Spoke, Spoke-to-Branches, Branch-to-Spokes) za pośrednictwem zapory azure.

Następujący scenariusz nie jest obsługiwany:

- Sieci wirtualnej do Internetu za pośrednictwem oferty partnera nie można połączyć z zaporą platformy Azure dla ruchu prywatnego. Zobacz następujące ograniczenia.

## <a name="current-limitations"></a>Bieżące ograniczenia

- W przypadku sieci wirtualnej do Internetu nie można mieszać dodawania zapory azure dla ruchu prywatnego i oferty partnera dla ruchu internetowego. Ruch internetowy można wysyłać do Zapory platformy Azure lub do zewnętrznego partnera zabezpieczeń oferowanego w zabezpieczonym centrum wirtualnym, ale nie do obu. 
- Można wdrożyć co najwyżej jednego partnera zabezpieczeń na koncentrator wirtualny. Jeśli musisz zmienić dostawcę, musisz usunąć istniejącego partnera i dodać nowego.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Najważniejsze wskazówki dotyczące filtrowania ruchu internetowego w zabezpieczonych koncentratorach wirtualnych

Ruch internetowy zazwyczaj obejmuje ruch internetowy. Ale obejmuje również ruch przeznaczony do aplikacji SaaS, takich jak Office 365 (O365) i publicznych usług PaaS platformy Azure, takich jak usługa Azure Storage, Azure Sql i tak dalej. Poniżej przedstawiono zalecenia dotyczące najlepszych rozwiązań dotyczących obsługi ruchu do tych usług:

### <a name="handling-azure-paas-traffic"></a>Obsługa ruchu paas platformy Azure
 
- Użyj Zapory Platformy Azure w celu ochrony, jeśli ruch składa się głównie z usługi Azure PaaS, a dostęp do zasobów dla aplikacji można filtrować przy użyciu adresów IP, sieci FQDN, tagów usługi lub tagów FQDN.

- Użyj rozwiązania partnerskiego innej firmy w centrach, jeśli ruch składa się z dostępu do aplikacji SaaS lub potrzebujesz filtrowania z uwzględnieniem użytkownika (na przykład dla obciążeń infrastruktury pulpitu wirtualnego (VDI) lub potrzebujesz zaawansowanych funkcji filtrowania internetu.

![Wszystkie scenariusze dla usługi Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Obsługa ruchu usługi Office 365 (O365)

W scenariuszach globalnie rozproszonej lokalizacji oddziału należy przekierować ruch usługi Office 365 bezpośrednio w gałęzi przed wysłaniem pozostałego ruchu internetowego przez centrum zabezpieczonego platformy Azure.

W przypadku usługi Office 365 opóźnienie sieci i wydajność mają kluczowe znaczenie dla pomyślnego korzystania z usługi. Aby osiągnąć te cele wokół optymalnej wydajności i środowiska użytkownika, klienci muszą zaimplementować bezpośrednią i lokalną ucieczkę usługi Office 365 przed rozważeniem routingu pozostałej części ruchu internetowego za pośrednictwem platformy Azure.

[Zasady łączności sieciowej usługi Office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) wymagają, aby kluczowe połączenia sieciowe usługi Office 365 były kierowane lokalnie z gałęzi użytkownika lub urządzenia przenośnego i bezpośrednio przez Internet do najbliższego punktu obecności sieci firmy Microsoft.

Ponadto połączenia usługi Office 365 są silnie szyfrowane ze względu na prywatność i używają wydajnych, zastrzeżonych protokołów ze względu na wydajność. To sprawia, że niepraktyczne i wpływ na poddanie tych połączeń do tradycyjnych rozwiązań zabezpieczeń na poziomie sieci. Z tych powodów zdecydowanie zaleca się, aby klienci wysyłali ruch w usłudze Office 365 bezpośrednio z oddziałów, przed wysłaniem pozostałej części ruchu za pośrednictwem platformy Azure. Firma Microsoft nawiązała współpracę z kilkoma dostawcami rozwiązań SD-WAN, którzy integrują się z platformą Azure i usługą Office 365 i ułatwiają klientom włączanie bezpośredniego i lokalnego rozwiązania internetowego usługi Office 365. Aby uzyskać szczegółowe informacje, zobacz [Jak ustawić zasady usługi O365 za pośrednictwem wirtualnej sieci WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Następne kroki

[Wdrażaj zaufaną ofertę zabezpieczeń w zabezpieczonym centrum, korzystając z usługi Azure Firewall Manager](deploy-trusted-security-partner.md).
