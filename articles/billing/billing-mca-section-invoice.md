---
title: Tworzenie sekcji faktur w celu organizowania kosztów — Azure
description: Dowiedz się, jak organizować koszty za pomocą sekcji faktur.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ff8b2da353d623cd9f05c8d0b0317587d7093ce3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75389285"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Tworzenie sekcji na fakturze w celu organizacji kosztów

Utwórz sekcje na fakturze, aby organizować koszty według działu, środowiska deweloperskiego lub w oparciu o potrzeby organizacji. Następnie nadaj innym użytkownikom uprawnienia do tworzenia subskrypcji platformy Azure, które są rozliczane w ramach sekcji. Wszelkie opłaty za użycie i zakupy dla subskrypcji są następnie rozliczane w ramach sekcji. Możesz wyświetlić łączne opłaty dla sekcji na fakturze w witrynie Azure Portal lub przejrzeć je w analizie kosztów platformy Azure. Aby uzyskać więcej informacji, zobacz [Wyświetlanie transakcji według sekcji faktury](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Tworzenie sekcji faktury w witrynie Azure Portal

Aby utworzyć sekcję faktury, musisz być **właścicielem profilu rozliczeniowego** lub **współautorem profilu rozliczeniowego**. Aby uzyskać więcej informacji, zobacz sekcję [Zarządzanie sekcjami faktury dla profilu rozliczeniowego](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Wybierz pozycję **Sekcje faktury** w okienku po lewej stronie. W zależności od praw dostępu może być konieczne wybranie profilu rozliczeniowego lub konta rozliczeniowego, a następnie wybranie pozycji **Sekcje faktury**.

   ![Zrzut ekranu przedstawiający listę sekcji faktury](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. W górnej części strony wybierz pozycję **Dodaj**.

5. Wprowadź nazwę sekcji faktury i wybierz profil rozliczeniowy. Na fakturze tego profilu rozliczeniowego zostanie wyświetlona sekcja odzwierciedlająca użycie poszczególnych subskrypcji i zakupów przypisanych do sekcji.

   ![Zrzut ekranu przedstawiający stronę tworzenia sekcji faktury](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Wybierz pozycję **Utwórz**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie dodatkowej subskrypcji platformy Azure dla Umowy klienta firmy Microsoft](billing-mca-create-subscription.md)
- [Zarządzanie rolami rozliczeniowymi w witrynie Azure Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Uzyskiwanie własności rozliczeń subskrypcji platformy Azure od użytkowników z innych kont rozliczeniowych](billing-mca-request-billing-ownership.md)
