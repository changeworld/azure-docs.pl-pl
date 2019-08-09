---
title: Funkcje i platformy obsługiwane przez Azure Security Center | Microsoft Docs
description: Ten dokument zawiera listę funkcji i platform obsługiwanych przez Azure Security Center.
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
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 7cd1d451b49faf2f8e3ad38f4ff780256ef2dc5d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883601"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformy i funkcje obsługiwane przez Azure Security Center

Monitorowanie stanu zabezpieczeń i zalecenia są dostępne dla maszyn wirtualnych utworzonych przy użyciu zarówno klasycznych, jak i Menedżer zasobów modeli wdrażania oraz komputerów.

> [!NOTE]
> Dowiedz się więcej na temat [modeli wdrażania klasycznego i Menedżer zasobów](../azure-classic-rm.md) dla zasobów platformy Azure.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Platformy obsługujące agenta zbierania danych 

Ta sekcja zawiera listę platform, na których można uruchomić agenta Azure Security Center i z którego można zbierać dane.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Obsługiwane platformy dla komputerów i maszyn wirtualnych z systemem Windows
Obsługiwane są następujące systemy operacyjne Windows:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Integracja z usługą Windows Defender ATP obsługuje tylko systemy Windows Server 2012 R2 i Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Obsługiwane platformy dla komputerów z systemem Linux i maszyn wirtualnych

Obsługiwane są następujące systemy operacyjne Linux:

> [!NOTE]
> Ponieważ lista obsługiwanych systemów operacyjnych Linux jest ciągle zmieniana, kliknij [tutaj](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) , aby wyświetlić najbardziej aktualną listę obsługiwanych wersji, na wypadek zmiany od momentu ostatniej publikacji tego tematu.

64-bitowa
* CentOS 6 i 7
* Amazon Linux 2017,09
* Oracle Linux 6 i 7
* Red Hat Enterprise Linux Server 6 i 7
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS i 18,04 LTS
* SUSE Linux Enterprise Server 12

32-bitowa
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14,04 LTS i 16,04 LTS

