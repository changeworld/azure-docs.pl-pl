---
title: Płatność dla subskrypcji platformy Azure przy użyciu faktury
description: Opisuje sposób płatności dla subskrypcji platformy Azure za pomocą faktury.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 9ca726ef737ce4750018d2461bc4bcd6c7ebb5f5
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491226"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Płatność dla subskrypcji platformy Azure przy użyciu faktury

Jeśli przełącznik przy użyciu faktury, oznacza to, że płaci się w ciągu 30 dni od daty wskazanej na fakturze za wyboru/polecenie przelewu bankowego na rachunku. Aby czerpać płatności dla subskrypcji platformy Azure za pomocą faktury, należy przesłać żądanie pomocy technicznej platformy Azure. Gdy Twoje żądanie zostanie zatwierdzone, możesz przełączyć się do faktury płatność (wyboru/przelew bankowy) w [witryny Azure portal](https://portal.azure.com).

> [!IMPORTANT]
> * Płatność faktury (wyboru/przelew bankowy) jest dostępna tylko dla konta firmowe.
> * Przed przełączeniem do faktury płatności, musisz zapłacić wszystkie zaległe opłaty.

## <a name="request-to-pay-by-invoice"></a>Żądanie przy użyciu faktury

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Wybierz **Pomoc i obsługa techniczna** > **nowe żądanie obsługi**.

    ![Pomoc i obsługa techniczna łącza](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Wybierz **rozliczeń** jako **typ problemu**. *Typ problemu* jest kategoria żądania pomocy technicznej. Wybierz subskrypcję, dla którego chcesz zapłacić za pomocą faktury, wybierz plan pomocy technicznej, a następnie wybierz **dalej**.

3. Wybierz wartość **Płatność za pomocą faktury** w polu **Typ problemu**. *Typ problemu* jest podkategorię żądania pomocy technicznej.

4. Wprowadź następujące informacje w **szczegóły** , a następnie wybierz **dalej**.

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

    - **Nazwa firmy** i **firmy adres** powinny odpowiadać informacje podane dla konta platformy Azure. Aby wyświetlić lub zaktualizować informacje, zobacz [zmiany informacji o Twoim profilu konta platformy Azure](billing-how-to-change-azure-account-profile.md).
    - Zanim może zostać zatwierdzone limit środków, należy dodać swoje informacje kontaktowe rozliczeń w witrynie Azure portal. Szczegółowe dane kontaktowe powinny być związane z działu płatne konta lub finansów firmy. Aby zaktualizować informacje kontaktowe rozliczeń, przejdź do [Centrum konta platformy Azure](https://account.azure.com/Profile).

5. Zweryfikuj informacje kontaktowe i preferowaną metodę kontaktu, a następnie kliknij polecenie **Utwórz**.

Jeśli będzie trzeba przeprowadzić kontrolę kredytową przez ze względu na ilość środków, które są potrzebne, wyślemy Ci środki sprawdzenie aplikacji.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Zmień na fakturę płatność (wyboru/przelew bankowy)

Po zatwierdzeniu przy użyciu faktury można przełączyć do faktury płatność (wyboru/przelew bankowy) w witrynie Azure portal.

Jeśli masz konto Microsoft Online Services Program, można przełączyć subskrypcji platformy Azure do wyboru/podczas transferu. Jeśli masz umowy klienta firmy Microsoft, możesz przełączyć profil rozliczeń do wyboru/podczas transferu. [Dowiedz się, jak sprawdzić typ konta](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Przełącz subskrypcji platformy Azure do wyboru/przewodowy transferu

Wykonaj poniższe kroki, aby przełączyć subskrypcji platformy Azure do faktury płatność (wyboru/przelew bankowy). **Kiedy przełączysz się do faktury płatność (wyboru/przelew bankowy) nie można przełączyć do karty kredytowej**.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) stanowisku administratora konta.
1. Wyszukaj **Cost Management + rozliczenia**.

    ![Zrzut ekranu pokazujący wyszukiwania](./media/billing-how-to-pay-by-invoice/search.png)

1. Wybierz subskrypcję, którą chcesz się przełączyć do faktury płatności.
1. Wybierz pozycję **Formy płatności**.
1. Na pasku poleceń kliknij **przy użyciu faktury** przycisku.

    ![Zrzut ekranu pokazujący płacić za przycisku faktury](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Przełącz profil rozliczeniowy do wyboru/przewodowy transferu

Wykonaj poniższe kroki, aby przełączyć profil rozliczeniowy do wyboru/podczas transferu. Należy pamiętać, że osoby, który utworzył konto na platformie Azure można zmienić domyślną metodę płatności profil rozliczeniowy.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.
1. W menu po lewej stronie kliknij pozycję **profile rozliczeń**.

    ![Zrzut ekranu przedstawiający profil rozliczeniowy, w menu](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Wybierz profil rozliczeniowy.
1. W menu po lewej stronie wybierz **metodami płatności**.

   ![Zrzut ekranu pokazujący formy płatności w menu](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Kliknij niebieski Baner, stwierdzający, że masz prawo do regulowania płatności przy wyboru/przelew bankowy.

    ![Zrzut ekranu pokazujący niebieski Baner, aby przełączyć się do wyboru/o komunikacji sieciowej](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

- W razie potrzeby zaktualizuj swoje informacje kontaktowe rozliczeń w [Centrum konta platformy Azure](https://account.azure.com/Profile).
