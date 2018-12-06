---
title: Podawanie szczegółów dotyczących kontaktu zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie pokazano, jak Podaj dane kontaktowe osoby w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: rkarlin
ms.openlocfilehash: 5ed7550b3582fe90bd581dff068e2a6e2c8b72d8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967439"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Podawanie szczegółów dotyczących kontaktu zabezpieczeń w usłudze Azure Security Center
Usługa Azure Security Center zaleci, podaj szczegóły dotyczące kontaktu zabezpieczeń dla subskrypcji platformy Azure, jeśli jeszcze nie. Te informacje będą używane przez firmę Microsoft do kontaktowania się z Tobą, gdy centrum Microsoft Security Response Center (MSRC) wykryje, że osoby nieupoważnione lub działające niezgodnie z prawem uzyskały dostęp do Twoich danych klienta. MSRC wykonuje monitorowania zabezpieczeń wybierz sieć platformy Azure i infrastrukturą i odbiera skarg analizy i nadużyć zagrożenia pochodzące od innych firm.

Wiadomość e-mail z powiadomieniem są wysyłane na pierwsze wystąpienie codzienne alertu i tylko w przypadku alertów o wysokiej ważności. Preferencje poczty e-mail można konfigurować tylko dla zasad subskrypcji. Grupy zasobów w ramach subskrypcji będą dziedziczyć te ustawienia.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W obszarze **zalecenia**, wybierz opcję **podawanie szczegółów dotyczących kontaktu zabezpieczeń**.
   ![Podaj kontaktu zabezpieczeń][1]
2. Wybierz subskrypcję platformy Azure, aby podać informacje kontaktowe na.
3. Spowoduje to otwarcie **wiadomości E-mail z powiadomieniami**.

   ![Podawanie szczegółów dotyczących kontaktu ds. zabezpieczeń][2]

   * Wprowadź adres kontaktowy adres e-mail zabezpieczeń lub adresów rozdzielonych przecinkami. Nie jest ograniczona liczba adresów e-mail, które można wprowadzić.
   * Wprowadź jeden zabezpieczeń międzynarodowy numer telefonu kontaktu.
   * Aby otrzymywać wiadomości e-mail o alertach o wysokiej ważności, włącz opcję **Wyślij do mnie wiadomości e-mail o alertach**.
   * W przyszłości będzie mieć możliwość wysyłania powiadomień e-mail do właścicieli subskrypcji. Ta opcja jest obecnie wyszarzone.
   * Wybierz **Zapisz** dotyczą informacje kontaktowe zabezpieczeń subskrypcji.

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-azure-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
