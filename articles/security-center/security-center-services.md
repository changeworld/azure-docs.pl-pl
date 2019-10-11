---
title: Obsługiwane funkcje dostępne w Azure Security Center | Microsoft Docs
description: Ten dokument zawiera listę usług obsługiwanych przez Azure Security Center.
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
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: ed231bc05d58a40c93cea74081c027e5b49f5306
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254510"
---
# <a name="supported-features-available-in-azure-security-center"></a>Obsługiwane funkcje dostępne w Azure Security Center

> [!NOTE]
>Niektóre funkcje są dostępne tylko w warstwie Standardowa. Jeśli nie masz jeszcze konta w usłudze Security Center Standard, dostępny jest bezpłatny okres próbny. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Security Center](https://azure.microsoft.com/pricing/details/security-center/).

W poniższych sekcjach przedstawiono Security Center funkcje, które są dostępne dla [obsługiwanych platform](security-center-os-coverage.md).

* [Maszyny wirtualne/serwery](#vm-server-features)
* [Usługi PaaS Services](#paas-services)


## Funkcje obsługiwane przez maszynę wirtualną/serwer<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Serwer|Windows|||Linux|||Cennik|
|----|----|----|----|----|----|----|----|
|**Środowisko**|**Azure**||**Poza platformą Azure**|**Azure**||**Poza platformą Azure**||
||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**|
|[Integracja z programem Microsoft Defender ATP](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|X|X|X|Standardowa (Standard)|
|[Alerty wykrywania zagrożeń analizy zachowań maszyn wirtualnych](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|Zalecenia (bezpłatne) wykrywanie zagrożeń (wersja standardowa)|
|[Alerty wykrywania zagrożeń bezplikowych](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standardowa (Standard)|
|[Alerty dotyczące wykrywania zagrożeń opartych na sieci](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|[Dostęp do maszyny wirtualnej just in Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standardowa (Standard)|
|[Monitorowanie integralności plików](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standardowa (Standard)|
|[Kontrolki adaptacyjne aplikacji](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standardowa (Standard)|
|[Mapa sieci](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|[Adaptacyjne ograniczanie sieci](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standardowa (Standard)|
|Adaptacyjne kontrolki sieci|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|[& Raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standardowa (Standard)|
|Zalecenia i wykrywanie zagrożeń w kontenerach IaaS hostowanych przez platformę Docker|X|X|X|✔|✔|✔|Standardowa (Standard)|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|✔|✔|✔|Bezpłatnie|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|✔|✔|✔|Bezpłatnie|
|[Ocena programu Endpoint Protection](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|Bezpłatnie|
|Ocena szyfrowania dysku|✔|✔|X|✔|✔|X|Bezpłatnie|
|Ocena luk w zabezpieczeniach innych firm|✔|X|X|✔|X|X|Bezpłatnie|
|[Ocena zabezpieczeń sieci]()|✔|✔|X|✔|✔|X|Bezpłatnie|

### Obsługiwane rozwiązania programu Endpoint Protection<a name="endpoint-supported"></a>

Poniższa tabela zawiera macierz:

 - Czy można użyć Azure Security Center, aby zainstalować każde rozwiązanie.
 - Security Center można odnaleźć rozwiązań programu Endpoint Protection. Jeśli zostanie odnalezione rozwiązanie programu Endpoint Protection z tej listy, Security Center nie zaleca się instalowania go.

Aby uzyskać informacje o tym, kiedy są generowane zalecenia dla każdej z tych ochrony, zobacz [Endpoint Protection oceny i rekomendacji](security-center-endpoint-protection.md).

| Ochrona punktów końcowych| Platformy | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
|------|------|-----|-----|
| Windows Defender (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem)| Windows Server 2016| Nie, wbudowana w system operacyjny| Tak |
| System Center Endpoint Protection (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem) | Windows Server 2012 R2, 2012, 2008 R2 (Zobacz uwagi poniżej) | Za pomocą rozszerzenia | Tak |
| Trend Micro — wszystkie wersje * | Rodzina systemów Windows Server  | Nie | Tak |
| Symantec — w wersji co najmniej 12.1.1100| Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina serwerów z systemem Linux  | Nie | Tak **\*** |
| Sophos V9 +| Rodzina serwerów z systemem Linux  | Nie | Tak **\***  |

 **\*** Stan pokrycia i dane pomocnicze są obecnie dostępne tylko w obszarze roboczym Log Analytics skojarzonym z chronionymi subskrypcjami. Nie jest to odzwierciedlone w portalu Azure Security Center.

> [!NOTE]
>
> - Wykrywanie programu System Center Endpoint Protection (SCEP) na maszynie wirtualnej z systemem Windows Server 2008 R2 wymaga zainstalowania protokołu SCEP po wystąpieniu programu PowerShell 3,0 (lub w górnej wersji).
> - Wykrywanie programu Trend Micro Protection jest obsługiwane przez agentów zabezpieczeń systemu głębokiego.  Agenci OfficeScan nie są obsługiwani.


## Obsługiwane funkcje <a name="paas-services"></a> usług PaaS Services

Następujące zasoby PaaS są obsługiwane przez Azure Security Center:

|Usługa|Zalecenia (bezpłatnie)|Wykrywanie zagrożeń (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL|✔| ✔|
|MySQL|✔| ✔|
|CosmosDB|X| ✔|
|Magazyn obiektów Blob|✔| ✔|
|Konto magazynu|✔| Nie dotyczy|
|Usługa App Service|✔| ✔|
|Funkcja|✔| X|
|Usługa w chmurze|✔| X|
|VNet|✔| Nie dotyczy|
|Podsieć|✔| Nie dotyczy|
|Karta sieciowa|✔| Nie dotyczy|
|Sieciowa grupa zabezpieczeń|✔| Nie dotyczy|
|Subskrypcja|✔ * *| ✔|
|Konto usługi Batch|✔| X|
|Konto usługi Service Fabric|✔| X|
|Konto usługi Automation|✔| X|
|Moduł równoważenia obciążenia|✔| X|
|Search|✔| X|
|Przestrzeń nazw usługi Service Bus|✔| X|
|Stream Analytics|✔| X|
|Przestrzeń nazw centrum zdarzeń|✔| X|
|Aplikacje logiki|✔| X|
|Redis|✔| Nie dotyczy|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Key Vault|✔| X|

\* te funkcje są obecnie obsługiwane w publicznej wersji zapoznawczej.

zalecenia dotyczące \* @ no__t-1 Azure Active Directory (Azure AD) są dostępne tylko w przypadku subskrypcji w warstwie Standardowa.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane i log Analytics agenta](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Dowiedz się, jak [planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center](security-center-planning-and-operations-guide.md).
- Przejrzyj [platformy, które obsługują Centrum zabezpieczeń](security-center-os-coverage.md).
- Dowiedz się więcej [na temat wykrywania zagrożeń dla maszyn wirtualnych & serwerów w Azure Security Center](security-center-alerts-iaas.md).
- Znajdowanie [często zadawanych pytań dotyczących korzystania z Azure Security Center](security-center-faq.md).
- Znajdź [wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure](https://blogs.msdn.com/b/azuresecurity/).
