---
title: Kreator zabezpieczeń ról usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Opisuje Kreatora zabezpieczeń, za pomocą którego można konwertować trwałe uprzywilejowane przypisania ról usługi Azure AD w celu kwalifikowania się do korzystania z Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375449"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Kreator zabezpieczeń ról usługi Azure AD w Privileged Identity Management

Jeśli jesteś pierwszą osobą, która będzie używać Privileged Identity Management (PIM) w organizacji Azure Active Directory (Azure AD), zostanie wyświetlony Kreator umożliwiający rozpoczęcie pracy. Kreator pomaga zrozumieć zagrożenia bezpieczeństwa dotyczące tożsamości uprzywilejowanych i użyć Privileged Identity Management, aby zmniejszyć te zagrożenia. Nie musisz wprowadzać żadnych zmian w istniejących przypisaniach ról w kreatorze, jeśli wolisz zrobić to później.

## <a name="wizard-overview"></a>Przegląd Kreatora

Zanim Twoja organizacja zacznie korzystać z Privileged Identity Management, wszystkie przypisania ról są trwałe: użytkownicy są zawsze w tych rolach, nawet jeśli nie są w pełni niepotrzebnymi uprawnieniami. Pierwszy krok kreatora pokazuje listę ról z wysokim poziomem uprawnień oraz liczbę użytkowników, którzy obecnie znajdują się w tych rolach. Aby dowiedzieć się więcej o użytkownikach w przypadku, gdy co najmniej jeden z nich nie jest znany, można przejść do konkretnej roli.

Drugi krok kreatora daje możliwość zmiany przypisań ról administratorów.  

> [!WARNING]
> Ważne jest, aby mieć co najmniej jednego administratora globalnego i więcej niż jednego administratora roli uprzywilejowanej z kontem organizacyjnym (nie konto Microsoft). Jeśli istnieje tylko jeden administrator ról uprzywilejowanych, organizacja nie może zarządzać Privileged Identity Management, jeśli to konto zostanie usunięte.
> Ponadto należy zachować stałe przypisania ról, jeśli użytkownik ma konto Microsoft (innymi słowy, konto używane do logowania się do usług firmy Microsoft, takich jak Skype i Outlook.com). Jeśli planujesz wymagać uwierzytelniania wieloskładnikowego na potrzeby aktywacji dla tej roli, ten użytkownik zostanie zablokowany.

## <a name="run-the-wizard"></a>Uruchamianie kreatora

1. Zaloguj się do [Azure portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD** , a następnie wybierz pozycję **Kreator**.

    ![Role usługi Azure AD — Strona Kreatora przedstawiająca 3 kroki służące do uruchamiania Kreatora](./media/pim-security-wizard/wizard-start.png)

1. Wybierz pozycję **1 odkryj role uprzywilejowane**.

1. Przejrzyj listę ról uprzywilejowanych, aby zobaczyć, którzy użytkownicy są trwale lub uprawnieni.

    ![Odkryj role uprzywilejowane — okienko roli przedstawiające trwałe i kwalifikujące się elementy członkowskie](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Wybierz pozycję **dalej** , aby wybrać użytkowników lub grupy, które chcesz kwalifikować.

    ![Konwertuj elementy członkowskie na kwalifikujące się strony z opcjami wyboru członków, którzy mają kwalifikować się do ról](./media/pim-security-wizard/convert-members-eligible.png)

1. Po wybraniu opcji użytkownicy lub grupy wybierz pozycję **dalej**.

    ![Przejrzyj zmiany na stronie zawierającej elementy członkowskie z trwałymi przypisaniami ról, które zostaną przekonwertowane](./media/pim-security-wizard/review-changes.png)

1. Wybierz **przycisk OK** , aby przekonwertować trwałe przypisania na kwalifikujące się.

    Po zakończeniu konwersji zobaczysz powiadomienie.

    ![Powiadomienie pokazujące stan konwersji](./media/pim-security-wizard/notification-completion.png)

Jeśli zachodzi potrzeba przekonwertowania innych przypisań ról uprzywilejowanych, można uruchomić kreatora ponownie. Jeśli chcesz użyć interfejsu Privileged Identity Management zamiast kreatora, zobacz [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Udzielanie dostępu innym administratorom w celu zarządzania Privileged Identity Management](pim-how-to-give-access-to-pim.md)
