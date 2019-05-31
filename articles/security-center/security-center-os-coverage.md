---
title: Funkcje i platformach obsługiwanych przez usługę Azure Security Center | Dokumentacja firmy Microsoft
description: Ten dokument zawiera listę funkcji i platformach obsługiwanych przez usługę Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/27/2019
ms.author: monhaber
ms.openlocfilehash: 807bde76bb6bb50490ee599768273a59c49d5e45
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258705"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformy i funkcji obsługiwanych przez usługę Azure Security Center

Monitorowanie stanu zabezpieczeń i zalecenia są dostępne dla maszyn wirtualnych (VM) utworzonych przy użyciu zarówno klasyczny i modelem wdrażania usługi Resource Manager i komputery.

> [!NOTE]
> Dowiedz się więcej o [klasycznej sieci wirtualnej i modelem wdrażania usługi Resource Manager](../azure-classic-rm.md) dla zasobów platformy Azure.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Temat platform obsługujących agent zbierania danych 

W tej sekcji przedstawiono platform, na których można uruchomić agenta usługi Azure Security Center i z którego mógł zbierać dane.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Obsługiwane platformy dla maszyn wirtualnych i komputerów Windows
Obsługiwane są następujące systemy operacyjne Windows:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Integracja z usługą Windows Defender ATP obsługuje tylko systemu Windows Server 2012 R2 i Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Obsługiwane platformy dla maszyn wirtualnych i komputerów z systemem Linux

Obsługiwane są następujące systemy operacyjne Linux:

> [!NOTE]
> Ponieważ lista obsługiwanych systemów operacyjnych Linux jest często zmieniane, jeśli wolisz, kliknij przycisk [tutaj](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) Aby wyświetlić najbardziej aktualną listę obsługiwanych wersji, w przypadku, gdy została zmieniona od czasu ostatniej publikacji w tym temacie.

64-bitowy
* CentOS 6 i 7
* Amazon Linux 2017.09
* Oracle Linux 6 i 7
* Red Hat Enterprise Linux Server 6 i 7
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS i 18.04 LTS
* SUSE Linux Enterprise Server 12

32-bitowy
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 i 9
* LTS Ubuntu Linux 14.04 i 16.04 LTS

