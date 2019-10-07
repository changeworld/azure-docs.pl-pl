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
ms.openlocfilehash: d756f9dfbd0012f884bb0c4a1e27efc76d613234
ms.sourcegitcommit: 387da88b8262368c1b67fffea58fe881308db1c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71982839"
---
# <a name="supported-features-available-in-azure-security-center"></a>Obsługiwane funkcje dostępne w Azure Security Center

> [!NOTE]
>Niektóre funkcje są dostępne tylko w warstwie Standardowa. Jeśli nie masz jeszcze konta w usłudze Security Center Standard, dostępny jest bezpłatny okres próbny. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Security Center](https://azure.microsoft.com/pricing/details/security-center/) .

W poniższych sekcjach przedstawiono Security Center funkcje, które są dostępne dla [obsługiwanych platform](security-center-os-coverage.md).

* [Maszyny wirtualne/serwery](#vm-server-features)
* [Usługi PaaS Services](#paas-services)


## Funkcje obsługiwane przez maszynę wirtualną/serwer<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Serwer|Windows|||Linux|||Cennik|
|----|----|----|----|----|----|----|----|
|**Środowisko**|**Azure**||**Poza platformą Azure**|**Azure**||**Poza platformą Azure**||
||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**|
|Alerty wykrywania zagrożeń analizy zachowań maszyn wirtualnych|✔|✔|✔|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|Zalecenia (bezpłatne) wykrywanie zagrożeń (wersja standardowa)|
|Alerty dotyczące wykrywania zagrożeń opartych na sieci|✔|✔|X|✔|✔|X|Standardowa|
|Integracja z programem Microsoft Defender ATP|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|X|X|X|Standardowa|
|Brakujące poprawki|✔|✔|✔|✔|✔|✔|Zwolniony|
|Konfiguracje zabezpieczeń|✔|✔|✔|✔|✔|✔|Zwolniony|
|Ocena programu Endpoint Protection|✔|✔|✔|X|X|X|Zwolniony|
|Dostęp do maszyny wirtualnej just in Time|✔|X|X|✔|X|X|Standardowa|
|Adaptacyjne kontrole aplikacji|✔|X|✔|✔|X|✔|Standardowa|
|Monitorowanie integralności plików|✔|✔|✔|✔|✔|✔|Standardowa|
|Ocena szyfrowania dysku|✔|✔|X|✔|✔|X|Zwolniony|
|Wdrożenie innych firm|✔|X|X|✔|X|X|Zwolniony|
|Ocena sieciowej grupy zabezpieczeń|✔|✔|X|✔|✔|X|Zwolniony|
|Wykrywanie zagrożeń bezplikowych|✔|✔|✔|X|X|X|Standardowa|
|Mapa sieci|✔|✔|X|✔|✔|X|Standardowa|
|Adaptacyjne ograniczanie sieci|✔|X|X|✔|X|X|Standardowa|
|Adaptacyjne kontrolki sieci|✔|✔|X|✔|✔|X|Standardowa|
|& Raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami|✔|✔|✔|✔|✔|✔|Standardowa|
|Zalecenia i wykrywanie zagrożeń w kontenerach IaaS hostowanych przez platformę Docker|X|X|X|✔|✔|✔|Standardowa|

### Obsługiwane rozwiązania programu Endpoint Protection<a name="endpoint-supported"></a>

Poniższa tabela zawiera macierz:

 - Czy można użyć Azure Security Center, aby zainstalować każde rozwiązanie.
 - Security Center można odnaleźć rozwiązań programu Endpoint Protection. Jeśli jedno z tych rozwiązań programu Endpoint Protection zostanie wykryte, Security Center nie zaleca się instalowania go.

Aby uzyskać informacje o tym, kiedy są generowane zalecenia dla każdej z tych ochrony, zobacz [Endpoint Protection oceny i rekomendacji](security-center-endpoint-protection.md).

| Program Endpoint Protection| Platformy | Instalacja Security Center | Odnajdywanie Security Center |
|------|------|-----|-----|
| Windows Defender (oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft)| Windows Server 2016| Nie, wbudowana w system operacyjny| Tak |
| System Center Endpoint Protection (oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (Zobacz uwagi poniżej) | Za pośrednictwem rozszerzenia | Tak |
| Trend Micro — wszystkie wersje * | Rodzina systemów Windows Server  | Nie | Tak |
| Symantec v 12.1.1100 +| Rodzina systemów Windows Server  | Nie | Tak |
| McAfee v10 + | Rodzina systemów Windows Server  | Nie | Tak |
| McAfee v10 + | Rodzina serwerów z systemem Linux  | Nie | Tak **\*** |
| Sophos V9 +| Rodzina serwerów z systemem Linux  | Nie | Tak **\***  |

 **\*** Stan pokrycia i dane pomocnicze są obecnie dostępne tylko w obszarze roboczym Log Analytics skojarzonym z chronionymi subskrypcjami i nie są odzwierciedlone w portalu Azure Security Center.

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
|Blob Storage|✔| ✔|
|Konto magazynu|✔| POTRĄCON|
|Usługa App Service|✔| ✔|
|Funkcja|✔| X|
|Usługa w chmurze|✔| X|
|Environment|✔| POTRĄCON|
|Podsieci|✔| POTRĄCON|
|10/100/1000|✔| POTRĄCON|
|SIECIOWEJ grupy zabezpieczeń|✔| POTRĄCON|
|Ramach|✔ * *| ✔|
|Konto wsadowe|✔| X|
|Konto usługi Service Fabric|✔| X|
|Konto usługi Automation|✔| X|
|Moduł równoważenia obciążenia|✔| X|
|Wyszukaj|✔| X|
|Przestrzeń nazw usługi Service Bus|✔| X|
|Stream Analytics|✔| X|
|Przestrzeń nazw centrum zdarzeń|✔| X|
|Aplikacje logiki|✔| X|
|Redis|✔| POTRĄCON|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Magazyn kluczy|✔| X|

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
