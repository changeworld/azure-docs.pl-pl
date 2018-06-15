---
title: Łączenie Microsoft Advanced Threat Analytics do Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Centrum zabezpieczeń Azure umożliwia integrację z Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: a3444b9d42ffdd5f81568f0e9e09557096b4415f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32775925"
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Łączącego Microsoft Advanced Threat Analytics do Centrum zabezpieczeń Azure
Ten dokument pomaga skonfigurować integrację Centrum zabezpieczeń Azure i Microsoft Advanced Threat Analytics.

## <a name="why-add-advanced-threat-analytics-data"></a>Dlaczego należy dodać dane Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) jest lokalna platforma, która pomaga wykrywać podejrzane użytkownika zachowania. Po podłączeniu, będą mogli wyświetlić podejrzanych działań wykrytych przez usługę ATA w Centrum zabezpieczeń. Integracja umożliwia wyświetlanie, korelowania i badanie wszystkie alerty zabezpieczeń związane z obciążeń chmury hybrydowej w Centrum zabezpieczeń. 

## <a name="how-do-i-configure-this-integration"></a>Jak skonfigurować tej integracji?
Przy założeniu, że masz już instalacji usługi ATA i działa prawidłowo lokalnych, wykonaj następujące kroki, aby skonfigurować tej integracji:

1. Zaloguj się do Centrum usługi ATA i uzyskać dostęp do portalu usługi ATA.
2. Kliknij przycisk **serwera Syslog** w okienku po lewej stronie.

    ![Serwer SYSLOG](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. W **punkt końcowy serwera Syslog** pola, wpisz 127.0.0.7 (musi być ten adres), a następnie wpisz od 5114 portu (zalecane). Numer portu jest zalecane, powinny działać dowolnego portu unikatowy. Pozostaw innych opcji jest, a następnie kliknij przycisk **zapisać**.
4. Kliknij przycisk **powiadomienia** w okienku po lewej stronie i Włącz powiadomienia Syslog (zalecane), jak pokazano na poniższej ilustracji:

    ![Powiadomienia](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Kliknij pozycję **Zapisz**.
6. Otwórz pulpit nawigacyjny usługi **Security Center**.
7. W okienku po lewej stronie, kliknij polecenie **rozwiązań zabezpieczeń**.
8. W obszarze **Advanced Threat Analytics**, kliknij przycisk **dodać**.

    ![USŁUGI ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Przejdź do ostatniego kroku, a następnie kliknij przycisk **pobierania agenta**.

    ![USŁUGI ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. W **Dodaj nowy komputer z systemem innym niż Azure** wybierz obszar roboczy.

    ![Poza platformą Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. W **bezpośredniego agenta** strony, Pobierz odpowiedniego agenta systemu Windows i zanotować **identyfikator obszaru roboczego** i **klucz podstawowy**.

    ![Bezpośrednie agenta](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Zainstaluj agenta w Centrum usługi ATA. Podczas instalacji, upewnij się, że opcja **Połącz agenta z usługą Analiza dzienników Azure**i podaj *identyfikator obszaru roboczego*, i *klucz podstawowy* na żądanie.


Po zakończeniu instalacji, zakończeniu integracji i można zobaczyć nowe alerty wysyłane z usługi ATA do Centrum zabezpieczeń w **wyszukiwania** wynik. Rozwiązanie wydaje w **rozwiązań zabezpieczeń** w obszarze **połączone rozwiązań**. 

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób Microsoft ATA nawiązać połączenia z Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Connecting Azure Active Directory Identity Protection to Azure Security Center (Łączenie usługi Azure Active Directory Identity Protection z usługą Azure Security Center)](security-center-aadip-integration.md)
* [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
- [Bezpieczeństwo danych w Centrum zabezpieczeń Azure](security-center-data-security.md) — Dowiedz się, jak dane są zarządzane i w Centrum zabezpieczeń.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje zabezpieczeń platformy Azure i informacje.


