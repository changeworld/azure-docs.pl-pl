---
title: Informacje o opłatach na fakturze umowy klienta firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odczytywać i informacje o opłatach na fakturze
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 3c5f5778d68b8df4d4b3132ff96bedb9d5da24ee
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249232"
---
# <a name="understand-the-charges-on-your-microsoft-customer-agreements-invoice"></a>Informacje o opłatach na fakturze umowy klienta firmy Microsoft

Można uzgodnić opłaty na fakturze, analizując poszczególnych transakcji na fakturze.

W koncie rozliczeniowym umowy klienta firmy Microsoft faktury jest generowany co miesiąc przez każdy profil rozliczeniowy. Faktura obejmuje wszystkie opłaty z poprzedniego miesiąca. Fakturach można wyświetlić w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [widoku i pobierania usługi Microsoft Azure faktury](billing-download-azure-invoice.md).

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Wyświetl transakcje dla faktury w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://www.azure.com).

2. Wyszukiwanie **Cost Management + rozliczenia**.

    ![Zrzut ekranu pokazujący Azure portal Wyszukaj Zarządzanie kosztami i rozliczenia](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Wybierz **wszystkie transakcje** po lewej stronie ekranu. W zależności od Twojego dostępu może mieć, aby wybrać konto rozliczeniowe lub profil rozliczeniowy, a następnie wybierz pozycję **wszystkie transakcje**.

4. Stronie wszystkie transakcje są wyświetlane następujące informacje:

    ![Zrzut ekranu przedstawiający listę rozliczane transakcje](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Kolumna  |Definicja  |
    |---------|---------|
    |Date     | Data transakcji  |
    |Identyfikator faktury     | Identyfikator dla faktury, na którym uzyskano rozliczane transakcji. Przesyłając żądanie pomocy technicznej, udostępniać identyfikator pomocy technicznej platformy Azure w celu przyspieszenia obsługi żądania pomocy technicznej |
    |Typ transakcji     |  Typ transakcji, takich jak opłaty za zakup, Anuluj i użycia  |
    |Rodzina produktów     | Kategorie produktów, takich jak zasoby obliczeniowe na potrzeby maszyn wirtualnych lub bazy danych dla bazy danych Azure SQL|
    |Jednostka sku produktu     | Unikatowy kod, który identyfikuje wystąpienia produktu |
    |Ilość     |  Liczba transakcji      |
    |Sekcja faktury     | Transakcja pojawia się w tej sekcji rozliczeń faktury w profilu |
    |Profil rozliczeniowy     | Transakcja pojawia się na fakturze profil rozliczeniowy |

5. Wyszukaj identyfikator faktury do filtrowania transakcji dla faktury.

### <a name="view-transactions-by-invoice-sections"></a>Wyświetl transakcje w sekcjach faktury

Sekcje faktury umożliwiają organizowanie koszty, faktury profil rozliczeniowy. Aby uzyskać więcej informacji, zobacz [sekcji faktury omówienie](billing-mca-overview.md#understand-invoice-sections). Po wygenerowaniu faktury, opłaty za wszystkie sekcje profil rozliczeniowy ujęte na fakturze.

Na poniższej ilustracji przedstawiono opłaty za części faktury dział IT na fakturze próbki.

![Przykładowy obraz przedstawiający szczegóły faktury sekcji informacje](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Po zidentyfikowaniu opłaty sekcji faktury transakcji można wyświetlić w witrynie Azure portal, aby uzgodnić opłaty.

1. Przejdź do wszystkich transakcji strony w witrynie Azure portal, aby wyświetlić następującą liczbę transakcji: faktury. Aby uzyskać więcej informacji, zobacz [Wyświetl transakcje dla faktury w witrynie Azure portal](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtruj według nazwy sekcji faktur do wyświetlania transakcji dla sekcji faktury.

## <a name="understand-pending-charges-to-estimate-your-next-invoice"></a>Zrozumienie oczekujące opłat można oszacować, na następnej fakturze

W koncie rozliczeniowym umowy klienta firmy Microsoft do momentu opłaty są fakturowane, są one szacowania i traktowane jako oczekujące. Czy sfinalizowana, a uważane za naliczane, gdy występują na fakturze. Możesz wyświetlić oczekujące opłaty za profil rozliczeniowy oszacowanie opłat na następnej fakturze.

### <a name="view-charges-summary"></a>Wyświetl podsumowanie kosztów

1. Zaloguj się w witrynie [Azure Portal](https://www.azure.com).

2. Wyszukiwanie **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący Azure portal Wyszukaj Zarządzanie kosztami i rozliczenia](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy. W zależności od dostęp może być konieczne wybieranie konta rozliczeniowego. Wybierz z konta rozliczeniowego **rozliczeń profile** następnie wybierz profil rozliczeniowy.

4. Wybierz **Podsumowanie** kartę w górnej części ekranu.

5. Sekcja opłaty wyświetlać miesiąc na do daty i opłaty za ostatni miesiąc.

   ![Zrzut ekranu pokazujący Azure portal Wyszukaj Zarządzanie kosztami i rozliczenia](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Opłaty od początku miesiąca są oczekujące opłaty dla bieżącego miesiąca i są rozliczane po wygenerowaniu faktury na miesiąc. Jeśli faktury w ostatnim miesiącu nie nadal jest generowany, a następnie opłaty za ostatni miesiąc, są również oczekujące.

### <a name="view-pending-transactions"></a>Wyświetlanie oczekujących transakcji

Po zidentyfikowaniu oczekujące opłat, możesz wyświetlić poszczególnych transakcji, które przyczyniają się do opłat za na stronie wszystkie transakcje. W tym momencie do czasu użycia opłaty nie są wyświetlane na stronie wszystkich transakcji. Opłaty za użycie oczekujące można wyświetlić na stronie subskrypcje platformy Azure. Aby uzyskać więcej informacji, zobacz [Wyświetl oczekujące opłaty za użycie](#view-pending-usage-charges)

1. Zaloguj się w witrynie [Azure Portal](https://www.azure.com).

2. Wyszukiwanie **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący Azure portal Wyszukaj Zarządzanie kosztami i rozliczenia](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy. W zależności od dostęp może być konieczne wybieranie konta rozliczeniowego. Wybierz z konta rozliczeniowego **rozliczeń profile** następnie wybierz profil rozliczeniowy.

4. Wybierz **wszystkie transakcje** po lewej stronie ekranu.

5. Wyszukaj **oczekujące**. Użyj **Timespan** filtr, aby wyświetlić oczekujące opłaty dla bieżącego lub ostatniego miesiąca.

   ![Zrzut ekranu przedstawiający listę transakcje oczekujące](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Wyświetl opłaty za użycie oczekujące

1. Zaloguj się w witrynie [Azure Portal](https://www.azure.com).

2. Wyszukiwanie **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący Azure portal Wyszukaj Zarządzanie kosztami i rozliczenia](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy. W zależności od dostęp może być konieczne wybieranie konta rozliczeniowego. Wybierz z konta rozliczeniowego **rozliczeń profile** następnie wybierz profil rozliczeniowy.

4. Wybierz **wszystkie subskrypcje** po lewej stronie ekranu.

5. Na stronie subskrypcje platformy Azure Wyświetla bieżące i opłaty za ostatni miesiąc dla każdej subskrypcji w profil rozliczeniowy. Opłaty od początku miesiąca są oczekujące opłaty dla bieżącego miesiąca i są rozliczane po wygenerowaniu faktury na miesiąc. Jeśli faktury w ostatnim miesiącu nie nadal jest generowany, a następnie opłaty za ostatni miesiąc, są również oczekujące.

    ![Zrzut ekranu, który wyświetla listę subskrypcji platformy Azure dla profil rozliczeniowy](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

Można wyświetlić opłat szczegółowym zestawieniem użycia, za pomocą plików platformy Azure, użycia i opłat. Aby uzyskać więcej informacji zobacz następną sekcję.

## <a name="analyze-your-azure-usage-charges"></a>Analizuj Twoje opłaty za użycie platformy Azure

Użyj platformy Azure pliku csv użycia i opłat do analizowania opłat na podstawie użycia. Możesz pobrać plik dla faktury lub do czasu opłaty. Aby uzyskać więcej informacji, zobacz [pobrać faktury i dane dziennego użycia rozliczeniową za platformę Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Wyświetl szczegóły użycia przez sekcję faktury

Można filtrować plików użycia i opłat platformy Azure do uzgodnienia opłaty za użycie usługi sekcji faktury.

W poniższych krokach objaśniono za pośrednictwem uzgadnianie opłaty za zasoby obliczeniowe dla działu księgowości sekcji faktury:

![Przykładowy obraz przedstawiający szczegóły faktury sekcji informacje](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Invoice PDF | Użycie platformy Azure i opłaty CSV |
 | --- | --- |
 |Dział IT |invoiceSectionName |
 |Opłaty za użycie — Standard platformy Azure |productOrderName |
 |Magazyn |serviceFamily |

1. Filtr **invoiceSectionName** kolumny w pliku CSV do **działu księgowości**.
2. Filtr **productOrderName** kolumny w pliku CSV do **Plan usługi Microsoft Azure**.
3. Filtr **serviceFamily** kolumny w pliku CSV do **Microsoft.Compute**.

![Zrzut ekranu, który przedstawia użycie i opłaty za pliku filtrowane przez sekcję faktury](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)

<!--Todo Add screenshot of usage file -->

### <a name="view-detailed-usage-by-subscription"></a>Wyświetl szczegóły użycia według subskrypcji

Można filtrować Azure pliku csv użycia i opłat do uzgodnienia opłaty za użycie dla subskrypcji. Aby wyświetlić wszystkie subskrypcje profil rozliczeniowy, zobacz [Wyświetl oczekujące opłaty za użycie](#view-pending-usage-charges).

Po zidentyfikowaniu opłaty za subskrypcję, analizowanie opłat przy użyciu funkcji Azure pliku csv użycia i opłat.

Poniższy zrzut ekranu przedstawia listę subskrypcji w witrynie Azure portal.

![Zrzut ekranu, który wyświetla listę subskrypcji platformy Azure dla profil rozliczeniowy](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtr **subscriptionName** kolumny w usłudze Azure pliku CSV użycia i opłat do **WA_Subscription** Aby wyświetlić szczegółowe opłaty WA_Subscription.

![Zrzut ekranu, który przedstawia użycie i opłaty za pliku filtrowana według subskrypcji](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Zapłacić rachunku

Instrukcje dotyczące płacenia rachunku są wyświetlane na dole faktury. [Dowiedz się, jak płacić](billing-mca-understand-your-invoice.md#how-to-pay).

Jeśli została już zapłacono swój rachunek, możesz sprawdzić stan płatności na stronie faktur w witrynie Azure portal. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat faktury i szczegóły użycia, zobacz:

- [Jak uzyskać fakturę rozliczeniową za platformę Azure i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md)
- [Zrozumienie warunki na fakturze umowy klienta firmy Microsoft](billing-mca-understand-your-invoice.md)
- [Zrozumienie warunki na wykorzystanie umowy klienta Microsoft CSV](billing-mca-understand-your-usage.md)
