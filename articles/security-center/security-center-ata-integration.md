---
title: Łączenie z Microsoft Advanced Threat Analytics w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Azure Security Center integruje się z Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: 6129c83e33d046c2fc9ce1724a27efce5efdfa0e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258803"
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Połączenie Microsoft Advanced Threat Analytics w usłudze Azure Security Center
Ten dokument pomoże Ci skonfigurować integrację między usługą Microsoft Advanced Threat Analytics i Azure Security Center.

## <a name="why-add-advanced-threat-analytics-data"></a>Dlaczego warto dodać dane usługi Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) jest lokalną platformą, która pomaga wykrywać użytkownika podejrzanego zachowania. Po nawiązaniu połączenia możesz będą mogli wyświetlić podejrzanych działań wykrytych przez usługę ATA w Centrum zabezpieczeń. Ta integracja umożliwia wyświetlanie, korelowanie i zbadać wszystkie alerty zabezpieczeń związane z obciążeń chmury hybrydowej w usłudze Security Center. 

## <a name="how-do-i-configure-this-integration"></a>Jak skonfigurować tę integrację?
Przy założeniu, że masz już instalacji usługi ATA i działa prawidłowo w środowisku lokalnym, wykonaj następujące kroki, aby skonfigurować tę integrację:

1. Zaloguj się do Centrum usługi ATA i uzyskać dostęp do portalu usługi ATA.
2. Kliknij przycisk **serwera Syslog** w okienku po lewej stronie.

    ![Serwer SYSLOG](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. W **punkt końcowy serwera Syslog** pola, wpisz 127.0.0.7 (musi być ten adres), a następnie wpisz od 5114 portu (zalecane). Gdy numer portu jest zalecany, każdy unikalny port powinien działać. Pozostaw inne opcje, i kliknij przycisk **Zapisz**.
4. Kliknij przycisk **powiadomienia** w okienku po lewej stronie i włączyć powiadomienia dziennika systemu (zalecane), jak pokazano na poniższej ilustracji:

    ![Powiadomienia](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Kliknij pozycję **Zapisz**.
6. Otwórz pulpit nawigacyjny usługi **Security Center**.
7. W okienku po lewej stronie, kliknij polecenie **rozwiązania w zakresie bezpieczeństwa**.
8. W obszarze **Advanced Threat Analytics**, kliknij przycisk **Dodaj**.

    ![USŁUGI ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Przejdź do ostatniego kroku, a następnie kliknij przycisk **Pobierz agenta**.

    ![USŁUGI ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. W **Dodaj nowy komputer spoza platformy Azure** wybierz obszar roboczy.

    ![Poza platformą Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. W **Agent bezpośredni** strony, Pobierz odpowiedniego agenta Windows i zanotować **identyfikator obszaru roboczego** i **klucz podstawowy**.

    ![Agent bezpośredni](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. W Centrum usługi ATA, należy zainstalować tego agenta. Podczas instalacji upewnij się wybrać opcję **Połącz agenta z usługą Azure Log Analytics**i podaj *identyfikator obszaru roboczego*, i *klucz podstawowy* żądanie.


Po zakończenia procesu instalacji, integracja zostało zakończone i będzie można zobaczyć nowe alerty wysyłane z usługi ATA do usługi Security Center w **wyszukiwania** wynik. To rozwiązanie jest wyświetlana w **rozwiązania w zakresie bezpieczeństwa** w obszarze **rozwiązania połączone**. 

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie pokazaliśmy ci, jak połączyć program Microsoft ATA z Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Connecting Azure Active Directory Identity Protection to Azure Security Center (Łączenie usługi Azure Active Directory Identity Protection z usługą Azure Security Center)](security-center-aadip-integration.md)
* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
- [Bezpieczeństwo danych w usłudze Azure Security Center](security-center-data-security.md) — Dowiedz się, jak jest zarządzane i chronione są dane w usłudze Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.