## <a name="vms-and-cloud-services"></a>Maszyny wirtualne i usługi w chmurze
Obsługiwane są również maszyny wirtualne, które są uruchamiane w usłudze w chmurze. Monitorowane są tylko usługi sieci web i proces roboczy role w chmurze działających w miejsce produkcyjne. Aby dowiedzieć się więcej o usługach cloud services, zobacz [Omówienie usługi Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Obsługiwane funkcje IaaS

> [!div class="mx-tableFixed"]
> 

|Serwer|Windows||Linux||||Cennik|
|----|----|----|----|----|----|----|----|
|**Środowisko**|**Azure**||**Spoza platformy Azure**|**Azure**||**Spoza platformy Azure**||
||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**|
|Alerty wykrywania zagrożeń VMBA|✔|✔|✔|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|Wykrywanie zagrożeń (wersja bezpłatna) zaleceń (standardowa)|
|Alerty wykrywania zagrożeń opartego na sieci|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|Integracja usługi Windows Defender ATP|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|X|X|X|Standardowa (Standard)|
|Brakujące poprawki|✔|✔|✔|✔|✔|✔|Wolne|
|Konfiguracje zabezpieczeń|✔|✔|✔|✔|✔|✔|Wolne|
|Ocena ochrony punktu końcowego|✔|✔|✔|X|X|X|Wolne|
|Dostęp do maszyn wirtualnych JIT|✔|X|X|✔|X|X|Standardowa (Standard)|
|Funkcje adaptacyjnego sterowania aplikacjami|✔|X|✔|✔|X|✔|Standardowa (Standard)|
|USŁUGI FIM|✔|✔|✔|✔|✔|✔|Standardowa (Standard)|
|Ocena szyfrowania dysku|✔|✔|X|✔|✔|X|Wolne|
|Wdrożenie innych firm|✔|X|X|✔|X|X|Wolne|
|Ocena sieciowych grup zabezpieczeń|✔|✔|X|✔|✔|X|Wolne|
|Wykrywanie zagrożeń fileless|✔|✔|✔|X|X|X|Standardowa (Standard)|
|Mapa sieci|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|Kontrolki adaptacyjne sieci|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|Pulpit nawigacyjny zgodności prawnych i raporty|✔|✔|✔|✔|✔|✔|Standardowa (Standard)|
|Zalecenia i wykrywania zagrożeń w kontenerach platformy Docker hostowanych IaaS|X|X|X|✔|✔|✔|Standardowa (Standard)|

### <a name="supported-endpoint-protection-solutions"></a>Rozwiązań ochrony obsługiwanych punktów końcowych

Poniższa tabela zawiera macierz:
 - Czy można użyć usługi Azure Security Center na instalowanie poszczególnych rozwiązań.
 - Można wykryć, które Centrum zabezpieczeń rozwiązania ochrony punktu końcowego. Jeśli jeden z tych rozwiązań do ochrony punktu końcowego zostanie wykryta, usługa Security Center będzie nie zaleca się zainstalowanie jednego.

Aby dowiedzieć się, jak podczas zalecenia są generowane dla każdego z tych środków ochronnych, zobacz [rekomendacje i ocena ochrony punktu końcowego](security-center-endpoint-protection.md).

| Ochrona punktów końcowych| Platformy | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
|------|------|-----|-----|
| Windows Defender (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem)| Windows Server 2016| Nie, wbudowana w system operacyjny| Tak |
| System Center Endpoint Protection (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem) | Windows Server 2012 R2, 2012, 2008 R2 (zobacz uwaga poniżej) | Za pomocą rozszerzenia | Tak |
| Trend Micro — wszystkie wersje | Rodzina systemów Windows Server  | Nie | Tak |
| Symantec — w wersji co najmniej 12.1.1100| Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina systemów Windows Server  | Nie | Tak |
| Kaspersky| Rodzina systemów Windows Server  | Nie | Nie  |
| Sophos| Rodzina systemów Windows Server  | Nie | Nie  |

> [!NOTE]
> - Wykrywanie programu System Center Endpoint Protection (SCEP) na maszynie wirtualnej systemu Windows Server 2008 R2 wymaga protokołu SCEP do zainstalowania programu PowerShell 3.0 (lub górną wersji).

## <a name="supported-paas-features"></a>Obsługiwane funkcje PaaS


|Usługa|Zaleceń (wersja bezpłatna)|Wykrywanie zagrożeń (standardowa)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Usługa Azure Blob storage konta *|✔| ✔|
|Usługi aplikacji|✔| ✔|
|Usługi w chmurze|✔| X|
|Sieci wirtualne|✔| Nie dotyczy|
|Podsieci|✔| Nie dotyczy|
|Karty interfejsów sieciowych|✔| Nie dotyczy|
|Sieciowe grupy zabezpieczeń|✔| Nie dotyczy|
|Subskrypcja|✔ **| ✔|
|App Service|✔| Nie dotyczy|
|Batch|✔| Nie dotyczy|
|Service Fabric|✔| Nie dotyczy|
|Konto usługi Automation|✔| Nie dotyczy|
|Moduł równoważenia obciążenia|✔| Nie dotyczy|
|Wyszukiwanie|✔| Nie dotyczy|
|Service Bus|✔| Nie dotyczy|
|Stream Analytics|✔| Nie dotyczy|
|Centrum zdarzeń|✔| Nie dotyczy|
|Aplikacje logiki|✔| Nie dotyczy|
|Podsieć|✔| Nie dotyczy|
|Sieć wirtualna|✔| Nie dotyczy|
|Konto magazynu|✔| Nie dotyczy|
|Redis|✔| Nie dotyczy|
|SQL|✔| Nie dotyczy|
|Usługa Data lake analytics|✔| Nie dotyczy|
|Konto magazynu|✔| Nie dotyczy|
|Subskrypcja|✔| Nie dotyczy|
|Magazyn kluczy|✔| Nie dotyczy|




\* Te funkcje są obecnie obsługiwane w publicznej wersji zapoznawczej.

\*\* Zalecenia usługi AAD są dostępne tylko dla subskrypcji standardowych



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center](security-center-planning-and-operations-guide.md).
- Dowiedz się więcej o [analiza zachowania maszyny wirtualnej i awaryjnego zrzutu pamięci analizy w usłudze Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Znajdź [często zadawane pytania dotyczące korzystania z usługi Azure Security Center](security-center-faq.md).
- Znajdź [wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności](https://blogs.msdn.com/b/azuresecurity/).
