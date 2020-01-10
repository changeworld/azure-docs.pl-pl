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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 7ffab200b50a7c4d1af60a230f237b18e8afc621
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754227"
---
# <a name="supported-features-available-in-azure-security-center"></a>Obsługiwane funkcje dostępne w Azure Security Center

> [!NOTE]
>Niektóre funkcje są dostępne tylko w warstwie Standardowa. Jeśli nie masz jeszcze konta w usłudze Security Center Standard, dostępny jest bezpłatny okres próbny. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Security Center](https://azure.microsoft.com/pricing/details/security-center/).

W poniższych sekcjach przedstawiono Security Center funkcje, które są dostępne dla [obsługiwanych platform](security-center-os-coverage.md).

* [Maszyny wirtualne/serwery](#vm-server-features)
* [Usługi PaaS Services](#paas-services)


## Funkcje obsługiwane przez maszynę wirtualną/serwer<a name="vm-server-features"></a>

### <a name="windowstabfeatures-windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Maszyny spoza platformy Azure**|**Cennik**
|[Integracja z programem Microsoft Defender ATP](security-center-wdatp.md)|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|Standardowa|
|[Alerty wykrywania zagrożeń analizy zachowań maszyn wirtualnych](security-center-alerts-iaas.md)|✔|✔|✔|Zalecenia (bezpłatne) wykrywanie zagrożeń (wersja standardowa)|
|[Alerty wykrywania zagrożeń bezplikowych](alerts-reference.md#alerts-windows)|✔|✔|✔|Standardowa|
|[Alerty dotyczące wykrywania zagrożeń opartych na sieci](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standardowa|
|[Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md)|✔|-|-|Standardowa|
|[Ocena natywnych luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standardowa|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardowa|
|[Kontrolki adaptacyjne aplikacji](security-center-adaptive-application.md)|✔|-|✔|Standardowa|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardowa|
|[Adaptacyjne ograniczanie sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standardowa|
|Adaptacyjne kontrolki sieci|✔|✔|-|Standardowa|
|[& Raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standardowa|
|Zalecenia i wykrywanie zagrożeń w kontenerach IaaS hostowanych przez platformę Docker|-|-|-|Standardowa|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Bezpłatnie|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Bezpłatnie|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Bezpłatnie|
|Ocena szyfrowania dysku|✔|✔|-|Bezpłatnie|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Bezpłatnie|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Bezpłatnie|


### <a name="linuxtabfeatures-linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Maszyny spoza platformy Azure**|**Cennik**
|[Integracja z programem Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Standardowa|
|[Alerty wykrywania zagrożeń analizy zachowań maszyn wirtualnych](security-center-alerts-iaas.md)|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|Zalecenia (bezpłatne) wykrywanie zagrożeń (wersja standardowa)|
|[Alerty wykrywania zagrożeń bezplikowych](alerts-reference.md#alerts-windows)|-|-|-|Standardowa|
|[Alerty dotyczące wykrywania zagrożeń opartych na sieci](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standardowa|
|[Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md)|✔|-|-|Standardowa|
|[Ocena natywnych luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standardowa|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardowa|
|[Kontrolki adaptacyjne aplikacji](security-center-adaptive-application.md)|✔|-|✔|Standardowa|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardowa|
|[Adaptacyjne ograniczanie sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standardowa|
|Adaptacyjne kontrolki sieci|✔|✔|-|Standardowa|
|[& Raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standardowa|
|Zalecenia i wykrywanie zagrożeń w kontenerach IaaS hostowanych przez platformę Docker|✔|✔|✔|Standardowa|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Bezpłatnie|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Bezpłatnie|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Bezpłatnie|
|Ocena szyfrowania dysku|✔|✔|-|Bezpłatnie|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Bezpłatnie|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Bezpłatnie|

--- 

## Obsługiwane rozwiązania programu Endpoint Protection<a name="endpoint-supported"></a>

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
> - Wykrywanie programu System Center Endpoint Protection (SCEP) na maszynie wirtualnej z systemem Windows Server 2008 R2 wymaga zainstalowania protokołu SCEP po wystąpieniu programu PowerShell 3,0 (lub w górnej wersji).
> - Wykrywanie programu Trend Micro Protection jest obsługiwane przez agentów zabezpieczeń systemu głębokiego.  Agenci OfficeScan nie są obsługiwani.


## Obsługiwane funkcje <a name="paas-services"></a> usług PaaS Services

Następujące zasoby PaaS są obsługiwane przez Azure Security Center:

|Usługa|Zalecenia (bezpłatnie)|Alerty wykrywania zagrożeń (standardowa)|Ocena luk w zabezpieczeniach (standardowa)|
|----|:----:|:----:|:----:|
|Bazy danych SQL|✔|✔|✔|
|Azure Container Registry|-|-|✔|
|Azure Kubernetes Service|✔|✔|-|
|Azure Database for PostgreSQL *|✔|✔|-|
|Azure Database for MySQL *|✔|✔|-|
|Azure CosmosDB *|-|✔|-|
|Konta magazynu|✔|-|-|
|Blob Storage|✔|✔|-|
|App Service|✔|✔|-|
|Aplikacja funkcji|✔|-|-|
|Cloud Services|✔|-|-|
|Sieć wirtualna|✔|-|-|
|Podsieć|✔|-|-|
|Karta sieciowa|✔|-|-|
|Sieciowe grupy zabezpieczeń|✔|-|-|
|Subskrypcja|✔ **|✔|-|
|Konto usługi Batch|✔|-|-|
|Konto Service Fabric|✔|-|-|
|Konto usługi Automation|✔|-|-|
|Równoważenie obciążenia|✔|-|-|
|Wyszukiwanie poznawcze|✔|-|-|
|Przestrzeń nazw magistrali usług|✔|-|-|
|Stream Analytics|✔|-|-|
|Przestrzeń nazw centrum zdarzeń|✔|-|-|
|Aplikacje logiki|✔|-|-|
|Pamięć podręczna dla Redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Magazyn kluczy|✔|✔ *|-|

\* te funkcje są obecnie obsługiwane w wersji zapoznawczej.

zalecenia dotyczące \*\* Azure Active Directory (Azure AD) są dostępne tylko dla subskrypcji w warstwie Standardowa.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane i log Analytics agenta](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Dowiedz się, jak [planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center](security-center-planning-and-operations-guide.md).
- Przejrzyj [platformy, które obsługują Centrum zabezpieczeń](security-center-os-coverage.md).
- Dowiedz się więcej [na temat wykrywania zagrożeń dla maszyn wirtualnych & serwerów w Azure Security Center](security-center-alerts-iaas.md).
- Znajdowanie [często zadawanych pytań dotyczących korzystania z Azure Security Center](security-center-faq.md).
- Znajdź [wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure](https://blogs.msdn.com/b/azuresecurity/).