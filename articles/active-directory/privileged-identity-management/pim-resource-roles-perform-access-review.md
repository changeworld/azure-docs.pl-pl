---
title: Jak przeprowadzić przegląd dostępu w Privileged Identity Management zasobów Azure | Dokumentacja firmy Microsoft
description: Ten dokument zawiera opis sposobu wykonywania i dostęp do przeglądu w PIM zasobów Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4afb923058143dd1771043db8433aa3a65541bf7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---perform-access-review"></a>Wykonaj Privileged Identity Management - rola zasobu: dostęp do przeglądu
Zarządzanie tożsamościami uprzywilejowanymi zasobów Azure upraszcza, jak zarządzać uprzywilejowanego dostępu do zasobów na platformie Azure w przedsiębiorstwach. 

Jeśli użytkownik został przypisany do roli administratora, administrator ról uprzywilejowanych w organizacji może poprosić o regularnie potwierdzić nadal potrzebujesz tej roli dla zadania. Może spowodować, że wiadomość e-mail zawierającą łącze lub można przejść bezpośrednio do [portalu Azure](https://portal.azure.com). Wykonaj kroki opisane w tym artykule, aby wykonać własnym przeglądu przypisane role.

Jeśli administrator ról uprzywilejowanych zainteresowana przeglądami dostępu, Dowiedz się więcej na [jak rozpocząć Przegląd dostępu](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Dodawanie aplikacji Privileged Identity Management
Możesz użyć aplikacji Azure AD Privileged Identity zarządzania (PIM) w [portalu Azure](https://portal.azure.com/) przeprowadzić zapoznania się z nimi.  Jeśli nie masz aplikacji usługi Azure AD Privileged Identity Management w portalu sieci, wykonaj następujące kroki, aby rozpocząć.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz swoją nazwę użytkownika w prawym górnym rogu portalu Azure i wybierz katalog, w której będzie można działać.
3. Wybierz pozycję **Wszystkie usługi** i użyj pola tekstowego filtru, aby wyszukać usługę **Azure AD Privileged Identity Management**.
4. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Zostanie otwarta aplikacja Privileged Identity Management.

## <a name="approve-or-deny-access"></a>Zatwierdzenia lub odmowy dostępu
Podczas zatwierdzania lub odmówić dostępu należy jest po prostu informuje recenzenta czy możesz nadal używać tej roli lub nie. Wybierz **Zatwierdź** aby pozostać w tej roli, lub **Odmów** Jeśli nie musisz już dostępu. Stan użytkownika nie zostaną zmienione od razu, dopóki recenzenta stosuje wyniki.
Wykonaj te czynności, aby go przejrzeć dostępu:
1. Przejdź do aplikacji Azure AD Privileged Identity Management.
2. Wybierz blok dostęp do przeglądu

![](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Wybierz przeglądu, którą chcesz wykonać. 
4. Wybierz **zatwierdzić** lub **odmowy**. Może być konieczne uwzględnienie Przyczyna decyzji w **przyczyny** pola tekstowego.

![](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
