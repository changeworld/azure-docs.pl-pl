---
title: Przeglądanie i pobieranie cen platformy Azure w organizacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić i pobrać cen lub Oszacuj koszty przy użyciu ceny Twojej organizacji.
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d4c64433089f14d845cea8be9adf0ef13675cfd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127637"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Przeglądanie i pobieranie cen platformy Azure w Twojej organizacji

Klienci platformy Azure przy użyciu Enterprise Agreement (EA) lub [umowy klienta Microsoft](#check-your-access-to-a-microsoft-customer-agreement) może wyświetlić i pobrać cen w witrynie Azure portal. Jeśli masz umowy klienta firmy Microsoft, zobacz widoku i pobierania ceny dla umowy klienta firmy Microsoft.

## <a name="view-and-download-ea-pricing"></a>Przeglądanie i pobieranie atrybutów Rozszerzonych ceny

W zależności od zasad ustawione dla Twojej organizacji przez administratora przedsiębiorstwa tylko niektóre role administracyjne zapewniają dostęp do informacji o cenach EA w Twojej organizacji. Aby uzyskać więcej informacji, zobacz [Enterprise Agreement platformy Azure zrozumienie ról administracyjnych na platformie Azure](billing-understand-ea-roles.md).

1. Jako administrator przedsiębiorstwa, zaloguj się do [witryny Azure portal](https://portal.azure.com/).
1. Wyszukiwanie **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. W obszarze konta rozliczeniowego, wybierz **użycie i opłaty za**.

   ![Zrzut ekranu pokazujący użycia i opłat w ramach rozliczeń](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Wybierz ![zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-ea-pricing/download-icon.png) **Pobierz** na miesiąc.
1. W obszarze **arkusz cen**, wybierz opcję **pobieranie pliku csv**.

   ![Csv przycisk Pobierz zrzut ekranu pokazujący arkusza cen](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>Przeglądanie i pobieranie ceny umowy klienta firmy Microsoft

Musi być rozliczeń profilu właścicielem, współautorem, Czytelnik lub Menedżer faktur Aby wyświetlić i pobrać ceny. Aby dowiedzieć się więcej o rolach rozliczeń dla umowy klienta firmy Microsoft, zobacz [rozliczeń profilu role i zadania](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Pobierz arkusze cen dla bieżącego okresu rozliczeniowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.
1. Wybierz profil rozliczeniowy. W zależności od dostęp może być konieczne najpierw wybrać konta rozliczeniowego.
1. W **Przegląd** bloku Znajdź łącza pobierania poniżej opłaty od początku miesiąca.
1. Wybierz **arkusz cen platformy Azure**.
![Zrzut ekranu przedstawiający pobieranie z przeglądu](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Pobierz arkusze cen opłaty rozliczane

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.
1. Wybierz profil rozliczeniowy. W zależności od dostęp może być konieczne najpierw wybrać konta rozliczeniowego.
1. Wybierz pozycję **Faktury**.
1. W siatce faktury Znajdź wiersz faktury odpowiadający arkusz cen, który chcesz pobrać.
1. Kliknij wielokropek (`...`) na końcu wiersza.
![Zrzut ekranu przedstawiający wybrany przycisk wielokropka](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Aby sprawdzić ceny dla usługi w wybranej faktury, należy zaznaczyć **arkusz cen faktury**.
1. Aby sprawdzić ceny dla wszystkich usług platformy Azure w danym okresie rozliczeniowym, należy zaznaczyć **arkusz cen platformy Azure**.

![Zrzut ekranu przedstawiający menu kontekstowe z arkuszy cen](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Oszacuj koszty przy użyciu kalkulatora cen platformy Azure

Można także użyć ceny Twojej organizacji do szacowania kosztów z Kalkulatora cen platformy Azure.

1. Przejdź do [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator).
1. W prawym górnym rogu, wybierz **Zaloguj**.
1. W obszarze **programy i oferty** > **Program licencjonowania**, wybierz opcję **Enterprise Agreement (EA)** .
1. W obszarze **programy i oferty** > **wybrane umowy**, wybierz opcję **niczego nie wybrano**.

    ![Csv przycisk Pobierz zrzut ekranu pokazujący arkusza cen](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Wybierz organizację.
1. Wybierz przycisk **Zastosuj**.
1. Wyszukaj i Dodaj produkty do szacowania.
1. Szacowane ceny dotyczą cennika dla organizacji, które wybrano.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Sprawdź swój dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Kolejne kroki

Jeśli jesteś klientem z umową EA, zobacz:

- [Zarządzanie dostępem do atrybutów Rozszerzonych informacji rozliczeniowych dla platformy Azure](billing-manage-access.md)
- [Wyświetl i Pobierz fakturę usługi Microsoft Azure](billing-download-azure-invoice.md)
- [Przeglądanie i pobieranie usługi Microsoft Azure użycia i opłat](billing-download-azure-daily-usage.md)
- [Opis zawartości rachunku dla klientów z umowami Enterprise Agreement](billing-understand-your-bill-ea.md)

Jeśli masz umowy klienta firmy Microsoft, zobacz:

- [Opis warunków w arkuszu cen dla umowy klienta firmy Microsoft](billing-mca-understand-pricesheet.md)
- [Wyświetl i Pobierz fakturę usługi Microsoft Azure](billing-download-azure-invoice.md)
- [Przeglądanie i pobieranie usługi Microsoft Azure użycia i opłat](billing-download-azure-daily-usage.md)
- [Przeglądanie i pobieranie dokumentów podatkowych dla swojego profilu rozliczeń](billing-mca-download-tax-document.md)
- [Informacje o opłatach na fakturze profil rozliczeniowy](billing-mca-understand-your-bill.md)
