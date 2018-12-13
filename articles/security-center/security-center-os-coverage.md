---
title: Funkcje i platformach obsługiwanych przez usługę Azure Security Center | Dokumentacja firmy Microsoft
description: Ten dokument zawiera listę funkcji i platformach obsługiwanych przez usługę Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 4108355415d1230f98db36a4f83497de2fa848f7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185583"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformy i funkcji obsługiwanych przez usługę Azure Security Center

Monitorowanie stanu zabezpieczeń i zalecenia są dostępne dla maszyn wirtualnych (VM) utworzonych przy użyciu zarówno klasyczny i modelem wdrażania usługi Resource Manager i komputery.

> [!NOTE]
> Dowiedz się więcej o [klasycznej sieci wirtualnej i modelem wdrażania usługi Resource Manager](../azure-classic-rm.md) dla zasobów platformy Azure.
>
>

## <a name="supported-platforms"></a>Obsługiwane platformy 

W tej sekcji przedstawiono platform, na których można uruchomić agenta usługi Azure Security Center i z którego mógł zbierać dane.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Obsługiwane platformy dla maszyn wirtualnych i komputerów Windows
Obsługiwane są następujące systemy operacyjne Windows:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Obsługiwane platformy dla maszyn wirtualnych i komputerów z systemem Linux
Obsługiwane są następujące systemy operacyjne Linux:

* Wersje Ubuntu 12.04 LTS, 14.04 LTS i 16.04 LTS.
* Wersje debian 6, 7, 8 i 9.
* CentOS w wersji 5, 6 i 7.
* Wersje Red Hat Enterprise Linux (RHEL), 5, 6 i 7.
* SUSE Linux Enterprise Server (SLES) w wersji 11 i 12.
* Wersje systemu Linux Oracle, 5, 6 i 7.
* Linux Amazon 2012.09 2017 r.
* OpenSSL 1.1.0 jest obsługiwana tylko na platformach x86_64, 64-bitowych.

> [!NOTE]
> Analiza zachowania maszyny wirtualnej nie są jeszcze dostępne w systemach operacyjnych Linux.
>
>

## <a name="vms-and-cloud-services"></a>Maszyny wirtualne i usługi w chmurze
Obsługiwane są również maszyny wirtualne, które są uruchamiane w usłudze w chmurze. Monitorowane są tylko usługi sieci web i proces roboczy role w chmurze działających w miejsce produkcyjne. Aby dowiedzieć się więcej o usługach cloud services, zobacz [Omówienie usługi Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Obsługiwane funkcje IaaS

> [!div class="mx-tableFixed"]
> 

|Serwer|Windows||Linux||
|----|----|----|----|----|
|Środowisko|Azure|Poza platformą Azure|Azure|Poza platformą Azure|
|Alerty wykrywania zagrożeń VMBA|✔|✔|✔ (w obsługiwanych wersjach)|✔|
|Alerty wykrywania zagrożeń opartego na sieci|✔|X|✔|X|
|Integracja usługi Windows Defender ATP *|✔ (w obsługiwanych wersjach)|✔|X|X|
|Brakujące poprawki|✔|✔|✔|✔|
|Konfiguracje zabezpieczeń|✔|✔|✔|✔|
|Programy chroniące przed złośliwym kodem|✔|✔|X|X|
|Dostęp JIT do maszyny wirtualnej|✔|X|✔|X|
|Funkcje adaptacyjnego sterowania aplikacjami|✔|X|X|X|
|USŁUGI FIM|✔|✔|✔|✔|
|Szyfrowanie dysków|✔|X|✔|X|
|Wdrożenie innych firm|✔|X|✔|X|
|Sieciowe grupy zabezpieczeń|✔|X|✔|X|
|Wykrywanie zagrożeń fileless|✔|✔|X|X|
|Mapa sieci|✔|X|✔|X|
|Kontrolki adaptacyjne sieci|✔|X|✔|X|

\* Te funkcje są obecnie obsługiwane w publicznej wersji zapoznawczej.


## <a name="supported-paas-features"></a>Obsługiwane funkcje PaaS 


|Usługa|Zalecenia|Wykrywanie zagrożeń|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL *|✔| ✔|
|MySQL *|✔| ✔|
|Usługa Azure Blob storage konta *|✔| ✔|
|Usługi aplikacji|✔| ✔|
|Cloud Services|✔| X|
|Sieci wirtualne|✔| Nie dotyczy|
|Podsieci|✔| Nie dotyczy|
|Karty interfejsów sieciowych|✔| ✔|
|Sieciowe grupy zabezpieczeń|✔| Nie dotyczy|
|Subskrypcja|✔| ✔|

\* Te funkcje są obecnie obsługiwane w publicznej wersji zapoznawczej. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center](security-center-planning-and-operations-guide.md).
- Dowiedz się więcej o [analiza zachowania maszyny wirtualnej i awaryjnego zrzutu pamięci analizy w usłudze Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Znajdź [często zadawane pytania dotyczące korzystania z usługi Azure Security Center](security-center-faq.md).
- Znajdź [wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności](https://blogs.msdn.com/b/azuresecurity/).
