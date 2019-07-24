---
title: Wyświetlanie i pobieranie faktury platformy Microsoft Azure
description: Opisuje sposób wyświetlania i pobierania faktury Microsoft Azure.
keywords: Faktura rozliczenia, pobieranie faktury, faktura platformy Azure, użycie platformy Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321765"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Wyświetlanie i pobieranie faktury platformy Microsoft Azure

W przypadku większości subskrypcji można pobrać fakturę z [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub wysłać ją w wiadomości e-mail. Jeśli jesteś klientem platformy Azure z Umowa Enterprise (klient z umową EA), nie możesz pobrać faktur swojej organizacji. Faktury są wysyłane do osoby, która jest skonfigurowana do otrzymywania faktur na potrzeby rejestracji.

Tylko niektóre role mają uprawnienia do wyświetlania faktur. Na przykład administrator konta lub administrator przedsiębiorstwa. Aby uzyskać więcej informacji o uzyskiwaniu dostępu do informacji dotyczących rozliczeń, zobacz [Zarządzanie dostępem do usługi Azure rozliczeniami przy użyciu ról](billing-manage-access.md).

Jeśli masz [umowę klienta firmy Microsoft](#check-your-access-to-a-microsoft-customer-agreement), musisz mieć jedną z następujących ról, aby uzyskać faktury:

- Właściciel profilu rozliczeń
- Współautor
- Czytelnik
- Menedżer faktur

Aby uzyskać więcej informacji na temat ról rozliczeń dla umów klientów firmy Microsoft, zobacz Opis [ról i zadań](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)dotyczących rozliczeń.

## <a name="noinvoice"></a>Dlaczego nie możesz otrzymać faktury

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.

- Na platformie Azure naliczane są opłaty na koniec okresu faktury. W związku z tym faktury mogły jeszcze nie zostać wygenerowane. Poczekaj na zakończenie okresu rozliczeniowego.

- Nie masz uprawnień do wyświetlania faktur. Jeśli masz umowę klienta firmy Microsoft, musisz być właścicielem profilu rozliczeń, współautorem, Czytelnikem lub menedżerem faktury. W przypadku innych subskrypcji mogą nie być wyświetlane stare faktury, jeśli nie jesteś administratorem konta. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

- Jeśli masz bezpłatną wersję próbną lub miesięczną kwotę środków w ramach subskrypcji, otrzymasz fakturę tylko w przypadku przekroczenia kwoty miesięcznych środków. Jeśli masz umowę klienta firmy Microsoft, zawsze otrzymujesz fakturę.

## <a name="download-your-azure-invoices-pdf"></a>Pobierz faktury platformy Azure (PDF)

W przypadku większości subskrypcji można pobrać fakturę z Azure Portal. Jeśli masz umowę klienta firmy Microsoft, zobacz artykuł [Pobieranie faktur dla umowy klienta firmy Microsoft](#download-invoices-for-a-microsoft-customer-agreement).

### <a name="download-invoices-for-an-individual-subscription"></a>Pobierz faktury dla pojedynczej subskrypcji

1. Wybierz swoją subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w Azure Portal jako [użytkownik z dostępem do faktur](billing-manage-access.md).

2. Wybierz pozycję **Faktury**.

    ![Zrzut ekranu przedstawiający opcję użycia & rozliczeń](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Kliknij pozycję **Pobierz fakturę** , aby wyświetlić kopię faktury w formacie PDF. Jeśli komunikat **nie jest dostępny**, zobacz [dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?](#noinvoice)

    ![Zrzut ekranu pokazujący okresy rozliczeniowe, opcję pobierania i łączne opłaty za każdy okres rozliczeniowy](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Możesz również wyświetlić dzienne użycie, klikając okres rozliczeniowy.

Aby uzyskać więcej informacji na temat faktury, zobacz [Opis rachunku na Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc dotyczącą zarządzania kosztami, zobacz zapobieganie nieoczekiwanym kosztom rozliczeń [i zarządzania kosztami platformy Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Pobierz faktury dla umowy klienta firmy Microsoft

Faktury są generowane dla każdego [profilu](billing-mca-overview.md#billing-profiles) rozliczeń w umowie klienta firmy Microsoft. Aby pobierać faktury z Azure Portal, musisz być właścicielem profilu rozliczeń, współautorem, Czytelnikem lub menedżerem faktury.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj *Cost Management i rozliczenia*.
1. Wybierz profil rozliczeń. W zależności od dostępu może być konieczne najpierw wybranie konta rozliczeniowego.
1. Wybierz pozycję **Faktury**.
1. W siatce faktury Znajdź wiersz faktury, którą chcesz pobrać.
1. Kliknij przycisk wielokropka (`...`) na końcu wiersza.
    ![Zrzut ekranu pokazujący wielokropek na końcu wiersza](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. W menu kontekstowym pobierania wybierz pozycję **Faktura**.

    ![Zrzut ekranu pokazujący menu kontekstowe](./media/billing-download-azure-invoice/contextmenu.png)

Jeśli nie widzisz faktury dla ostatniego okresu rozliczeniowego, zobacz [dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Pobierz fakturę w wiadomości e-mail (. PDF)

Możesz zrezygnować i skonfigurować dodatkowych odbiorców, aby otrzymać fakturę platformy Azure w wiadomości e-mail. Ta funkcja może być niedostępna dla niektórych subskrypcji, takich jak oferty pomocy technicznej, umowy Enterprise Agreement lub Azure in Open. Jeśli masz umowę klienta firmy Microsoft, zobacz następną sekcję, aby [uzyskać faktury profilu rozliczeń w wiadomości e-mail](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Pobierz faktury subskrypcji w wiadomości e-mail

1. Wybierz swoją subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Zezwól na uczestnictwo w każdej posiadanej subskrypcji. Kliknij przycisk **faktury** , a następnie **Wyślij wiadomość e-mail na moją fakturę**.

    ![Zrzut ekranu pokazujący, że przepływ jest w użyciu](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Kliknij  pozycję Wybierz i zaakceptuj warunki.

    ![Zrzut ekranu pokazujący przepływ z zapytaniem krok 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Po zaakceptowaniu umowy można skonfigurować dodatkowych adresatów. Po usunięciu odbiorcy adres e-mail nie jest już przechowywany. Jeśli zmienisz zdanie, musisz je przeczytać.

    ![Zrzut ekranu pokazujący przepływ z zapytaniem krok 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Jeśli po wykonaniu tych czynności nie otrzymasz wiadomości e-mail, upewnij się, że Twój adres e-mail jest poprawny w [preferencjach komunikacji w Twoim profilu](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Rezygnacja z pobierania faktur subskrypcji w wiadomości e-mail

Aby zrezygnować z pobierania faktury za pośrednictwem poczty e-mail, wykonaj powyższe kroki, a następnie kliknij pozycję **rezygnacja z faktur w wiadomościach e-mail**. Ta opcja usuwa wszystkie adresy e-mail ustawione w celu otrzymywania faktur pocztą e-mail. Możesz ponownie skonfigurować odbiorców, Jeśli zrezygnujesz z programu.

 ![Zrzut ekranu przedstawiający przepływ rezygnacji](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Pobierz faktury umów dla klientów firmy Microsoft w wiadomości e-mail

Jeśli masz umowę klienta firmy Microsoft, możesz zrezygnować z uzyskiwania faktury w wiadomości e-mail. Wszyscy właściciele profilów rozliczeń, współautorzy, czytelnicy i menedżerowie faktury otrzymają fakturę pocztą e-mail. Czytelnicy nie mogą zaktualizować preferencji dotyczącej faktury e-mail.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj **Cost Management i rozliczenia**.
1. Wybierz profil rozliczeń. W zależności od dostępu może być konieczne najpierw wybranie konta rozliczeniowego.
1. W obszarze **Ustawienia**wybierz pozycję **Właściwości**.
1. W obszarze **Faktura e-mail**wybierz pozycję **Aktualizuj preferencję faktury e-mail**.

    ![Zrzut ekranu pokazujący właściwości faktury e-mail](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Wybierz pozycję zgadzam **się**.
1. Kliknij przycisk **Aktualizuj**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Rezygnacja z pobierania faktur z umów dotyczących klientów firmy Microsoft w wiadomości e-mail

Aby zrezygnować z pobierania faktury za pośrednictwem poczty e-mail, wykonaj powyższe kroki i kliknij pozycję **Zrezygnuj**. Wszyscy właściciele, współautorzy, czytelnicy i menedżerowie faktury mają również możliwość pobrania faktury za pośrednictwem poczty e-mail. Jeśli jesteś czytelnikiem, nie możesz zmienić preferencji faktura e-mail.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat faktury i opłat, zobacz:

- [Wyświetlaj i pobieraj Microsoft Azure użycie i opłaty](billing-download-azure-daily-usage.md)
- [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md)
- [Zrozumienie warunków na fakturze platformy Azure](billing-understand-your-invoice.md)
- [Poznanie terminów na Microsoft Azure szczegółowym użyciu](billing-understand-your-usage.md)
- [Wyświetlanie cen platformy Azure w organizacji](billing-ea-pricing.md)

Jeśli masz umowę klienta firmy Microsoft, zobacz:

- [Zapoznaj się z opłatami za fakturę dla swojego profilu rozliczeń](billing-mca-understand-your-bill.md)
- [Informacje na temat faktury dotyczącej Twojego profilu rozliczeń](billing-mca-understand-your-invoice.md)
- [Opis pliku użycia i opłat dotyczących platformy Azure dla Twojego profilu rozliczeń](billing-mca-understand-your-usage.md)
- [Wyświetlanie i pobieranie dokumentów podatkowych dla profilu rozliczeń](billing-mca-download-tax-document.md)
- [Wyświetlanie cen platformy Azure w organizacji](billing-ea-pricing.md)
