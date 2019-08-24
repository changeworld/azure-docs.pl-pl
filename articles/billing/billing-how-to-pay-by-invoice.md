---
title: Płatność za subskrypcje platformy Azure według faktury
description: Opisuje sposób płacenia za subskrypcje platformy Azure według faktury.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: 9e4e05acd88e9b0f0c17d4dd4caf5eb5a883d63d
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012591"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Płatność za subskrypcję platformy Azure według faktury

Jeśli przełączysz się na płatność za pomocą faktury, oznacza to, że opłata jest naliczana w ciągu 30 dni od daty faktury przez transfer/przelew. Aby kwalifikować się do płacenia za subskrypcję platformy Azure za pomocą faktury, Prześlij żądanie do pomocy technicznej platformy Azure. Gdy żądanie zostanie zatwierdzone, możesz przełączyć się do faktury płatność (czek/przelew) w [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> * Płatność faktury (czek/przelew) jest dostępna tylko dla kont firmowych.
> * Przed przełączeniem na fakturę płatność należy uregulować wszystkie zaległe opłaty.
> * Obecnie płatność faktury nie jest obsługiwana w przypadku globalnej platformy Azure w Chinach.

## <a name="request-to-pay-by-invoice"></a>Żądanie wypłaty według faktury

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Wybierz pozycję **Pomoc i obsługa** > **nowe żądanie obsługi**.

    ![Link do pomocy i obsługi technicznej](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Wybierz pozycję rozliczenia jako **typ problemu**. *Typ problemu* to kategoria żądania pomocy technicznej. Wybierz subskrypcję, dla której chcesz uiścić płatność według faktury, wybierz plan pomocy technicznej, a następnie wybierz przycisk **dalej**.

3. Wybierz **płatność** jako **typ problemu**. *Typ problemu* to Podkategoria żądania pomocy technicznej.

4. Wybierz pozycję **Przełącz na płatność według faktury** jako **podtyp problemu**

5. Wprowadź następujące informacje w polu **szczegóły** , a następnie wybierz przycisk **dalej**.

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

    - **Nazwa firmy** i **adres firmy** powinny być zgodne z informacjami podanymi dla konta platformy Azure. Aby wyświetlić lub zaktualizować informacje, zobacz [zmiana informacji o profilu konta platformy Azure](billing-how-to-change-azure-account-profile.md).
    - Dodaj informacje kontaktowe dotyczące rozliczeń w Azure Portal przed zatwierdzeniem limitu kredytowego. Szczegóły kontaktu powinny być powiązane z działem rachunków firmy lub działu finansów. Aby zaktualizować informacje kontaktowe dotyczące rozliczeń, przejdź do [centrum konta platformy Azure](https://account.azure.com/Profile).

6. Zweryfikuj informacje kontaktowe i preferowaną metodę kontaktu, a następnie kliknij polecenie **Utwórz**.

Jeśli konieczne jest przetestowanie kredytu z powodu wymaganej kwoty kredytu, wyślemy Ci wniosek o sprawdzenie kredytu.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Przejdź do faktury płatność (czek/przelew)

Gdy zatwierdzisz płatność za pomocą faktury, możesz przełączyć się do faktury płatność (czek/przelew) w Azure Portal.

Jeśli masz konto programu Microsoft Online Services, możesz przełączyć swoją subskrypcję platformy Azure w celu sprawdzenia/transferu. Umowa klienta firmy Microsoft umożliwia przełączenie profilu rozliczeń w celu sprawdzenia/transferu danych. [Dowiedz się, jak sprawdzić typ konta](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Przełącz subskrypcję platformy Azure w celu sprawdzenia/transferu

Postępuj zgodnie z poniższymi instrukcjami, aby przełączyć subskrypcję platformy Azure na fakturę (czek/transfer). **Po przełączeniu do faktury płatność (czek/transfer) nie można wrócić do karty kredytowej**.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj **Cost Management i rozliczenia**.

    ![Zrzut ekranu pokazujący wyszukiwanie](./media/billing-how-to-pay-by-invoice/search.png)

1. Wybierz subskrypcję, którą chcesz przełączyć na fakturę.
1. Wybierz pozycję **Formy płatności**.
1. Na pasku poleceń kliknij przycisk **płatność według faktury** .

    ![Zrzut ekranu przedstawiający przycisk płatność według faktury](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Przełącz profil rozliczeń w celu sprawdzenia/transferu

Postępuj zgodnie z poniższymi instrukcjami, aby przełączyć profil rozliczeń w celu sprawdzenia/transferu. Tylko osoba, która zarejestrowała się na platformie Azure, może zmienić domyślną metodę płatności dla profilu rozliczeń.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.
1. W menu po lewej stronie kliknij pozycję **Profile**rozliczeń.

    ![zrzut ekranu przedstawiający profil rozliczeń w menu](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Wybierz profil rozliczeń.
1. W menu po lewej stronie wybierz pozycję **formy płatności**.

   ![Zrzut ekranu przedstawiający metody płatności w menu](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Kliknij niebieski baner, który oznacza, że kwalifikujesz się do płacenia za transfer/przelew.

    ![Zrzut ekranu pokazujący niebieski baner, aby przełączyć się na znacznik wyboru](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- W razie potrzeby Zaktualizuj informacje kontaktowe dotyczące rozliczeń na [centrum konta platformy Azure](https://account.azure.com/Profile).
