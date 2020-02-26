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
ms.openlocfilehash: 8700421551af227f158abaa38d5f96c8e2987ba3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603394"
---
# <a name="supported-features-available-in-azure-security-center"></a>Obsługiwane funkcje dostępne w Azure Security Center

> [!NOTE]
>Niektóre funkcje są dostępne tylko w warstwie Standardowa. Jeśli nie masz jeszcze konta w usłudze Security Center Standard, dostępny jest bezpłatny okres próbny. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Security Center](https://azure.microsoft.com/pricing/details/security-center/).

W poniższych sekcjach przedstawiono Security Center funkcje, które są dostępne dla [obsługiwanych platform](security-center-os-coverage.md).

* [Maszyny wirtualne/serwery](#vm-server-features)
* [Usługi PaaS Services](#paas-services)


## Funkcje obsługiwane przez maszynę wirtualną/serwer<a name="vm-server-features"></a>

### <a name="windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Virtual Machine Scale Sets platformy Azure**|**Maszyny spoza platformy Azure**|**Cennik**
|[Integracja z programem Microsoft Defender ATP](security-center-wdatp.md)|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|Standardowy|
|[Alerty wykrywania zagrożeń analizy zachowań maszyn wirtualnych](security-center-alerts-iaas.md)|✔|✔|✔|Zalecenia (bezpłatne) wykrywanie zagrożeń (wersja standardowa)|
|[Alerty wykrywania zagrożeń bezplikowych](alerts-reference.md#alerts-windows)|✔|✔|✔|Standardowy|
|[Alerty dotyczące wykrywania zagrożeń opartych na sieci](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standardowy|
|[Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md)|✔|-|-|Standardowy|
|[Ocena natywnych luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standardowy|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardowy|
|[Kontrolki adaptacyjne aplikacji](security-center-adaptive-application.md)|✔|-|✔|Standardowy|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardowy|
|[Adaptacyjne ograniczanie sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standardowy|
|Adaptacyjne kontrolki sieci|✔|✔|-|Standardowy|
|[& Raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standardowy|
|Zalecenia i wykrywanie zagrożeń w kontenerach IaaS hostowanych przez platformę Docker|-|-|-|Standardowy|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Wolne|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Wolne|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Wolne|
|Ocena szyfrowania dysku|✔|✔|-|Wolne|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Wolne|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Wolne|


### <a name="linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Virtual Machine Scale Sets platformy Azure**|**Maszyny spoza platformy Azure**|**Cennik**
|[Integracja z programem Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Standardowy|
|[Alerty wykrywania zagrożeń analizy zachowań maszyn wirtualnych](security-center-alerts-iaas.md)|✔ (w obsługiwanych wersjach)|✔ (w obsługiwanych wersjach)|✔|Zalecenia (bezpłatne) wykrywanie zagrożeń (wersja standardowa)|
|[Alerty wykrywania zagrożeń bezplikowych](alerts-reference.md#alerts-windows)|-|-|-|Standardowy|
|[Alerty dotyczące wykrywania zagrożeń opartych na sieci](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standardowy|
|[Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md)|✔|-|-|Standardowy|
|[Ocena natywnych luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standardowy|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardowy|
|[Kontrolki adaptacyjne aplikacji](security-center-adaptive-application.md)|✔|-|✔|Standardowy|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardowy|
|[Adaptacyjne ograniczanie sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standardowy|
|Adaptacyjne kontrolki sieci|✔|✔|-|Standardowy|
|[& Raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standardowy|
|Zalecenia i wykrywanie zagrożeń w kontenerach IaaS hostowanych przez platformę Docker|✔|✔|✔|Standardowy|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Wolne|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Wolne|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Wolne|
|Ocena szyfrowania dysku|✔|✔|-|Wolne|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Wolne|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Wolne|

--- 

## Obsługiwane rozwiązania programu Endpoint Protection<a name="endpoint-supported"></a>

Poniższa tabela zawiera macierz:

 - Czy można użyć Azure Security Center, aby zainstalować każde rozwiązanie.
 - Security Center można odnaleźć rozwiązań programu Endpoint Protection. Jeśli zostanie odnalezione rozwiązanie programu Endpoint Protection z tej listy, Security Center nie zaleca się instalowania go.

Aby uzyskać informacje o tym, kiedy są generowane zalecenia dla każdej z tych ochrony, zobacz [Endpoint Protection oceny i rekomendacji](security-center-endpoint-protection.md).

| Program Endpoint Protection| Platformy | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
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

|NDES|Zalecenia (bezpłatnie)|Alerty wykrywania zagrożeń (standardowa)|Ocena luk w zabezpieczeniach (standardowa)|
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
|Usługi w chmurze|✔|-|-|
|Sieć wirtualna|✔|-|-|
|Subnet|✔|-|-|
|Karta sieciowa|✔|-|-|
|Grupy zabezpieczeń sieci|✔|-|-|
|Subskrypcja|✔ **|✔|-|
|Konto usługi Batch|✔|-|-|
|Konto Service Fabric|✔|-|-|
|Konto usługi Automation|✔|-|-|
|Moduł równoważenia obciążenia|✔|-|-|
|Cognitive Search|✔|-|-|
|Przestrzeń nazw magistrali usług|✔|-|-|
|Stream Analytics|✔|-|-|
|Przestrzeń nazw centrum zdarzeń|✔|-|-|
|Aplikacje logiki|✔|-|-|
|Pamięć podręczna dla Redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Usługa Key Vault|✔|✔ *|-|

\* te funkcje są obecnie obsługiwane w wersji zapoznawczej.

zalecenia dotyczące \*\* Azure Active Directory (Azure AD) są dostępne tylko dla subskrypcji w warstwie Standardowa.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane i log Analytics agenta](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Dowiedz się, jak [planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center](security-center-planning-and-operations-guide.md).
- Przejrzyj [platformy, które obsługują Centrum zabezpieczeń](security-center-os-coverage.md).
- Dowiedz się więcej [na temat wykrywania zagrożeń dla maszyn wirtualnych & serwerów w Azure Security Center](security-center-alerts-iaas.md).
- Znajdź [często zadawane pytania dotyczące Azure Security Center](faq-general.md).
- Znajdź [wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure](https://blogs.msdn.com/b/azuresecurity/).