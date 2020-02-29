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
ms.openlocfilehash: 14c54028dacb545532b540ae8ff79fc3dc5356bc
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921270"
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
|[Integracja z programem Microsoft Defender ATP](security-center-wdatp.md)|✔</br>(w obsługiwanych wersjach)|✔</br>(w obsługiwanych wersjach)|✔|Standard|
|[Analiza behawioralna maszyn wirtualnych (i alerty zabezpieczeń)](threat-protection.md)|✔|✔|✔|Zalecenia (bezpłatnie) </br></br> Alerty zabezpieczeń (standard)|
|[Alerty zabezpieczeń bezplikowych](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Alerty zabezpieczeń oparte na sieci](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Ocena natywnych luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Kontrolki adaptacyjne aplikacji](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptacyjne ograniczanie sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptacyjne kontrolki sieci|✔|✔|-|Standard|
|[& Raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Zalecenia i ochrona przed zagrożeniami w kontenerach IaaS hostowanych przez platformę Docker|-|-|-|Standard|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Bezpłatna|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Bezpłatna|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Bezpłatna|
|Ocena szyfrowania dysku|✔|✔|-|Bezpłatna|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Bezpłatna|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Bezpłatna|


### <a name="linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Virtual Machine Scale Sets platformy Azure**|**Maszyny spoza platformy Azure**|**Cennik**
|[Integracja z programem Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Standard|
|[Analiza behawioralna maszyn wirtualnych (i alerty zabezpieczeń)](security-center-alerts-iaas.md)|✔</br>(w obsługiwanych wersjach)|✔</br>(w obsługiwanych wersjach)|✔|Zalecenia (bezpłatnie) </br></br> Alerty zabezpieczeń (standard)|
|[Alerty zabezpieczeń bezplikowych](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Alerty zabezpieczeń oparte na sieci](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Ocena natywnych luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Kontrolki adaptacyjne aplikacji](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptacyjne ograniczanie sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptacyjne kontrolki sieci|✔|✔|-|Standard|
|[& Raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Zalecenia i ochrona przed zagrożeniami w kontenerach IaaS hostowanych przez platformę Docker|✔|✔|✔|Standard|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Bezpłatna|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Bezpłatna|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Bezpłatna|
|Ocena szyfrowania dysku|✔|✔|-|Bezpłatna|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Bezpłatna|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Bezpłatna|

--- 

## Obsługiwane rozwiązania programu Endpoint Protection<a name="endpoint-supported"></a>

Poniższa tabela zawiera macierz:

 - Czy można użyć Azure Security Center, aby zainstalować każde rozwiązanie.
 - Security Center można odnaleźć rozwiązań programu Endpoint Protection. Jeśli zostanie odnalezione rozwiązanie programu Endpoint Protection z tej listy, Security Center nie zaleca się instalowania go.

Aby uzyskać informacje o tym, kiedy są generowane zalecenia dla każdej z tych ochrony, zobacz [Endpoint Protection oceny i rekomendacji](security-center-endpoint-protection.md).

| Ochrona punktów końcowych| Platformy | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
|------|------|-----|-----|
| Windows Defender (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem)| Windows Server 2016| Nie, wbudowana w system operacyjny| Yes |
| System Center Endpoint Protection (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem) | Windows Server 2012 R2, 2012, 2008 R2 (Zobacz uwagi poniżej) | Za pomocą rozszerzenia | Yes |
| Trend Micro — wszystkie wersje * | Rodzina systemów Windows Server  | Nie | Yes |
| Symantec — w wersji co najmniej 12.1.1100| Rodzina systemów Windows Server  | Nie | Yes |
| McAfee — w wersji co najmniej 10 | Rodzina systemów Windows Server  | Nie | Yes |
| McAfee — w wersji co najmniej 10 | Rodzina serwerów z systemem Linux  | Nie | Tak **\*** |
| Sophos V9 +| Rodzina serwerów z systemem Linux  | Nie | Tak **\***  |

 **\*** Stan pokrycia i dane pomocnicze są obecnie dostępne tylko w obszarze roboczym Log Analytics skojarzonym z chronionymi subskrypcjami. Nie jest to odzwierciedlone w portalu Azure Security Center.

> [!NOTE]
> - Wykrywanie programu System Center Endpoint Protection (SCEP) na maszynie wirtualnej z systemem Windows Server 2008 R2 wymaga zainstalowania protokołu SCEP po wystąpieniu programu PowerShell 3,0 (lub w górnej wersji).
> - Wykrywanie programu Trend Micro Protection jest obsługiwane przez agentów zabezpieczeń systemu głębokiego.  Agenci OfficeScan nie są obsługiwani.


## Obsługiwane funkcje <a name="paas-services"></a> usług PaaS Services

Następujące zasoby PaaS są obsługiwane przez Azure Security Center:

|Usługa|Zalecenia (bezpłatnie)|Alerty zabezpieczeń (standard)|Ocena luk w zabezpieczeniach (standardowa)|
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
|Virtual Network|✔|-|-|
|Podsieć|✔|-|-|
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
- Dowiedz się więcej o [ochronie przed zagrożeniami dla maszyn z systemami Windows i Linux w Azure Security Center](threat-protection.md#windows-machines).
- Znajdź [często zadawane pytania dotyczące Azure Security Center](faq-general.md).