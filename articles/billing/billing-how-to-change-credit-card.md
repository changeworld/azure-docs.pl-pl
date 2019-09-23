---
title: Zmiana karty kredytowej dla platformy Azure
description: Opisuje sposób zmiany karty kredytowej używanej do płacenia za subskrypcję platformy Azure.
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
ms.openlocfilehash: 05b1fe3d061e735322e6eb6c65e60f63d8adb4d6
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933871"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Dodawanie, aktualizowanie lub usuwanie karty kredytowej dla platformy Azure

Ten dokument dotyczy klientów, którzy zarejestrowali się na platformie Azure w trybie online przy użyciu karty kredytowej.

W witrynie Azure Portal możesz dodać nową, zaktualizować istniejącą lub usunąć nieużywaną kartę kredytową. Musisz być [administratorem konta](billing-subscription-transfer.md#whoisaa), aby wprowadzić te zmiany.

W przypadku [umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement) formy płatności są skojarzone z profilami rozliczeniowymi. Dowiedz się, jak [zmienić domyślną formę płatności dla profilu rozliczeniowego](#change-payment-method-for-a-billing-profile). Tylko użytkownik, który utworzył konto na platformie Azure, może zaktualizować formę płatności.

**Czy chcesz przejść na płatność za pomocą faktury (czek/przelew)?** Zobacz [Płatność za subskrypcje platformy Azure przy użyciu faktury](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Dodawanie nowej karty kredytowej do subskrypcji platformy Azure

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie](./media/billing-how-to-change-credit-card/search.png)

1. Wybierz subskrypcję, do której chcesz dodać kartę kredytową.
1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję Zarządzaj metodami płatności.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. W lewym górnym rogu wybierz pozycję „+”, aby dodać kartę. Po prawej stronie zostanie wyświetlony formularz karty kredytowej.
1. Podaj szczegółowe informacje dotyczące karty kredytowej.

    ![Zrzut ekranu pokazujący dodawanie nowej karty.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Aby ustawić tę kartę jako aktywną formę płatności, zaznacz pole wyboru obok pola **Ustaw jako moją aktywną formę płatności** znajdującego się powyżej formularza. Ta karta stanie się aktywnym instrumentem płatniczym dla wszystkich subskrypcji używających tej karty jako wybranej subskrypcji.

1. Wybierz opcję **Dalej**.

Jeśli po dodaniu karty kredytowej wystąpi błąd, zobacz [Karta kredytowa została odrzucona podczas tworzenia konta platformy Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Aktualizowanie istniejącej karty kredytowej

Jeśli karta kredytowa jest odnawiana, a numer pozostaje taki sam, zaktualizuj istniejące szczegóły karty kredytowej, takie jak data wygaśnięcia. Jeśli numer karty kredytowej zmienia się, ponieważ karta została utracona, skradziona lub wygasła, wykonaj kroki opisane w sekcji [Dodawanie karty kredytowej jako formy płatności](#addcard). Nie musisz aktualizować kodu CVV.

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie](./media/billing-how-to-change-credit-card/search.png)

1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję Zarządzaj metodami płatności.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Kliknij kartę kredytową, którą chcesz edytować. Po prawej stronie zostanie wyświetlony formularz karty kredytowej.

    ![Zrzut ekranu przedstawiający wybraną kartę kredytową.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Zaktualizuj szczegółowe informacje o karcie kredytowej.
1. Wybierz pozycję **Zapisz**.

## <a name="use-a-different-credit-card"></a>Użycie innej karty kredytowej

Jeśli więcej niż jedna subskrypcja ma aktywną tę samą formę płatności, zmiana aktywnej formy płatności dowolnej z tych subskrypcji spowoduje także zaktualizowanie aktywnej formy płatności pozostałych subskrypcji.

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie](./media/billing-how-to-change-credit-card/search.png)

1. Wybierz subskrypcję, do której chcesz dodać kartę kredytową.
1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję Zarządzaj metodami płatności.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaznacz pole obok karty, którą chcesz ustawić jako aktywną formę płatności.
1. Kliknij pozycję **Ustaw aktywną**.
    ![Zrzut ekranu przedstawiający wybraną i aktywowaną kartę kredytową.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Usuwanie karty kredytowej z konta

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wybierz pozycję **Zarządzanie kosztami i rozliczenia** w lewej części strony.

    ![Zrzut ekranu przedstawiający wyszukiwanie](./media/billing-how-to-change-credit-card/search.png)

1. W obszarze **Rozliczenia** wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję Zarządzaj metodami płatności.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaznacz pole wyboru obok karty, którą chcesz usunąć.
1. Kliknij polecenie **Usuń**.

Jeśli karta kredytowa jest aktywną formą płatności dla którejkolwiek z subskrypcji firmy Microsoft, nie możesz usunąć jej z konta platformy Azure. Zmień aktywną formę płatności dla wszystkich subskrypcji połączonych z tą kartą kredytową i spróbuj ponownie.
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

## <a name="change-payment-method-for-a-billing-profile"></a>Zmiana formy płatności dla profilu rozliczeniowego

Aby zmienić formę płatności dla profilu rozliczeniowego, musisz być osobą zarejestrowaną na platformie Azure.

Jeśli chcesz zmienić domyślną formę płatności na czek/przelew, dowiedz się, jak [przełączyć profil rozliczeniowy na czek/przelew](billing-how-to-pay-by-invoice.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
1. W menu po lewej stronie kliknij pozycję **Profile rozliczeniowe**.

    ![Zrzut ekranu przedstawiający profil rozliczeniowy w menu](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Wybierz profil rozliczeniowy.
1. W menu po lewej stronie wybierz pozycję **Formy płatności**.

   ![Zrzut ekranu przedstawiający formy płatności w menu](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Powyżej domyślnej formy płatności kliknij pozycję **Zmień**.

    ![Zrzut ekranu pokazujący przycisk zmiany](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Wybierz istniejącą kartę lub dodaj nową.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
W poniższych sekcjach zamieszczono odpowiedzi na często zadawane pytania dotyczące zmiany danych karty kredytowej.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moja subskrypcja jest wyłączona. Dlaczego nie mogę teraz usunąć karty kredytowej?

Po wyłączeniu lub anulowaniu subskrypcji czekamy 90 dni przed trwałym usunięciem subskrypcji. W okresie przechowywania zachowujemy informację o formie płatności na wypadek ponownego aktywowania subskrypcji. Po tym okresie subskrypcja zostanie trwale usunięta.

Jeśli musisz usunąć kartę kredytową przed upływem 90-dniowego okresu przechowywania, [aktywuj ponownie swoją subskrypcję](billing-subscription-become-disable.md). Jeśli nie możesz przeprowadzić ponownej aktywacji, [ skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Dlaczego ciągle otrzymuję komunikat „Twoja sesja wygasła. Kliknij tutaj, aby ponownie się zalogować.”?

Jeśli ten komunikat o błędzie pojawia się nawet po wylogowaniu i ponownym zalogowaniu, spróbuj ponownie za pomocą prywatnej sesji przeglądania.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak mogę użyć innej karty dla każdej mojej subskrypcji?

Niestety, jeśli subskrypcje już korzystają z tej samej karty, nie jest możliwe rozdzielenie ich, tak aby korzystały z różnych kart. Jednak po zarejestrowaniu nowej subskrypcji możesz wybrać dla niej nową formę płatności.

### <a name="how-do-i-make-payments"></a>Jak mogę płacić?

Jeśli skonfigurujesz kartę kredytową jako formę płatności, automatycznie obciążamy kartę po każdym okresie rozliczeniowym. Nie musisz wykonywać żadnych czynności.

W przypadku [płatności przy użyciu faktury](billing-how-to-pay-by-invoice.md) wyślij płatność do lokalizacji wskazanej na dole faktury.

### <a name="how-do-i-change-the-tax-id"></a>Jak mogę zmienić numer NIP?

Aby dodać lub zaktualizować numer NIP, zaktualizuj swój profil w [Centrum konta platformy Azure](https://account.azure.com/Profile), a następnie wybierz pozycję **Rejestr podatkowy**. Numer NIP jest używany do obliczenia kwoty zwolnienia z podatku i pojawia się na fakturze.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [rezerwacjami platformy Azure](billing-save-compute-costs-reservations.md), aby dowiedzieć się, czy możesz oszczędzić pieniądze.
