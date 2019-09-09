---
title: Przeglądanie dostępu do ról zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przeglądać dostęp do ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c0ce1a5eee4b8d4cc40c47dadd4bcdc74d03ba
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804103"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Przeglądanie dostępu do ról zasobów platformy Azure w usłudze PIM
Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) upraszcza zarządzanie dostępem uprzywilejowanym do zasobów na platformie Azure przez przedsiębiorstwa. 

Jeśli masz przypisaną rolę administracyjną, administrator ról uprzywilejowanych w organizacji może zażądać regularnego potwierdzenia, że nadal potrzebujesz tej roli dla danego zadania. Możesz otrzymać wiadomość e-mail zawierającą link lub możesz przejść bezpośrednio do [Azure Portal](https://portal.azure.com). Wykonaj kroki opisane w tym artykule, aby przeprowadzić samodzielną weryfikację przypisanych ról.

Jeśli jesteś administratorem roli uprzywilejowanej interesu w przeglądach dostępu, uzyskaj więcej szczegółowych informacji na [temat sposobu uruchamiania przeglądu dostępu](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Dodawanie aplikacji Privileged Identity Management
Aby przeprowadzić przegląd, można użyć aplikacji PIM Azure Active Directory (Azure AD) w [Azure Portal](https://portal.azure.com/) . Jeśli nie masz aplikacji w portalu, wykonaj następujące kroki, aby rozpocząć pracę.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz swoją nazwę użytkownika w prawym górnym rogu Azure Portal i wybierz katalog, w którym będziesz działać.
3. Wybierz pozycję **wszystkie usługi**, a następnie użyj pola **Filtr** , aby wyszukać *Azure AD Privileged Identity Management*.
4. Zaznacz pole **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**. Zostanie otwarta aplikacja PIM.

## <a name="approve-or-deny-access"></a>Zatwierdź lub Odmów dostępu
Po zatwierdzeniu lub odrzuceniu dostępu użytkownik otrzymuje jedynie informację o tym, czy nadal używasz tej roli, czy nie. Wybierz pozycję **Zatwierdź** , jeśli chcesz pozostać w roli, lub **Odmów** , jeśli nie potrzebujesz już dostępu. Stan zmieni się tylko wtedy, gdy recenzent zastosuje wyniki.

Wykonaj następujące kroki, aby znaleźć i ukończyć przegląd dostępu:
1. Przejdź do aplikacji PIM usługi Azure AD.
2. Wybierz blok **Przegląd dostępu** .

   ![Zrzut ekranu aplikacji PIM z wybranym blokiem przegląd dostępu](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

3. Wybierz przegląd, który chcesz zakończyć. 
4. Wybierz opcję **Zatwierdź** lub **Odmów**. W **polu podaj przyczynę**może być konieczne uwzględnienie przyczyny decyzji.

   ![Zrzut ekranu przedstawiający stronę szczegółów przeglądu](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Następne kroki

- [Przeprowadzanie przeglądu dostępu do ról usługi Azure AD w usłudze PIM](pim-how-to-perform-security-review.md)
