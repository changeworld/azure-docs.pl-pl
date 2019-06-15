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
ms.date: 3/13/2019
ms.author: banders
ms.openlocfilehash: b910cb3061b1451ea80b9843e2aa4047a784548f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918839"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Dodawanie, aktualizowanie lub usuwanie karty kredytowej lub debetowej dla platformy Azure

W witrynie Azure portal możesz dodać nową kartę kredytową, aktualizowanie istniejącej karty kredytowej lub usunąć karty kredytowej, która nie jest używany. Musi być [administratora konta](billing-subscription-transfer.md#whoisaa) do wprowadzenia tych zmian.

<!-- If your Angola, Belize, Algeria, Vietnam, or Virgin Islands. -->

**Czy chcesz przełączyć się przy użyciu faktury?** Zobacz [płatność dla subskrypcji platformy Azure przy użyciu faktury](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card-to-an-azure-subscription"></a>Dodaj nową kartę kredytową lub debetową z subskrypcją platformy Azure

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) stanowisku administratora konta.
1. Wyszukiwanie **Cost Management + rozliczenia**.

    ![Zrzut ekranu pokazujący wyszukiwania](./media/billing-how-to-change-credit-card/search.png)

1. Wybierz subskrypcję, czy chcesz dodać karty kredytowej lub debetowej do.
1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcją metody płatności Zarządzanie.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. W lewym górnym rogu wybierz pozycję „+”, aby dodać kartę. Po prawej stronie zostanie wyświetlony formularz karty kredytowej.
1. Wprowadź szczegółowe informacje o karcie kredytowej lub debetowej.

    ![Zrzut ekranu przedstawiający dodawanie nowej karty.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Aby to kart aktywną formę płatności, zaznacz pole wyboru obok pozycji **Ustaw jako moją aktywną formę płatności** powyżej formularza. Ta karta stanie się aktywnym instrumentem płatniczym dla wszystkich subskrypcji używających tej karty jako wybranej subskrypcji.

1. Wybierz opcję **Dalej**.

Jeśli wystąpi błąd, po dodaniu karty kredytowej, zobacz [karty kredytowej odrzucone podczas tworzenia konta platformy Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Aktualizowanie istniejącej kredytowej lub debetowej

W przypadku odnowienia pobiera dane karty kredytowej i numer pozostaje taka sama, należy zaktualizować istniejące dane karty kredytowej, takie jak Data wygaśnięcia. Jeśli zmiany numeru karty kredytowej ponieważ karty zostaną utracone, skradzione lub uznawane za wygasłe, postępuj zgodnie z instrukcjami w [Dodaj karty kredytowej jako metody płatności](#addcard) sekcji. Nie musisz zaktualizować CVV.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) stanowisku administratora konta.
1. Wyszukiwanie **Cost Management + rozliczenia**.

    ![Zrzut ekranu pokazujący wyszukiwania](./media/billing-how-to-change-credit-card/search.png)

1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcją metody płatności Zarządzanie.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Kliknij kartę kredytową lub debetową, który chcesz edytować. Po prawej stronie zostanie wyświetlony formularz karty kredytowej.

    ![Zrzut ekranu pokazujący wybrana karta kredytowa lub debetowa.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Zaktualizuj szczegółowe informacje o karcie kredytowej lub debetowej.
1. Wybierz pozycję **Zapisz**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Użyj innej karty kredytowej dla subskrypcji platformy Azure

Jeśli masz więcej niż jednej z Twoich subskrypcji tego samego aktywną formę płatności, następnie zmieniając aktywną formę płatności na każdą z tych subskrypcji aktualizuje również aktywną formę płatności na inne.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) stanowisku administratora konta.
1. Wyszukiwanie **Cost Management + rozliczenia**.

    ![Zrzut ekranu pokazujący wyszukiwania](./media/billing-how-to-change-credit-card/search.png)

1. Wybierz subskrypcję, czy chcesz dodać karty kredytowej lub debetowej do.
1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcją metody płatności Zarządzanie.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaznacz pole obok karty, czy chcesz wprowadzić aktywną formę płatności.
1. Kliknij przycisk **aktywne**.
    ![Zrzut ekranu pokazujący karty kredytowej lub debetowej zaznaczone i Ustaw aktywne.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Usuwanie karty kredytowej lub debetowej z konta

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) stanowisku administratora konta.
1. Wybierz **Zarządzanie kosztami i rozliczenia** w lewej części strony.

    ![Zrzut ekranu pokazujący wyszukiwania](./media/billing-how-to-change-credit-card/search.png)

1. W obszarze **rozliczeń**, wybierz opcję **metodami płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcją metody płatności Zarządzanie.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaznacz pole wyboru obok kart, które chcesz usunąć.
1. Kliknij przycisk **Usuń**.

Jeśli karta kredytowa jest aktywną formę płatności dla żadnej z Twoich subskrypcji firmy Microsoft, nie możesz usunąć go z kontem platformy Azure. Zmień aktywną formę płatności dla wszystkich subskrypcji, połączone z tym karta kredytowa lub debetowa i spróbuj ponownie
<!-- # Add, update, or remove a credit or debit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**. 

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card. 

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->
## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moja subskrypcja jest wyłączona. Dlaczego nie można teraz usunąć Moja karta kredytowa?

Po Twoja subskrypcja została wyłączona lub zostało anulowane, poczekamy 90 dni przed trwale Twojej subskrypcji. Przechowujemy Twojej bieżącej formy płatności pliku podczas okresu przechowywania danych w przypadku, gdy chcesz ponownie aktywować subskrypcję. Po tym subskrypcji zostaną trwale usunięte.

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

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
