---
title: Wyświetl i Pobierz fakturę usługi Microsoft Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wyświetlania i Pobierz fakturę usługi Microsoft Azure
keywords: faktury, pobierania faktur, faktury platformy azure, użycie platformy azure
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: f71fe9b02765e0fc8fd5f3b7abbd54c87b08132f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617951"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Wyświetlanie i pobieranie faktury platformy Microsoft Azure

W przypadku większości subskrypcji, możesz pobrać fakturę z [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub wysłać w wiadomości e-mail. Jeśli jesteś klientem Azure z umową Enterprise Agreement (EA klienta), nie może pobrać faktury w organizacji. Faktury są wysyłane do spojrzenia jest skonfigurowany do otrzymywać faktur do rejestracji.

Tylko niektóre role mają uprawnienia do wyświetlania faktur, takich jak konta administratora lub administratora przedsiębiorstwa. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

Jeśli masz [umowy klienta Microsoft](#check-your-access-to-a-microsoft-customer-agreement), musi być profil rozliczeniowy właściciel, współautor, Czytelnik, lub wystawia faktury manager można pobrać faktur. Aby dowiedzieć się więcej o rolach rozliczeń dla umowy klienta firmy Microsoft, zobacz [rozliczeń profilu role i zadania](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-your-azure-invoices-pdf"></a>Pobieranie faktur platformy Azure (PDF)

W przypadku większości subskrypcji możesz pobrać fakturę w witrynie Azure portal. Jeśli masz umowy klienta firmy Microsoft, zobacz pobierania faktur dla profil rozliczeniowy.

### <a name="download-invoices-for-an-individual-subscription"></a>Pobieranie faktur dla indywidualnej subskrypcji

1. Wybierz swoją subskrypcję z [strony subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal jako [użytkownikowi dostęp do faktury](billing-manage-access.md).

2. Wybierz pozycję **Faktury**.

    ![Zrzut ekranu pokazujący opcję rozliczenia i użycie](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Kliknij przycisk **Pobierz fakturę** Aby wyświetlić kopię faktury w formacie PDF. Jeśli wyświetlany jest tekst **nie jest dostępna**, zobacz [Dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?](#noinvoice)

    ![Zrzut ekranu pokazujący okresów rozliczeniowych, opcja pobierania i łączne opłaty dla każdego okresu rozliczeniowego](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Z dziennym użyciem można również wyświetlić, klikając okresu rozliczeniowego.

Aby uzyskać więcej informacji na temat fakturze zobacz [opis zawartości rachunku dla systemu Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc w zarządzaniu kosztami, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Pobieranie faktur dla umowy klienta firmy Microsoft

Faktury są generowane dla każdego [rozliczeń profilu](billing-mca-overview.md#understand-billing-profiles) w umowie klienta firmy Microsoft. Musi być profil rozliczeniowy właściciel, współautor, Czytelnik, lub faktury Menedżera uprawnień umożliwiających pobieranie faktur w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.
1. Wybierz profil rozliczeniowy. W zależności od dostęp może być konieczne najpierw wybrać konta rozliczeniowego.
1. Wybierz pozycję **Faktury**.
1. W siatce faktury Znajdź wiersz faktury, który chcesz pobrać.
1. Kliknij wielokropek (`...`) na końcu wiersza.
    ![Zrzut ekranu pokazujący Wielokropek na końcu wiersza.](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. Wybierz z menu kontekstowego pobierania **faktury**.

    ![Zrzut ekranu przedstawiający menu kontekstowe](./media/billing-download-azure-invoice/contextmenu.png)

Jeśli nie widzisz faktury w ostatnim okresie rozliczeniowym, zobacz [Dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Pobierz fakturę w wiadomości e-mail (PDF)

Można włączyć i skonfigurować dodatkowych adresatów otrzymujących subskrypcji platformy Azure faktury w wiadomości e-mail. Ta funkcja nie może być dostępna dla niektórych subskrypcji, takie jak oferty pomocy technicznej, umowy Enterprise Agreement lub Azure in Open. Jeśli masz umowę Microsoft Customer Zobacz Get profilu rozliczeń faktury w wiadomości e-mail.

### <a name="get-your-subscriptions-invoices-in-email"></a>Pobieranie faktur swoją subskrypcję w wiadomości e-mail

1. Wybierz swoją subskrypcję z [strony subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Zgódź się dla każdej subskrypcji, których jesteś właścicielem. Kliknij przycisk **faktur** następnie **E-mail faktury**.

    ![Zrzut ekranu pokazujący zgłoszenie zgody na uczestnictwo w usłudze flow](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Kliknij przycisk **Zgadzaj** i zaakceptuj warunki.

    ![Zrzut ekranu pokazujący przepływ zgłoszenie zgody na uczestnictwo w kroku 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Po po zaakceptowaniu umowy, możesz skonfigurować dodatkowych adresatów. Kiedy adresat jest usuwany, adres e-mail jest już przechowywana. Jeśli zmienisz zdanie, należy ponownie dodać je.

    ![Zrzut ekranu pokazujący przepływ zgłoszenie zgody na uczestnictwo w kroku 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Jeśli nie otrzymasz wiadomość e-mail po wykonaniu kroków upewnij się, Twój adres e-mail jest poprawny w [preferencje dotyczące komunikacji w Twoim profilu](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Zrezygnować z pobierania faktur swoją subskrypcję w wiadomości e-mail

Użytkownik może zrezygnować z wprowadzenie fakturze za pośrednictwem poczty e-mail, wykonując kroki opisane powyżej i kliknięcie przycisku **zrezygnować z faktur pocztą e-mail**. Ta opcja usuwa wszystkie adresy e-mail ustawione w celu otrzymywania faktur pocztą e-mail. Można ponownie skonfigurować adresatów w przypadku zgody na uczestnictwo w.

 ![Zrzut ekranu pokazujący przepływ rezygnacji z](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Pobieranie faktur umowy klienta firmy Microsoft w wiadomości e-mail

Jeśli masz umowy klienta firmy Microsoft, możesz zrezygnować w można pobrać fakturę w wiadomości e-mail. Wszystkie rozliczeń profilu właściciele, współautorzy, czytelnicy i faktury menedżerów otrzyma fakturę za pośrednictwem poczty e-mail. Czytelnicy nie można zaktualizować preferencji faktury wiadomości e-mail.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.
1. Wybierz profil rozliczeniowy. W zależności od dostęp może być konieczne najpierw wybrać konta rozliczeniowego.
1. W obszarze **ustawienia**, wybierz opcję **właściwości**.
1. W obszarze **Wyślij fakturę pocztą E-mail**, wybierz opcję **preferencji faktury e-mail aktualizacji**.

    ![Zrzut ekranu pokazujący właściwości faktury poczty e-mail](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Wybierz **Zgadzaj**.
1. Kliknij przycisk **Aktualizuj**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Zrezygnować z wprowadzenie fakturach umowy klienta firmy Microsoft w wiadomości e-mail

Użytkownik może zrezygnować z wprowadzenie fakturze za pośrednictwem poczty e-mail, wykonując kroki opisane powyżej i kliknięcie przycisku **zrezygnować**. Wszystkie właściciele, współautorzy, czytelnicy i faktury menedżerów zostanie wyłączony z zbyt faktury za pośrednictwem poczty e-mail,. Jeśli czytnik, nie można zmienić preferencji faktury wiadomości e-mail.

### <a name="noinvoice"></a> Dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.

- Faktura nie została jeszcze wygenerowana. Poczekaj na zakończenie okresu rozliczeniowego.

- Nie masz uprawnień do wyświetlania faktur. W przypadku umowy klienta firmy Microsoft muszą być profil rozliczeniowy właściciel, współautor, Czytelnik, lub faktury menedżera. W przypadku innych subskrypcji może nie być wyświetlana stare faktury Jeśli nie jesteś administratorem konta. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

- Jeśli masz bezpłatną wersję próbną lub miesięczną ilość środków w ramach subskrypcji, która nie przekracza, nie będą otrzymywać faktury, o ile nie masz umowy klienta firmy Microsoft.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Sprawdź swój dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat faktury i opłaty, zobacz:

- [Przeglądanie i pobieranie usługi Microsoft Azure użycia i opłat](billing-download-azure-daily-usage.md)
- [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md)
- [Zrozumienie warunki na fakturze platformy Azure](billing-understand-your-invoice.md)
- [Zrozumienie warunki na usługi Microsoft Azure szczegółowe użycia](billing-understand-your-usage.md)
- [Wyświetl cennik platformy Azure w Twojej organizacji](billing-ea-pricing.md)

Jeśli masz umowy klienta firmy Microsoft, zobacz:

- [Informacje o opłatach w faktury dla swojego profilu rozliczeń](billing-mca-understand-your-bill.md)
- [Zrozumienie warunki na fakturze dla swojego profilu rozliczeń](billing-mca-understand-your-invoice.md)
- [Omówienie plików użycia i opłat platformy Azure dla swojego profilu rozliczeń](billing-mca-understand-your-usage.md)
- [Przeglądanie i pobieranie dokumentów podatkowych dla swojego profilu rozliczeń](billing-mca-download-tax-document.md)
- [Wyświetl cennik platformy Azure w Twojej organizacji](billing-ea-pricing.md)
