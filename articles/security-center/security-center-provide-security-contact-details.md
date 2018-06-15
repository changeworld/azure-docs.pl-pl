---
title: Podaj szczegóły dotyczące kontaktu zabezpieczeń w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument przedstawia sposób podać szczegóły dotyczące kontaktu zabezpieczeń w Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: terrylan
ms.openlocfilehash: 726b59c45e2eb18eebe28a180db23336ae141408
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866527"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Podaj szczegóły dotyczące kontaktu zabezpieczeń w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure zaleca Podaj szczegóły dotyczące kontaktu zabezpieczeń dla subskrypcji platformy Azure, jeśli nie jest jeszcze. Te informacje będą używane przez firmę Microsoft do kontaktowania się z Tobą, gdy centrum Microsoft Security Response Center (MSRC) wykryje, że osoby nieupoważnione lub działające niezgodnie z prawem uzyskały dostęp do Twoich danych klienta. MSRC wykonuje monitorowanie zabezpieczeń wybierz sieć platformy Azure i infrastruktury i odbiera zagrożeń analizy i nadużycia utrudnień od osób trzecich.

Wiadomość e-mail z powiadomieniem jest wysyłany na pierwsze wystąpienie codzienne alertu oraz tylko alerty o wysokim znaczeniu. Preferencje poczty e-mail można konfigurować tylko dla zasad subskrypcji. Grupy zasobów w ramach subskrypcji odziedziczy te ustawienia.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
1. W obszarze **zalecenia**, wybierz pozycję **podać szczegóły dotyczące kontaktu zabezpieczeń**.
   ![Podaj kontaktu zabezpieczeń][1]
2. Wybierz subskrypcję platformy Azure, aby udostępnić informacje kontaktowe na.
3. Spowoduje to otwarcie **zasady zabezpieczeń — powiadomienia E-mail**.

   ![Podawanie szczegółów dotyczących kontaktu ds. zabezpieczeń][2]

   * Wprowadź adres e-mail kontaktu zabezpieczeń lub adresów rozdzielonych przecinkami. Nie jest ograniczona liczba adresów e-mail, które można wprowadzić.
   * Wprowadź jeden zabezpieczeń kontaktu międzynarodowy numer telefonu.
   * Aby otrzymywać wiadomości e-mail o alertach o wysokim znaczeniu, włącz opcję **Wyślij do mnie wiadomość e-mail o alertach**.
   * W przyszłości konieczne będzie opcja do wysyłania powiadomień e-mail do właścicieli subskrypcji. Ta opcja jest obecnie wygaszone.
   * Wybierz **zapisać** do zastosowania zabezpieczeń informacji kontaktowych do subskrypcji.

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje zabezpieczeń platformy Azure i informacje.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
