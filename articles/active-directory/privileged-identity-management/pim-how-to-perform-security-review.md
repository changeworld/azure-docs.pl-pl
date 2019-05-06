---
title: Przegląd dostępu wszystkich użytkowników do ról usługi Azure AD w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić przegląd dostępu ról usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3850af026ea13e1920aea65e18358ebb04ef0d25
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140980"
---
# <a name="review-access-to-azure-ad-roles-in-pim"></a>Przegląd dostępu wszystkich użytkowników do ról usługi Azure AD w usłudze PIM

Azure Active Directory (AD) Privileged Identity Management (PIM) upraszcza sposób przedsiębiorstw Zarządzanie uprzywilejowanego dostępu do zasobów w usłudze Azure AD i innych usług online firmy Microsoft, takich jak usługi Office 365 lub Microsoft Intune.  

Jeśli masz przypisaną rolę administracyjną, administratorem ról uprzywilejowanych Twoja organizacja może poprosić o regularnie upewnij się, nadal potrzebujesz tej roli dla zadania. Możesz otrzymać wiadomość e-mail zawierającą łącze lub możesz przejść bezpośrednio do [witryny Azure portal](https://portal.azure.com). Wykonaj kroki opisane w tym artykule, aby wykonać własnym przeglądu przypisane role.

Jeśli jesteś administratorem ról uprzywilejowanych lub administrator globalny zainteresowani przeglądów dostępu, Dowiedz się więcej o [sposobu uruchamiania przeglądu dostępu](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Dodawanie aplikacji Privileged Identity Management
Za pomocą aplikacji Azure AD Privileged Identity Management (PIM) w [witryny Azure portal](https://portal.azure.com/) przeprowadzić zapoznania się z nimi.  Jeśli nie masz aplikacji usługi Azure AD Privileged Identity Management w portalu, wykonaj następujące kroki, aby rozpocząć pracę.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz swoją nazwę użytkownika w prawym górnym rogu witryny Azure portal i wybierz katalog, w której można będzie działać.
3. Wybierz pozycję **Wszystkie usługi** i użyj pola tekstowego filtru, aby wyszukać usługę **Azure AD Privileged Identity Management**.
4. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Zostanie otwarta aplikacja Privileged Identity Management.

## <a name="approve-or-deny-access"></a>Zatwierdź lub Odrzuć dostęp
Podczas zatwierdzania lub nie zezwoli na dostęp, możesz teraz po prostu informuje recenzenta czy możesz nadal używać tej roli lub nie. Wybierz **Zatwierdź** aby pozostać w tej roli, lub **Odmów** Jeśli nie potrzebujesz już dostęp. Twój status zmienią się natychmiast, dopóki recenzenta stosuje wyniki.
Wykonaj następujące kroki, aby znaleźć i zakończyć Przegląd dostępu:

1. W aplikacji PIM zaznacz **przeglądu uprzywilejowany dostęp**. Jeśli masz żadnych oczekujących przeglądów, pojawiają się w bloku przeglądów dostępu usługi Azure AD.
2. Wybierz pozycję Przegląd, który chcesz wykonać.
3. Jeśli nie utworzono przeglądu, wyświetlane jako jedynym użytkownikiem w przeglądzie. Kliknij znacznik wyboru obok nazwy.
4. Wybierz opcję **zatwierdzić** lub **Odmów**. Może być konieczne podanie przyczynę swojej decyzji w **Podaj przyczynę** pola tekstowego.  
5. Zamknij **role Przegląd usługi Azure AD** bloku.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki

- [Wykonywanie przeglądu dostępu ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-perform-access-review.md)
