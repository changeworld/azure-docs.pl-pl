---
title: Tworzenie sekcji na fakturze do organizowania koszty - Azure
description: Dowiedz się, do organizowania koszty przy użyciu faktury sekcje.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: eadaf34dc5bdd93af532362e8f8542de3f17f414
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490768"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Tworzenie sekcji na fakturze w taki sposób, aby zorganizować koszty

Tworzenie sekcji na fakturze do organizowania koszty działu, środowisko programistyczne, lub odpowiednio do potrzeb Twojej organizacji. Następnie Zezwól na uprawnienia do tworzenia subskrypcji platformy Azure, które są rozliczane w sekcji. Wszelkie opłaty za użycie i zakupów dla subskrypcji, następnie są rozliczane w sekcji. Możesz wyświetlić łączne opłaty dla sekcji na fakturze w witrynie Azure portal lub zapoznaj się z nimi w analizy kosztów platformy Azure. Aby uzyskać więcej informacji, zobacz [wyświetlać transakcje w sekcjach faktury](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Utwórz sekcję faktury w witrynie Azure portal

Aby utworzyć sekcji faktury, muszą być **rozliczeń właściciela profilu** lub **rozliczeń Współautor profilu**. Aby uzyskać więcej informacji, zobacz [zarządzania sekcjami faktury dla rozliczeń profilu](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Wybierz **faktury sekcje** z okienka po lewej stronie. W zależności od dostęp, może być konieczne wybierz profil rozliczeń lub konta rozliczeniowego, a następnie wybierz pozycję **faktury sekcje**.

   ![Zrzut ekranu przedstawiający listę sekcji faktury](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. W górnej części strony wybierz pozycję **Dodaj**.

5. Wprowadź nazwę dla sekcji faktury, a następnie wybierz profil rozliczeniowy. Zobaczysz sekcji na fakturze profil rozliczeniowy odzwierciedlający użytkowania każdej subskrypcji i zakupów, przypisanej do sekcji. 

   ![Zrzut ekranu przedstawiający stronę tworzenia sekcji faktury](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Wybierz pozycję **Utwórz**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

- [Należy utworzyć dodatkową subskrypcję platformy Azure dla umowy klienta firmy Microsoft](billing-mca-create-subscription.md)
- [Zezwól na uprawnienia do tworzenia subskrypcji platformy Azure](billing-mca-create-subscription.md#give-others-permission)
- [Uzyskaj własność rozliczeń subskrypcji platformy Azure od użytkowników w innych kont rozliczeń](billing-mca-request-billing-ownership.md)
