---
title: Zarządzanie połączonych partnerem rozwiązań w usłudze Azure Security Center | Dokumentacja firmy Microsoft
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
ms.date: 08/20/2018
ms.author: terrylan
ms.openlocfilehash: 3d4c9c79ff90ee816719f0179fbb5096b0035854
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056316"
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Zarządzanie połączonych rozwiązań partnerskich w Centrum zabezpieczeń Azure
W tym artykule przedstawiono sposób zarządzania i monitorowania rozwiązań połączonych zabezpieczeń w usłudze Azure Security Center.

## <a name="monitoring-partner-solutions"></a>Monitorowanie rozwiązań partnerskich
Aby monitorować stan kondycji rozwiązania zabezpieczeń połączone i wykonywania podstawowych operacji zarządzania:

1. W obszarze **Security Center — Przegląd**, wybierz opcję **rozwiązania w zakresie bezpieczeństwa**.

  ![Wybierz rozwiązania zabezpieczeń][1]

  **Rozwiązania połączone** sekcja zawiera rozwiązania zabezpieczeń, które są podłączone do usługi Security Center i informacje o stanie kondycji poszczególnych rozwiązań.

  ![Rozwiązania partnerskie][2]

   Stan rozwiązania partnerskiego można:

   * Zdrowy (zielony) — Brak problemów dotyczących kondycji.
   * W złej kondycji (kolor czerwony) — istnieje problem z kondycją wymagający natychmiastowej uwagi.
   * Problemy dotyczące kondycji (kolor pomarańczowy) — rozwiązania zostało zatrzymane raportowanie dotyczące kondycji.
   * Niezgłoszone (kolor szary) — rozwiązanie nie zgłosiło niczego jeszcze, stan rozwiązania może zostać zgłoszony, jeśli został ostatnio połączony i jego wdrażanie nadal trwa lub nie są dostępne żadne dane kondycji.

   > [!NOTE]
   > Jeśli dane o stanie kondycji nie jest dostępna, usługa Security Center Pokazuje datę i godzinę ostatniego zdarzenia odebrana, aby wskazać, czy rozwiązanie jest raportowanie. Jeśli nie są dostępne żadne dane kondycji i alerty nie są odbierane w ciągu ostatnich 14 dni, usługa Security Center wskazuje, że rozwiązanie jest nieprawidłowy lub nie raportowania.
   >
   >

2. Wybierz **WIDOKU** dodatkowe informacje i opcji, która obejmuje:

  - **Konsola rozwiązań**. Otwiera środowisko zarządzania dla tego rozwiązania.
  - **Łączenie maszyny Wirtualnej**. Zostanie otwarty blok łączenie aplikacji. W tym miejscu możesz połączyć zasoby z rozwiązaniem partnerskim.
  - **Usuń rozwiązanie**.
  - **Konfigurowanie**.

   ![Szczegóły rozwiązania partnerskiego][3]

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób zarządzania i monitorowania rozwiązań połączonych zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Omówienie rozwiązania zabezpieczeń](security-center-partner-integration.md) — Dowiedz się, jak połączyć i zarządzanie rozwiązaniami zabezpieczeń.
* [Łączenie z Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) — Dowiedz się, jak połączyć alertów z usługi ATA.
* [Proces łączenia usługi Azure Active Directory (AD) Identity Protection ](security-center-aadip-integration.md) — Dowiedz się, jak nawiązać połączenie alertów z usługi Azure AD Identity Protection.
* [Integracja z partnerami i rozwiązaniami](security-center-partner-integration.md) — zapoznaj się z omówieniem integracji innych rozwiązań zabezpieczeń.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
