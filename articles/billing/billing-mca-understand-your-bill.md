---
title: Omówienie opłat na fakturze za umowę klienta firmy Microsoft — Azure
description: Dowiedz się, jak czytać fakturę i co oznaczają poszczególne opłaty.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8ab6cf061531a1ef3c72b2f36d25932c7498d291
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345288"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Omówienie opłat na fakturze za umowę klienta firmy Microsoft

Aby wyjaśnić opłaty wymienione na fakturze, przeanalizuj poszczególne transakcje. Na koncie rozliczeniowym umowy klienta firmy Microsoft co miesiąc generowana jest jedna faktura na każdy profil rozliczeniowy. Faktura zawiera wszystkie opłaty z poprzedniego miesiąca. Możesz wyświetlać faktury w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Pobieranie faktur za umowę klienta firmy Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Wyświetlanie transakcji uwzględnionych na fakturze w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://www.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Wybierz pozycję **Wszystkie transakcje** po lewej stronie. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego, profilu rozliczeniowego lub sekcji faktury, a następnie wybranie pozycji **Wszystkie transakcje**.

4. Na stronie Wszystkie transakcje są wyświetlane następujące informacje:

    ![Zrzut ekranu przedstawiający listę rozliczonych transakcji](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Kolumna  |Definicja  |
    |---------|---------|
    |Date     | Data transakcji  |
    |Identyfikator faktury     | Identyfikator faktury, na której jest uwzględniona transakcja. Jeśli przesyłasz wniosek o pomoc techniczną, podaj ten identyfikator, aby przyspieszyć obsługę wniosku przez zespół pomocy technicznej platformy Azure |
    |Typ transakcji     |  Typ transakcji, na przykład zakup, anulowanie, opłaty za użycie  |
    |Rodzina produktów     | Kategoria produktu, na przykład obliczenia w przypadku maszyn wirtualnych lub baza danych w przypadku usługi Azure SQL Database|
    |Jednostka SKU produktu     | Unikatowy kod identyfikujący wystąpienie produktu |
    |Kwota     |  Kwota transakcji      |
    |Sekcja faktury     | Sekcja faktury danego profilu rozliczeniowego, w której jest widoczna ta transakcja |
    |Profil rozliczeniowy     | Profil rozliczeniowy, na którego fakturze jest widoczna ta transakcja |

5. Możesz wyszukać identyfikator faktury, aby odfiltrować transakcje z tej faktury.

### <a name="view-transactions-by-invoice-sections"></a>Wyświetlanie transakcji według sekcji faktury

Sekcje faktur ułatwiają organizowanie kosztów na fakturze danego profilu rozliczeniowego. Aby uzyskać więcej informacji, zobacz [Omówienie sekcji faktur](billing-mca-overview.md#invoice-sections). Po wygenerowaniu faktury są na niej widoczne opłaty z wszystkich sekcji w ramach danego profilu rozliczeniowego.

Na poniżej ilustracji przedstawiono opłaty z sekcji działu księgowości na przykładowej fakturze.

![Przykładowy obraz pokazujący szczegółowe informacje w sekcji faktury](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Po znalezieniu opłat w odpowiedniej sekcji faktury możesz wyświetlić transakcje w witrynie Azure Portal, aby wyjaśnić opłaty.

1. Przejdź na stronę Wszystkie transakcje w witrynie Azure Portal, aby wyświetlić transakcje z faktury. Aby uzyskać więcej informacji, zobacz [Wyświetlanie transakcji uwzględnionych na fakturze w witrynie Azure Portal](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Możesz filtrować według nazwy sekcji faktury, aby wyświetlić odpowiednie transakcje.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat

Na koncie rozliczeniowym umowy klienta firmy Microsoft niezafakturowane opłaty są szacowane i wyświetlane jako oczekujące. Możesz wyświetlić oczekujące opłaty w witrynie Azure Portal, aby oszacować kwotę kolejnej faktury. Oczekujące opłaty są szacunkowe i nie zawierają podatków. Rzeczywiste opłaty na kolejnej fakturze mogą się różnić od opłat oczekujących.

### <a name="view-summary-of-pending-charges"></a>Wyświetlanie podsumowania opłat oczekujących

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

4. Wybierz kartę **Podsumowanie** w górnej części ekranu.

5. W sekcji Opłaty są wyświetlane opłaty za bieżący i poprzedni miesiąc.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące i pojawią się na najbliższej fakturze.

### <a name="view-pending-transactions"></a>Wyświetlanie oczekujących transakcji

Gdy znajdziesz oczekujące opłaty, możesz je wyjaśnić, analizując poszczególne transakcje, które im odpowiadają. Obecnie oczekujące opłaty za użycie nie są wyświetlane na stronie Wszystkie transakcje. Możesz wyświetlić je na stronie subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Wyświetlanie oczekujących opłat za użycie](#view-pending-usage-charges).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

4. Wybierz pozycję **Wszystkie transakcje** po lewej stronie.

5. Wyszukaj *oczekujące*. Użyj filtru **Przedział czasu**, aby wyświetlić opłaty za bieżący lub ubiegły miesiąc.

   ![Zrzut ekranu przedstawiający listę oczekujących transakcji](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Wyświetlanie oczekujących opłat za użycie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

4. Wybierz pozycję **Wszystkie subskrypcje** po lewej stronie.

5. Na stronie subskrypcji platformy Azure są wyświetlane opłaty za bieżący i ubiegły miesiąc, dotyczące poszczególnych subskrypcji z profilu rozliczeniowego. Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące.

    ![Zrzut ekranu przedstawiający listę subskrypcji platformy Azure dla profilu rozliczeniowego](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analizowanie opłat za użycie platformy Azure

Możesz przeanalizować opłaty związane z użyciem, korzystając z pliku CSV z danymi o użyciu platformy Azure i opłatach. Możesz pobrać taki plik dla faktury lub dla oczekujących opłat. Aby uzyskać więcej informacji, zobacz [Pobieranie faktury rozliczeniowej za platformę Azure i danych dziennego użycia](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Wyświetlanie szczegółów użycia według sekcji faktury

Możesz przefiltrować plik z danymi o użyciu platformy Azure i opłatach, aby uzgodnić opłaty za użycie w ramach poszczególnych sekcji faktury.

Poniżej przedstawiono kroki uzgadniania opłat za obliczenia w ramach sekcji faktury działu księgowości:

![Przykładowy obraz pokazujący szczegółowe informacje w sekcji faktury](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Faktura (PDF) | Plik CSV użycia platformy Azure i opłata |
 | --- | --- |
 |Dział księgowości |invoiceSectionName |
 |Opłaty za użycie — plan Microsoft Azure |productOrderName |
 |Wystąpienia obliczeniowe |serviceFamily |

1. Przefiltruj kolumnę **invoiceSectionName** w pliku CSV według nazwy **Dział księgowości**.
2. Przefiltruj kolumnę **productOrderName** w pliku CSV według produktu **plan Microsoft Azure**.
3. Przefiltruj kolumnę **servicefamily** w pliku CSV według wartości **Microsoft.Compute**.

![Zrzut ekranu przedstawiający plik użycia i opłat przefiltrowany według sekcji faktury](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Wyświetlanie szczegółów użycia według subskrypcji

Możesz przefiltrować plik CSV z danymi o użyciu platformy Azure i opłatach, aby uzgodnić opłaty za użycie w ramach poszczególnych subskrypcji. Jeśli chcesz wyświetlić wszystkie subskrypcje w profilu rozliczeniowym, zobacz [Wyświetlanie oczekujących opłat za użycie](#view-pending-usage-charges).

Gdy znajdziesz opłaty w ramach określonej subskrypcji, możesz je przeanalizować, korzystając z pliku CSV z danymi o użyciu platformy Azure i opłatach.

Na poniższej ilustracji przedstawiono listę subskrypcji w witrynie Azure Portal.

![Zrzut ekranu przedstawiający listę subskrypcji platformy Azure dla profilu rozliczeniowego](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Przefiltruj kolumnę **subscriptionName** w pliku CSV użycia platformy Azure i opłat według wartości **WA_Subscription**, aby wyświetlić szczegółowe informacje o opłatach za użycie w ramach tej subskrypcji.

![Zrzut ekranu przedstawiający plik użycia i opłat przefiltrowany według subskrypcji](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Płatność za rachunek

Instrukcje dotyczące płatności znajdują się na końcu faktury. [Dowiedz się, jak zapłacić](billing-mca-understand-your-invoice.md#how-to-pay).

Jeśli już zapłacono za rachunek, możesz sprawdzić stan płatności na stronie Faktury w witrynie Azure Portal.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat faktury i szczegółów użycia, zobacz:

- [Jak uzyskać fakturę rozliczeniową za platformę Azure i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md)
- [Omówienie terminów na fakturze za umowę klienta firmy Microsoft](billing-mca-understand-your-invoice.md)
- [Omówienie terminów w pliku CSV z danymi o użyciu w ramach umowy klienta firmy Microsoft](billing-mca-understand-your-usage.md)
