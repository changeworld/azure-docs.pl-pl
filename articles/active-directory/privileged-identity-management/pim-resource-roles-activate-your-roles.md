---
title: Aktywuj moje role zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak aktywować role zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e2bf1253f1c2f53b839a73034fcdb33f3aab463
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804338"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Aktywuj moje role zasobów platformy Azure w usłudze PIM

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kwalifikujące się elementy członkowskie roli dla zasobów platformy Azure mogą zaplanować aktywację dla przyszłej daty i godziny. Mogą również wybrać określony czas trwania aktywacji w maksymalnym (skonfigurowanym przez administratorów).

Ten artykuł dotyczy członków, którzy muszą aktywować swoją rolę zasobów platformy Azure w usłudze PIM.

## <a name="activate-a-role"></a>Aktywuj rolę

Gdy musisz przejąć rolę zasobów platformy Azure, możesz zażądać aktywacji przy użyciu opcji nawigacji **Moje role** w programie PIM.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**. Aby uzyskać informacje na temat dodawania kafelka PIM do pulpitu nawigacyjnego, zobacz [Rozpoczynanie korzystania](pim-getting-started.md)z usługi PIM.

1. Kliknij pozycję **Moje role**.

    ![Strona Moje role pokazujące role, które można uaktywnić](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Kliknij pozycję **role zasobów platformy Azure** , aby wyświetlić listę kwalifikujących się ról zasobów platformy Azure.

   ![Moje role — strona ról zasobów platformy Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Na liście **role zasobów platformy Azure** Znajdź rolę, którą chcesz aktywować.

    ![Role zasobów platformy Azure — lista wszystkich uprawnionych ról](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Kliknij przycisk **Aktywuj** , aby otworzyć okienko Aktywuj.

1. Jeśli Twoja rola wymaga uwierzytelniania wieloskładnikowego (MFA), kliknij przycisk **Weryfikuj swoją tożsamość przed kontynuowaniem**. Musisz tylko raz uwierzytelnić się na sesję.

    ![Weryfikuj moją tożsamość z użyciem usługi MFA przed aktywacją roli](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Kliknij pozycję **Weryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.

    ![Ekran zapewniający weryfikację zabezpieczeń, np. kod PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Jeśli chcesz określić ograniczony zakres, kliknij pozycję **zakres** , aby otworzyć okienko filtr zasobów.

    Najlepszym rozwiązaniem jest zażądanie dostępu do zasobów, które są potrzebne. W okienku Filtr zasobów można określić grupy zasobów lub zasoby, do których jest wymagany dostęp.

    ![Aktywuj okienko filtru zasobów, aby określić zakres](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. W razie potrzeby określ czas rozpoczęcia aktywacji niestandardowej. Członek zostanie aktywowany po wybranym czasie.

1. W polu **Przyczyna** wprowadź przyczynę żądania aktywacji.

    ![Okienko ukończono aktywację z zakresem, czasem rozpoczęcia, czasem trwania i przyczynie](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Kliknij pozycję **Aktywuj**.

    Jeśli rola nie wymaga zatwierdzenia, zostanie aktywowana i dodana do listy aktywnych ról. Jeśli chcesz użyć roli, postępuj zgodnie z instrukcjami w następnej sekcji.

    Jeśli [rola wymaga zatwierdzenia](pim-resource-roles-approval-workflow.md) do aktywacji, w prawym górnym rogu przeglądarki zostanie wyświetlone powiadomienie z informacją, że żądanie oczekuje na zatwierdzenie.

    ![Żądanie aktywacji oczekuje na powiadomienie o zatwierdzeniu](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Korzystanie z roli natychmiast po aktywacji

W przypadku wszelkich opóźnień po aktywacji wykonaj następujące czynności po aktywowaniu programu, aby natychmiast korzystać z ról zasobów platformy Azure.

1. Otwórz Azure AD Privileged Identity Management.

1. Kliknij pozycję **Moje role** , aby wyświetlić listę uprawnionych ról usługi Azure AD i ról zasobów platformy Azure.

1. Kliknij pozycję **role zasobów platformy Azure**.

1. Kliknij kartę **aktywne role** .

1. Po uaktywnieniu roli Wyloguj się z portalu i zaloguj się ponownie.

    Rola powinna być teraz dostępna do użycia.

## <a name="view-the-status-of-your-requests"></a>Wyświetlanie stanu żądań

Można wyświetlić stan oczekujących żądań, które należy aktywować.

1. Otwórz Azure AD Privileged Identity Management.

1. Kliknij pozycję **Moje żądania** , aby wyświetlić listę ról usługi Azure AD i żądań ról zasobów platformy Azure.

    ![Moje żądania — Strona zasobów platformy Azure zawierająca oczekujące żądania](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Przewiń w prawo, aby wyświetlić kolumnę **stan żądania** .

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Jeśli nie wymagasz aktywacji roli wymagającej zatwierdzenia, możesz w dowolnym momencie anulować oczekujące żądanie.

1. Otwórz Azure AD Privileged Identity Management.

1. Kliknij pozycję **Moje żądania**.

1. W przypadku roli, którą chcesz anulować, kliknij link **Anuluj** .

    Po kliknięciu przycisku Anuluj żądanie zostanie anulowane. Aby ponownie aktywować rolę, trzeba będzie przesłać nowe żądanie aktywacji.

   ![Lista moje żądania z wyróżnioną akcją anulowania](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nie udzielono uprawnień po aktywowaniu roli

W przypadku aktywowania roli w programie PIM Aktywacja może nie być natychmiast propagowana do wszystkich portali, które wymagają roli uprzywilejowanej. Czasami nawet po rozpropagowaniu zmiany buforowanie internetowe w portalu może spowodować, że zmiany nie zostaną natychmiast uwzględnione. Jeśli aktywacja jest opóźniona, Oto co należy zrobić.

1. Wyloguj się z witryny Azure Portal, a następnie zaloguj się ponownie.

    Po aktywowaniu roli zasobów platformy Azure zobaczysz etapy aktywacji. Po zakończeniu wszystkich etapów zobaczysz link **Wyloguj**. Możesz go użyć do wylogowania się. Rozwiąże to większość problemów z opóźnieniem aktywacji.

1. W programie PIM Sprawdź, czy jesteś członkiem roli.

## <a name="next-steps"></a>Następne kroki

- [Przedłużanie lub odnowienie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-renew-extend.md)
- [Aktywowanie ról usługi Azure AD w usłudze PIM](pim-how-to-activate-role.md)
