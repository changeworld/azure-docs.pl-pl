---
title: Śledzenie salda środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft
description: Dowiedz się, jak sprawdzić saldo środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ea3fc21891f1e4d4e744449032a4b2cfcdfbb2f0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177533"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Śledzenie salda środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft

Saldo środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft można sprawdzić w witrynie Azure Portal. Należności za używanie niektórych produktów są regulowane za pomocą środków.

Opłaty są naliczane w przypadku korzystania z produktów, do których nie stosuje się środków, lub jeśli użycie tych produktów przekroczy saldo środków. Aby uzyskać więcej informacji, zobacz [Produkty nieobjęte środkami na korzystanie z platformy Azure](#products-that-arent-covered-by-azure-credits).

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Sprawdzanie salda środków

1. Zaloguj się w witrynie [Azure Portal]( https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Wybierz pozycję **Środki na korzystanie z platformy Azure** wyświetlaną po lewej stronie. W zależności od praw dostępu może być konieczne wybranie profilu rozliczeniowego lub konta rozliczeniowego, a następnie wybranie pozycji **Środki na korzystanie z platformy Azure**.

4. Strona środków na korzystanie z platformy Azure zawiera następujące informacje:

   ![Zrzut ekranu przedstawiający saldo środków i transakcje w profilu rozliczeniowym](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termin               | Definicja                           |
   |--------------------|--------------------------------------------------------|
   | Szacowane saldo  | Szacowana kwota środków po uwzględnieniu wszystkich rozliczonych i oczekujących transakcji |
   | Bieżące saldo    | Kwota środków od ostatniej faktury. Nie obejmuje ona oczekujących transakcji |
   | Transakcje       | Wszystkie transakcje rozliczeniowe, które miały wpływ na saldo środków na korzystanie z platformy Azure |

   Gdy szacowane saldo spadnie do 0, rozpocznie się naliczanie opłat za używanie wszystkich produktów, również tych, których użycie jest rozliczane za pomocą środków.

6. Wybierz pozycję **Lista środków**, aby wyświetlić listę środków dla profilu rozliczeniowego. Lista środków zawiera następujące informacje:

   ![Zrzut ekranu przedstawiający listę środków dla profilu rozliczeniowego](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termin | Definicja |
   |---|---|
   | Szacowane saldo | Kwota środków na korzystanie z platformy Azure po odjęciu nierozliczonych opłat objętych środkami od bieżącego salda|
   | Bieżące saldo | Kwota środków na korzystanie z platformy Azure przed uwzględnieniem nierozliczonych opłat objętych środkami. Oblicza się ją, dodając otrzymane środki na korzystanie z platformy Azure do salda środków w dniu wystawiania ostatniej faktury|
   | Element źródłowy | Źródło pozyskiwania środków |
   | Data rozpoczęcia | Data nabycia środków |
   | Data wygaśnięcia | Data wygaśnięcia środków |
   | Saldo | Saldo od ostatniej faktury |
   | Pierwotna kwota | Pierwotna kwota środków |
   | Stan | Bieżący stan środków. Stan może mieć wartość: aktywne, użyte, wygasłe lub wygasające |

## <a name="how-credits-are-used"></a>Sposób używania środków

Konto rozliczeniowe umowy klienta firmy Microsoft zawiera profile rozliczeniowe, które umożliwiają zarządzanie fakturami i formami płatności. W każdym profilu rozliczeniowym co miesiąc jest generowana faktura, którą opłaca się przy użyciu wybranych form płatności.

Jedną z tych form są środki na korzystanie z platformy Azure. Środki można otrzymać od firmy Microsoft — na przykład środki promocyjne lub środki na poziomie usługi. Są one przypisywane do profilu rozliczeniowego. Podczas generowania faktury w profilu rozliczeniowym środki są automatycznie stosowane do łącznej kwoty podlegającej rozliczeniu w celu obliczenia należnej kwoty. Wynikową kwotę uiszcza się, korzystając z innej formy płatności, takiej jak czek lub przelew bankowy.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkty nieobjęte środkami na korzystanie z platformy Azure

 Następujące produkty nie są objęte środkami na korzystanie z platformy Azure. Za ich używanie są naliczane opłaty — niezależnie od salda środków:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (miesięczny)
- Visual Studio Enterprise (roczny)
- Visual Studio Professional (miesięczny)
- Visual Studio Professional (roczny)
- Produkty z witryny Azure Marketplace
- Plany pomocy technicznej platformy Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- [Omówienie konta rozliczeniowego umowy klienta firmy Microsoft](billing-mca-overview.md)
- [Omówienie terminów na fakturze za umowę klienta firmy Microsoft](billing-mca-understand-your-invoice.md)
