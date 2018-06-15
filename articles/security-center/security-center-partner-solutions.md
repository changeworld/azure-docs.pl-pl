---
title: Zarządzanie połączonych partnerskich rozwiązań w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument przedstawia sposób, w jaki Centrum zabezpieczeń Azure umożliwia ogólne monitorowanie kondycji rozwiązań partnerskich zintegrowanych z subskrypcją platformy Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2017
ms.locfileid: "23891027"
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Zarządzanie połączonych rozwiązań partnerskich w Centrum zabezpieczeń Azure
W tym artykule przedstawiono sposób zarządzania i monitorowania zabezpieczeń połączonych rozwiązań w Centrum zabezpieczeń Azure.

## <a name="monitoring-partner-solutions"></a>Monitorowanie rozwiązań partnerskich
Aby monitorować stan kondycji zabezpieczeń połączonych rozwiązań i wykonywania podstawowych operacji zarządzania:

1. W obszarze **Centrum zabezpieczeń — omówienie**, wybierz pozycję **rozwiązań zabezpieczeń**.

  ![Wybierz rozwiązań zabezpieczeń][1]

  **Połączone rozwiązań** sekcja zawiera rozwiązań zabezpieczeń, które są połączone z Centrum zabezpieczeń i informacje o stanie kondycji poszczególnych rozwiązań.

  ![Rozwiązania partnerskie][2]

   Stan rozwiązania partnerskiego można:

   * Zdrowy (zielony) — Brak problemów dotyczących kondycji.
   * W złej kondycji (kolor czerwony) — istnieje problem z kondycją wymagający natychmiastowej uwagi.
   * Problemy dotyczące kondycji (kolor pomarańczowy) — rozwiązanie zostało zatrzymane, raportowanie dotyczące kondycji.
   * Nie zgłoszono (kolor szary) — rozwiązanie nie zgłosił żadnych czynności, ale stan rozwiązania może zostać zgłoszony, jeśli został ostatnio podłączony i jego wdrażanie nadal trwa lub nie są dostępne żadne dane kondycji.

   > [!NOTE]
   > Jeśli nie ma danych stanu kondycji, Centrum zabezpieczeń zawiera datę i godzinę ostatniego zdarzenia odebrana, aby wskazać, czy rozwiązanie jest raportowania lub nie. Jeśli nie są dostępne żadne dane kondycji i alerty nie są odbierane w ciągu ostatnich 14 dni, Centrum zabezpieczeń oznacza, że rozwiązanie jest nieprawidłowy lub nie raportowania.
   >
   >

2. Wybierz **WIDOKU** dodatkowe informacje i opcje, w tym:

  - **Konsola rozwiązań**. Otwiera możliwości zarządzania dla tego rozwiązania.
  - **Łączenie maszyny Wirtualnej**. Otwiera blok łączenie aplikacji. W tym miejscu możesz połączyć zasoby z rozwiązaniem partnerskim.
  - **Usuwanie rozwiązania**.
  - **Skonfiguruj**.

   ![Szczegóły rozwiązania partnerskiego][3]

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób monitorowania rozwiązania połączonych zabezpieczeń w Centrum zabezpieczeń i zarządzania nimi. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Omówienie rozwiązań zabezpieczeń](security-center-partner-integration.md) — Dowiedz się, jak połączyć się i zarządzać rozwiązań zabezpieczeń.
* [Łączenie Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) — Dowiedz się, jak połączyć alertów z usługi ATA.
* [Łączenie ochrony tożsamości usługi Azure Active Directory (AD) ](security-center-aadip-integration.md) — Dowiedz się, jak połączyć alertów z usługi Azure AD Identity Protection.
* [Integracja z partnerami i rozwiązań](security-center-partner-integration.md) — Przegląd integrowania z innych rozwiązań zabezpieczeń.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
