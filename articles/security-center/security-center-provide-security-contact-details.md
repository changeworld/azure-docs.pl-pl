---
title: Podaj szczegóły kontaktu zabezpieczeń w Azure Security Center | Microsoft Docs
description: W tym dokumencie pokazano, jak zapewnić szczegóły dotyczące kontaktu z zabezpieczeniami w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: fabccb7a3f49442a009fd69ee6d3b831760751d2
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201027"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Podaj szczegóły kontaktu zabezpieczeń w Azure Security Center
W Azure Security Center zalecamy podanie szczegółowych informacji o kontakcie zabezpieczeń dla subskrypcji platformy Azure, jeśli jeszcze tego nie zrobiono. Te informacje będą używane przez firmę Microsoft do kontaktowania się z Tobą, gdy centrum Microsoft Security Response Center (MSRC) wykryje, że osoby nieupoważnione lub działające niezgodnie z prawem uzyskały dostęp do Twoich danych klienta. Usługa MSRC wybiera pozycję monitorowanie zabezpieczeń sieci i infrastruktury platformy Azure, a następnie odbiera analizę zagrożeń i nieodpowiednie skargi od stron trzecich.

Powiadomienie e-mail jest wysyłane po pierwszym wystąpieniu alertu w ciągu dnia i tylko w przypadku alertów o wysokiej ważności. Preferencje poczty e-mail można konfigurować tylko dla zasad subskrypcji. Grupy zasobów w ramach subskrypcji będą dziedziczyć te ustawienia. 

Powiadomienia e-mail o alertach są wysyłane:
- Tylko w przypadku alertów o wysokiej ważności
- Do pojedynczego adresata wiadomości e-mail na każdy typ alertu dziennie  
- Nie więcej niż 3 wiadomości e-mail są wysyłane do pojedynczego odbiorcy w jednym dniu
- Każda wiadomość e-mail zawiera jeden alert, a nie agregację alertów
 
Jeśli na przykład została już wysłana wiadomość e-mail z powiadomieniem o ataku przez protokół RDP, tego samego dnia nie otrzymasz kolejnej wiadomości e-mail o ataku przez protokół RDP, nawet jeśli zostanie wyzwolony kolejny alert. 

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.

## Konfigurowanie powiadomień e-mail dla alertów<a name="email"></a>

1. W portalu wybierz pozycję **Cennik ustawienia &** .
1. Kliknij subskrypcję.
1. Kliknij pozycję **powiadomienia e-mail**.

> [!NOTE]
> Jeśli wdrażasz zalecenie, w obszarze **rekomendacje**wybierz pozycję **Podaj szczegóły kontaktu zabezpieczeń**, wybierz subskrypcję platformy Azure, w której mają zostać wprowadzone informacje kontaktowe. Spowoduje to otwarcie **powiadomień e-mail**.

   ![Podaj szczegóły kontaktu ds. zabezpieczeń][2]

   * Wprowadź adres lub adresy e-mail kontaktu z zabezpieczeniami oddzielone przecinkami. Nie ma limitu liczby adresów e-mail, które można wprowadzić.
   * Wprowadź jeden międzynarodowy numer telefonu kontaktu z zabezpieczeniami.
   * Aby otrzymywać wiadomości e-mail o alertach o wysokiej ważności, Włącz opcję **Wyślij mi wiadomości e-mail o alertach**.
   * W przyszłości będziesz mieć możliwość wysyłania powiadomień e-mail do właścicieli subskrypcji. Ta opcja jest obecnie wyszarzona.
   * Wybierz pozycję **Zapisz** , aby zastosować informacje o kontakcie zabezpieczeń do subskrypcji.

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
