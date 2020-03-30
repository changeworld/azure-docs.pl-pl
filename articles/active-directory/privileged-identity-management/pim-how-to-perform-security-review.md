---
title: Przeglądanie dostępu do ról usługi Azure AD w usłudze PIM — usługa Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak przejrzeć dostęp do ról usługi Azure Active Directory w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847086"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Przeglądanie dostępu do ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Zarządzanie tożsamościami uprzywilejowanymi (PIM) upraszcza sposób zarządzania przez przedsiębiorstwa uprzywilejowanym dostępem do zasobów w usłudze Azure Active Directory (AD) i innych usługach online firmy Microsoft, takich jak Office 365 lub Microsoft Intune. Wykonaj kroki opisane w tym artykule, aby pomyślnie samodzielnie przejrzeć przypisane role.

Jeśli jesteś przypisany do roli administracyjnej, administrator ról uprzywilejowanych w organizacji może poprosić o regularne potwierdzanie, że nadal potrzebujesz tej roli do zadania. Możesz otrzymać wiadomość e-mail zawierającą łącze lub przejść bezpośrednio do [witryny Azure portal](https://portal.azure.com) i rozpocząć.

Jeśli jesteś administratorem ról uprzywilejowanych lub administratorem globalnym zainteresowanym recenzjami dostępu, uzyskaj więcej informacji na temat [Jak rozpocząć przegląd dostępu](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Dodawanie kafelka pulpitu nawigacyjnego pim

Jeśli nie masz usługi Azure AD Privileged Identity Management przypięte do pulpitu nawigacyjnego w witrynie Azure Portal, wykonaj następujące kroki, aby rozpocząć.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Wybierz swoją nazwę użytkownika w prawym górnym rogu witryny Azure portal i wybierz katalog, w którym będziesz działać.
3. Wybierz pozycję **Wszystkie usługi** i użyj pola tekstowego filtru, aby wyszukać usługę **Azure AD Privileged Identity Management**.
4. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Zostanie otwarta aplikacja Privileged Identity Management.

## <a name="approve-or-deny-access"></a>Zatwierdzanie lub odmawianie dostępu

Gdy zatwierdzasz lub odmawiasz dostępu, po prostu informujesz recenzenta, czy nadal używasz tej roli, czy nie. Wybierz **pozycję Zatwierdź,** jeśli chcesz pozostać w tej roli, lub **odmów,** jeśli nie potrzebujesz już dostępu. Twój status nie zmieni się od razu, dopóki recenzent nie zastosuje wyników.
Wykonaj następujące kroki, aby znaleźć i zakończyć przegląd dostępu:

1. W usłudze Zarządzanie tożsamościami uprzywilejowanymi wybierz pozycję **Przejrzyj dostęp uprzywilejowany**. Jeśli masz żadnych oczekujących przeglądów dostępu, są one wyświetlane na stronie **Przeglądy usługi** Azure AD Access.
2. Wybierz recenzję, którą chcesz ukończyć.
3. Jeśli nie utworzono recenzji, pojawi się jako jedyny użytkownik w recenzji. Zaznacz znacznik wyboru obok swojego imienia i nazwiska.
4. Wybierz pozycję **Zatwierdź** lub **Odmów**. Może być konieczne uwzględnienie przyczyny decyzji w polu **tekstowym Podaj przyczynę.**  
5. Zamknij **narzędzie Przejrzyj blok ról usługi Azure AD.**

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki

- [Wykonywanie przeglądu dostępu ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-perform-access-review.md)
