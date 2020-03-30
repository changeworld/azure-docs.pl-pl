---
title: Obsługiwane funkcje dostępne w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Ten dokument zawiera listę usług obsługiwanych przez usługę Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245241"
---
# <a name="feature-coverage-for-machines"></a>Pokrycie funkcji dla maszyn

W poniższych tabelach pokazano funkcje usługi Azure Security Center, które są dostępne dla maszyn wirtualnych i serwerów.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Obsługiwane funkcje maszyn wirtualnych i serwerów<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Maszyny z systemem Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkcja**|**Maszyny wirtualne platformy Azure**|**Azure Virtual Machine Scale Sets**|**Maszyny nieobjęte platformą Azure**|**Cennik**
|[Integracja z centrum ATP usługi Microsoft Defender](security-center-wdatp.md)|✔</br>(w obsługiwanych wersjach)|✔</br>(w obsługiwanych wersjach)|✔|Standardowa|
|[Analiza behawioralna maszyny wirtualnej (i alerty zabezpieczeń)](threat-protection.md)|✔|✔|✔|Rekomendacje (bezpłatne) </br></br> Alerty zabezpieczeń (standard)|
|[Alerty zabezpieczeń bez plików](alerts-reference.md#alerts-windows)|✔|✔|✔|Standardowa|
|[Sieciowe alerty zabezpieczeń](threat-protection.md#network-layer)|✔|✔|-|Standardowa|
|[Dostęp do maszyny wirtualnej just-in-time](security-center-just-in-time.md)|✔|-|-|Standardowa|
|[Natywna ocena luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standardowa|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardowa|
|[Kontrolki adaptacyjne aplikacji](security-center-adaptive-application.md)|✔|-|✔|Standardowa|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardowa|
|[Adaptacyjne wzmocnienie zabezpieczeń sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standardowa|
|Adaptacyjne sterowanie siecią|✔|✔|-|Standardowa|
|[Raporty & pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standardowa|
|Zalecenia i ochrona przed zagrożeniami w kontenerach IaaS hostowanych w programie Docker|-|-|-|Standardowa|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Bezpłatna|
|Ocena błędnych konfiguracji zabezpieczeń|✔|✔|✔|Bezpłatna|
|[Ocena ochrony punktów końcowych](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Bezpłatna|
|Ocena szyfrowania dysku|✔|✔|-|Bezpłatna|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Bezpłatna|
|[Ocena bezpieczeństwa sieci](security-center-network-recommendations.md)|✔|✔|-|Bezpłatna|


### <a name="linux-machines"></a>[Maszyny linuzownie](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkcja**|**Maszyny wirtualne platformy Azure**|**Azure Virtual Machine Scale Sets**|**Maszyny nieobjęte platformą Azure**|**Cennik**
|[Integracja z centrum ATP usługi Microsoft Defender](security-center-wdatp.md)|-|-|-|Standardowa|
|[Analiza behawioralna maszyny wirtualnej (i alerty zabezpieczeń)](security-center-alerts-iaas.md)|✔</br>(w obsługiwanych wersjach)|✔</br>(w obsługiwanych wersjach)|✔|Rekomendacje (bezpłatne) </br></br> Alerty zabezpieczeń (standard)|
|[Alerty zabezpieczeń bez plików](alerts-reference.md#alerts-windows)|-|-|-|Standardowa|
|[Sieciowe alerty zabezpieczeń](threat-protection.md#network-layer)|✔|✔|-|Standardowa|
|[Dostęp do maszyny wirtualnej just-in-time](security-center-just-in-time.md)|✔|-|-|Standardowa|
|[Natywna ocena luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standardowa|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardowa|
|[Kontrolki adaptacyjne aplikacji](security-center-adaptive-application.md)|✔|-|✔|Standardowa|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardowa|
|[Adaptacyjne wzmocnienie zabezpieczeń sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standardowa|
|Adaptacyjne sterowanie siecią|✔|✔|-|Standardowa|
|[Raporty & pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standardowa|
|Zalecenia i ochrona przed zagrożeniami w kontenerach IaaS hostowanych w programie Docker|✔|✔|✔|Standardowa|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Bezpłatna|
|Ocena błędnych konfiguracji zabezpieczeń|✔|✔|✔|Bezpłatna|
|[Ocena ochrony punktów końcowych](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Bezpłatna|
|Ocena szyfrowania dysku|✔|✔|-|Bezpłatna|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Bezpłatna|
|[Ocena bezpieczeństwa sieci](security-center-network-recommendations.md)|✔|✔|-|Bezpłatna|

--- 


> [!TIP]
>Aby eksperymentować z funkcjami, które są dostępne tylko w standardowej warstwie cenowej, użytkownicy warstwy bezpłatnej mogą zarejestrować się w 30-dniowej wersji próbnej. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Obsługiwane rozwiązania ochrony punktów końcowych<a name="endpoint-supported"></a>

Poniższa tabela zawiera macierz:

 - Czy można użyć usługi Azure Security Center, aby zainstalować każde rozwiązanie dla Ciebie.
 - Jakie rozwiązania ochrony punktów końcowych usługa Security Center może odnajdować. Jeśli zostanie wykryte rozwiązanie ochrony punktu końcowego z tej listy, usługa Security Center nie będzie zalecać instalowania.

Aby uzyskać informacje o tym, kiedy zalecenia są generowane dla każdej z tych zabezpieczeń, zobacz [Ocena ochrony punktu końcowego i zalecenia](security-center-endpoint-protection.md).

| Ochrona punktów końcowych| Platformy | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
|------|------|-----|-----|
| Windows Defender (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem)| Windows Server 2016| Nie, wbudowana w system operacyjny| Tak |
| System Center Endpoint Protection (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem) | Windows Server 2012 R2, 2012, 2008 R2 (patrz uwaga poniżej) | Za pomocą rozszerzenia | Tak |
| Trend Micro – wszystkie wersje* | Rodzina systemów Windows Server  | Nie | Tak |
| Symantec — w wersji co najmniej 12.1.1100| Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina serwerów Linux  | Nie | Tak**\*** |
| Sophos V9+| Rodzina serwerów Linux  | Nie | Tak**\***  |

 **\*** Stan zapotrzebowania i dane pomocnicze są obecnie dostępne tylko w obszarze roboczym usługi Log Analytics skojarzonym z chronionymi subskrypcjami. Nie jest to odzwierciedlone w portalu Usługi Azure Security Center.

> [!NOTE]
> - Wykrywanie systemu Center Endpoint Protection (SCEP) na maszynie wirtualnej systemu Windows Server 2008 R2 wymaga zainstalowania programu SCEP po programie PowerShell 3.0 (lub wersji wyższej).
> - Wykrywanie ochrony trendu Micro jest obsługiwane przez agentów Deep Security.  Agenci OfficeScan nie są obsługiwani.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [Usługa Security Center zbiera dane i agenta analizy dzienników](security-center-enable-data-collection.md).
- Dowiedz się, jak [Usługa Security Center zarządza i zabezpiecza dane.](security-center-data-security.md)
- Dowiedz się, jak [zaplanować i zrozumieć zagadnienia dotyczące projektu, aby przyjąć usługę Azure Security Center.](security-center-planning-and-operations-guide.md)
- Przejrzyj [platformy obsługujące centrum zabezpieczeń](security-center-os-coverage.md).
- Dowiedz się więcej o [ochronie przed zagrożeniami dla komputerów z systemem Windows i Linux w usłudze Azure Security Center](threat-protection.md#windows-machines).
- Znajdź [często zadawane pytania dotyczące usługi Azure Security Center](faq-general.md).