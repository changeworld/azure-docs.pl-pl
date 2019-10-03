---
title: Tworzenie dodatkowej subskrypcji platformy Azure na koncie rozliczeniowym
description: Dowiedz się, jak dodać nową subskrypcję platformy Azure w witrynie Azure Portal.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e1fafafe5f67d1775ca80a1f7c2aff4dec9e0bc4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709590"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Tworzenie dodatkowej subskrypcji platformy Azure dla umowy klienta firmy Microsoft

Utwórz dodatkowe subskrypcje na koncie rozliczeniowym, aby skonfigurować oddzielne środowiska na potrzeby programowania i testowania, zabezpieczeń lub odizolowania danych ze względów zachowania zgodności.

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access). Jeśli chcesz utworzyć subskrypcje dla innych typów kont rozliczeniowych, zobacz [Tworzenie dodatkowej subskrypcji w witrynie Azure Portal](billing-create-subscription.md).

Aby utworzyć subskrypcję, musisz być **właścicielem sekcji faktury**, **współautor sekcji faktury** lub **twórcą subskrypcji platformy Azure**. Aby uzyskać więcej informacji, zobacz [Role rozliczeniowe i zadania w subskrypcji](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Aby nadać innym osobom uprawnienia do tworzenia subskrypcji platformy Azure dla konta rozliczeniowego, zobacz [Nadawanie innym osobom uprawnienia do tworzenia subskrypcji platformy Azure](#give-others-permission).

## <a name="create-a-subscription"></a>Tworzenie subskrypcji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Subskrypcje**.

   ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Wybierz pozycję **Dodaj**.

4. Jeśli masz dostęp do wielu kont rozliczeniowych, wybierz konto rozliczeniowe dla Twojej umowy klienta firmy Microsoft.

   ![Zrzut ekranu przedstawiający stronę tworzenia subskrypcji](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Wybierz profil rozliczeniowy. Opłaty za subskrypcję będą naliczane w wybranym profilu rozliczeniowym. Jeśli masz dostęp tylko do jednego profilu rozliczeniowego, opcje wyboru będą wyszarzone.

6. Wybierz sekcję faktury. Opłaty za subskrypcję będą naliczane w tej sekcji faktury w profilu rozliczeniowym. Jeśli masz dostęp tylko do jednej sekcji faktury, opcje wyboru będą wyszarzone.

7. Wybierz plan dla subskrypcji. Wybierz **plan platformy Microsoft Azure na potrzeby tworzenia i testowania**, jeśli zamierzasz używać tej subskrypcji do obciążeń programistycznych lub testowych. W przeciwnym razie wybierz **plan platformy Microsoft Azure**. Jeśli masz dostęp tylko do jednego planu, opcje wyboru będą wyszarzone.

8. Wprowadź nazwę subskrypcji. Nazwa ułatwia znajdowanie subskrypcji w witrynie Azure Portal.

9. Wybierz pozycję **Utwórz**.

## <a name="give-others-permission"></a>Nadawanie uprawnienia innym osobom

Aby nadać użytkownikom uprawnienie do tworzenia subskrypcji platformy Azure, dodaj ich jako twórców subskrypcji platformy Azure w sekcji faktury.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Przejdź do sekcji faktury. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego lub profilu rozliczeniowego. Z poziomu profilu lub konta rozliczeniowego wybierz pozycję **Sekcje faktury**, a następnie wybierz sekcję faktury z listy. Każda subskrypcja utworzona przez użytkowników będzie rozliczana w tej sekcji faktury.
   
   ![Zrzut ekranu przedstawiający wybieranie sekcji faktury](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Wybierz pozycję **Zarządzanie dostępem (Zarządzanie dostępem i tożsamościami)** w lewym górnym rogu.

5. W górnej części strony wybierz pozycję **Dodaj**.

6. Jako rolę wybierz **Twórca subskrypcji platformy Azure**.

7. Wprowadź adres e-mail użytkownika, któremu chcesz przyznać dostęp.

8. Wybierz pozycję **Zapisz**.

## <a name="check-access"></a>Sprawdzanie dostępu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Przyznawanie innym osobom uprawnienia do tworzenia zasobów platformy Azure przy użyciu wbudowanych ról](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Tworzenie maszyny wirtualnej z systemem Windows](../virtual-machines/windows/quick-create-portal.md)
- [Tworzenie maszyny wirtualnej z systemem Linux](../virtual-machines/linux/quick-create-portal.md)
- [Tworzenie grup zarządzania na potrzeby organizowania zasobów i zarządzania nimi](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
