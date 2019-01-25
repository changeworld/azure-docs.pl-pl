---
title: Zmienianie karty kredytowej na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zmienić karty kredytowej użytej do zapłacenia za subskrypcję platformy Azure
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 4e3c04d0cf53b53f3221effca8c8ae534990fdb3
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901177"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Dodawanie, aktualizowanie lub usuwanie karty kredytowej lub debetowej dla platformy Azure

W Centrum konta możesz dodać nową kartę kredytową, aktualizowanie istniejącej karty kredytowej lub usunąć karty kredytowej, która nie jest używany. Musi być [administratora konta](billing-subscription-transfer.md#whoisaa) do wprowadzenia tych zmian.

**Czy chcesz przełączyć się przy użyciu faktury?** Zobacz [płatność dla subskrypcji platformy Azure przy użyciu faktury](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Dodaj nową kartę kredytową lub debetową

1. Zaloguj się do [Centrum kont](https://account.windowsazure.com/Subscriptions) jako [administratora konta](billing-subscription-transfer.md#whoisaa).
1. Wybierz subskrypcję.
1. W prawej części strony wybierz pozycję **Zarządzaj metodami płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcją metody płatności Zarządzanie.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Wybierz pozycję "+" Aby dodać kartę.

    ![Zrzut ekranu pokazujący opcję Edytuj obok formy płatności.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Wprowadź szczegółowe informacje o karcie kredytowej lub debetowej.
1. Wybierz pozycję **Zapisz**. 

Jeśli wystąpi błąd, po dodaniu karty kredytowej, zobacz [karty kredytowej odrzucone podczas tworzenia konta platformy Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Aktualizowanie istniejącej kredytowej lub debetowej

W przypadku odnowienia pobiera dane karty kredytowej i numer pozostaje taki sam, należy zaktualizować istniejące dane karty kredytowej, takie jak Data wygaśnięcia. Jeśli zmiany numeru karty kredytowej ponieważ karty zostaną utracone, skradzione lub uznawane za wygasłe, postępuj zgodnie z instrukcjami w [Dodaj karty kredytowej jako metody płatności](#addcard) sekcji. Nie musisz zaktualizować CVV.

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako [administratora konta](billing-subscription-transfer.md#whoisaa).
1. Wybierz subskrypcję, która jest połączona karta.
1. Wybierz **Zarządzaj metodami płatności**.
1. Wybierz **Edytuj** obok karty do zaktualizowania.
1. Aktualizuj szczegóły karty kredytowej lub debetowej.
1. Wybierz pozycję **Zapisz**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Użyj innej karty kredytowej dla subskrypcji platformy Azure

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako [administratora konta](billing-subscription-transfer.md#whoisaa).
1. Wybierz subskrypcję, która jest połączona karta.
1. W prawej części strony wybierz pozycję **Zarządzaj metodami płatności**.
1. Kliknij przycisk **Użyj zamiast tego** obok karty, do którego chcesz użyć. Spowoduje to również zaktualizowanie innych subskrypcji aktualnie skojarzone z tą kartą. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Usuwanie karty kredytowej lub debetowej z konta

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako [administratora konta](billing-subscription-transfer.md#whoisaa).
1. Wybierz subskrypcję, która jest połączona karta.
3. W prawej części strony wybierz pozycję **Zarządzaj metodami płatności**.
4. Kliknij przycisk **Usuń** dla karty kredytowej, która ma zostać usunięty.

Jeśli karta kredytowa jest skojarzony z innych aktywnych subskrypcji firmy Microsoft, nie możesz usunąć go z kontem platformy Azure. Usuń kartę kredytową z wszystkie aktywne subskrypcje, które mają się z firmą Microsoft i spróbuj ponownie.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moja subskrypcja jest wyłączona. Dlaczego nie można teraz usunąć Moja karta kredytowa?

Po Twoja subskrypcja została wyłączona lub zostało anulowane, poczekamy 90 dni przed trwale Twojej subskrypcji. Przechowujemy Twojej bieżącej formy płatności pliku podczas okresu przechowywania danych w przypadku, gdy chcesz ponownie aktywować subskrypcję. Po tym subskrypcji jest całkowicie usunięty.

Jeśli musisz usunąć karty kredytowej lub debetowej, przed zakończeniem 90-dniowy okres przechowywania [ponownie aktywować swoją subskrypcję](billing-subscription-become-disable.md). Jeśli nie można ponownie aktywować [skontaktuj się z działem pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Dlaczego wciąż "Twoja sesja logowania wygasła. Kliknij tutaj, aby ponownie się zalogować"?

Jeśli nadal jest wyświetlany ten komunikat o błędzie nawet wtedy, gdy użytkownik został już zalogowany w i w, spróbuj ponownie, używając prywatnego sesji przeglądania.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak używać innej karty dla każdej subskrypcji, który jest dostępny?

Niestety Jeśli subskrypcji jest już używany w tej samej karty, nie jest możliwe oddzielenie ich do używania różnych kart. Jednak po zarejestrowaniu się na nową subskrypcję, można użyć nową metodę płatności dla tej subskrypcji.

### <a name="how-do-i-make-payments"></a>Jak utworzyć płatności?

Jeśli konfigurujesz kartę kredytową lub debetową jako metody płatności, automatycznie opłaty są naliczane karty od zakończenia każdego okresu rozliczeniowego. Nie trzeba podejmować żadnych działań.

Jeśli jesteś [płacenia za pomocą faktury](billing-how-to-pay-by-invoice.md), Wyślij płatność do lokalizacji wymienionych na dole faktury.

### <a name="how-do-i-make-a-one-time-payment"></a>Jak utworzyć jednorazowej płatności?

Niestety Azure nie obsługuje obecnie jednorazowej płatności dla karty kredytowej lub debetowej. 

### <a name="how-do-i-change-the-tax-id"></a>Jak zmienić identyfikator podatek?

Aby dodać lub zaktualizować NIP, odwiedź stronę [ **profilu** w Centrum konta platformy Azure](https://account.azure.com/Profile), a następnie wybierz **podatkowych rekordu**. Numer NIP jest używany do obliczenia kwoty zwolnienia z podatku i pojawia się na fakturze.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
