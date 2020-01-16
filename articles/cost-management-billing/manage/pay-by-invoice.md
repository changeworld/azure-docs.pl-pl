---
title: Płatność za subskrypcje platformy Azure przy użyciu faktury
description: W artykule opisano płatność za subskrypcje platformy Azure przy użyciu faktury.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: a0f012145788d2d1d4935e10691859e5aaf71255
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994339"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Płatność za subskrypcje platformy Azure przy użyciu faktury

Jeśli przełączysz się na płatność na podstawie faktury, konieczne będzie zapłacenie rachunku czekiem lub przelewem w ciągu 30 dni od daty jego wystawienia. Aby kwalifikować się do płacenia za subskrypcję platformy Azure na podstawie faktury, prześlij żądanie do pomocy technicznej platformy Azure. Jeśli Twoje żądanie zostanie zatwierdzone, w witrynie[Azure Portal](https://portal.azure.com) będzie można zmienić subskrypcję na płatność na podstawie faktury (czekiem lub przelewem).

> [!IMPORTANT]
> * Płatność na podstawie faktury (czekiem lub przelewem) jest dostępna tylko dla kont firmowych.
> * Przed przełączeniem się na płatność na podstawie faktury musisz uregulować wszystkie zaległe opłaty.
> * Obecnie płatność faktury nie jest obsługiwana w przypadku globalnej platformy Azure w Chinach.

## <a name="request-to-pay-by-invoice"></a>Żądanie płatności na podstawie faktury

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby przesłać żądanie pomocy technicznej. Wyszukaj i wybierz pozycję **Pomoc i obsługa techniczna**.

    ![Wyszukaj pomoc i pomoc techniczną, Microsoft Azure Portal](./media/pay-by-invoice/search-for-help-and-support.png)

2. Wybierz pozycję **Nowe żądanie obsługi**.

    ![Nowe łącze żądania obsługi, ekran Pomoc i obsługa techniczna, Microsoft Azure Portal](./media/pay-by-invoice/help-and-support.png)

2. W kategorii **Typ problemu** wybierz **Rozliczenia**. *Typ problemu* to kategoria żądania pomocy technicznej. Wybierz subskrypcję, która ma być opłacana za pomocą faktury, wybierz plan pomocy technicznej, a następnie pozycję **Dalej**.

3. W kategorii **Typ problemu** wybierz **Płatność**. *Typ problemu* to podkategoria żądania pomocy technicznej.

4. Wybierz pozycję **Przełącz na płatność według faktury** jako **podtyp problemu**.

5. Wprowadź następujące informacje w polu **Szczegóły**, a następnie wybierz przycisk **Dalej**.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

         For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - Informacje w polach **Nazwa firmy** i **Adres firmy** powinny być zgodne z informacjami podanymi dla konta platformy Azure. Aby wyświetlić lub zaktualizować informacje, zobacz [Zmiana informacji o profilu konta platformy Azure](change-azure-account-profile.md).
    - Przed zatwierdzeniem limitu kredytowego dodaj informacje kontaktowe dotyczące rozliczeń w witrynie Azure Portal. Szczegóły kontaktu powinny być powiązane z księgowością lub działem finansów firmy. Aby zaktualizować informacje kontaktowe dotyczące rozliczeń, przejdź do [Centrum konta platformy Azure](https://account.azure.com/Profile).

6. Sprawdź informacje kontaktowe i preferowaną metodę kontaktu, a następnie wybierz pozycję **Utwórz**.

Jeśli konieczne jest sprawdzenie zdolności kredytowej z powodu wymaganej kwoty kredytu, wyślemy Ci odpowiedni wniosek.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Przełączanie się na płatność na podstawie faktury (czekiem lub przelewem)

Po zatwierdzeniu żądania płatności na podstawie faktury w witrynie Azure Portal można się przełączyć na płatność na podstawie faktury (czekiem lub przelewem).

Jeśli masz konto programu Microsoft Online Services, możesz przełączyć swoją subskrypcję platformy Azure do płatności za pomocą czeku/przelewu. Jeśli masz Umowę klienta firmy Microsoft, możesz przełączyć profil rozliczeniowy na czek lub przelew. [Dowiedz się, jak sprawdzić typ konta](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Przełączanie subskrypcji platformy Azure na płatność czekiem lub przelewem

Postępuj zgodnie z poniższymi instrukcjami, aby przełączyć subskrypcję platformy Azure na płatność na podstawie fakturę (czekiem lub przelewem). *Po przełączeniu na płatność na podstawie faktury (czekiem lub przelewem) nie można wrócić do płatności za pomocą karty kredytowej*.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zalogować się jako administrator konta. Wyszukaj i wybierz pozycję **Cost Management + rozliczenia**.

    ![Wyszukaj Cost Management i rozliczenia, Microsoft Azure Portal](./media/pay-by-invoice/search.png)

1. Wybierz subskrypcję, którą chcesz przełączyć do płatności na podstawie faktury.
1. Wybierz pozycję **Formy płatności**.
1. Na pasku poleceń wybierz przycisk **płatność według faktury** .

    ![Przycisk płatność według faktury, formy płatności, Microsoft Azure Portal](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Przełączanie profilu rozliczeniowego na płatność czekiem lub przelewem

Postępuj zgodnie z poniższymi instrukcjami, aby przełączyć profil rozliczeniowy na płatność czekiem lub przelewem. Aby zmienić domyślną formę płatności profilu rozliczeniowego, musisz być osobą zarejestrowaną na platformie Azure.

1. Przejdź do [Azure Portal](https://portal.azure.com) Wyświetl informacje o rozliczeniach. Wyszukaj i wybierz pozycję **Cost Management + rozliczenia**.
1. Z menu wybierz pozycję **Profile rozliczeń**.

    ![Elementy menu Profile rozliczeń, Cost Management i rozliczeń, Microsoft Azure Portal](./media/pay-by-invoice/billing-profile.png)

1. Wybierz profil rozliczeniowy.
1. W menu **profil rozliczeń** wybierz pozycję **formy płatności**.

   ![Element menu formy płatności, profile rozliczeń, Cost Management, Microsoft Azure Portal](./media/pay-by-invoice/billing-profile-payment-methods.png)

1. Wybierz transparent informujący o tym, że kwalifikujesz się do płacenia za transfer/przelew.

    ![Transparent do przełączania do opcji Check/Wire, form płatności, Microsoft Azure Portal](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- W razie potrzeby zaktualizuj informacje kontaktowe dotyczące rozliczeń w [Centrum konta platformy Azure](https://account.azure.com/Profile).
