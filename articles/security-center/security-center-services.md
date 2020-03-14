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
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245241"
---
# <a name="feature-coverage-for-machines"></a>Pokrycie funkcji dla maszyn

W poniższych tabelach przedstawiono funkcje Azure Security Center, które są dostępne dla maszyn wirtualnych i serwerów.

## Obsługiwane funkcje dla maszyn wirtualnych i serwerów<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Maszyny z systemem Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkcja**|**Azure Virtual Machines**|**Virtual Machine Scale Sets platformy Azure**|**Maszyny spoza platformy Azure**|**Cennik**
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


### <a name="linux-machines"></a>[Maszyny z systemem Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkcja**|**Azure Virtual Machines**|**Virtual Machine Scale Sets platformy Azure**|**Maszyny spoza platformy Azure**|**Cennik**
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


> [!TIP]
>Aby eksperymentować z funkcjami, które są dostępne tylko w warstwie cenowej standardowa, użytkownicy warstwy Bezpłatna mogą zarejestrować się w 30-dniowej wersji próbnej. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).


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


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane i log Analytics agenta](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Dowiedz się, jak [planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center](security-center-planning-and-operations-guide.md).
- Przejrzyj [platformy, które obsługują Centrum zabezpieczeń](security-center-os-coverage.md).
- Dowiedz się więcej o [ochronie przed zagrożeniami dla maszyn z systemami Windows i Linux w Azure Security Center](threat-protection.md#windows-machines).
- Znajdź [często zadawane pytania dotyczące Azure Security Center](faq-general.md).