## <a name="vms-and-cloud-services"></a>Maszyny wirtualne i Cloud Services
Obsługiwane są również maszyny wirtualne, które są uruchomione w usłudze w chmurze. Monitorowane są tylko role sieć Web i proces roboczy usług Cloud Services, które działają w gniazdach produkcyjnych. Aby dowiedzieć się więcej o usługach w chmurze, zobacz [Omówienie usługi Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Obsługiwane funkcje IaaS

> [!div class="mx-tableFixed"]
> 

|Serwer|Windows|||Linux|||Cennik|
|----|----|----|----|----|----|----|----|
|**Środowisko**|**Azure**||**Poza platformą Azure**|**Azure**||**Poza platformą Azure**||
||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**|
|Alerty wykrywania zagrożeń VMBA|✔|✔|✔|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|Zalecenia (bezpłatne) wykrywanie zagrożeń (wersja standardowa)|
|Alerty dotyczące wykrywania zagrożeń opartych na sieci|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|Integracja usługi Windows Defender ATP|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|X|X|X|Standardowa (Standard)|
|Brakujące poprawki|✔|✔|✔|✔|✔|✔|Wolne|
|Konfiguracje zabezpieczeń|✔|✔|✔|✔|✔|✔|Wolne|
|Ocena programu Endpoint Protection|✔|✔|✔|X|X|X|Wolne|
|Dostęp JIT do maszyny wirtualnej|✔|X|X|✔|X|X|Standardowa (Standard)|
|Adaptacyjne kontrole aplikacji|✔|X|✔|✔|X|✔|Standardowa (Standard)|
|KOD|✔|✔|✔|✔|✔|✔|Standardowa (Standard)|
|Ocena szyfrowania dysku|✔|✔|X|✔|✔|X|Wolne|
|Wdrożenie innych firm|✔|X|X|✔|X|X|Wolne|
|Ocena sieciowej grupy zabezpieczeń|✔|✔|X|✔|✔|X|Wolne|
|Wykrywanie zagrożeń bezplikowych|✔|✔|✔|X|X|X|Standardowa (Standard)|
|Mapa sieci|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|Adaptacyjne kontrolki sieci|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|& Raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami|✔|✔|✔|✔|✔|✔|Standardowa (Standard)|
|Zalecenia i wykrywanie zagrożeń w kontenerach IaaS hostowanych przez platformę Docker|X|X|X|✔|✔|✔|Standardowa (Standard)|

### <a name="supported-endpoint-protection-solutions"></a>Obsługiwane rozwiązania programu Endpoint Protection

Poniższa tabela zawiera macierz:
 - Czy można użyć Azure Security Center, aby zainstalować każde rozwiązanie.
 - Security Center można odnaleźć rozwiązań programu Endpoint Protection. Jeśli jedno z tych rozwiązań programu Endpoint Protection zostanie wykryte, Security Center nie zaleca się instalowania go.

Aby uzyskać informacje o tym, kiedy są generowane zalecenia dla każdej z tych ochrony, zobacz [Endpoint Protection oceny i rekomendacji](security-center-endpoint-protection.md).

| Endpoint Protection| Platformy | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
|------|------|-----|-----|
| Windows Defender (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem)| Windows Server 2016| Nie, wbudowana w system operacyjny| Yes |
| System Center Endpoint Protection (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem) | Windows Server 2012 R2, 2012, 2008 R2 (Zobacz uwagi poniżej) | Za pomocą rozszerzenia | Yes |
| Trend Micro — wszystkie wersje | Rodzina systemów Windows Server  | Nie | Tak |
| Symantec — w wersji co najmniej 12.1.1100| Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina systemów Windows Server  | Nie | Yes |
| McAfee — w wersji co najmniej 10 | Rodzina serwerów z systemem Linux  | Nie | Opcję **\*** |
| Sophos V9 +| Rodzina serwerów z systemem Linux  | Nie | Opcję **\***  |

 **\*** Stan pokrycia i dane pomocnicze są obecnie dostępne tylko w obszarze roboczym Log Analytics skojarzonym z chronionymi subskrypcjami i nie są odzwierciedlone w portalu Azure Security Center.

> [!NOTE]
> - Wykrywanie programu System Center Endpoint Protection (SCEP) na maszynie wirtualnej z systemem Windows Server 2008 R2 wymaga zainstalowania protokołu SCEP po wystąpieniu programu PowerShell 3,0 (lub w górnej wersji).

## <a name="supported-paas-features"></a>Obsługiwane funkcje PaaS


|Usługa|Zalecenia (bezpłatnie)|Wykrywanie zagrożeń (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL|✔| ✔|
|Konta usługi Azure Blob Storage|✔| ✔|
|Usługi aplikacji|✔| ✔|
|Usługi w chmurze|✔| X|
|Sieci wirtualne|✔| Nie dotyczy|
|Podsieci|✔| Nie dotyczy|
|Karty interfejsów sieciowych|✔| Nie dotyczy|
|sieciowych grup zabezpieczeń|✔| Nie dotyczy|
|Subscription|✔ **| ✔|
|Batch|✔| Nie dotyczy|
|Service Fabric|✔| Nie dotyczy|
|Konto usługi Automation|✔| Nie dotyczy|
|Moduł równoważenia obciążenia|✔| Nie dotyczy|
|Wyszukaj|✔| Nie dotyczy|
|Magistrala usług|✔| Nie dotyczy|
|Stream Analytics|✔| Nie dotyczy|
|Centrum zdarzeń|✔| Nie dotyczy|
|Aplikacje logiki|✔| Nie dotyczy|
|Konto magazynu|✔| Nie dotyczy|
|Redis|✔| Nie dotyczy|
|Data Lake Analytics|✔| Nie dotyczy|
|Magazyn kluczy|✔| Nie dotyczy|




\*Te funkcje są obecnie obsługiwane w publicznej wersji zapoznawczej.

\*\*Zalecenia dotyczące usługi AAD są dostępne tylko dla subskrypcji standardowych



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center](security-center-planning-and-operations-guide.md).
- Dowiedz się więcej o [analizie behawioralnej maszyn wirtualnych i analizie pamięci zrzutu awaryjnego w Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Znajdowanie [często zadawanych pytań dotyczących korzystania z Azure Security Center](security-center-faq.md).
- Znajdź [wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure](https://blogs.msdn.com/b/azuresecurity/).
