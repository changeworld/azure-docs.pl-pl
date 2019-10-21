---
title: Wyświetlanie i pobieranie faktury platformy Microsoft Azure
description: Opis sposobu wyświetlania i pobierania faktury za korzystanie z platformy Microsoft Azure.
keywords: faktura rozliczeniowa, pobieranie faktury, faktura platformy azure, dane użycia platformy azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 80ec40a7411a370460d663084f9f7034b28e1a2e
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375760"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Wyświetlanie i pobieranie faktury platformy Microsoft Azure

W przypadku większości subskrypcji możesz pobrać fakturę z [witryny Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub zażądać jej wysłania w wiadomości e-mail. Jeśli jesteś klientem platformy Azure z umową Enterprise Agreement (umową EA), nie możesz pobrać faktur swojej organizacji. Faktury są wysyłane do osoby skonfigurowanej jako odbiorca faktur dla rejestracji.

Tylko niektóre role mają uprawnienia do wyświetlania faktur. Są to na przykład role administratora konta lub administratora przedsiębiorstwa. Aby uzyskać więcej szczegółów na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról](billing-manage-access.md).

Jeśli masz umowę klienta firmy Microsoft, musisz mieć jedną z następujących ról, aby otrzymywać faktury:

- Właściciel profilu rozliczeniowego
- Współautor
- Czytelnik
- Menedżer faktur

