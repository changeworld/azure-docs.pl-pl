---
title: Przeglądanie dostępu do ról zasobów platformy Azure w usłudze PIM — Usługa Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak przejrzeć dostęp do ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847014"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Przeglądanie dostępu do ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi

Przeglądy dostępu do uprzywilejowanego zarządzania tożsamościami (PIM) mogą pomóc w bezpiecznym dostępie do ról uprzywilejowanych w usłudze Azure Active Directory (Azure AD). W tym artykule kroki, aby zakończyć przegląd przypisania ról uprzywilejowanych w przeglądzie dostępu usługi Azure AD.

Jeśli jesteś przypisany do roli administracyjnej, może być konieczne ukończenie przeglądu dostępu przez administratora, aby potwierdzić potrzebę roli. Żądanie potwierdzenia może pochodzić z wiadomości e-mail zawierającej łącze lub można je potwierdzić w [witrynie Azure portal.](https://portal.azure.com)

Jeśli jesteś administratorem ról uprzywilejowanych zainteresowanym przeglądami dostępu, uzyskaj więcej informacji na temat [Jak rozpocząć przegląd dostępu](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Zatwierdzanie lub odmawianie dostępu

Możesz zatwierdzić lub odmówić dostępu na podstawie tego, czy nadal używasz tej roli, czy nie. Wybierz **pozycję Zatwierdź,** jeśli chcesz pozostać w tej roli, lub **odmów,** jeśli nie potrzebujesz już dostępu. Stan zmieni się dopiero po tym, jak recenzent zastosuje wyniki.

Wykonaj następujące kroki, aby znaleźć i zakończyć przegląd dostępu:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Wybierz **pozycję Usługa Azure Active Directory** i otwórz zarządzanie **tożsamościami uprzywilejowanymi**.
1. Wybierz **pozycję Przejrzyj dostęp**.

   ![Zrzut ekranu przedstawiający aplikację Zarządzania tożsamościami uprzywilejowanymi z wybraną wybraną wiórką Dostęp do recenzji](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Wybierz recenzję, którą chcesz ukończyć.
1. Wybierz **pozycję Zatwierdź** lub **Odmów**. W **polu Podaj przyczynę**wprowadź uzasadnienie biznesowe swojej decyzji, jeśli zajdzie taka potrzeba.

   ![Zrzut ekranu przedstawiający stronę Przejrzyj szczegóły](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Następne kroki

- [Wykonywanie przeglądu dostępu do moich ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-perform-security-review.md)
