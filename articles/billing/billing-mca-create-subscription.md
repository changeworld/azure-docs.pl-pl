---
title: Należy utworzyć dodatkową subskrypcję platformy Azure dla swojego konta rozliczeniowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać nową subskrypcję platformy Azure w witrynie Azure portal.
services: billing
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
ms.author: banders
ms.openlocfilehash: 105b8481486c088a05e3acb95081d3ee55b55f52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372024"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Należy utworzyć dodatkową subskrypcję platformy Azure dla umowy klienta firmy Microsoft

Tworzenie dodatkowych subskrypcji dla swojego konta rozliczeniowego, aby skonfigurować oddzielne środowiska do tworzenia i testowania, zabezpieczeń lub do izolowania danych w celu zachowania zgodności.

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement). Jeśli chcesz tworzyć subskrypcji na inne konta rozliczeniowego, zobacz [należy utworzyć dodatkową subskrypcję w witrynie Azure portal](billing-create-subscription.md).

Aby utworzyć subskrypcję, musisz być **właściciel sekcji faktury**, **Współautor sekcji faktury**, lub **twórca subskrypcji platformy Azure**. Aby uzyskać więcej informacji, zobacz [role rozliczeń subskrypcji i zadania](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Aby podać inne uprawnienia do tworzenia subskrypcji platformy Azure dla swojego konta rozliczeniowego, zobacz [daje uprawnienia do tworzenia subskrypcji platformy Azure](#give-others-permission-to-create-azure-subscriptions).

## <a name="create-a-subscription-in-the-azure-portal"></a>Tworzenie subskrypcji w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj **subskrypcje**.

   ![Zrzut ekranu pokazujący wyszukiwania w portalu dla subskrypcji](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Wybierz **Dodaj**

4. Jeśli masz dostęp do wielu kont rozliczeń, wybierz konto rozliczeniowe dla umowy klienta firmy Microsoft.

   ![Zrzut ekranu przedstawiający tworzenie strony subskrypcji](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Wybierz profil rozliczeniowy. Opłaty za subskrypcję, zostanie naliczona na fakturze profil rozliczeniowy i będzie wypłacane przy użyciu jego metod płatności. Jeśli masz dostęp do tylko jeden profil rozliczeniowy, wybór będą wyszarzone.

6. Wybierz sekcję faktury. Opłaty za subskrypcję, zostanie naliczona w tej sekcji profil rozliczeniowy faktury. Jeśli masz dostęp do tylko jednej sekcji faktury zaznaczenia będą wyszarzone.

7. Wybierz plan dla subskrypcji. Wybierz **Microsoft Azure planowanie DevTest**, jeśli planujesz używać tej subskrypcji do tworzenia lub testowania obciążenia innego za pomocą **Microsoft Azure Plan**. Jeśli masz dostęp do tylko jeden plan, wybór będą wyszarzone.

8. Wprowadź nazwę subskrypcji. Nazwa pomaga łatwo zidentyfikować subskrypcji w witrynie Azure portal.

9. Wybierz pozycję **Utwórz**.

## <a name="give-others-permission-to-create-azure-subscriptions"></a>Zezwól na uprawnienia do tworzenia subskrypcji platformy Azure

Dodawać użytkowników jako subskrypcji platformy Azure dla twórców w sekcji faktury, aby dać im uprawnień do utworzenia subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu pokazujący wyszukiwania w portalu dla subskrypcji](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Przejdź do sekcji faktury. W zależności od dostęp może być konieczne wybierz konto rozliczeniowe lub profil rozliczeniowy. Z konta fakturowania i profilu, wybierz **faktury sekcje** i następnie sekcji faktury.

4. Wybierz **zarządzania dostępu (IAM)** z góry z lewej strony.

5. W górnej części strony wybierz pozycję **Dodaj**.

6. Wybierz **twórca subskrypcji platformy Azure** dla roli.

   ![Zrzut ekranu pokazujący, zapewniając roli Twórca subskrypcji platformy Azure dla użytkownika](./media/billing-mca-create-subscription/billing-mca-add-azure-subscription-creator.png)

7. Wprowadź adres e-mail użytkownika, któremu chcesz udzielić dostępu.

8. Wybierz pozycję **Zapisz**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

- [Zezwól na uprawnienia do tworzenia zasobów platformy Azure przy użyciu wbudowanych ról](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Utwórz maszynę wirtualną systemu windows](../virtual-machines/windows/quick-create-portal.md)
- [Utwórz maszynę wirtualną systemu linux](../virtual-machines/linux/quick-create-portal.md)
- [Tworzenie grupy zarządzania do organizacji zasobów i zarządzania](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
