---
title: Proces łączenia usługi Azure Active Directory Identity Protection do usługi Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Azure Security Center integruje się z usługi Azure Active Directory Identity Protection.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 6ab3611d47b32eddd41736df69a3e7382c2b220d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298765"
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Proces łączenia usługi Azure Active Directory Identity Protection do usługi Azure Security Center
Ten dokument pomoże Ci skonfigurować integrację między usługami Azure Active Directory (AD) Identity Protection i usługa Azure Security Center.

## <a name="why-connect-azure-ad-identity-protection"></a>Dlaczego łączy się z usługi Azure AD Identity Protection?
[Usługa Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) pomaga wykrywać potencjalne luki mające wpływ na tożsamości w organizacji. Po nawiązaniu połączenia możesz będą mogli wyświetlać alerty usługi Azure AD Identity Protection w usłudze Security Center. Ta integracja umożliwia wyświetlanie, korelowanie i zbadać wszystkie alerty zabezpieczeń związane z obciążeń chmury hybrydowej w usłudze Security Center. 

## <a name="how-do-i-configure-this-integration"></a>Jak skonfigurować tę integrację?
Jeśli Twoja organizacja używa już usługi Azure AD Identity Protection, wykonaj poniższe kroki, aby skonfigurować integrację:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W okienku po lewej stronie, kliknij polecenie **rozwiązania w zakresie bezpieczeństwa**. Usługa Security Center automatycznie wykrywa, czy usługi Azure AD Identity Protection jest włączony dla Twojej organizacji.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Kliknij przycisk **CONNECT**.
4. W **integracji usługi Azure AD Identity Protection** strony, przewiń w dół i wybierz odpowiedni obszar roboczy:

    ![obszar roboczy](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Kliknij przycisk **Połącz**.

Po ukończeniu tej konfiguracji rozwiązania usługi Azure AD Identity Protection jest wyświetlana w **rozwiązania w zakresie bezpieczeństwa** w obszarze **rozwiązania połączone**. 

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączeń z Centrum zabezpieczeń Azure AD Identity Protection. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Connecting Microsoft Advanced Threat Analytics to Azure Security Center (Łączenie usługi Microsoft Advanced Threat Analytics z usługą Azure Security Center)](security-center-ata-integration.md)
* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
- [Bezpieczeństwo danych w usłudze Azure Security Center](security-center-data-security.md) — Dowiedz się, jak jest zarządzane i chronione są dane w usłudze Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.


