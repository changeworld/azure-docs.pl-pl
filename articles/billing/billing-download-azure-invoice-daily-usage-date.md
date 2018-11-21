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
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: 9067162c6f1bd95b0a1c25bef17836b19ecf6946
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276113"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Pobieranie i wyświetlanie Azure faktury i dane dotyczące dziennego wykorzystania
Możesz pobrać fakturę z [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub wysłać w wiadomości e-mail. Aby pobrać z dziennym użyciem, przejdź do [Centrum konta platformy Azure](https://account.azure.com/Subscriptions). Tylko określone role, które ma uprawnienia do faktury i informacje o użyciu, takich jak konto administratora. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

W tym artykule nie ma zastosowania do klientów z umową Enterprise Agreement (EA). Jeśli jesteś klientem z umową EA, fakturach są wysyłane bezpośrednio do administratorów rejestracji.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

> [!div class="nextstepaction"]
> [Pomoc w ulepszaniu dokumentów rozliczeniowych platformy Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="get-your-invoice-in-email-pdf"></a>Pobierz fakturę w wiadomości e-mail (PDF)
Można włączyć i skonfigurować dodatkowych adresatów otrzymujących subskrypcji platformy Azure faktury w wiadomości e-mail. Ta funkcja nie może być dostępna dla niektórych subskrypcji, takie jak oferty pomocy technicznej, umowy Enterprise Agreement lub Azure in Open.

1. Wybierz swoją subskrypcję z [strony subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Włączanie w każdej subskrypcji, których jesteś właścicielem. Kliknij przycisk **faktur** następnie **E-mail faktury**. 

    ![Zrzut ekranu pokazujący zgłoszenie zgody na uczestnictwo w usłudze flow](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Kliknij przycisk **Zgadzaj** i zaakceptuj warunki.

    ![Zrzut ekranu pokazujący przepływ zgłoszenie zgody na uczestnictwo w kroku 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Po po zaakceptowaniu umowy, możesz skonfigurować dodatkowych adresatów. Kiedy adresat jest usuwany, adres e-mail jest już przechowywana. Jeśli zmienisz zdanie, należy ponownie dodać je.

    ![Zrzut ekranu pokazujący przepływ zgłoszenie zgody na uczestnictwo w kroku 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Jeśli nie otrzymasz wiadomość e-mail po wykonaniu kroków upewnij się, Twój adres e-mail jest poprawny w [preferencje dotyczące komunikacji w Twoim profilu](https://account.windowsazure.com/profile).

### <a name="opt-out-from-getting-your-invoice-in-email"></a>Brak zgody na przed pobraniem na fakturze w wiadomości e-mail
Jeśli nie chcesz uzyskać fakturę w wiadomości e-mail, kliknij pozycję opcjonalna się faktur pocztą e-mail. Spowoduje to usunięcie żadne adresy e-mail, ustaw w celu otrzymywania faktur pocztą e-mail. Jeśli postanowisz ponownie w przypadku należy ponownie skonfigurować adresatów.

 ![Zrzut ekranu pokazujący przepływ rezygnacji z](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

## <a name="download-invoice-from-azure-portal-pdf"></a>Pobierz fakturę z witryny Azure portal (PDF)

1. Wybierz swoją subskrypcję z [strony subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal jako [użytkownikowi dostęp do faktury](billing-manage-access.md).

2. Wybierz **faktur**. 

    ![Zrzut ekranu pokazujący opcję rozliczenia i użycie](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Kliknij przycisk **Pobierz fakturę** Aby wyświetlić kopię faktury w formacie PDF. Jeśli wyświetlany jest tekst **nie jest dostępna**, zobacz [Dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?](#noinvoice)

    ![Zrzut ekranu pokazujący okresów rozliczeniowych, opcja pobierania i łączne opłaty dla każdego okresu rozliczeniowego](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Z dziennym użyciem można również wyświetlić, klikając okresu rozliczeniowego. 

Aby uzyskać więcej informacji na temat fakturze zobacz [opis zawartości rachunku dla systemu Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc w zarządzaniu kosztami, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Pobierz dane użycia z Centrum kont (.csv)

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

## <a name="noinvoice"></a> Dlaczego nie widzę faktury dla ostatniego okresu rozliczeniowego?

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Masz miesięczną kwotę środków w ramach subskrypcji, która nie została przekroczona, lub masz bezpłatną wersję próbną. Faktura jest generowana tylko w sytuacji, gdy masz do zapłacenia pewną kwotę.

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.

- Faktura nie została jeszcze wygenerowana. Poczekaj na zakończenie okresu rozliczeniowego.

- Jeśli nie jesteś administratorem konta, starsze faktury mogą być dla Ciebie niedostępne.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

