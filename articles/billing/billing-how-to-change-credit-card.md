---
title: Zmiana karty kredytowej na platformie Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób jak zmienić karty kredytowej do realizacji płatności subskrypcji platformy Azure
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6b0db12da7c187ce80f1d55ce8b9900601033068
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Dodać, zaktualizować lub usunąć karta kredytowa lub debetowa dla platformy Azure

W Centrum konta możesz dodać nową kartę kredytową, aktualizować istniejącej karty kredytowej lub usuwać karty kredytowej, które nie są używane. Musi być [konto administratora](billing-subscription-transfer.md#whoisaa) do wprowadzenia tych zmian.

**Chcesz płacić za faktury?** Zobacz [płacić za subskrypcje platformy Azure przez faktury](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Dodaj nową kartę kredytową lub debetową

1. Zaloguj się do [Centrum konta](https://account.windowsazure.com/Subscriptions) jako administratora konta.
1. Wybierz subskrypcję.
1. W prawej części strony wybierz pozycję **Zarządzaj metodami płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcją metody płatności Zarządzanie.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Wybierz pozycję "+" Aby dodać kartę.

    ![Zrzut ekranu pokazujący opcji edycji obok formy płatności.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Wprowadź kredytowej lub obciążania szczegóły karty.
1. Wybierz pozycję **Zapisz**. 

Jeśli błąd pojawia się po dodaniu karty kredytowej, zobacz [karty kredytowej odrzucone na Azure rejestracji](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Aktualizowanie istniejących kredytową lub debetową

Pobiera odnowiony karty kredytowej i liczba pozostaje taki sam, zaktualizować istniejący szczegóły karty kredytowej, takie jak Data wygaśnięcia. Jeśli zmiany numerów kart kredytowych, ponieważ karta zostało utracone lub skradzione, lub ważność, postępuj zgodnie z instrukcjami [Dodaj formę płatności kartą kredytową](#addcard) sekcji. Nie trzeba zaktualizować CVV.

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako administratora konta.
1. Wybierz subskrypcję, która jest połączona z kartą.
1. Wybierz **Zarządzanie formy płatności**.
1. Wybierz **Edytuj** obok karty, którą chcesz zaktualizować.
1. Aktualizuj szczegóły kartę kredytową lub debetową.
1. Wybierz pozycję **Zapisz**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Użyj innej karty kredytowej subskrypcji platformy Azure

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako administratora konta.
1. Wybierz subskrypcję, która jest połączona z kartą.
1. W prawej części strony wybierz pozycję **Zarządzaj metodami płatności**.
1. Kliknij przycisk **Użyj zamiast tego** obok karta, która ma być używany. Spowoduje to zaktualizowanie również inne subskrypcje aktualnie skojarzony z tą kartą. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Usuń karta kredytowa lub debetowa z konta

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako administratora konta.
1. Wybierz subskrypcję, która jest połączona z kartą.
3. W prawej części strony wybierz pozycję **Zarządzaj metodami płatności**.
4. Kliknij przycisk **usunąć** karty kredytowej, który chcesz usunąć.

Jeśli karty kredytowej jest skojarzony z innych aktywnych subskrypcji firmy Microsoft, nie można go usunąć z konta platformy Azure. Usuń karty kredytowej z wszystkie aktywne subskrypcje, które mają z firmą Microsoft i spróbuj ponownie.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moja subskrypcja jest wyłączona. Dlaczego nie można usunąć kartą kredytową teraz?

Po subskrypcji zostało wyłączone lub anulowane, oczekiwania 90 dni przed trwałe usunięcie subskrypcji. Zachowaj możemy formy płatności na plik w okresie przechowywania w przypadku, gdy chcesz ponownie aktywować subskrypcję. Po wykonaniu tej subskrypcji jest całkowicie usunięty.

Jeśli musisz usunąć kartą kredytową lub debetową, przed zakończeniem okresu przechowywania 90-dniowy [ponownie aktywować subskrypcję](billing-subscription-become-disable.md). Jeśli nie można ponownie aktywować [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Dlaczego wciąż "logowania sesja wygasła. Kliknij tutaj, aby ponownie się zalogować"?

Jeśli będzie nadal wyświetlany ten komunikat o błędzie, nawet jeśli już zalogowania i ponownie w, spróbuj ponownie, podając prywatnej sesji przeglądania.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak użyć innej karty dla każdej subskrypcji, który jest dostępny?

Niestety Jeśli subskrypcji jest już używany w tej samej karty, nie leży można rozdzielić je, aby użyć innej karty. Jednak po utworzeniu konta na nową subskrypcję, można użyć nowej metody płatności dla tej subskrypcji.

### <a name="how-do-i-make-payments"></a>Jak utworzyć płatności?

Jeśli konfigurujesz kartą kredytową lub debetową jako metody płatności, firma Microsoft automatycznie obciążyć karty po każdym okresie rozliczeniowym. Nie trzeba wykonywać żadnych czynności.

Jeśli jesteś [za pomocą faktury](billing-how-to-pay-by-invoice.md), Wyślij płatność do lokalizacji podanej w dolnej części faktury.

### <a name="how-do-i-make-a-one-time-payment"></a>Jak dokonać jednorazowej płatności?

Niestety Azure aktualnie nie obsługuje płatności jednorazowe kredytową lub debetową kart. 

### <a name="how-do-i-change-the-tax-id"></a>Jak zmienić NIP?

Aby dodać lub zaktualizować NIP, odwiedź stronę [ **profilu** w Centrum konta platformy Azure](https://account.azure.com/Profile), a następnie wybierz pozycję **podatku rekordu**. Ten NIP służy do obliczenia wykluczania podatku i pojawia się na faktury.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
