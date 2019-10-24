---
title: Zacznij korzystać z usługi PIM Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak włączyć i rozpocząć korzystanie z Azure AD Privileged Identity Management (PIM) w Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0ba7846b60ca6649b4342d5096e92dfd8c96601
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756309"
---
# <a name="start-using-privileged-identity-management"></a>Rozpoczęcie korzystania z usługi Privileged Identity Management

Dzięki programowi Privileged Identity Management (PIM) możesz zarządzać dostępem, kontrolować i monitorować je w organizacji Azure Active Directory (Azure AD). Ten zakres obejmuje dostęp do zasobów platformy Azure, usługi Azure AD i innych Usługi online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

W tym artykule opisano sposób włączania i rozpoczynania korzystania z Privileged Identity Management.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z Privileged Identity Management, musisz mieć jedną z następujących licencji:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5

Aby uzyskać więcej informacji, zobacz [wymagania licencyjne do używania Privileged Identity Management](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Pierwsza osoba do korzystania z programu PIM

Jeśli jesteś pierwszą osobą, której chcesz używać Privileged Identity Management w Twoim katalogu, masz automatycznie przypisaną rolę [administrator zabezpieczeń](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) i [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) w katalogu. Tylko Administratorzy ról uprzywilejowanych mogą zarządzać przypisaniami użytkowników w usłudze Azure AD. Ponadto można uruchomić [Kreatora zabezpieczeń](pim-security-wizard.md) , który przeprowadzi Cię przez początkowe środowisko odnajdywania i przypisywania.

## <a name="enable-pim"></a>Włącz usługę PIM

Aby rozpocząć korzystanie z Privileged Identity Management w katalogu, należy najpierw włączyć Privileged Identity Management.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako Administrator globalny katalogu.

    Musisz być administratorem globalnym z kontem organizacyjnym (na przykład @yourdomain.com), a nie konto Microsoft (na przykład @outlook.com), aby włączyć Privileged Identity Management dla katalogu.

1. Kliknij pozycję **wszystkie usługi** i znajdź usługę **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management we wszystkich usługach](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknij, aby otworzyć Privileged Identity Management Szybki Start.

1. Na liście kliknij pozycję **wyrażanie zgody na usługę PIM**.

    ![Wyrażanie zgody na Privileged Identity Management w celu włączenia Privileged Identity Management](./media/pim-getting-started/consent-pim.png)

1. Kliknij pozycję **Weryfikuj moją tożsamość** , aby zweryfikować swoją tożsamość za pomocą usługi Azure MFA. Zostanie wyświetlony monit o wybranie konta.

    ![Wybierz okno konta, aby zweryfikować swoją tożsamość](./media/pim-getting-started/pick-account.png)

1. Jeśli do weryfikacji są wymagane więcej informacji, zostanie przeprowadzony krok po kroku. Aby uzyskać więcej informacji, zobacz [Uzyskaj pomoc dotyczącą weryfikacji dwuetapowej](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Okno wymagane więcej informacji, jeśli Twoja organizacja potrzebuje więcej informacji](./media/pim-getting-started/more-information-required.png)

    Na przykład może zostać wyświetlony monit o podanie weryfikacji telefonu.

    ![Dodatkowa strona weryfikacji zabezpieczeń z prośbą o kontakt z Tobą](./media/pim-getting-started/additional-security-verification.png)

1. Po zakończeniu procesu weryfikacji kliknij przycisk **wyrażanie zgody** .

1. W wyświetlonym komunikacie kliknij przycisk **tak** , aby wyrazić zgodę na usługę Privileged Identity Management.

    ![Wyrażanie zgody na Privileged Identity Management komunikatu w celu ukończenia procesu wyrażania zgody](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Zarejestruj się w usłudze PIM dla ról usługi Azure AD

Po włączeniu Privileged Identity Management dla katalogu należy zarejestrować Privileged Identity Management, aby zarządzać rolami usługi Azure AD.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

    ![Rejestrowanie Privileged Identity Management dla ról usługi Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Kliknij pozycję **zarejestruj się**.

1. W wyświetlonym komunikacie kliknij przycisk **tak** , aby zarejestrować się Privileged Identity Management w celu zarządzania rolami usługi Azure AD.

    ![Zarejestruj się Privileged Identity Management dla wiadomości ról usługi Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Po zakończeniu rejestracji zostaną włączone opcje usługi Azure AD. Może być konieczne odświeżenie portalu.

    Aby uzyskać informacje na temat odnajdywania i wybierania zasobów platformy Azure w celu ochrony za pomocą Privileged Identity Management, zobacz sekcję [odnajdywanie zasobów platformy Azure do zarządzania w programie Privileged Identity Management](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Przejdź do zadań

Po skonfigurowaniu Privileged Identity Management można rozpocząć zadania zarządzania tożsamościami.

![Okno nawigacji w Privileged Identity Management pokazujące zadania i opcje zarządzania](./media/pim-getting-started/pim-quickstart-tasks.png)

| Zadanie + zarządzanie | Opis |
| --- | --- |
| **Moje role**  | Wyświetla listę kwalifikujących się i aktywnych ról przypisanych do użytkownika. Możesz tu aktywować dowolne przypisane, kwalifikujące się role. |
| **Moje żądania** | Wyświetla oczekujące żądania aktywowania kwalifikujących się przypisań ról. |
| **Zatwierdzanie żądań** | Przedstawia listę żądań, które umożliwiają aktywowanie kwalifikujących się ról użytkownikom w katalogu wskazanym do zatwierdzenia. |
| **Przeglądanie dostępu** | Wyświetla listę aktywnych przeglądów dostępu, które są przypisane do ukończenia, niezależnie od tego, czy przeglądasz dostęp dla siebie, czy do innej osoby. |
| **Role usługi Azure AD** | Wyświetla pulpit nawigacyjny i ustawienia administratorów ról uprzywilejowanych w celu zarządzania przypisaniami ról usługi Azure AD. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |
| **Zasoby platformy Azure** | Wyświetla pulpit nawigacyjny i ustawienia administratorów ról uprzywilejowanych w celu zarządzania przypisaniami ról zasobów platformy Azure. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Dodawanie kafelka PIM do pulpitu nawigacyjnego

Aby ułatwić otwieranie Privileged Identity Management, należy dodać kafelek Privileged Identity Management do pulpitu nawigacyjnego Azure Portal.

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).

1. Kliknij pozycję **wszystkie usługi** i znajdź usługę **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management we wszystkich usługach](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknij, aby otworzyć Privileged Identity Management Szybki Start.

1. Zaznacz pole **Przypnij blok do pulpitu nawigacyjnego** , aby przypiąć blok Privileged Identity Management szybkiego startu do pulpitu nawigacyjnego.

    ![Ikona pinezki do przypinania Privileged Identity Management bloku do pulpitu nawigacyjnego](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na pulpicie nawigacyjnym platformy Azure zobaczysz kafelek podobny do tego:

    ![Kafelek szybkiego startu Privileged Identity Management na pulpicie nawigacyjnym](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Odnajdywanie zasobów platformy Azure w celu zarządzania nimi w Privileged Identity Management](pim-resource-roles-discover-resources.md)
