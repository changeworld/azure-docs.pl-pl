---
title: Aktywuj moje role usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak aktywować role usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9d9b2aff1d196d8b2987d77046831e7200ee2fe
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804473"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktywuj moje role usługi Azure AD w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) upraszcza zarządzanie dostępem uprzywilejowanym do zasobów w usłudze Azure AD oraz innych Usługi online, takich jak Office 365 czy Microsoft Intune.  

Jeśli masz uprawnienia do roli administracyjnej, oznacza to, że można aktywować tę rolę, gdy konieczne jest wykonanie uprzywilejowanych akcji. Na przykład w przypadku okresowego zarządzania funkcjami pakietu Office 365 Administratorzy ról uprzywilejowanych w organizacji mogą nie nakonywać stałego administratora globalnego, ponieważ rola ta ma wpływ na inne usługi. Zamiast tego uprawniają do skorzystania z ról usługi Azure AD, takich jak administrator usługi Exchange Online. Możesz zażądać aktywowania tej roli, gdy będzie potrzebne jej uprawnienia, a następnie będziesz mieć kontrolę administratora dla wstępnie wyznaczonych okresów.

Ten artykuł jest przeznaczony dla administratorów, którzy muszą aktywować swoją rolę usługi Azure AD w usłudze PIM.

## <a name="activate-a-role"></a>Aktywuj rolę

Jeśli musisz wykonać tę rolę usługi Azure AD, możesz zażądać aktywacji przy użyciu opcji nawigacji **Moje role** w programie PIM.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**. Aby uzyskać informacje na temat dodawania kafelka PIM do pulpitu nawigacyjnego, zobacz [Rozpoczynanie korzystania](pim-getting-started.md)z usługi PIM.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **Moje role** , aby wyświetlić listę uprawnionych ról usługi Azure AD.

    ![Role usługi Azure AD — moje role pokazujące listę ról kwalifikujących się lub aktywnych](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Znajdź rolę, którą chcesz aktywować.

    ![Role usługi Azure AD — Lista moich uprawnionych ról pokazująca link aktywowania](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Kliknij przycisk **Aktywuj** , aby otworzyć okienko Szczegóły aktywacji roli.

1. Jeśli Twoja rola wymaga uwierzytelniania wieloskładnikowego (MFA), kliknij przycisk **Weryfikuj swoją tożsamość przed kontynuowaniem**. Musisz tylko raz uwierzytelnić się na sesję.

    ![Weryfikowanie okienka moje tożsamości za pomocą usługi MFA przed aktywacją roli](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Kliknij pozycję **Weryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.

    ![Dodatkowa strona weryfikacji zabezpieczeń z prośbą o kontakt z Tobą](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Kliknij przycisk **Aktywuj** , aby otworzyć okienko aktywacja.

    ![Okienko aktywacji, aby określić czas rozpoczęcia, czas trwania, bilet i powód](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. W razie potrzeby określ czas rozpoczęcia aktywacji niestandardowej.

1. Określ czas trwania aktywacji.

1. W polu **Przyczyna aktywacji** wprowadź przyczynę żądania aktywacji. Niektóre role wymagają podania numeru biletu problemu.

    ![Okienko ukończono aktywację z niestandardowym czasem rozpoczęcia, czasem trwania, biletem i powodem](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Kliknij pozycję **Aktywuj**.

    Jeśli rola nie wymaga zatwierdzenia, zostanie wyświetlone okienko **stanu aktywacji** z wyświetlonym stanem aktywacja.

    ![Na stronie stanu aktywacji są wyświetlane trzy etapy aktywacji](./media/pim-how-to-activate-role/activation-status.png)

    Po zakończeniu wszystkich etapów kliknij link **Wyloguj** się, aby wylogować się z Azure Portal. Po ponownym zalogowaniu się do portalu możesz teraz korzystać z roli.

    Jeśli [rola wymaga zatwierdzenia](./azure-ad-pim-approval-workflow.md) do aktywacji, w prawym górnym rogu przeglądarki zostanie wyświetlone powiadomienie z informacją, że żądanie oczekuje na zatwierdzenie.

    ![Żądanie aktywacji oczekuje na powiadomienie o zatwierdzeniu](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Wyświetlanie stanu żądań

Można wyświetlić stan oczekujących żądań, które należy aktywować.

1. Otwórz Azure AD Privileged Identity Management.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **Moje żądania** , aby wyświetlić listę Twoich żądań.

    ![Role usługi Azure AD — moje żądania](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Dezaktywowanie roli

Po aktywowaniu roli zostanie ona automatycznie zdezaktywowana, gdy zostanie osiągnięty limit czasu (uprawniony czas trwania).

W przypadku wczesnego wykonywania zadań administratora można także dezaktywować rolę ręcznie w Azure AD Privileged Identity Management.

1. Otwórz Azure AD Privileged Identity Management.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **Moje role**.

1. Kliknij pozycję **aktywne role** , aby wyświetlić listę aktywnych ról.

1. Znajdź rolę, której używasz, a następnie kliknij pozycję **Dezaktywuj**.

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Jeśli nie wymagasz aktywacji roli wymagającej zatwierdzenia, możesz w dowolnym momencie anulować oczekujące żądanie.

1. Otwórz Azure AD Privileged Identity Management.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **Moje żądania**.

1. W przypadku roli, którą chcesz anulować, kliknij przycisk **Anuluj** .

    Po kliknięciu przycisku Anuluj żądanie zostanie anulowane. Aby ponownie aktywować rolę, trzeba będzie przesłać nowe żądanie aktywacji.

   ![Lista moje żądania z wyróżnionym przyciskiem Anuluj](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nie udzielono uprawnień po aktywowaniu roli

W przypadku aktywowania roli w programie PIM Aktywacja może nie być natychmiast propagowana do wszystkich portali, które wymagają roli uprzywilejowanej. Czasami nawet po rozpropagowaniu zmiany buforowanie internetowe w portalu może spowodować, że zmiany nie zostaną natychmiast uwzględnione. Jeśli aktywacja jest opóźniona, Oto co należy zrobić.

1. Wyloguj się z witryny Azure Portal, a następnie zaloguj się ponownie.

    Po aktywowaniu roli usługi Azure AD zobaczysz etapy aktywacji. Po zakończeniu wszystkich etapów zobaczysz link **Wyloguj**. Możesz go użyć do wylogowania się. Rozwiąże to większość problemów z opóźnieniem aktywacji.

1. W programie PIM Sprawdź, czy jesteś członkiem roli.

## <a name="next-steps"></a>Następne kroki

- [Aktywowanie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-activate-your-roles.md)
