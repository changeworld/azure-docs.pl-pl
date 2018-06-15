---
title: Łączenie ochrony tożsamości usługi Azure Active Directory z Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Centrum zabezpieczeń Azure umożliwia integrację z usługi Azure Active Directory Identity Protection.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: b15508acea92c165cba85e39ab907fa524088164
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774186"
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Łączenie ochrony tożsamości usługi Azure Active Directory z Centrum zabezpieczeń Azure
Ten dokument pomaga skonfigurować integrację między ochroną tożsamości usługi Azure Active Directory (AD) i Centrum zabezpieczeń Azure.

## <a name="why-connect-azure-ad-identity-protection"></a>Dlaczego łączyć usługi Azure AD Identity Protection?
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) pomaga wykrywać potencjalne luki tożsamości organizacji. Kiedy połączenie, jest możliwość wyświetlania alertów usługi Azure AD Identity Protection w Centrum zabezpieczeń. Integracja umożliwia wyświetlanie, korelowania i badanie wszystkie alerty zabezpieczeń związane z obciążeń chmury hybrydowej w Centrum zabezpieczeń. 

## <a name="how-do-i-configure-this-integration"></a>Jak skonfigurować tej integracji?
Jeśli organizacja już korzysta z usługi Azure AD Identity Protection, wykonaj poniższe kroki, aby skonfigurować integrację:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W okienku po lewej stronie, kliknij polecenie **rozwiązań zabezpieczeń**. Centrum zabezpieczeń automatycznie odnajduje, jeśli program Azure AD Identity Protection jest włączona dla Twojej organizacji.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Kliknij przycisk **CONNECT**.
4. W **zintegrować usługę Azure AD Identity Protection** strony, przewiń w dół i wybierz odpowiedniego obszaru roboczego:

    ![Obszar roboczy](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Kliknij przycisk **Połącz**.

Po zakończeniu tej konfiguracji rozwiązania Azure AD Identity Protection pojawia się w **rozwiązań zabezpieczeń** w obszarze **połączone rozwiązań**. 

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób nawiązania połączenia Centrum zabezpieczeń Azure AD Identity Protection. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Connecting Microsoft Advanced Threat Analytics to Azure Security Center (Łączenie usługi Microsoft Advanced Threat Analytics z usługą Azure Security Center)](security-center-ata-integration.md)
* [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
- [Bezpieczeństwo danych w Centrum zabezpieczeń Azure](security-center-data-security.md) — Dowiedz się, jak dane są zarządzane i w Centrum zabezpieczeń.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje zabezpieczeń platformy Azure i informacje.


