---
title: Rozpoczynanie korzystania z usługi PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć i rozpoczęcie korzystania z usługi Azure AD Privileged Identity Management (PIM) w witrynie Azure portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 651dc4efa653e7a4eb77f153280579ffd87fe8f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60289995"
---
# <a name="start-using-pim"></a>Rozpoczynanie korzystania z usługi PIM

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), możesz zarządzanie, sterowanie i monitorowanie dostępu w organizacji. Ten zakres obejmuje dostęp do zasobów platformy Azure, usługi Azure AD i innych usług online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

W tym artykule opisano sposób włączania i rozpoczęcie korzystania z usługi PIM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać aplikacji PIM, musisz mieć jedną z następujących licencji:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5

Aby uzyskać więcej informacji, zobacz [licencji wymagania dotyczące korzystania z usługi PIM](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Pierwszą osobą, aby używać aplikacji PIM

Jeśli jesteś pierwszą osobą, która można używać aplikacji PIM w katalogu, są automatycznie przypisywane [Administrator zabezpieczeń](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) i [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ról w katalogu. Tylko administratorzy ról uprzywilejowanych mogą zarządzać przypisaniami ról użytkowników usługi Azure AD. Ponadto możesz zdecydować się na uruchomienie [Kreator zabezpieczeń](pim-security-wizard.md) który przeprowadzi Cię przez środowisko początkowego odnajdywania i przypisywania.

## <a name="enable-pim"></a>Włącz usługę PIM

Aby rozpocząć korzystanie z usługi PIM w katalogu, należy włączyć usługę PIM.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako Administrator globalny katalogu.

    Musi być administratorem globalnym z kontem organizacyjnym (na przykład @yourdomain.com), nie jest konto Microsoft (na przykład @outlook.com), aby włączyć usługi PIM dla katalogu.

1. Kliknij przycisk **wszystkich usług** i Znajdź **usługi Azure AD Privileged Identity Management** usługi.

    ![Usługa Azure AD Privileged Identity Management w przypadku wszystkich usług](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknij, aby otworzyć tego przewodnika Szybki Start usługi PIM.

1. Na liście kliknij **wyrazić zgodę na usługę PIM**.

    ![Wyraź zgodę na usługę PIM](./media/pim-getting-started/consent-pim.png)

1. Kliknij przycisk **Zweryfikuj moją tożsamość** Aby zweryfikować swoją tożsamość za pomocą usługi Azure MFA. Użytkownik zostanie zapytany, aby wybrać konto.

    ![Wybieranie konta](./media/pim-getting-started/pick-account.png)

1. Jeżeli jest wymaganych więcej informacji o weryfikację, poprowadzą Cię przez proces. Aby uzyskać więcej informacji, zobacz [Uzyskaj pomoc dotyczącą weryfikacji dwuetapowej](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Potrzeba więcej informacji](./media/pim-getting-started/more-information-required.png)

    Na przykład użytkownik może zostać poproszona o zapewniają Weryfikacja telefonu.

    ![Dodatkowa weryfikacja zabezpieczeń](./media/pim-getting-started/additional-security-verification.png)

1. Po zakończeniu procesu weryfikacji kliknij **zgody** przycisku.

1. W oknie komunikatu kliknij **tak** do wyrażenia zgody na usługę PIM.

    ![Zgoda na komunikat usługi PIM](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Zaloguj się w usłudze PIM dla ról usługi Azure AD

Po włączeniu usługi PIM dla katalogu należy zarejestrować się w usłudze PIM do zarządzania rolami w usłudze Azure AD.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD**.

    ![Zaloguj się w usłudze PIM dla ról usługi Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Kliknij przycisk **Zarejestruj**.

1. W oknie komunikatu kliknij **tak** do logowania się w usłudze PIM do zarządzania rolami w usłudze Azure AD.

    ![Załóż konto PIM komunikat ról usługi Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Po zakończeniu procesu rejestracji, będzie można włączyć opcji usługi Azure AD. Może być konieczne odświeżenie w portalu.

    Aby dowiedzieć się, jak odnajdywać i wybrać zasoby platformy Azure, które mają być chronione przez usługę PIM, zobacz [odnajdywanie zasobów platformy Azure do zarządzania w usłudze PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Przejdź do zadań

Po skonfigurowaniu usługi PIM można wykonywać zadania związane z zarządzaniem tożsamościami.

![Zrzut ekranu przedstawiający zadania najwyższego poziomu dla aplikacji PIM](./media/pim-getting-started/pim-quickstart-tasks.png)

| Zadania i zarządzanie nimi | Opis |
| --- | --- |
| **Moje role**  | Wyświetla listę kwalifikujących się i aktywnych ról przypisanych do Ciebie. Możesz tu aktywować dowolne przypisane, kwalifikujące się role. |
| **Moje żądania** | Wyświetla oczekujące żądania aktywowania kwalifikujących się przypisań ról. |
| **Zatwierdzanie żądań** | Przedstawia listę żądań aktywowania kwalifikujących się ról użytkowników w katalogu, które zostały oznaczone do zatwierdzenia. |
| **Przegląd dostępu wszystkich użytkowników** | Wyświetla listę aktywnych przeglądów dostępu, które są przypisane do ukończenia, czy przeglądasz dostęp dla siebie lub kogoś innego. |
| **Role usługi Azure AD** | Przedstawia pulpit nawigacyjny i ustawienia dla ról uprzywilejowanych administratorów do zarządzania przypisaniami ról usługi Azure AD. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |
| **Zasoby platformy Azure** | Przedstawia pulpit nawigacyjny i ustawienia dla ról uprzywilejowanych administratorów do zarządzania przypisaniami ról zasobów platformy Azure. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Dodaj Kafelek usługi PIM do pulpitu nawigacyjnego

Aby ułatwić Otwórz PIM, należy dodać Kafelek usługi PIM do pulpitu nawigacyjnego witryny Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Kliknij przycisk **wszystkich usług** i Znajdź **usługi Azure AD Privileged Identity Management** usługi.

    ![Usługa Azure AD Privileged Identity Management w przypadku wszystkich usług](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknij, aby otworzyć tego przewodnika Szybki Start usługi PIM.

1. Sprawdź **Przypnij blok do pulpitu nawigacyjnego** Aby przypiąć blok szybkiego startu usługi PIM do pulpitu nawigacyjnego.

    ![Przypnij blok do pulpitu nawigacyjnego](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na pulpicie nawigacyjnym platformy Azure zobaczysz Kafelek następująco:

    ![Przewodnik Szybki Start usługi PIM kafelka](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Kolejne kroki

- [Przypisz role usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Odnajdź zasoby platformy Azure do zarządzania w usłudze PIM](pim-resource-roles-discover-resources.md)
