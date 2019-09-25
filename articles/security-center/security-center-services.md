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
ms.openlocfilehash: ad662cdeb0a87e57eb9e3e7480d120be1c6c729e
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218262"
---
# <a name="supported-features-available-in-azure-security-center"></a>Obsługiwane funkcje dostępne w Azure Security Center

> [!NOTE]
>Niektóre funkcje są dostępne tylko w warstwie Standardowa. Jeśli nie masz jeszcze konta w usłudze Security Center Standard, dostępny jest bezpłatny okres próbny. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

W poniższych sekcjach przedstawiono Security Center funkcje, które są dostępne dla [obsługiwanych platform](security-center-os-coverage.md).

* [Maszyny wirtualne/serwery](#vm-server-features)
* [Usługi PaaS Services](#paas-services)


## Funkcje obsługiwane przez maszynę wirtualną/serwer<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Serwer|Windows|||Linux|||Cennik|
|----|----|----|----|----|----|----|----|
|**Środowisko**|**Azure**||**Poza platformą Azure**|**Azure**||**Poza platformą Azure**||
||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**||**Maszyna wirtualna**|**Zestaw skalowania maszyn wirtualnych**|
|Alerty wykrywania zagrożeń VMBA|✔|✔|✔|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|Zalecenia (bezpłatne) wykrywanie zagrożeń (wersja standardowa)|
|Alerty dotyczące wykrywania zagrożeń opartych na sieci|✔|✔|X|✔|✔|X|Standardowa (Standard)|
|Integracja z programem Microsoft Defender ATP|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|X|X|X|Standardowa (Standard)|
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

### Obsługiwane rozwiązania programu Endpoint Protection<a name="endpoint-supported"></a>

Poniższa tabela zawiera macierz:

 - Czy można użyć Azure Security Center, aby zainstalować każde rozwiązanie.
 - Security Center można odnaleźć rozwiązań programu Endpoint Protection. Jeśli jedno z tych rozwiązań programu Endpoint Protection zostanie wykryte, Security Center nie zaleca się instalowania go.

Aby uzyskać informacje o tym, kiedy są generowane zalecenia dla każdej z tych ochrony, zobacz [Endpoint Protection oceny i rekomendacji](security-center-endpoint-protection.md).

| Endpoint Protection| Platformy | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
|------|------|-----|-----|
| Windows Defender (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem)| Windows Server 2016| Nie, wbudowana w system operacyjny| Tak |
| System Center Endpoint Protection (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem) | Windows Server 2012 R2, 2012, 2008 R2 (Zobacz uwagi poniżej) | Za pomocą rozszerzenia | Tak |
| Trend Micro — wszystkie wersje * | Rodzina systemów Windows Server  | Nie | Tak |
| Symantec — w wersji co najmniej 12.1.1100| Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina serwerów z systemem Linux  | Nie | Opcję **\*** |
| Sophos V9 +| Rodzina serwerów z systemem Linux  | Nie | Opcję **\***  |

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
|PostGreSQL*|✔| ✔|
|MySQL|✔| ✔|
|CosmosDB|X| ✔|
|Blob Storage|✔| ✔|
|Konto magazynu|✔| Nie dotyczy|
|App Service|✔| ✔|
|Funkcja|✔| X|
|Usługa w chmurze|✔| X|
|Sieć wirtualna|✔| Nie dotyczy|
|Subnet|✔| Nie dotyczy|
|NIC|✔| Nie dotyczy|
|Sieciowa grupa zabezpieczeń|✔| Nie dotyczy|
|Subscription|✔ **| ✔|
|Konto usługi Batch|✔| X|
|Konto usługi Service Fabric|✔| X|
|Konto usługi Automation|✔| X|
|Moduł równoważenia obciążenia|✔| X|
|Wyszukiwanie|✔| X|
|Przestrzeń nazw magistrala usług|✔| X|
|Stream Analytics|✔| X|
|Przestrzeń nazw centrum zdarzeń|✔| X|
|Aplikacje logiki|✔| X|
|Redis|✔| Nie dotyczy|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Magazyn kluczy|✔| X|

\*Te funkcje są obecnie obsługiwane w publicznej wersji zapoznawczej.

\*\*Zalecenia dotyczące Azure Active Directory (Azure AD) są dostępne tylko w przypadku subskrypcji w warstwie Standardowa.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane i log Analytics agenta](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Dowiedz się, jak [planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center](security-center-planning-and-operations-guide.md).
- Przejrzyj [platformy, które obsługują Centrum zabezpieczeń](security-center-os-coverage.md).
- Dowiedz się więcej [na temat wykrywania zagrożeń dla maszyn wirtualnych & serwerów w Azure Security Center](security-center-alerts-iaas.md).
- Znajdowanie [często zadawanych pytań dotyczących korzystania z Azure Security Center](security-center-faq.md).
- Znajdź [wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure](https://blogs.msdn.com/b/azuresecurity/).
