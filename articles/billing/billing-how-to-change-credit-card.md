---
title: Zmień kartę kredytową na platformę Azure
description: Opisuje, w jaki sposób zmienić kartę kredytową używaną do płacenia za subskrypcję platformy Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7719ae83525883a6d3f014dbb99877b7319f2ccd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383663"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Dodawanie, aktualizowanie lub usuwanie karty kredytowej na platformie Azure

W Azure Portal możesz dodać nową kartę kredytową, zaktualizować istniejącą kartę kredytową lub usunąć kartę kredytową, której nie używasz. Musisz być [administratorem konta](billing-subscription-transfer.md#whoisaa) , aby wprowadzić te zmiany.

Jeśli masz [umowę klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement), Twoje metody płatności są skojarzone z profilami rozliczeń. Dowiedz się [, jak zmienić domyślną metodę płatności dla profilu](#change-payment-method-for-a-billing-profile)rozliczania. Tylko użytkownik, który zarejestrował się na platformie Azure, może zaktualizować metodę płatności.

**Czy chcesz przełączyć się na płatność za pomocą faktury (czek/przelew)?** Zobacz [płatność za subskrypcje platformy Azure według faktury](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Dodaj nową kartę kredytową do subskrypcji platformy Azure

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj **Cost Management i rozliczenia**.

    ![Zrzut ekranu pokazujący wyszukiwanie](./media/billing-how-to-change-credit-card/search.png)

1. Wybierz subskrypcję, do której chcesz dodać kartę kredytową.
1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję zarządzania metodami płatności.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. W lewym górnym rogu wybierz pozycję „+”, aby dodać kartę. Po prawej stronie zostanie wyświetlony formularz karty kredytowej.
1. Wprowadź szczegóły karty kredytowej.

    ![Zrzut ekranu pokazujący Dodawanie nowej karty.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Aby zapewnić tej karcie aktywną formę płatności, zaznacz pole wyboru obok formularza **Ustaw tę moją aktywną metodę płatności** . Ta karta stanie się aktywnym instrumentem płatniczym dla wszystkich subskrypcji używających tej karty jako wybranej subskrypcji.

1. Wybierz opcję **Dalej**.

Jeśli wystąpi błąd po dodaniu karty kredytowej, zobacz [karta kredytowa odrzucona na platformie Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Aktualizowanie istniejącej karty kredytowej

Jeśli karta kredytowa zostanie odnowiona, a liczba pozostaje taka sama, zaktualizuj istniejące szczegóły karty kredytowej, takie jak data wygaśnięcia. Jeśli numer karty kredytowej ulegnie zmianie, ponieważ karta zostanie utracona, skradziona lub wygasła, wykonaj kroki opisane w sekcji [Dodawanie karty kredytowej jako metody płatności](#addcard) . Nie musisz aktualizować CVV.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj **Cost Management i rozliczenia**.

    ![Zrzut ekranu pokazujący wyszukiwanie](./media/billing-how-to-change-credit-card/search.png)

1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję zarządzania metodami płatności.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Kliknij kartę kredytową, którą chcesz edytować. Po prawej stronie zostanie wyświetlony formularz karty kredytowej.

    ![Zrzut ekranu przedstawiający wybraną kartę kredytową.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Zaktualizuj szczegóły karty kredytowej.
1. Wybierz pozycję **Zapisz**.

## <a name="use-a-different-credit-card"></a>Użyj innej karty kredytowej

Jeśli więcej niż jedna z subskrypcji ma tę samą aktywną metodę płatności, zmiana aktywnej metody płatności w dowolnej z tych subskrypcji spowoduje również zaktualizowanie aktywnej metody płatności dla innych osób.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj **Cost Management i rozliczenia**.

    ![Zrzut ekranu pokazujący wyszukiwanie](./media/billing-how-to-change-credit-card/search.png)

1. Wybierz subskrypcję, do której chcesz dodać kartę kredytową.
1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję zarządzania metodami płatności.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaznacz pole obok karty, dla której chcesz wybrać aktywną metodę płatności.
1. Kliknij pozycję **Ustaw aktywny**.
    ![Zrzut ekranu przedstawiający wybraną kartę kredytową i ustawioną wartość aktywną.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Usuń kartę kredytową z konta

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wybierz pozycję **Cost Management i rozliczenia** w lewej części strony.

    ![Zrzut ekranu pokazujący wyszukiwanie](./media/billing-how-to-change-credit-card/search.png)

1. W obszarze rozliczenia wybierz pozycję **formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję zarządzania metodami płatności.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaznacz pole obok karty, która ma zostać usunięta.
1. Kliknij przycisk **Usuń**.

Jeśli Twoja karta kredytowa jest aktywną formą płatności dla którejkolwiek z subskrypcji firmy Microsoft, nie możesz usunąć jej z konta platformy Azure. Zmień aktywną metodę płatności dla wszystkich subskrypcji połączonych z tą kartą kredytową i spróbuj ponownie
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Zmiana formy płatności dla profilu rozliczeń

Aby zmienić formę płatności dla profilu rozliczeń, musisz być osobą, która zarejestrowała się na platformie Azure.

Jeśli chcesz zmienić domyślną metodę płatności na transfer/przelew, Dowiedz się, jak [przełączyć profil rozliczeń w celu sprawdzenia/transferu](billing-how-to-pay-by-invoice.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.
1. W menu po lewej stronie kliknij pozycję **Profile**rozliczeń.

    ![zrzut ekranu przedstawiający profil rozliczeń w menu](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Wybierz profil rozliczeń.
1. W menu po lewej stronie wybierz pozycję **formy płatności**.

   ![Zrzut ekranu przedstawiający metody płatności w menu](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Powyżej domyślnej metody płatności kliknij przycisk **Zmień**.

    ![Zrzut ekranu pokazujący przycisk zmiany](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Wybierz istniejącą kartę lub Dodaj nową.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
W poniższych sekcjach zamieszczono odpowiedzi na często zadawane pytania dotyczące zmiany danych karty kredytowej.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moja subskrypcja jest wyłączona. Dlaczego nie mogę usunąć karty kredytowej teraz?

Po wyłączeniu lub anulowaniu subskrypcji czekamy 90 dni przed trwałym usunięciem subskrypcji. Utrzymujemy Twoją formę płatności w pliku w okresie przechowywania, jeśli chcesz ponownie aktywować subskrypcję. Następnie subskrypcja zostanie trwale usunięta.

Jeśli musisz usunąć kartę kredytową przed upływem 90-dniowego okresu przechowywania, [ponownie Aktywuj swoją subskrypcję](billing-subscription-become-disable.md). Jeśli nie możesz przeprowadzić ponownej aktywacji, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Dlaczego otrzymuję "sesja logowania wygasła. Kliknij tutaj, aby zalogować się ponownie "?

Jeśli ten komunikat o błędzie będzie nadal występować nawet wtedy, gdy wcześniej wylogowano się z powrotem, spróbuj ponownie, używając prywatnej sesji przeglądania.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak mogę użyć innej karty dla każdej posiadanej subskrypcji?

Niestety, jeśli subskrypcje już korzystają z tej samej karty, nie jest możliwe oddzielenie ich do korzystania z różnych kart. Jednak po zarejestrowaniu się w celu uzyskania nowej subskrypcji można wybrać opcję użycia nowej formy płatności dla tej subskrypcji.

### <a name="how-do-i-make-payments"></a>Jak mogę wprowadzić płatności?

Jeśli skonfigurujesz kartę kredytową jako formę płatności, automatycznie naliczamy kartę po każdym okresie rozliczeniowym. Nie musisz nic robić.

Jeśli [płacisz według faktury](billing-how-to-pay-by-invoice.md), Wyślij płatność do lokalizacji wymienionej w dolnej części faktury.

### <a name="how-do-i-change-the-tax-id"></a>Jak mogę zmienić identyfikator podatkowy?

Aby dodać lub zaktualizować identyfikator podatkowy, Zaktualizuj swój profil w [centrum konta platformy Azure](https://account.azure.com/Profile)a następnie wybierz pozycję **rekord podatkowy**. Numer NIP jest używany do obliczenia kwoty zwolnienia z podatku i pojawia się na fakturze.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [rezerwacjach platformy Azure](billing-save-compute-costs-reservations.md) , aby sprawdzić, czy mogą oni zaoszczędzić pieniądze.
