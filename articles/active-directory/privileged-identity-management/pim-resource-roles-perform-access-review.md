---
title: Wykonaj przegląd dostępu w Privileged Identity Management zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Tym dokumencie opisano jak przeprowadzić przegląd dostępu w PIM dla zasobów platformy Azure, zgodnie z roli zasobów.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 794875cbee91c8aa4c926dbaa6931b250201a1bd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233958"
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>Wykonaj przegląd dostępu w PIM, zgodnie z roli zasobów
Privileged Identity zarządzania (PIM) dla zasobów platformy Azure upraszcza, jak zarządzać uprzywilejowanego dostępu do zasobów na platformie Azure w przedsiębiorstwach. 

Jeśli użytkownik został przypisany do roli administratora, administrator ról uprzywilejowanych w organizacji może poprosić o regularnie potwierdzić nadal potrzebujesz tej roli dla zadania. Może spowodować, że wiadomość e-mail zawierającą łącze lub można przejść bezpośrednio do [portalu Azure](https://portal.azure.com). Wykonaj kroki opisane w tym artykule, aby wykonać własnym przeglądu przypisane role.

Jeśli administrator ról uprzywilejowanych zainteresowana przeglądami dostępu, Dowiedz się więcej na [jak rozpocząć Przegląd dostępu](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Dodawanie aplikacji Privileged Identity Management
Możesz użyć aplikacji PIM w usłudze Azure Active Directory (Azure AD) w [portalu Azure](https://portal.azure.com/) przeprowadzić zapoznania się z nimi. Jeśli nie masz aplikacji w portalu, wykonaj następujące kroki, aby rozpocząć pracę.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz użytkownika nazwy w prawym górnym rogu portalu Azure, a następnie wybierz katalog, w którym można będzie działać.
3. Wybierz **wszystkie usługi**i użyj **filtru** wyszukać *Azure AD Privileged Identity Management*.
4. Sprawdź **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**. Otwarcie aplikacji PIM.

## <a name="approve-or-deny-access"></a>Zatwierdzenia lub odmowy dostępu
Podczas zatwierdzania lub odmówić dostępu należy jest po prostu informuje recenzenta czy możesz nadal używać tej roli lub nie. Wybierz **Zatwierdź** aby pozostać w tej roli, lub **Odmów** Jeśli nie musisz już dostępu. Zmiany stanu tylko wtedy, gdy osoba dokonująca przeglądu stosuje wyniki.

Wykonaj te czynności, aby go przejrzeć dostępu:
1. Przejdź do aplikacji Azure AD PIM.
2. Wybierz **sprawdzaj dostęp** bloku.

   ![Zrzut ekranu PIM aplikacji, z bloku dostępu Przejrzyj wybrane](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Wybierz przeglądu, które chcesz wykonać. 
4. Wybierz **zatwierdzić** lub **odmowy**. W **Podaj pole Przyczyna**, może być konieczne podanie Przyczyna decyzji.

   ![Zrzut ekranu przeglądu strony szczegółów](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
