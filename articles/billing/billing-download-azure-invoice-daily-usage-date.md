---
title: Pobieranie faktury rozliczeniowej i danych dziennego użycia platformy Azure | Microsoft Docs
description: Opis dotyczący pobierania lub wyświetlania faktury rozliczeniowej i danych dziennego użycia.
keywords: faktura rozliczeniowa, pobieranie faktury, faktura platformy azure, dane użycia platformy azure
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491404"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Pobieranie lub wyświetlanie faktury rozliczeniowej i danych dziennego użycia platformy Azure

W przypadku większości subskrypcji możesz pobrać fakturę z [witryny Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub zażądać jej wysłania w wiadomości e-mail. Jeśli jesteś klientem platformy Azure z umową Enterprise Agreement (umową EA), nie możesz pobrać faktur swojej organizacji. Faktury są wysyłane do osoby skonfigurowanej jako odbiorca faktur dla rejestracji.

Jeśli jesteś klientem z umową EA lub masz [umowę klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement), możesz pobrać dane użycia w [witrynie Azure Portal](https://portal.azure.com/). W przypadku innych subskrypcji przejdź do [Centrum konta platformy Azure](https://account.azure.com/Subscriptions), aby pobrać dane użycia.

Tylko niektóre role, takie jak administrator konta i administrator przedsiębiorstwa, mają uprawnienie do pobierania danych użycia i faktur rozliczeniowych. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

Jeśli masz umowę klienta firmy Microsoft, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego, aby móc wyświetlić informacje dotyczące rozliczeń i użycia. Aby dowiedzieć się więcej o rolach rozliczeniowych na potrzeby umów klienta firmy Microsoft, zobacz [Zadania i role profilu rozliczeniowego](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Pobieranie faktur platformy Azure (PDF)

W przypadku większości subskrypcji możesz pobrać fakturę z witryny Azure Portal. Jeśli masz umowę klienta firmy Microsoft, zobacz artykuł Pobieranie faktur dla profilu rozliczeniowego.

### <a name="download-invoices-for-an-individual-subscription"></a>Pobieranie faktur dla pojedynczej subskrypcji

1. Wybierz swoją subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure Portal jako [użytkownik z dostępem do faktur](billing-manage-access.md).

2. Wybierz pozycję **Faktury**.

    ![Zrzut ekranu przedstawiający opcję Rozliczenia i użycie](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Kliknij pozycję **Pobierz fakturę**, aby wyświetlić kopię faktury w formacie PDF. Jeśli jest wyświetlany tekst **Niedostępna**, zobacz sekcję [Dlaczego nie widzę faktury za ostatni okres rozliczeniowy?](#noinvoice)

    ![Zrzut ekranu pokazujący okresy rozliczeniowe, opcję pobierania i łączne opłaty za każdy okres rozliczeniowy](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Możesz również wyświetlać dzienne użycie, klikając okres rozliczeniowy.

Aby uzyskać więcej informacji na temat faktury, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc dotyczącą zarządzania kosztami, zobacz [Zapobieganie powstawaniu nieoczekiwanych kosztów w rozliczeniach platformy Azure i zarządzanie kosztami](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Pobieranie faktur dla umowy klienta firmy Microsoft

Faktury są generowane dla każdego [profilu rozliczeniowego](billing-mca-overview.md#billing-profiles) w ramach umowy klienta firmy Microsoft. Aby pobierać faktury z witryny Azure Portal, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego.

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
2. Wybierz profil rozliczeniowy.
3. Wybierz pozycję **Faktury**.
4. W siatce faktur znajdź wiersz faktury, którą chcesz pobrać.
5. Kliknij symbol wielokropka (`...`) na końcu wiersza.
6. W menu kontekstowym pobierania wybierz pozycję **Faktura**.

Jeśli nie widzisz faktury dla ostatniego okresu rozliczeniowego, zobacz **Dodatkowe informacje**. <!-- Fix this -->
### <a name="noinvoice"></a>Dlaczego nie widzę faktury za ostatni okres rozliczeniowy?

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.

- Faktura nie została jeszcze wygenerowana. Poczekaj na zakończenie okresu rozliczeniowego.

- Nie masz uprawnień do wyświetlania faktur. Jeśli masz umowę klienta firmy Microsoft, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego. W przypadku innych subskrypcji stare faktury mogą nie być widoczne, jeśli nie jesteś administratorem konta. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

- Jeśli masz bezpłatną wersję próbną lub niewykorzystane miesięczne środki w ramach subskrypcji, nie otrzymasz faktury, chyba że masz umowę klienta firmy Microsoft.

## <a name="get-your-invoice-in-email-pdf"></a>Otrzymywanie faktury w wiadomości e-mail (PDF)

Możesz wyrazić zgodę i skonfigurować dodatkowych adresatów, aby otrzymywać fakturę platformy Azure w wiadomości e-mail. Ta funkcja może być niedostępna dla niektórych subskrypcji, takich jak oferty pomocy technicznej, umowy Enterprise Agreement lub platforma Azure w ramach programu licencjonowania Open. Jeśli masz umowę klienta firmy Microsoft, zobacz artykuł Otrzymywanie faktur dla profilu rozliczeniowego w wiadomości e-mail.

### <a name="get-your-subscriptions-invoices-in-email"></a>Otrzymywanie faktur dla subskrypcji w wiadomości e-mail

1. Wybierz swoją subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wyraź zgodę dla każdej posiadanej subskrypcji. Kliknij pozycję **Faktury**, a następnie pozycję **Wyślij do mnie wiadomość e-mail z fakturą**.

    ![Zrzut ekranu pokazujący przepływ z wyrażeniem zgody](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Kliknij pozycję **Wyraź zgodę** i zaakceptuj warunki.

    ![Zrzut ekranu pokazujący 2. krok przepływu z wyrażeniem zgody](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Po zaakceptowaniu umowy można skonfigurować dodatkowych adresatów. Po usunięciu adresata adres e-mail nie jest dłużej przechowywany. Jeśli zmienisz zdanie, musisz dodać go ponownie.

    ![Zrzut ekranu pokazujący 3. krok przepływu z wyrażeniem zgody](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Jeśli nie otrzymasz wiadomości e-mail po wykonaniu tych kroków, upewnij się, że Twój adres e-mail w [preferencjach komunikacji w Twoim profilu](https://account.windowsazure.com/profile) jest poprawny.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Rezygnacja z otrzymywania faktur dla subskrypcji w wiadomości e-mail

Możesz zrezygnować z otrzymywania faktur pocztą e-mail, wykonując powyższe kroki i klikając pozycję **Rezygnuję z faktur wysyłanych pocztą e-mail**. Ta opcja usuwa wszystkie adresy e-mail ustawione w celu otrzymywania faktur pocztą e-mail. Możesz ponownie skonfigurować adresatów, jeśli ponownie wyrazisz zgodę.

 ![Zrzut ekranu przedstawiający przepływ rezygnacji](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Otrzymywanie faktur dla umowy klienta firmy Microsoft w wiadomości e-mail

Jeśli masz umowę klienta firmy Microsoft, możesz wyrazić zgodę na otrzymywanie faktur w wiadomości e-mail. Wiadomości e-mail z fakturą będą wysyłane do wszystkich właścicieli, współautorów, czytelników i menedżerów faktur dla profilu rozliczeniowego. Czytelnicy nie mogą aktualizować preferencji wysyłania faktur za pomocą poczty e-mail.

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
1. Wybierz profil rozliczeniowy.
1. W obszarze **Ustawienia** wybierz pozycję **Właściwości**.
1. W obszarze **Wyślij fakturę pocztą e-mail** wybierz pozycję **Aktualizuj preferencje dotyczące wysyłania faktur pocztą e-mail**.
1. Wybierz pozycję **Wyraź zgodę**.
1. Kliknij przycisk **Update** (Aktualizuj).

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Rezygnacja z otrzymywania faktur dla profilu rozliczeniowego w wiadomości e-mail

Możesz zrezygnować z otrzymywania faktur za pomocą poczty e-mail, wykonując powyższe kroki i klikając pozycję **Zrezygnuj**. Rezygnacja z otrzymywania faktur w wiadomości e-mail obejmie także wszystkich właścicieli, współautorów, czytelników i menedżerów faktur. Czytelnicy nie mogą zmieniać preferencji wysyłania faktur za pomocą poczty e-mail.

## <a name="download-usage"></a>Pobieranie danych użycia

 W przypadku większości subskrypcji plik dziennego użycia jest dostępny w [Centrum konta platformy Azure](https://account.azure.com/Subscriptions). Jeśli jesteś klientem z umową EA lub masz umowę klienta firmy Microsoft, możesz pobrać dane użycia w [witrynie Azure Portal](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Pobieranie danych użycia z Centrum konta (CSV)

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/subscriptions) jako administrator konta.

2. Wybierz subskrypcję, dla której chcesz otrzymać fakturę i dane użycia.

3. Wybierz pozycję **HISTORIA ROZLICZEŃ**.

    ![Zrzut ekranu przedstawiający opcję historii rozliczeń](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Dostępne są zestawienia za sześć ostatnich okresów rozliczeniowych i bieżący, nierozliczony okres.

    ![Zrzut ekranu pokazujący okresy rozliczeniowe, opcję pobierania faktury i danych dziennego użycia oraz łączne opłaty za każdy okres rozliczeniowy](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Wybierz pozycję **Wyświetl bieżące zestawienie**, aby zobaczyć oszacowanie opłat na dzień wygenerowania tego oszacowania. Te informacje są aktualizowane tylko raz dziennie i mogą nie uwzględniać całego użycia. Rachunek miesięczny może różnić się od podanego oszacowania.

    ![Zrzut ekranu przedstawiający opcję Wyświetl bieżące zestawienie](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Zrzut ekranu przedstawiający oszacowanie bieżących opłat](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Wybierz pozycję **Pobierz zestawienie użycia**, aby pobrać dane dziennego użycia w postaci pliku CSV. Jeśli będą dostępne dwie wersje, pobierz wersję 2.

    ![Zrzut ekranu przedstawiający opcję Pobierz zestawienie użycia](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Tylko administrator konta może uzyskać dostęp do Centrum konta platformy Azure. Inni administratorzy rozliczeń, tacy jak właściciel, mogą uzyskać informacje dotyczące użycia za pomocą [interfejsów API rozliczeń](billing-usage-rate-card-overview.md).

Aby uzyskać więcej informacji na temat dziennego użycia, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc dotyczącą zarządzania kosztami, zobacz [Zapobieganie powstawaniu nieoczekiwanych kosztów w rozliczeniach platformy Azure i zarządzanie kosztami](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Pobieranie zestawienia użycia dla klientów z umową EA

Aby przeglądać dane użycia jako klient z umową EA i pobierać je, musisz być administratorem przedsiębiorstwa, właścicielem konta lub administratorem działu z włączonymi zasadami wyświetlania opłat.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

    ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Wybierz pozycję **Użycie i opłaty**.
1. Wybierz pozycję **Pobierz** dla miesiąca, dla którego chcesz pobrać dane.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Pobieranie zestawienia użycia dla umowy klienta firmy Microsoft

Aby wyświetlić i pobrać zestawienie użycia dla profilu rozliczeniowego, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego.

#### <a name="download-usage-for-billed-charges"></a>Pobieranie zestawienia użycia dla naliczonych opłat

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
2. Wybierz profil rozliczeniowy.
3. Wybierz pozycję **Faktury**.
4. W siatce faktur znajdź wiersz faktury odpowiadający danym użycia, które chcesz pobrać.
5. Kliknij symbol wielokropka (`...`) na końcu wiersza.
6. W menu kontekstowym pobierania wybierz pozycję **Użycie i opłaty platformy Azure**.

#### <a name="download-usage-for-open-charges"></a>Pobieranie zestawienia użycia dla „otwartych” opłat

Możesz także pobrać zestawienie użycia miesięcznego dla bieżącego okresu rozliczeniowego dotyczące opłat, które nie zostały jeszcze rozliczone.

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
2. Wybierz profil rozliczeniowy.
3. W bloku **Przegląd** kliknij pozycję **Pobierz informacje o użyciu platformy Azure i opłatach**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat faktur i opłat, zobacz:

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
