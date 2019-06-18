---
title: Zrozumienie fakturę umowy klienta firmy Microsoft | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odczytywać i informacje o rachunku MCA
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: aee51793c66ae57f740300797b8fdc1799e685cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65604040"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>Zrozumienie warunki na fakturze umowy klienta firmy Microsoft

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement).

Twoja faktura zawiera podsumowanie opłat i instrukcje dotyczące płatności. Jest dostępna do pobrania w Portable Document Format (PDF) z [witryny Azure portal](https://portal.azure.com/) lub mogą być wysyłane za pośrednictwem poczty e-mail. Aby uzyskać więcej informacji, zobacz [widoku i pobierania usługi Microsoft Azure faktury](billing-download-azure-invoice.md).

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>Warunki faktury wraz z opisami

W poniższych sekcjach wymieniono ważne pojęcia, które widzisz na faktury i opisy dla każdego okresu.

### <a name="invoice-summary"></a>Podsumowanie faktury

**Podsumowanie faktury** znajduje się na szczycie pierwszej strony i zawiera informacje o profilu rozliczeń i sposób płatności.

![Sekcja Podsumowanie faktury](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termin | Opis |
| --- | --- |
| Sprzedano dla |Adres firmy znalezione w rozliczeniach właściwości konta|
| Rachunek dla |Adres profil rozliczeniowy otrzymywanie faktury w rozliczeń właściwości profilu rozliczeniowy|
| Profil rozliczeniowy |Nazwa profilu rozliczeń, otrzymywanie faktury |
| POCZTOWEJ numer |Numer zamówienia zakupu opcjonalne przypisanych przez użytkownika do śledzenia |
| Numer faktury |Unikatowy, generowanych przez Microsoft płatnika używanych na potrzeby śledzenia |
| Data faktury |Data faktury, jest generowany, zwykle 5 do 12 dni, po zakończeniu cyklu rozliczeniowym. Możesz sprawdzić swoje Data faktury w rozliczeń właściwości profilu.|
| Warunki płatności |Jak zapłacić za swój rachunek firmy Microsoft. *30 dni roboczych* oznacza, że płaci się w ciągu 30 dni od daty wskazanej na fakturze. |

### <a name="billing-summary"></a>Podsumowanie rozliczeń

**Rozliczeń Podsumowanie** pokazuje opłaty względem profil rozliczeniowy od czasu poprzedniego okresu rozliczeniowego, wszystkie środki, które zostały zastosowane, podatku i sumy ukończenia.

![Sekcja podsumowania rozliczeń](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Termin | Opis |
| --- | --- |
| Opłaty za|Łączna liczba opłat firmy Microsoft dla tego profilu rozliczeń od czasu ostatniego okresu rozliczeniowego |
| Napisy końcowe |Kredyty otrzymany od zwraca |
| Stosowane środki platformy Azure | Kredytów systemu Azure, które są automatycznie stosowane do platformy Azure opłaty za każdego okresu rozliczeniowego |
| Suma częściowa |Kwota przed opodatkowaniem |
| Podatku |Typ i kwota podatku, że opłaty są naliczane, w zależności od kraju/regionie rozliczeniowym profilu. Jeśli nie masz płacenia podatków, nie będziesz widzieć podatku na fakturze. |
| Szacowany łączne oszczędności |Szacowana łączna kwota zapisane ze skutecznego rabatów. Jeśli to konieczne, obowiązywać stawki z rabatem są wymienione poniżej pozycje zakupu w szczegółach przez sekcję faktury. |

### <a name="invoice-sections"></a>Sekcje faktury

Dla każdej sekcji faktury w ramach rozliczeń profilu zostaną wyświetlone opłaty, ilość środków platformy Azure są stosowane, podatku i łączna kwota.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Szczegóły przez sekcję faktury

Szczegóły dotyczące pokazują kosztów dla każdej sekcji faktury, podzielone według kolejności produktu. W ramach każdego zamówienia produktów kosztów jest podzielone według typu usługi. Opłat dzienne dla swoich produktów i usług można znaleźć w witrynie Azure portal i użycie platformy Azure i opłaty za CSV. Aby dowiedzieć się więcej, zobacz [informacje o opłatach na fakturze umowy klienta Microsoft](billing-mca-understand-your-bill.md).

Łączna kwota należności dla każdej rodziny service jest obliczany przez odjęcie *kredytów systemu Azure* z *środki na korzystanie z/opłaty* i dodawanie *podatku*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![Szczegóły przez sekcję faktury](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termin |Opis |
| --- | --- |
| Cena jednostkowa | Ceny obowiązujące jednostki usługi (w zmienionych cennikach walucie), który jest używany do szybkości użycia. To jest unikatowy dla produktu, rodzina usługi, licznik i oferty. |
| Wad | Ilość zakupionych lub używane w trakcie okresu rozliczeniowego |
| Opłaty za/środków | Kwota netto opłat, po zastosowaniu środki na korzystanie z/zwroty |
| Kredyt na korzystanie z systemu Azure | Kwota kredytów systemu Azure, które dotyczą opłat za/środków|
| Stawka podatku | Podatkowej w zależności od kraju/regionu |
| Kwota podatku | Kwota podatku stosowane do zakupu w oparciu podatku |
| Łącznie | Łączna kwota należności na zakup |

### <a name="how-to-pay"></a>Sposób zapłaty

Na dole faktury istnieją instrukcje płatności na rachunku. Można płacić, używając wyboru, o komunikacji sieciowej, lub w trybie online. Płatności w trybie online, można użyć karty kredytowej lub kredytów systemu Azure, jeśli ma to zastosowanie.

### <a name="publisher-information"></a>Informacje o wydawcy

Jeśli masz usługi innych firm na rachunku, nazwę i adres każdego wydawcy znajduje się na dole faktury.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

- [Informacje o opłatach na fakturze profil rozliczeniowy](billing-mca-understand-your-bill.md)
- [Jak uzyskać fakturę rozliczeniową za platformę Azure i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md)
- [Wyświetl cennik platformy Azure w Twojej organizacji](billing-ea-pricing.md)
- [Wyświetlanie dokumentów podatku dla swojego profilu rozliczeń](billing-mca-download-tax-document.md)
