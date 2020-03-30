---
title: Konfigurowanie ustawień roli usługi Azure AD w usłudze PIM — Usługa Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia roli usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205028"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurowanie ustawień roli usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Administrator ról uprzywilejowanych może dostosować zarządzanie tożsamościami uprzywilejowanymi (PIM) w organizacji usługi Azure Active Directory (Azure AD), w tym zmieniać środowisko dla użytkownika, który aktywuje przypisanie kwalifikującej się roli.

## <a name="determine-your-version-of-pim"></a>Określanie wersji programu PIM

Począwszy od listopada 2019 r. część ról usługi Azure AD usługi Zarządzanie tożsamościami uprzywilejowanymi jest aktualizowana do nowej wersji, która odpowiada środowiskom ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmiany w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, które procedury, które należy wykonać w tym artykule, zależą od wersji zarządzania tożsamościami uprzywilejowanymi, które obecnie masz. Wykonaj kroki opisane w tej sekcji, aby ustalić, którą wersję zarządzania tożsamościami uprzywilejowanymi masz. Po poznaniu wersji zarządzania tożsamościami uprzywilejowanymi można wybrać procedury w tym artykule, które są zgodne z tą wersją.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest w roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**. Jeśli u góry strony przeglądu masz baner, postępuj zgodnie z instrukcjami na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami na karcie **Poprzednia wersja.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Wykonaj kroki opisane w tym artykule, aby zatwierdzić lub odrzucić żądania dla ról usługi Azure AD.

# <a name="new-version"></a>[Nowa wersja](#tab/new)

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia roli usługi Azure AD.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem w roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
gt
1. Otwórz ustawienia ról ról usługi **Azure Uprzywilejowane zarządzanie tożsamościami** &gt; **Azure AD roles** &gt; usługi Azure AD platformy Azure **.**

    ![Strona ustawień roli z listą ról usługi Azure AD](./media/pim-how-to-change-default-settings/role-settings.png)

