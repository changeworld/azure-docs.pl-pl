---
title: Zapoznaj się z fakturą umowy dla klientów firmy Microsoft na platformie Azure
description: Dowiedz się, jak czytać i zrozumieć rozliczenia z umowy klienta firmy Microsoft na platformie Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383536"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Warunki na fakturze umowy klienta firmy Microsoft

Ten artykuł dotyczy konta rozliczeń platformy Azure dla umowy klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Faktura zawiera podsumowanie opłat i instrukcje dotyczące płatności. Jest on dostępny do pobrania w formacie dokumentu przenośnego (PDF) z [Azure Portal](https://portal.azure.com/) lub można go wysłać za pośrednictwem poczty e-mail. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i pobieranie faktury Microsoft Azure](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Okres rozliczeniowy

Są one comiesięczne miesięcznie. Możesz sprawdzić, który dzień miesiąca otrzymuje faktury, sprawdzając *datę faktury* w obszarze właściwości profilu rozliczeń w [Azure Portal](https://portal.azure.com/). Opłaty naliczane między końcem okresu rozliczeniowego a datą faktury są uwzględniane w fakturze w następnym miesiącu, ponieważ znajdują się w następnym okresie rozliczeniowym. Daty rozpoczęcia i zakończenia okresu rozliczeniowego dla każdej faktury są wymienione w pliku PDF faktury powyżej **podsumowania**rozliczeń.

## <a name="invoice-terms-and-descriptions"></a>Warunki i opisy faktury

W poniższych sekcjach wymieniono ważne terminy, które są widoczne na fakturze, oraz opisy poszczególnych okresów.

### <a name="invoice-summary"></a>Podsumowanie faktury

**Podsumowanie faktury** znajduje się w górnej części pierwszej strony i zawiera informacje o Twoim profilu rozliczeń oraz o sposobie płacenia.

![Sekcja podsumowania faktury](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termin | Opis |
| --- | --- |
| Sprzedano dla |Adres podmiotu prawnego, który znaleziono we właściwościach konta rozliczeniowego|
| Rachunek dla |Adres rozliczeniowy profilu rozliczania otrzymującego fakturę, znaleziony we właściwościach profilu rozliczeń|
| Profil rozliczeniowy |Nazwa profilu rozliczeń otrzymującego fakturę |
| POCZTOW numer |Opcjonalny numer zamówienia zakupu przypisany przez Ciebie do śledzenia |
| Numer faktury |Unikatowy, wygenerowany przez firmę Microsoft numer faktury używany do śledzenia |
| Data faktury |Data wygenerowania faktury, zwykle od pięciu do 12 dni po zakończeniu cyklu rozliczeniowego. Datę faktury można sprawdzić we właściwościach profilu rozliczeń.|
| Warunki płatności |Jak płacisz za firmę Microsoft. *30 dni netto* oznacza, że płacisz w ciągu 30 dni od daty faktury. |

### <a name="billing-summary"></a>Podsumowanie rozliczeń

**Podsumowanie** rozliczeń przedstawia opłaty względem profilu rozliczeń od poprzedniego okresu rozliczeniowego, wszystkie środki, które zostały zastosowane, podatek i łączną kwotę należną.

![Sekcja podsumowania rozliczeń](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Termin | Opis |
| --- | --- |
| Opłaty|Łączna liczba opłat firmy Microsoft dla tego profilu rozliczeń od ostatniego okresu rozliczeniowego |
| Napisy końcowe |Kredyty otrzymane od zwrotów |
| Zastosowane środki na korzystanie z platformy Azure | Kredyty na korzystanie z platformy Azure, które są automatycznie stosowane do usługi Azure, są naliczane za każdy okres |
| Suma częściowa |Należna kwota przed opodatkowaniem |
| Podatek |Typ i kwota podatku płatnego w zależności od kraju/regionu Twojego profilu rozliczeń. Jeśli nie musisz uiszczać podatku, nie zobaczysz podatku na fakturze. |
| Szacowane całkowite oszczędności |Szacowana łączna kwota, która została zapisana z efektywnych rabatów. Jeśli ma to zastosowanie, obowiązujące stawki rabatu są wyświetlane poniżej w sekcji Szczegóły według faktury. |

### <a name="invoice-sections"></a>Sekcje faktur

Dla każdej sekcji faktury w ramach Twojego profilu rozliczania zobaczysz opłaty, zastosowane środki na korzystanie z platformy Azure, podatek oraz łączną kwotę należną.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Sekcja szczegółów według faktury

Szczegóły przedstawiają koszt każdej sekcji faktury w podziale według kolejności produktu. W ramach każdego zamówienia produktu koszt jest podzielony na typ usługi. Możesz znaleźć opłaty dzienne za swoje produkty i usługi w Azure Portal oraz w woluminie CSV i opłaty za korzystanie z platformy Azure. Aby dowiedzieć się więcej, zobacz temat [opłaty za fakturę dla umowy klienta firmy Microsoft](billing-mca-understand-your-bill.md).

Łączna kwota należna każdej rodziny usług jest obliczana przez odjęcie kredytów na korzystanie z *platformy Azure* od *kredytów/opłat* i dodanie *podatku*:


![Sekcja szczegółów według faktury](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termin |Opis |
| --- | --- |
| Cena jednostkowa | Obowiązująca cena jednostkowa usługi (w walucie cenowej), która jest używana do oceniania użycia. Jest to unikatowe dla produktu, rodziny usług, miernika i oferty. |
| Ilość | Ilość zakupione lub zużyte w okresie rozliczeniowym |
| Opłaty/kredyty | Opłata netto za środki za kredyty/zwroty są stosowane |
| Kredyt na korzystanie z systemu Azure | Kwota środków na korzystanie z platformy Azure zastosowana do opłat/kredytów|
| Stawka podatku | Stawki podatkowe w zależności od kraju/regionu |
| Kwota podatku | Kwota podatku zastosowana do zakupu w oparciu o stawkę podatku |
| Łącznie | Łączna kwota należna za zakup |

### <a name="how-to-pay"></a>Jak płacisz

W dolnej części faktury znajdują się instrukcje dotyczące płacenia rachunku. Możesz zapłaciłać według czeku, przewodu lub online. Jeśli płacisz w trybie online, możesz skorzystać z karty kredytowej lub kredytów na korzystanie z platformy Azure, jeśli ma to zastosowanie.

### <a name="publisher-information"></a>Informacje o wydawcy

Jeśli w rachunku istnieją usługi innych firm, nazwa i adres każdego wydawcy są wyświetlane u dołu faktury.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z opłatami na fakturze dotyczącym Twojego profilu rozliczeń](billing-mca-understand-your-bill.md)
- [Jak uzyskać fakturę rozliczeniową za platformę Azure i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md)
- [Wyświetlanie cen platformy Azure w organizacji](billing-ea-pricing.md)
- [Wyświetlanie dokumentów podatkowych dla profilu rozliczeń](billing-mca-download-tax-document.md)
