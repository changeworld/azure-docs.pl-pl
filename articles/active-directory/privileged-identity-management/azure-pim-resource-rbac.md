---
title: Widok, który ma role zasobów platformy Azure w usłudze PIM | Dokumentacja firmy Microsoft
description: Widok, który ma role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: ce7c96d92938c4e3b4cc0b53271df48350083754
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465235"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>Widok, który ma role zasobów platformy Azure w usłudze PIM

Za pomocą usługi Azure Active Directory Privileged Identity Management (PIM), możesz zarządzanie, sterowanie i monitorowanie dostępu do zasobów platformy Azure w Twojej organizacji. W tym subskrypcji, grupy zasobów i nawet maszyn wirtualnych. Dowolnego zasobu w witrynie Azure portal, która korzysta z funkcji kontroli dostępu opartej na rolach na platformie Azure korzystać z zalet zabezpieczeń i cyklu życia możliwości zarządzania w usłudze Azure AD PIM. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>Usługa PIM dla zasobów platformy Azure pomaga administratorom zasobów

- Zobacz, którzy użytkownicy i grupy są przypisane role zasobów platformy Azure, którymi administrujesz
- Włącz na żądanie, dostęp "just in time" do zarządzania zasobami, takimi jak subskrypcji, grupy zasobów i więcej
- Wygasa automatycznie z nowymi ustawieniami przypisania czasowo, dostęp do zasobów przypisanych użytkowników/grup
- Przypisz dostęp do zasobów tymczasowego dla szybkich zadań lub harmonogramów dyżurów
- Wymuszanie uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do zasobów w dowolnej roli wbudowanej lub niestandardowej 
- Uzyskiwanie raportów o działaniu zasobów skorelowane dostępu do zasobów podczas aktywnej sesji użytkownika
- Otrzymuj alerty, gdy nowych użytkowników lub grup są przypisywane dostęp do zasobów, a podczas aktywacji z kwalifikującymi się przypisaniami

## <a name="view-activation-and-azure-resource-activity"></a>Wyświetl aktywacji i działanie zasobów platformy Azure

W przypadku potrzebujesz zobaczyć, jakie akcje określony użytkownik nie wykonał dla różnych zasobów, możesz sprawdzić działanie zasobów platformy Azure skojarzone z okresem danego aktywacji (w przypadku uprawnionych użytkowników). Rozpocznij od wybranie użytkownika z widoku elementów członkowskich lub listę elementów członkowskich w określonej roli. Wynik przedstawia widok graficzny akcje użytkownika dla zasobów platformy Azure według daty, a ostatnie aktywacje ról w tym samym przedziale czasu.

![](media/azure-pim-resource-rbac/user-details.png)

Wybieranie aktywacji określonej roli zostaną wyświetlone szczegóły uaktywnienia roli, a odpowiadającego im działania zasobów platformy Azure, który wystąpił podczas, gdy ten użytkownik był aktywny.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>Przejrzyj, kto ma dostęp w ramach subskrypcji

Aby zapoznać się z przypisań ról w ramach subskrypcji, wybierz kartę elementy członkowskie w lewym obszarze nawigacji, lub wybierz role, a następnie wybierz określoną rolę, aby zapoznać się z elementów członkowskich. 

Wybierz pozycję Przegląd, na pasku akcji, aby wyświetlić istniejące przeglądów dostępu, a następnie wybierz pozycję Dodaj, aby utworzyć nowy Przegląd.

![](media/azure-pim-resource-rbac/owner.png)

[Dowiedz się więcej o przeglądach dostępu](pim-how-to-perform-security-review.md)

>[!NOTE]
Recenzje są obsługiwane tylko dla typów zasobów subskrypcji w tej chwili.

## <a name="next-steps"></a>Kolejne kroki

- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
- [Zatwierdź lub Odrzuć żądania dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-approval-workflow.md)
- [Wbudowane role na platformie Azure](../../role-based-access-control/built-in-roles.md)
