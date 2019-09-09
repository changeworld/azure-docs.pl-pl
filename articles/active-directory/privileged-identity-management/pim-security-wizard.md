---
title: Kreator zabezpieczeń ról usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Opisuje Kreatora zabezpieczeń, za pomocą którego można konwertować trwałe uprzywilejowane przypisania ról usługi Azure AD w celu kwalifikowania się do korzystania z Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d6d94df29ba16ecee06d70f5edac15a96a4299d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804019"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Kreator zabezpieczeń ról usługi Azure AD w usłudze PIM

Jeśli jesteś pierwszą osobą do uruchamiania usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) dla organizacji, zostanie wyświetlony Kreator. Kreator ułatwia zrozumienie zagrożeń bezpieczeństwa związanych z tożsamościami uprzywilejowanymi oraz korzystanie z programu PIM w celu zmniejszenia ryzyka. Nie musisz wprowadzać żadnych zmian w istniejących przypisaniach ról w kreatorze, jeśli wolisz zrobić to później.

## <a name="wizard-overview"></a>Przegląd Kreatora

Przed rozpoczęciem korzystania z usługi PIM przez organizację wszystkie przypisania ról są trwałe: użytkownicy są zawsze w tych rolach, nawet jeśli nie są w pełni niepotrzebnymi uprawnieniami. Pierwszy krok kreatora pokazuje listę ról z wysokim poziomem uprawnień oraz liczbę użytkowników, którzy obecnie znajdują się w tych rolach. Aby dowiedzieć się więcej o użytkownikach w przypadku, gdy co najmniej jeden z nich nie jest znany, można przejść do konkretnej roli.

Drugi krok kreatora daje możliwość zmiany przypisań ról administratorów.  

> [!WARNING]
> Ważne jest, aby mieć co najmniej jednego administratora globalnego i więcej niż jednego administratora roli uprzywilejowanej z kontem organizacyjnym (nie konto Microsoft). Jeśli istnieje tylko jeden administrator ról uprzywilejowanych, organizacja nie będzie w stanie zarządzać usługą PIM, jeśli to konto zostanie usunięte.
> Ponadto należy zachować stałe przypisania ról, jeśli użytkownik ma konto Microsoft (konto używane do logowania się do usług firmy Microsoft, takich jak Skype i Outlook.com). Jeśli planujesz wymagać uwierzytelniania wieloskładnikowego do aktywacji dla tej roli, ten użytkownik zostanie zablokowany.

## <a name="run-the-wizard"></a>Uruchamianie kreatora

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD** , a następnie kliknij pozycję **Kreator**.

    ![Role usługi Azure AD — Strona Kreatora przedstawiająca 3 kroki służące do uruchamiania Kreatora](./media/pim-security-wizard/wizard-start.png)

1. Kliknij pozycję **1 odkryj role uprzywilejowane**.

1. Przejrzyj listę ról uprzywilejowanych, aby zobaczyć, którzy użytkownicy są trwale lub uprawnieni.

    ![Odkryj role uprzywilejowane — okienko roli przedstawiające trwałe i kwalifikujące się elementy członkowskie](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Kliknij przycisk **dalej** , aby wybrać członków, których chcesz udostępnić.

    ![Konwertuj elementy członkowskie na kwalifikujące się strony z opcjami wyboru członków, którzy mają kwalifikować się do ról](./media/pim-security-wizard/convert-members-eligible.png)

1. Po wybraniu członków kliknij przycisk **dalej**.

    ![Przejrzyj zmiany na stronie zawierającej elementy członkowskie z trwałymi przypisaniami ról, które zostaną przekonwertowane](./media/pim-security-wizard/review-changes.png)

1. Kliknij przycisk **OK** , aby przekonwertować trwałe przypisania na kwalifikujące się.

    Po zakończeniu konwersji zobaczysz powiadomienie.

    ![Powiadomienie pokazujące stan konwersji](./media/pim-security-wizard/notification-completion.png)

Jeśli zachodzi potrzeba przekonwertowania innych przypisań ról uprzywilejowanych, można uruchomić kreatora ponownie. Jeśli chcesz użyć interfejsu PIM zamiast kreatora, zobacz [Przypisywanie ról usługi Azure AD w programie PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Udzielanie dostępu innym administratorom w celu zarządzania usługą PIM](pim-how-to-give-access-to-pim.md)