1. Wybierz rolę, której ustawienia chcesz skonfigurować.

    ![Strona szczegółów ustawień roli zawierająca listę kilku ustawień przypisania i aktywacji](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Wybierz **pozycję Edytuj,** aby otworzyć stronę Ustawienia roli.

    ![Edytowanie strony ustawień roli z opcjami aktualizacji ustawień przypisania i aktywacji](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    W okienku ustawień roli dla każdej roli istnieje kilka ustawień, które można skonfigurować.

## <a name="assignment-duration"></a>Czas trwania przydziału

Podczas konfigurowania ustawień roli można wybrać jedną z dwóch opcji czasu trwania przydziału dla każdego typu przydziału (kwalifikującego się i aktywnego). Te opcje stają się domyślnym maksymalnym czasem trwania, gdy użytkownik jest przypisany do roli w zarządzanie tożsamością uprzywilejowaną.

Możesz wybrać jedną z tych **kwalifikujących się** opcji czasu trwania przydziału:

| | |
| --- | --- |
| **Zezwalaj na stałe kwalifikujące się przydziały** | Administratorzy globalni i administratorzy ról uprzywilejowanych mogą przypisywać stałe kwalifikujące się przypisanie. |
| **Wygaśnie kwalifikujący się przydział po** | Administratorzy globalni i administratorzy ról uprzywilejowanych mogą wymagać, aby wszystkie kwalifikujące się przydziały miały określoną datę rozpoczęcia i zakończenia. |

Możesz też wybrać jedną z tych **aktywnych** opcji czasu trwania przydziału:

| | |
| --- | --- |
| **Zezwalaj na stałe aktywne przypisanie** | Administratorzy globalni i administratorzy ról uprzywilejowanych mogą przypisywać stałe aktywne przypisanie. |
| **Wygaśnie aktywne przypisanie po** | Administratorzy globalni i administratorzy ról uprzywilejowanych mogą wymagać, aby wszystkie aktywne przypisania miały określoną datę rozpoczęcia i zakończenia. |

> [!NOTE]
> Wszystkie przypisania z określoną datą zakończenia mogą być odnawiane przez administratorów globalnych i administratorów ról uprzywilejowanych. Ponadto użytkownicy mogą inicjować żądania samoobsługi w celu [rozszerzenia lub odnowienia przydziałów ról](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

Zarządzanie tożsamościami uprzywilejowanymi zapewnia opcjonalne wymuszanie uwierzytelniania wieloskładnikowego platformy Azure dla dwóch różnych scenariuszy.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Wymagaj uwierzytelniania wieloskładnikowego przy aktywnym przypisaniu

W niektórych przypadkach można przypisać użytkownika do roli na krótki czas (na przykład jeden dzień). W takim przypadku przypisani użytkownicy nie muszą żądać aktywacji. W tym scenariuszu zarządzanie tożsamością uprzywilejowaną nie można wymusić uwierzytelniania wieloskładnikowego, gdy użytkownik używa przypisania roli, ponieważ są już aktywne w roli od momentu, w którym jest przypisany.

Aby upewnić się, że administrator wypełniający przypisanie jest tym, kim twierdzi, że jest, możesz wymusić uwierzytelnianie wieloskładnikowe przy aktywnym przypisaniu, zaznaczając pole **Wymagaj uwierzytelniania wieloskładnikowego w aktywnym przypisaniu.**

### <a name="require-multi-factor-authentication-on-activation"></a>Wymagaj uwierzytelniania wieloskładnikowego podczas aktywacji

Możesz wymagać od użytkowników, którzy kwalifikują się do roli, aby udowodnić, kogo używają uwierzytelniania wieloskładnikowego platformy Azure, zanim będą mogli aktywować. Uwierzytelnianie wieloskładnikowe zapewnia, że użytkownik jest tym, kim mówią, że są z rozsądną pewnością. Wymuszanie tej opcji chroni krytyczne zasoby w sytuacjach, gdy konto użytkownika mogło zostać naruszone.

Aby wymagać uwierzytelniania wieloskładnikowego przed aktywacją, zaznacz pole wyboru **Wymagaj uwierzytelniania wieloskładnikowego podczas aktywacji** na karcie Przypisanie **ustawienia roli Edytowanie**.

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie wieloskładnikowe i Zarządzanie tożsamościami uprzywilejowanymi](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maksymalny czas trwania aktywacji

Użyj **suwaka Maksymalny czas trwania aktywacji,** aby ustawić maksymalny czas w godzinach, przez który rola pozostaje aktywna przed jej wygaśnięciem. Ta wartość może wynosić od jednej do 24 godzin.

## <a name="require-justification"></a>Wymagaj uzasadnienia

Podczas aktywacji można wymagać, aby użytkownicy wprowadzali uzasadnienie biznesowe. Aby wymagać uzasadnienia, zaznacz pole **Wymagaj justowania w aktywnym przypisaniu** lub **Pole Wymagaj uzasadnienia w aktywacji.**

## <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia, aby aktywować

Jeśli ustawienie wielu osób zatwierdzających zostanie zakończone, gdy tylko jedna z nich zatwierdzi lub odrzuci. Nie można wymagać zatwierdzenia od co najmniej dwóch użytkowników. Aby wymagać zatwierdzenia, aby aktywować rolę, wykonaj następujące kroki.

1. Zaznacz pole wyboru **Wymagaj zatwierdzenia, aby aktywować.**

1. Wybierz **pozycję Wybierz osoby zatwierdzające**.

    ![Wybieranie okienka użytkownika lub grupy w celu wybrania osób zatwierdzających](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Zaznacz co najmniej jednego użytkownika, a następnie kliknij przycisk **Wybierz**. Musisz wybrać co najmniej jedną osobę zatwierdzającej. Nie ma żadnych domyślnych osób zatwierdzających.

    Wybrane osoby zatwierdzające pojawią się na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkich ustawień roli wybierz pozycję **Aktualizuj,** aby zapisać zmiany.

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia roli usługi Azure AD.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **pozycję Ustawienia**.

    ![Role usługi Azure AD — ustawienia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Wybierz **pozycję Role**.

1. Wybierz rolę, której ustawienia chcesz skonfigurować.

    ![Role usługi Azure AD — role ustawień](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na stronie ustawień dla każdej roli znajduje się kilka ustawień, które można skonfigurować. Te ustawienia dotyczą tylko użytkowników, którzy kwalifikują **się do** przypisania, a nie **stałych** przypisań.

## <a name="activations"></a>Aktywacji

Użyj **suwaka Aktywacje,** aby ustawić maksymalny czas w godzinach, aby rola pozostała aktywna przed jej wygaśnięciem. Ta wartość może wynosić od 1 do 72 godzin.

## <a name="notifications"></a>Powiadomienia

Użyj **przełącznika Powiadomienia,** aby określić, czy administratorzy będą otrzymywać powiadomienia e-mail po aktywowaniu ról. To powiadomienie może być przydatne do wykrywania nieautoryzowanych lub bezprawnych aktywacji.

Po **ustawieniu**opcji Włącz powiadomienia są wysyłane do:

- Administrator ról uprzywilejowanych
- Administrator zabezpieczeń
- Administrator globalny

Aby uzyskać więcej informacji, zobacz [Powiadomienia e-mail w obszarze Zarządzanie tożsamościami uprzywilejowanymi](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Bilet na incydent/żądanie

Użyj przełącznika **Bilet incydentu/żądania,** aby wymagać od kwalifikujących się administratorów podania numeru biletu podczas aktywowania swojej roli. Praktyka ta może sprawić, że audyty dostępu do ról będą bardziej skuteczne.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Użyj **przełącznika uwierzytelniania wieloskładnikowego,** aby określić, czy użytkownicy mają wymagać weryfikacji tożsamości za pomocą usługi MFA, zanim będą mogli aktywować swoje role. Muszą zweryfikować swoją tożsamość tylko raz na sesję, a nie za każdym razem, gdy aktywują rolę. Istnieją dwie wskazówki, o których należy pamiętać po włączeniu usługi MFA:

- Użytkownicy, którzy mają konta Microsoft @outlook.comdla swoich adresów e-mail (zazwyczaj , ale nie zawsze) nie mogą zarejestrować się w celu uzyskania uwierzytelniania wieloskładnikowego platformy Azure. Jeśli chcesz przypisać role do użytkowników z kontami Microsoft, należy je albo stali administratorzy lub wyłączyć uwierzytelnianie wieloskładnikowe dla tej roli.
- Nie można wyłączyć uwierzytelniania wieloskładnikowego platformy Azure dla ról o wysokim stopniu uprzywilejowania dla usługi Azure AD i usługi Office 365. Ta funkcja bezpieczeństwa pomaga chronić następujące role:  
  
  - Administrator usługi Azure Information Protection
  - Administrator rozliczeń
  - Administrator aplikacji w chmurze
  - Administrator zgodności
  - Administrator dostępu warunkowego
  - Administrator dynamics 365
  - Osoba zatwierdzająca dostęp do skrytki klienta
  - Autorzy katalogów
  - Administrator programu Exchange
  - Administrator globalny
  - Administrator usługi Intune
  - Administrator usługi Power BI
  - Administrator ról uprzywilejowanych
  - Administrator zabezpieczeń
  - Administrator programu SharePoint
  - Administrator programu Skype dla firm
  - Administrator użytkownika

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie wieloskładnikowe i Zarządzanie tożsamościami uprzywilejowanymi](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Wymagaj zatwierdzenia

Jeśli chcesz delegować wymagane zatwierdzenie do aktywowania roli, wykonaj następujące kroki.

1. Ustaw przełącznik **Wymagaj zatwierdzenia** na **Włączone**. Okienko zostanie rozwinięte o opcje wyboru osób zatwierdzających.

    ![Role usługi Azure AD — ustawienia — wymagaj zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Jeśli nie określisz żadnych osób zatwierdzających, administrator roli uprzywilejowanej staje się domyślną aprobatorem, a następnie musi zatwierdzić wszystkie żądania aktywacji dla tej roli.

1. Aby dodać osoby zatwierdzające, kliknij pozycję **Wybierz osoby zatwierdzające**.

    ![Role usługi Azure AD — ustawienia — wymagaj zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Wybierz jedną lub więcej osób zatwierdzających oprócz administratora ról uprzywilejowanych, a następnie kliknij przycisk **Wybierz**. Zaleca się dodanie co najmniej dwóch osób zatwierdzających. Nawet jeśli dodasz siebie jako osobę zatwierdzającej, nie możesz samodzielnie zatwierdzić aktywacji roli. Wybrane osoby zatwierdzające pojawią się na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkich ustawień roli wybierz pozycję **Zapisz,** aby zapisać zmiany.

---

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-add-role-to-user.md)
- [Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-configure-security-alerts.md)
