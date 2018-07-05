---
title: Jak przeprowadzić przegląd dostępu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić przegląd przy użyciu aplikacji Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f3d8cbdcef25509304785090190f172ae20913b6
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440812"
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>Jak przeprowadzić przegląd dostępu w usłudze Azure AD Privileged Identity Management
Usługa Azure Active Directory (AD) Privileged Identity Management upraszcza sposób przedsiębiorstw Zarządzanie uprzywilejowanego dostępu do zasobów w usłudze Azure AD i innych usług online firmy Microsoft, takich jak usługi Office 365 lub Microsoft Intune.  

Jeśli masz przypisaną rolę administracyjną, administratorem ról uprzywilejowanych Twoja organizacja może poprosić o regularnie upewnij się, nadal potrzebujesz tej roli dla zadania. Możesz otrzymać wiadomość e-mail zawierającą łącze lub możesz przejść bezpośrednio do [witryny Azure portal](https://portal.azure.com). Wykonaj kroki opisane w tym artykule, aby wykonać własnym przeglądu przypisane role.

Jeśli jesteś administratorem ról uprzywilejowanych lub administrator globalny zainteresowani przeglądów dostępu, Dowiedz się więcej o [sposobu uruchamiania przeglądu dostępu](active-directory-privileged-identity-management-how-to-start-security-review.md).

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
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
