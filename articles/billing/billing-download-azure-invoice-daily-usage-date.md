---
title: Pobierz rozliczeń faktury i dziennego użycia danych Azure | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu pobierania lub wyświetlania Azure rozliczeń faktury i dziennego użycia danych.
keywords: faktury rozliczeń, pobierania faktury, faktury azure, azure użycie
services: ''
documentationcenter: ''
author: genlin
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6fa9f9ae4ef2bd39f3c6d3ec7c6d757aa50b2fd1
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Pobierz lub wyświetlanie Twoich Azure rozliczeń faktury i dziennego użycia danych
Możesz pobrać faktury z [portalu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub została ona wysłana w wiadomości e-mail. Aby pobrać dzienne użycie, przejdź do [Centrum konta platformy Azure](https://account.azure.com/Subscriptions). Tylko w przypadku niektórych ról ma uprawnienia do rozliczeń faktury i informacje o użyciu, takie jak konto administratora. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [zarządzanie dostępem do platformy Azure, rozliczeń, za pomocą ról](billing-manage-access.md).

>[!NOTE]
>W tym artykule nie dotyczą klientów Enterprise Agreement (EA). Jeśli EA klientów, faktur są wysyłane bezpośrednio do administratorów rejestracji.

## <a name="get-your-invoice-in-email-pdf"></a>Pobierz faktury w wiadomości e-mail (PDF)
Można włączyć i skonfigurować dodatkowych adresatów, aby otrzymać Azure faktury w wiadomości e-mail. Ta funkcja nie mogą być dostępne dla niektórych subskrypcji, takie jak ofert obsługi, umowy Enterprise Agreement lub Azure otwartym.

1. Wybierz subskrypcję z [subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Zgody na każdej subskrypcji użytkownika. Kliknij przycisk **faktury** następnie **E-mail fakturą**. 

    ![Zrzut ekranu przedstawiający przepływ opcjonalnych](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Kliknij przycisk **uczestnictwa w** i zaakceptuj postanowienia.

    ![Zrzut ekranu przedstawiający przepływ opcjonalnych](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Po zaakceptowane umowy, można skonfigurować dodatkowych adresatów.

    ![Zrzut ekranu przedstawiający przepływ opcjonalnych](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Jeśli nie otrzymasz wiadomość e-mail po wykonaniu kroków, upewnij się, Twój adres e-mail jest poprawny w [preferencjach komunikacji w profilu](https://account.windowsazure.com/profile).

## <a name="download-invoice-from-azure-portal-pdf"></a>Pobierać faktury z portalu Azure (PDF)

1. Wybierz subskrypcję z [subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w portalu Azure jako [użytkownika z dostępem do faktury](billing-manage-access.md).

2. Wybierz **faktury**. 

    ![Zrzut ekranu pokazujący opcja użycia procesora & rozliczeń](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Kliknij przycisk **Pobierz faktury** Aby wyświetlić kopię faktury PDF. Informacja **nie jest dostępny**, zobacz [Dlaczego nie widzę faktury w ostatnim okresie rozliczeniowym?](#noinvoice)

    ![Zrzut ekranu pokazujący rozliczeń okresów, opcja pobierania i sumy opłat dla każdego okresu rozliczeniowego](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Dzienne użycie można także wyświetlić, klikając w okresie rozliczeniowym. 

Aby uzyskać więcej informacji na temat faktury, zobacz [zrozumieć rachunku platformy Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc, zarządzanie kosztami, [uniknąć kosztów nieoczekiwany rozliczenia Azure i kosztów zarządzania](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Pobierz użycia z Centrum konta (.csv)

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/subscriptions) jako administratora konta.

2. Wybierz subskrypcję, dla której ma zostać faktury i użycie informacji.

3. Wybierz **HISTORII rozliczeń**. 

    ![Zrzut ekranu pokazujący opcji historii rozliczeń](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Instrukcje użytkownika zostaną wyświetlone ostatnich sześciu okresów rozliczeń i bieżącego okresu unbilled. 

    ![Zrzut ekranu pokazujący rozliczeń okresów, opcje pobierania dla każdego okresu rozliczeniowego faktury i dziennego użycia i sumy opłat](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Wybierz **widoku bieżącej instrukcji** wyświetlić szacunkową informacji o opłatach w chwili szacowania został wygenerowany. Te informacje jest aktualizowana tylko raz dziennie i może nie zawierać wszystkich użycie. Faktura miesięczne mogą się różnić od tego szacowania.

    ![Zrzut ekranu pokazujący opcji instrukcji bieżącego widoku](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Zrzut ekranu pokazujący oszacowanie bieżących opłat](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Wybierz **Pobierz dane użycia** Aby pobrać dzienne dane użycia do pliku CSV. Jeśli zobaczysz dostępne dwie wersje, Pobierz w wersji 2.

    ![Zrzut ekranu pokazujący opcji Pobierz dane użycia](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Tylko Administrator konta może uzyskać dostęp do Centrum konta platformy Azure. Innych administratorów rozliczeń, takich jak właściciela, można uzyskać informacji użycia za pomocą [rozliczeń interfejsów API](billing-usage-rate-card-overview.md).

Aby uzyskać więcej informacji na temat dzienne użycie zobacz [zrozumieć rachunku platformy Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc, zarządzanie kosztami, [uniknąć kosztów nieoczekiwany rozliczenia Azure i kosztów zarządzania](billing-getting-started.md).

## <a name="noinvoice"></a> Dlaczego nie widzę faktury w ostatnim okresie rozliczeniowym?

Może istnieć kilka przyczyn widzisz faktury:

- Ma wartość miesięczne środki w ramach subskrypcji, która nie przekracza lub masz bezpłatnej wersji próbnej. Faktury generowany jest tylko wtedy, gdy zobowiązań pieniędzy.

- Jest to mniej niż 30 dni od dnia, które subskrybujesz Azure.

- Faktury nie jest jeszcze wygenerowane. Poczekaj, aż do zakończenia okresu rozliczeniowego.

- Jeśli nie jesteś administratorem konta, starsze faktury nie można dostępne dla Ciebie.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.