Jeśli masz umowę partnerską firmy Microsoft, musisz być administratorem globalnym lub agentem administracyjnym w organizacji partnerskiej, aby wyświetlać i pobierać faktury platformy Azure. Zobacz temat [Sprawdzanie typu konta rozliczeniowego](#check-your-billing-account-type), aby dowiedzieć się, jakich uprawnień potrzebujesz. 

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](billing-understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Dlaczego nie możesz wyświetlić faktury

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.

- Na platformie Azure opłaty są naliczane na koniec okresu fakturowania. W związku z tym faktura mogła jeszcze nie zostać wygenerowana. Poczekaj na zakończenie okresu rozliczeniowego.

- Nie masz uprawnień do wyświetlania faktur. Jeśli masz umowę klienta firmy Microsoft lub umowę partnerską firmy Microsoft, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur w profilu rozliczeniowym. W przypadku innych subskrypcji stare faktury mogą nie być widoczne, jeśli nie jesteś administratorem konta. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

- Jeśli masz bezpłatną wersję próbną lub miesięczną kwotę środków w ramach subskrypcji, otrzymasz fakturę tylko w przypadku przekroczenia kwoty miesięcznych środków. Użytkownicy z umową klienta firmy Microsoft lub umową partnerską firmy Microsoft zawsze otrzymują fakturę. 

## <a name="download-invoices-in-the-azure-portal"></a>Pobieranie faktur w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.
1. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego lub profilu rozliczeniowego.
1. W menu po lewej stronie wybierz pozycję **Faktury** w obszarze **Rozliczenia**.
1. W siatce faktur znajdź wiersz faktury, którą chcesz pobrać.
1. Kliknij ikonę pobierania lub symbol wielokropka (`...`) na końcu wiersza.
1. W menu pobierania wybierz pozycję **Faktura**.

Aby uzyskać więcej informacji na temat faktury, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc dotyczącą zarządzania kosztami, zobacz [Zapobieganie powstawaniu nieoczekiwanych kosztów w rozliczeniach platformy Azure i zarządzanie kosztami](billing-getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Otrzymywanie faktur dla subskrypcji w wiadomości e-mail

Możesz wyrazić zgodę i skonfigurować dodatkowych adresatów, aby otrzymywać fakturę platformy Azure w wiadomości e-mail. Ta funkcja może być niedostępna dla niektórych subskrypcji, takich jak oferty pomocy technicznej, umowy Enterprise Agreement lub platforma Azure w ramach programu licencjonowania Open.

1. Wybierz swoją subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wyraź zgodę dla każdej posiadanej subskrypcji. Kliknij pozycję **Faktury**, a następnie pozycję **Wyślij do mnie wiadomość e-mail z fakturą**.

    ![Zrzut ekranu pokazujący przepływ z wyrażeniem zgody](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Kliknij pozycję **Wyraź zgodę** i zaakceptuj warunki.

    ![Zrzut ekranu pokazujący 2. krok przepływu z wyrażeniem zgody](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Po zaakceptowaniu umowy można skonfigurować dodatkowych adresatów. Po usunięciu adresata adres e-mail nie jest dłużej przechowywany. Jeśli zmienisz zdanie, musisz dodać go ponownie.

    ![Zrzut ekranu pokazujący 3. krok przepływu z wyrażeniem zgody](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Jeśli nie otrzymasz wiadomości e-mail po wykonaniu tych kroków, upewnij się, że Twój adres e-mail w [preferencjach komunikacji w Twoim profilu](https://account.windowsazure.com/profile) jest poprawny.

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Rezygnacja z otrzymywania faktur dla subskrypcji w wiadomości e-mail

Aby zrezygnować z otrzymywania faktur za pośrednictwem poczty e-mail, wykonaj powyższe kroki, a następnie kliknij pozycję **Zrezygnuj z otrzymywania faktur pocztą e-mail**. Ta opcja usuwa wszystkie adresy e-mail ustawione w celu otrzymywania faktur pocztą e-mail. Możesz ponownie skonfigurować adresatów, jeśli ponownie wyrazisz zgodę.

 ![Zrzut ekranu przedstawiający przepływ rezygnacji](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Otrzymywanie faktur dla umowy klienta firmy Microsoft w wiadomości e-mail

Jeśli masz umowę klienta firmy Microsoft, możesz wyrazić zgodę na otrzymywanie faktur w wiadomości e-mail. Wiadomości e-mail z fakturą będą wysyłane do wszystkich właścicieli, współautorów, czytelników i menedżerów faktur dla profilu rozliczeniowego. Czytelnicy nie mogą aktualizować preferencji wysyłania faktur za pomocą poczty e-mail.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
1. Wybierz profil rozliczeniowy. W zależności od praw dostępu może być konieczne wybranie najpierw konta rozliczeniowego.
1. W obszarze **Ustawienia** wybierz pozycję **Właściwości**.
1. W obszarze **Wyślij fakturę pocztą e-mail** wybierz pozycję **Aktualizuj preferencje dotyczące wysyłania faktur pocztą e-mail**.

    ![Zrzut ekranu przedstawiający właściwości opcji wysyłania faktury pocztą e-mail](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Wybierz pozycję **Wyraź zgodę**.
1. Kliknij przycisk **Update** (Aktualizuj).

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Rezygnacja z otrzymywania faktur dla umowy klienta firmy Microsoft w wiadomości e-mail

Aby zrezygnować z otrzymywania faktur za pośrednictwem poczty e-mail, wykonaj powyższe kroki, a następnie kliknij pozycję **Zrezygnuj**. Rezygnacja z otrzymywania faktur w wiadomości e-mail obejmuje także wszystkich właścicieli, współautorów, czytelników i menedżerów faktur. Czytelnicy nie mogą zmieniać preferencji wysyłania faktur za pomocą poczty e-mail.

## <a name="check-your-billing-account-type"></a>Sprawdzanie typu konta rozliczeniowego
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat faktur i opłat, zobacz:

- [Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Microsoft Azure](billing-download-azure-daily-usage.md)
- [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md)
- [Omówienie terminów na fakturze za korzystanie z platformy Azure](billing-understand-your-invoice.md)
- [Omówienie terminów dotyczących szczegółów użycia platformy Microsoft Azure](billing-understand-your-usage.md)
- [Wyświetlanie cennika platformy Azure obowiązującego w organizacji](billing-ea-pricing.md)

Jeśli masz umowę klienta firmy Microsoft, zobacz:

- [Omówienie opłat na fakturze dla profilu rozliczeniowego](billing-mca-understand-your-bill.md)
- [Omówienie terminów na fakturze dla profilu rozliczeniowego](billing-mca-understand-your-invoice.md)
- [Omówienie pliku użycia i opłat dotyczących platformy Azure dla profilu rozliczeniowego](billing-mca-understand-your-usage.md)
- [Wyświetlanie i pobieranie dokumentów podatkowych dla profilu rozliczeniowego](billing-mca-download-tax-document.md)
- [Wyświetlanie cennika platformy Azure obowiązującego w organizacji](billing-ea-pricing.md)
