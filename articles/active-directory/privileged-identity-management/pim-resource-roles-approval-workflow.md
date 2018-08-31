---
title: Zatwierdź lub Odrzuć żądania dla ról zasobów platformy Azure w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak akceptują lub odrzucają żądania dla ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189808"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Zatwierdź lub Odrzuć żądania dla ról zasobów platformy Azure w usłudze PIM

Aby zatwierdzić lub odrzucić żądanie, musi być członkiem listy osoby zatwierdzającej. 

1. W usłudze PIM, wybierz **zatwierdzanie żądań** z karty w menu po lewej stronie i Znajdź żądania.

   ![Okienko "Zatwierdź żądania"](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Wybierz żądanie, podać uzasadnienie, aby decyzję, a następnie wybierz **Zatwierdź** lub **Odmów**. Następnie usunięciu żądania.

   ![Wybrane żądania wraz ze szczegółowymi informacjami](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Powiadomienia dotyczące przepływu pracy

Poniżej przedstawiono informacje na temat powiadomienia dotyczące przepływu pracy:

- Wszyscy członkowie listy Osoba zatwierdzająca powiadomienie za pośrednictwem poczty e-mail, gdy żądanie dla roli oczekuje na ich przeglądu. Powiadomienia e-mail zawierają bezpośredni link do żądania, gdy osoba zatwierdzająca mogli zatwierdzać lub odrzucać.
- Żądania są rozpoznawane przez pierwszego elementu członkowskiego listy, która zatwierdza lub odrzuca. 
- Gdy osoba zatwierdzająca odpowiada na żądanie, wszyscy członkowie listy Osoba zatwierdzająca powiadomienie o akcji. 
- Administratorom zasobów są powiadamiani, gdy zatwierdzonego elementu członkowskiego staje się aktywny w danej roli. 

>[!Note]
>Administrator zasobu, który uważa, że zatwierdzonego elementu członkowskiego nie powinien być aktywny, można usunąć przypisania roli active w usłudze PIM. Mimo że administratorom zasobów nie są powiadamiani o oczekujących żądań, chyba że są one członków listy osoby zatwierdzającej, mogą wyświetlać i anulować oczekujące żądania wszystkim użytkownikom wyświetlanie oczekujących żądań w usłudze PIM. 

## <a name="next-steps"></a>Kolejne kroki

- [Zatwierdź lub Odrzuć żądania dla ról katalogu usługi Azure AD w usłudze PIM](azure-ad-pim-approval-workflow.md)
- [Powiadomienia e-mail w usłudze PIM](pim-email-notifications.md)
