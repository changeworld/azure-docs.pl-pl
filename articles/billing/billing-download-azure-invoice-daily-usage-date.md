---
title: Pobieranie faktur i dane dziennego użycia rozliczeniową za platformę Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób pobrania lub wyświetlenia usługi platformy Azure rozliczeń faktury i dane dziennego użycia.
keywords: faktury, pobierania faktur, faktury platformy azure, użycie platformy azure
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 16b2eaef74a7aa0e3e28bfcbb6dbd9da568db6cf
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491404"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Pobieranie i wyświetlanie Azure faktury i dane dotyczące dziennego wykorzystania

W przypadku większości subskrypcji, możesz pobrać fakturę z [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub wysłać w wiadomości e-mail. Jeśli jesteś klientem Azure z umową Enterprise Agreement (EA klienta), nie może pobrać faktury w organizacji. Faktury są wysyłane do spojrzenia jest skonfigurowany do otrzymywać faktur do rejestracji.

Jeśli jesteś klientem z umową EA lub mieć [umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement), możesz pobrać użycie [witryny Azure portal](https://portal.azure.com/). W przypadku innych subskrypcji, przejdź do [Centrum konta platformy Azure](https://account.azure.com/Subscriptions) można pobrać użycia.

Tylko określone role mają uprawnienia do Pobierz rozliczeń faktur i danych użycia informacje, takie jak konta administratora lub administratora przedsiębiorstwa. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

W przypadku umowy klienta firmy Microsoft, trzeba być profil rozliczeniowy właściciel, współautor, Czytelnik, lub faktury manager, aby wyświetlić informacje dotyczące rozliczeń i użycia. Aby dowiedzieć się więcej o rolach rozliczeń dla umowy klienta firmy Microsoft, zobacz [rozliczeń profilu role i zadania](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

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

Faktury są generowane dla każdego [rozliczeń profilu](billing-mca-overview.md#billing-profiles) w umowie klienta firmy Microsoft. Musi być profil rozliczeniowy właściciel, współautor, Czytelnik, lub faktury Menedżera uprawnień umożliwiających pobieranie faktur w witrynie Azure portal.

1. Wyszukaj **Cost Management + rozliczenia**.
2. Wybierz profil rozliczeniowy.
3. Wybierz pozycję **Faktury**.
4. W siatce faktury Znajdź wiersz faktury, który chcesz pobrać.
5. Kliknij wielokropek (`...`) na końcu wiersza.
6. Wybierz z menu kontekstowego pobierania **faktury**.

Jeśli nie widzisz faktury w ostatnim okresie rozliczeniowym, zobacz **dodatkowe informacje**. <!-- Fix this -->
### <a name="noinvoice"></a> Dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.

- Faktura nie została jeszcze wygenerowana. Poczekaj na zakończenie okresu rozliczeniowego.

- Nie masz uprawnień do wyświetlania faktur. W przypadku umowy klienta firmy Microsoft muszą być profil rozliczeniowy właściciel, współautor, Czytelnik, lub faktury menedżera. W przypadku innych subskrypcji może nie być wyświetlana stare faktury Jeśli nie jesteś administratorem konta. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

- Jeśli masz bezpłatną wersję próbną lub miesięczną ilość środków w ramach subskrypcji, która nie przekracza, nie będą otrzymywać faktury, o ile nie masz umowy klienta firmy Microsoft.

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

1. Wyszukaj **Cost Management + rozliczenia**.
1. Wybierz profil rozliczeniowy.
1. W obszarze **ustawienia**, wybierz opcję **właściwości**.
1. W obszarze **Wyślij fakturę pocztą E-mail**, wybierz opcję **preferencji faktury e-mail aktualizacji**.
1. Wybierz **Zgadzaj**.
1. Kliknij przycisk **Aktualizuj**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Zrezygnować z wprowadzenie fakturach profilu rozliczeń w wiadomości e-mail

Użytkownik może zrezygnować z wprowadzenie fakturze za pośrednictwem poczty e-mail, wykonując kroki opisane powyżej i kliknięcie przycisku **zrezygnować**. Wszystkie właściciele, współautorzy, czytelnicy i faktury menedżerów zostanie wyłączony z zbyt faktury za pośrednictwem poczty e-mail,. Jeśli czytnik, nie można zmienić preferencji faktury wiadomości e-mail.

## <a name="download-usage"></a>Pobieranie danych użycia

 Większość subskrypcji można znaleźć w pliku dzienne użycie w [Centrum konta platformy Azure](https://account.azure.com/Subscriptions). Jeśli jesteś klientem z umową EA lub masz umowy klienta firmy Microsoft, możesz pobrać użycie w [witryny Azure portal](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Pobierz dane użycia z Centrum kont (.csv)

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/subscriptions) stanowisku administratora konta.

2. Wybierz subskrypcję, dla którego chcesz informacji faktur i danych użycia.

3. Wybierz **Historia rozliczeń**.

    ![Zrzut ekranu pokazujący opcja Historia rozliczeń](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Widać zestawienia sześć ostatnich okresów rozliczeniowych i bieżący nierozliczony okres.

    ![Zrzut ekranu pokazujący okresów rozliczeniowych, opcje, aby pobrać faktury i dzienne użycie i łączne opłaty dla każdego okresu rozliczeniowego](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Wybierz pozycję **Wyświetl bieżące zestawienie**, aby zobaczyć oszacowanie opłat na dzień wygenerowania tego oszacowania. Te informacje są aktualizowane tylko raz dziennie i nie może zawierać Twojego użycia. Twoja faktura miesięczne mogą się różnić od tego oszacowania.

    ![Zrzut ekranu pokazujący opcję Wyświetl bieżące zestawienie](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Zrzut ekranu, który przedstawia oszacowanie opłaty bieżące](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Wybierz pozycję **Pobierz zestawienie użycia**, aby pobrać dane dziennego użycia w postaci pliku CSV. Jeśli będą dostępne dwie wersje, pobierz wersję 2.

    ![Zrzut ekranu pokazujący opcję Pobierz dane użycia](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Tylko Administrator konta może uzyskać dostęp do Centrum konta platformy Azure. Innych administratorów rozliczeń, takich jak właściciel, można uzyskać informacji użycia za pomocą [interfejsów API rozliczeń](billing-usage-rate-card-overview.md).

Aby uzyskać więcej informacji na temat dziennego użycia, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc w zarządzaniu kosztami, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Pobierz dane użycia dla klientów z umową EA

Administrator działu z widokiem opłaty włączone zasady ani na przeglądanie i pobieranie danych użycia jako klient korzystający z umową EA, musisz być administratorem przedsiębiorstwa, właściciel konta.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj *Cost Management + rozliczenia*.

    ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Wybierz **użycie i opłaty za**.
1. Na miesiąc, którą chcesz pobrać, zaznacz **Pobierz**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Pobierz dane użycia dla umowy klienta firmy Microsoft

Aby wyświetlić i pobrać dane użycia dla profil rozliczeniowy, musi rozliczeń profil właściciel, współautor, Czytelnik, lub faktury menedżera.

#### <a name="download-usage-for-billed-charges"></a>Pobierz dane użycia opłaty rozliczane

1. Wyszukaj **Cost Management + rozliczenia**.
2. Wybierz profil rozliczeniowy.
3. Wybierz pozycję **Faktury**.
4. W siatce faktury Znajdź wiersz faktury odpowiadający użycia, który chcesz pobrać.
5. Kliknij wielokropek (`...`) na końcu wiersza.
6. Wybierz z menu kontekstowego pobierania **użycia platformy Azure i opłaty za**.

#### <a name="download-usage-for-open-charges"></a>Pobierz dane użycia Otwórz opłaty

Możesz również pobrać użycia data miesiąca dla bieżącego okresu rozliczeniowego, co oznacza, że opłaty nie zostały jeszcze naliczane.

1. Wyszukaj **Cost Management + rozliczenia**.
2. Wybierz profil rozliczeniowy.
3. W **Przegląd** bloku kliknij **Azure Pobierz użycia i opłat**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat faktury i opłaty, zobacz:

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
