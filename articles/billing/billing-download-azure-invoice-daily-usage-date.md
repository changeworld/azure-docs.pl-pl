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
ms.date: 11/16/2018
ms.author: banders
ms.openlocfilehash: 669d4be52cb74296bb034c773b820e14d7eede96
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902197"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Pobieranie i wyświetlanie Azure faktury i dane dotyczące dziennego wykorzystania

W przypadku większości subskrypcji, możesz pobrać fakturę z [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub wysłać w wiadomości e-mail. Jeśli jesteś klientem Azure z umową Enterprise Agreement (EA klienta), nie może pobrać faktury w organizacji. Faktury są wysyłane do spojrzenia jest skonfigurowany do otrzymywać faktur do rejestracji.

Jeśli chcesz pobrać użycia jako klientem z umową EA, jest dostępna w [witryny Azure portal](https://portal.azure.com/) > **Zarządzanie kosztami i rozliczenia** > **użycie i opłaty za**. W przypadku innych subskrypcji, przejdź do [Centrum konta platformy Azure](https://account.azure.com/Subscriptions).

Tylko określone role, które ma uprawnienia do pobrać faktur i danych użycia informacji, takich jak konta administratora lub administratora przedsiębiorstwa dotyczących rozliczeń. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-or-view-your-invoice"></a>Pobieranie i wyświetlanie na fakturze

 Jeśli jesteś klientem z umową EA, nie może pobrać faktury w organizacji. Faktury są wysyłane do spojrzenia jest skonfigurowany do otrzymywać faktur do rejestracji. W przypadku innych subskrypcji możesz pobrać fakturę w wiadomości e-mail lub go pobrać z witryny Azure portal.

### <a name="get-your-invoice-in-email-pdf"></a>Pobierz fakturę w wiadomości e-mail (PDF)
Można włączyć i skonfigurować dodatkowych adresatów otrzymujących subskrypcji platformy Azure faktury w wiadomości e-mail. Ta funkcja nie może być dostępna dla niektórych subskrypcji, takie jak oferty pomocy technicznej, umowy Enterprise Agreement lub Azure in Open.

1. Wybierz swoją subskrypcję z [strony subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Zgódź się dla każdej subskrypcji, których jesteś właścicielem. Kliknij przycisk **faktur** następnie **E-mail faktury**. 

    ![Zrzut ekranu pokazujący zgłoszenie zgody na uczestnictwo w usłudze flow](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Kliknij przycisk **Zgadzaj** i zaakceptuj warunki.

    ![Zrzut ekranu pokazujący przepływ zgłoszenie zgody na uczestnictwo w kroku 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Po po zaakceptowaniu umowy, możesz skonfigurować dodatkowych adresatów. Kiedy adresat jest usuwany, adres e-mail jest już przechowywana. Jeśli zmienisz zdanie, należy ponownie dodać je.

    ![Zrzut ekranu pokazujący przepływ zgłoszenie zgody na uczestnictwo w kroku 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Jeśli nie otrzymasz wiadomość e-mail po wykonaniu kroków upewnij się, Twój adres e-mail jest poprawny w [preferencje dotyczące komunikacji w Twoim profilu](https://account.windowsazure.com/profile).

### <a name="opt-out-from-getting-your-invoice-in-email"></a>Brak zgody na przed pobraniem na fakturze w wiadomości e-mail
Jeśli nie chcesz uzyskać fakturę w wiadomości e-mail, kliknij przycisk **zrezygnować z faktur pocztą e-mail**. Ta opcja usuwa wszystkie adresy e-mail, ustaw w celu otrzymywania faktur pocztą e-mail. Jeśli postanowisz w trzeba będzie ponownie skonfigurować adresatów.

 ![Zrzut ekranu pokazujący przepływ rezygnacji z](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="download-invoice-from-azure-portal-pdf"></a>Pobierz fakturę z witryny Azure portal (PDF)

1. Wybierz swoją subskrypcję z [strony subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal jako [użytkownikowi dostęp do faktury](billing-manage-access.md).

2. Wybierz **faktur**. 

    ![Zrzut ekranu pokazujący opcję rozliczenia i użycie](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Kliknij przycisk **Pobierz fakturę** Aby wyświetlić kopię faktury w formacie PDF. Jeśli wyświetlany jest tekst **nie jest dostępna**, zobacz [Dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?](#noinvoice)

    ![Zrzut ekranu pokazujący okresów rozliczeniowych, opcja pobierania i łączne opłaty dla każdego okresu rozliczeniowego](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Z dziennym użyciem można również wyświetlić, klikając okresu rozliczeniowego. 

Aby uzyskać więcej informacji na temat fakturze zobacz [opis zawartości rachunku dla systemu Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc w zarządzaniu kosztami, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing-getting-started.md).

### <a name="noinvoice"></a> Dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Masz miesięczną kwotę środków w ramach subskrypcji, która nie została przekroczona, lub masz bezpłatną wersję próbną. Faktura jest generowana tylko w sytuacji, gdy masz do zapłacenia pewną kwotę.

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.

- Faktura nie została jeszcze wygenerowana. Poczekaj na zakończenie okresu rozliczeniowego.

- Jeśli nie jesteś administratorem konta, starsze faktury mogą być dla Ciebie niedostępne.

## <a name="download-usage"></a>Pobierz dane użycia

 Większość subskrypcji można znaleźć w pliku dzienne użycie w [Centrum konta platformy Azure](https://account.azure.com/Subscriptions). Jeśli chcesz pobrać użycia jako klientem z umową EA, jest dostępna w [witryny Azure portal](https://portal.azure.com/) > **Zarządzanie kosztami i rozliczenia** > **użycie i opłaty za**. 

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

Aby wyświetlić i pobrać dane użycia jako klient korzystający z umową EA, musi być Administrator przedsiębiorstwa lub właściciel konta lub administratora działu z włączonymi zasadami opłaty widoku.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.

    ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Wybierz **użycie i opłaty za**.
1. Na miesiąc, którą chcesz pobrać, zaznacz **Pobierz**.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
