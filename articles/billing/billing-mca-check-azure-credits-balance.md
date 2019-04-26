---
title: Śledź salda środków platformy Azure do umowy klienta firmy Microsoft | Dokumentacja firmy Microsoft
description: Dowiedz się sprawdzić saldo środków platformy Azure dla umowy klienta firmy Microsoft.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: amberb
ms.openlocfilehash: 1e8c3e6863b9cd8f2f5ced18a57918c32c865e75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372262"
---
# <a name="track-azure-credit-balance-for-microsoft-customer-agreement"></a>Śledź salda środków platformy Azure do umowy klienta firmy Microsoft

Możesz sprawdzić saldo środków platformy Azure dla umowy klienta firmy Microsoft w witrynie Azure portal. Środki na korzystanie z umożliwia płacić za produkty, które są objęte kredytu.

Opłaty są naliczane, gdy używasz produktów, które nie są objęte środki na korzystanie z lub użycie przekracza saldo środków. Aby uzyskać więcej informacji, zobacz [produktów, które nie są objęte kredytów systemu Azure.](#products-that-arent-covered-by-azure-credits)

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-credit-balance-in-the-azure-portal"></a>Sprawdź Saldo środków w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal]( https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu pokazujący wyszukiwania w portalu, aby zarządzanie kosztami i rozliczenia](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Przejdź do profilu rozliczeniowego. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego. Wybierz z konta rozliczeniowego **rozliczeń profile** i następnie profil rozliczeniowy.

4. Wybierz **kredytów systemu Azure**.

5. Na stronie kredytów systemu Azure zawiera następujące informacje:

   ![Zrzut ekranu przedstawiający salda środków i transakcje dla profilu rozliczeń](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termin               | Definicja                           |
   |--------------------|--------------------------------------------------------|
   | Szacowane saldo  | Szacowana ilość środków masz po uwzględnieniu wszystkich rozliczane i oczekujące transakcje |
   | Bieżące saldo    | Kwota kredytów, począwszy od ostatniej faktury. Nie zawiera wszystkie oczekujące transakcje |
   | Transakcje       | Wszystkie transakcje rozliczeń, które wpływ Saldo środków platformy Azure |

   Po spadku saldo szacowane na 0, opłaty są naliczane za swoje użycie, w tym dla produktów, które są objęte środków.

6. Wybierz **środki na korzystanie z listy** do widoku listy kredytów na profil rozliczeniowy. Środki na korzystanie z listy zawiera następujące informacje:

   ![Zrzut ekranu przedstawiający środki na korzystanie z listy profil rozliczeń](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termin                 | Definicja                           |
   |----------------------|--------------------------------------------------------|
   | Element źródłowy               | Źródło nabycia środków |
   | Data rozpoczęcia           | Data, jeśli klient nabył środków |
   | Data ważności      | Data wygaśnięcia środków |
   | Saldo              | Saldo od ostatniej faktury |
   | Kwota      | Pierwotna ilość środków |
   | Stan               | Bieżący stan środków. Stan może być aktywny, używa, wygasł lub wygaśnie |

## <a name="how-credits-are-used-in-microsoft-customer-agreement"></a>Jak kredyty są używane w umowie klienta firmy Microsoft

W konta rozliczeniowego dla umowy klienta firmy Microsoft umożliwia rozliczeń profile Zarządzaj metodami płatności i faktur. Miesięcznej fakturze jest generowany dla każdego profilu rozliczeń i użyć metody płatności fakturę.

Kredytów systemu Azure są jedną z metod płatności. Otrzymasz środki przez firmę Microsoft takich jak promocyjne płatności i środki poziomu usługi. Te środki na korzystanie z są przypisane do profilu rozliczeń. Po wygenerowaniu faktury dla profil rozliczeniowy środki są automatycznie stosowane do łączna kwota rachunku do obliczenia wartości, które trzeba płacić. Płatność pozostała kwota przy użyciu innej metody płatności, takich jak sprawdzanie lub przelewem.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkty, które nie są objęte kredytów systemu Azure

 Następujące produkty nie są objęte kredytów systemu Azure. Opłaty są naliczane dotyczące korzystania z tych produktów, niezależnie od tego, Saldo środków:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 
- Zarejestrowany użytkownik
- Openlogic
- Dostęp zdalny praw usługa XenApp Essentials zarejestrowany użytkownik
- Ubuntu Advantage
- Visual Studio Enterprise (co miesiąc)
- Visual Studio Enterprise (roczna)
- Visual Studio Professional (miesięcznie)
- Visual Studio Professional (roczna)
- Produkty platformy Azure Marketplace
- Plany pomocy technicznej platformy Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

- [Opis konta rozliczeniowego dla umowy klienta firmy Microsoft](billing-mca-overview.md)
- [Zrozumienie warunki na fakturze umowy klienta firmy Microsoft](billing-mca-understand-your-invoice.md)