---
title: Zatwierdź lub Odrzuć żądania dla ról katalogu usługi Azure AD w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak akceptują lub odrzucają żądania dla ról katalogu usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189162"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Zatwierdź lub Odrzuć żądania dla ról katalogu usługi Azure AD w usłudze PIM

Privileged Identity Management można skonfigurować role, aby wymagały zatwierdzenia aktywacji i wybierz jednego lub wielu użytkowników lub grup w ramach delegowanego osób zatwierdzających.

## <a name="view-pending-approvals-requests"></a>Wyświetlanie oczekujących zatwierdzeń (żądań)

Jako delegowany osoby zatwierdzającej będzie otrzymywał powiadomienia e-mail, kiedy żądanie oczekuje na zatwierdzenie. Aby wyświetlić te żądania w portalu usługi PIM, z poziomu pulpitu nawigacyjnego (w nowy sposób nawigacji) wybierz kartę "Oczekujące żądania zatwierdzenia" na pasku nawigacyjnym po lewej stronie.

![](media/azure-ad-pim-approval-workflow/image023.png)

W tym miejscu zobaczysz listę żądań oczekujących na zatwierdzenie:

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>Zatwierdź lub Odrzuć żądań o podniesienie uprawnień roli, (pojedynczy i/lub zbiorcze)

Zaznacz żądania, które chcesz zatwierdzić lub odrzucić, a następnie kliknij przycisk na pasku akcji, który odpowiada podjęcie decyzji:

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>Podaj uzasadnienie dla mojej zatwierdzania/odmowa.

Spowoduje to otwarcie nowego bloku Zatwierdź lub Odrzuć wiele żądań jednocześnie. Wprowadź uzasadnienie decyzji, a następnie kliknij pozycję Zatwierdź (lub Odrzuć) u dołu lub bloku:

![](media/azure-ad-pim-approval-workflow/image029.png)

Po zakończeniu procesu żądania w symbolu stanu będzie odzwierciedlać decyzję podjęliśmy (w tym przykładzie decyzji jest zatwierdzanie):

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>Kolejne kroki

- [Zatwierdź lub Odrzuć żądania dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-approval-workflow.md)
- [Powiadomienia e-mail w usłudze PIM](pim-email-notifications.md)
