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
ms.openlocfilehash: f4bc90b2d1a80125ae88b4b5c4c11e42a34a985a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240430"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformy i funkcji obsługiwanych przez usługę Azure Security Center

Monitorowanie stanu zabezpieczeń i zalecenia są dostępne dla maszyn wirtualnych (VM) utworzone za pomocą zarówno klasyczny i modelem wdrażania usługi Resource Manager i komputery.

> [!NOTE]
> Dowiedz się więcej o [klasycznej sieci wirtualnej i modelem wdrażania usługi Resource Manager](../azure-classic-rm.md) dla zasobów platformy Azure.
>
>

## <a name="supported-platforms"></a>Obsługiwane platformy 

W tej sekcji przedstawiono platform, na których można uruchomić agenta usługi Azure Security Center i z którego mógł zbierać dane.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Obsługiwane platformy dla maszyn wirtualnych i komputerów Windows
Obsługiwane systemy operacyjne Windows:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Obsługiwane platformy dla maszyn wirtualnych i komputerów z systemem Linux
Obsługiwane systemy operacyjne Linux:

* Wersje Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS
* Wersje debian 6, 7, 8, 9
* CentOS w wersji 5, 6, 7
* Red Hat Enterprise Linux (RHEL) w wersji 5, 6, 7
* SUSE Linux Enterprise Server (SLES) w wersji 11, 12
* Oracle Linux w wersji 5, 6, 7
* Linux Amazon 2012.09 2017
* Openssl 1.1.0 jest obsługiwana tylko na platformach x86_64 (64-bitowy)

> [!NOTE]
> Analiza zachowania maszyny wirtualnej nie są jeszcze dostępne w systemach operacyjnych Linux.
>
>

## <a name="vms-and-cloud-services"></a>Maszyny wirtualne i usługi w chmurze
Obsługiwane są również maszyny wirtualne działające w usłudze w chmurze. Jedyna chmura usług ról sieć web i procesów roboczych działających w środowisku produkcyjnym, które mają być monitorowane przez miejsc. Aby dowiedzieć się więcej na temat usługi w chmurze, zobacz [Omówienie usług w chmurze](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Obsługiwane funkcje IaaS

> [!div class="mx-tableFixed"]
> 

|Serwer|Windows||Linux||
|----|----|----|----|----|
|Środowisko|Azure|Poza platformą Azure|Azure|Poza platformą Azure|
|Alerty wykrywania zagrożeń VMBA|✔|✔|✔ (w obsługiwanych wersjach)|✔|
|Alerty wykrywania zagrożeń sieci|✔|X|✔|X|
|Integracja usługi Windows Defender ATP *|✔ (w obsługiwanych wersjach)|✔|X|X|
|Brakujące poprawki|✔|✔|✔|✔|
|Konfiguracje zabezpieczeń|✔|✔|✔|✔|
|Chroniące przed złośliwym kodem|✔|✔|X|X|
|Dostęp JIT do maszyny wirtualnej|✔|X|✔|X|
|Funkcje adaptacyjnego sterowania aplikacjami|✔|X|X|X|
|USŁUGI FIM|✔|✔|✔|✔|
|Szyfrowanie dysków|✔|X|✔|X|
|Wdrożenie innych firm|✔|X|✔|X|
|Sieciowe grupy zabezpieczeń|✔|X|✔|X|
|Wykrywanie zagrożeń Filess|✔|✔|X|X|
|Mapa sieci|✔|X|✔|X|
|Kontrolki adaptacyjne sieci|✔|X|✔|X|

\* Te funkcje są obecnie obsługiwane w publicznej wersji zapoznawczej.


## <a name="supported-paas-features"></a>Obsługiwane funkcje PaaS


|Usługa|Zalecenia|Wykrywanie zagrożeń|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL *|✔| ✔|
|MySQL *|✔| ✔|
|Konta magazynu obiektów blob *|✔| ✔|
|Usługi aplikacji|✔| ✔|
|Usługi w chmurze|✔| X|
|Sieci wirtualne|✔| Nie dotyczy|
|Podsieci|✔| Nie dotyczy|
|Karty interfejsów sieciowych|✔| ✔|
|Sieciowe grupy zabezpieczeń|✔| Nie dotyczy|
|Subskrypcja|✔| ✔|

\* Te funkcje są obecnie obsługiwane w publicznej wersji zapoznawczej.

## <a name="next-steps"></a>Kolejne kroki

- [Przewodnik planowania usługi Azure Security Center i](security-center-planning-and-operations-guide.md) — informacje o sposobie planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center
- [Alerty zabezpieczeń według typu w usłudze Azure Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis) — Dowiedz się więcej o analiza zachowania maszyny wirtualnej i awarii zrzutu pamięci analizy w usłudze Security Center
- [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi
- [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności
