---
title: Kreator zabezpieczeń usługi Azure AD Privileged Identity Management
description: Użyj rozszerzenia usługi Azure Active Directory Privileged Identity Management po raz pierwszy zostanie wyświetlona za pomocą Kreatora zabezpieczeń. W tym artykule opisano kroki dotyczące korzystania z kreatora.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 02/27/2017
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 4b3856d74b1109b20a1ff9f93b76ee36b66ee312
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="using-the-security-wizard-in-azure-ad-privileged-identity-management"></a>Za pomocą Kreatora zabezpieczeń w usłudze Azure AD Privileged Identity Management 
Jeśli jesteś pierwszą osobą, która Uruchom Azure Privileged Identity zarządzania (PIM) dla organizacji, użytkownik zobaczy za pomocą kreatora. Kreator pomaga w zrozumieniu zagrożenia bezpieczeństwa tożsamości uprzywilejowanych oraz sposobu używania usługi PIM te zagrożenia. Nie trzeba wprowadzać żadnych zmian w istniejących przypisań ról w kreatorze, aby zrobić to później.

## <a name="what-to-expect"></a>Czego oczekiwać
Przed rozpoczęciem organizacji przy użyciu usługi PIM są trwałe, wszystkie przypisania roli: użytkownicy są zawsze w ramach tych ról, nawet jeśli ich nie są obecnie wymagane ich uprawnienia.  Pierwszy krok kreatora zawiera listę ról z wysokim poziomem i ilu użytkowników aktualnie znajdują się w tych ról. Można przejść do określonej roli, aby uzyskać więcej informacji użytkowników, jeśli jeden lub jeden z nich jest nieznane.

Drugim kroku kreatora daje możliwość zmiany przypisań ról administratora.  

> [!WARNING]
> Jest ważne, czy masz co najmniej jednego administratora globalnego i więcej niż jeden administrator ról uprzywilejowanych za pomocą konta organizacyjnego (a nie kontem Microsoft). Jeśli istnieje tylko jeden administrator ról uprzywilejowanych, organizacji, nie będzie mógł zarządzać PIM usunięcie tego konta.
> Ponadto przypisań ról Zachowaj stałe, jeśli użytkownik ma konto Microsoft (konta używanego do logowania do usług firmy Microsoft, takich jak Skype i Outlook.com). Jeśli planujesz wymagają usługi MFA do aktywacji dla tej roli użytkownika zostanie zablokowane.
> 
> 

Po dokonaniu zmian, Kreator będzie już wyświetlany. Następnym razem, użytkownik lub inny administrator ról uprzywilejowanych używać aplikacji PIM, zostanie wyświetlony pulpit nawigacyjny usługi PIM.  

* Jeśli chcesz dodać lub usunąć użytkowników z ról lub zmienić przypisania z stałe kwalifikujących się Dowiedz się więcej o [jak dodać lub usunąć rolę użytkownika](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
* Jeśli chcesz umożliwić użytkownikom więcej dostęp do zarządzania PIM, Dowiedz się więcej o [sposób udzielić dostępu do zarządzania w PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

