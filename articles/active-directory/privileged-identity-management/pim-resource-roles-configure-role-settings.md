---
title: Konfigurowanie ustawień roli zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia roli zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88645d07ff213b0b7a56f2b081f0aaed01fd2929
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804204"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM

Podczas konfigurowania ustawień roli zasobów platformy Azure należy zdefiniować ustawienia domyślne, które są stosowane do przypisań ról zasobów platformy Azure w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Poniższe procedury służą do konfigurowania przepływu pracy zatwierdzania i określania, kto może zatwierdzać lub odrzucać żądania.

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia roli zasobów platformy Azure.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **zasoby platformy Azure**.

1. Kliknij zasób, którym chcesz zarządzać, na przykład subskrypcję lub grupę zarządzania.

    ![Strona zasobów platformy Azure z listą zasobów, które mogą być zarządzane](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Kliknij pozycję **Ustawienia roli**.

    ![Strona ustawień roli z listą ról zasobów platformy Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Kliknij rolę, której ustawienia chcesz skonfigurować.

    ![Strona szczegółów ustawienia roli wyświetlanie kilku ustawień przypisywania i aktywacji](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Kliknij przycisk **Edytuj** , aby otworzyć okienko ustawienia roli.

    ![Strona edytowania ustawień roli z opcjami aktualizowania ustawień przypisywania i aktywacji](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    W okienku Ustawienia roli dla każdej roli istnieje kilka ustawień, które można skonfigurować.

## <a name="assignment-duration"></a>Czas trwania przypisywania

Można wybrać jedną z dwóch opcji czasu trwania przypisania dla każdego typu przypisania (kwalifikujące się i aktywne) podczas konfigurowania ustawień dla roli. Te opcje stają się domyślnym maksymalnym czasem trwania, gdy członek jest przypisany do roli w programie PIM.

Można wybrać jedną **z następujących opcji** czasu trwania przypisania:

| | |
| --- | --- |
| **Zezwalaj na trwałe przypisanie** | Administratorzy zasobów mogą przypisywać trwałe członkowstwo kwalifikujące się. |
| **Unieważnij przypisanie kwalifikujące się po** | Administratorzy zasobów mogą wymagać, aby wszystkie kwalifikujące się przypisania mieli określoną datę początkową i końcową. |

Można też wybrać jedną z następujących opcji **aktywnego** przydziału czasu trwania:

| | |
| --- | --- |
| **Zezwalaj na trwałe aktywne przypisanie** | Administratorzy zasobów mogą przypisywać trwałe aktywne członkowstwo. |
| **Wygasa aktywne przypisanie po** | Administratorzy zasobów mogą wymagać, aby wszystkie aktywne przypisania miały określoną datę początkową i końcową. |

> [!NOTE] 
> Wszystkie przypisania o określonej dacie końcowej mogą zostać odnowione przez administratorów zasobów. Ponadto członkowie mogą inicjować żądania samoobsługi w celu [przedłużenia lub odnowienia przypisań ról](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

Program PIM zapewnia opcjonalne Wymuszanie usługi Azure Multi-Factor Authentication (MFA) dla dwóch różnych scenariuszy.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Wymagaj Multi-Factor Authentication przy aktywnym przypisaniu

W niektórych przypadkach może być konieczne przypisanie składowej do roli przez krótki czas trwania (na przykład jeden dzień). W takim przypadku nie potrzebują przypisanych członków do żądania aktywacji. W tym scenariuszu program PIM nie może wymusić uwierzytelniania MFA, gdy członek korzysta z przypisywania ról, ponieważ są one już aktywne w roli od momentu, w którym są przypisane.

Aby upewnić się, że administrator zasobów spełniający przypisanie, należy wymusić uwierzytelnianie wieloskładnikowe na aktywnym przypisaniu, zaznaczając pole **wymagaj Multi-Factor Authentication w aktywnym przypisaniu** .

### <a name="require-multi-factor-authentication-on-activation"></a>Wymagaj Multi-Factor Authentication podczas aktywacji

Przed aktywowaniem usługi MFA można wymagać od uprawnionych członków roli. Ten proces zapewnia, że użytkownik, który żąda aktywacji, jest odpowiedzialny za uzyskanie odpowiednich pewności. Wymuszanie tej opcji chroni zasoby o znaczeniu krytycznym w sytuacjach, gdy naruszone konto użytkownika mogło ulec naruszeniu.

Aby wymagać, aby uprawniony członek mógł uruchomić usługę MFA przed aktywacją, zaznacz pole **wymagaj Multi-Factor Authentication przy aktywacji** .

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie wieloskładnikowe (MFA) i program PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maksymalny czas trwania uaktywniania

Suwak **Maksymalny czas trwania aktywacji** umożliwia ustawienie maksymalnego czasu (w godzinach), przez który rola pozostaje aktywna przed wygaśnięciem. Ta wartość może należeć do zakresu od 1 do 24 godzin.

## <a name="require-justification"></a>Wymagaj uzasadnienia

Możesz wymagać, aby członkowie mogli wprowadzić uzasadnienie aktywnego przypisania lub kiedy aktywują. Aby wymagać uzasadnienia, zaznacz pole wyboru **Wymagaj uzasadnienia przy aktywnym przypisaniu** lub wybierz opcję **Wymagaj uzasadnienia przy aktywacji** .

## <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia w celu aktywacji

Jeśli chcesz wymagać zatwierdzenia w celu aktywowania roli, wykonaj następujące czynności.

1. Zaznacz pole wyboru **Wymagaj zatwierdzenia do uaktywnienia** .

1. Kliknij przycisk **Wybierz osoby zatwierdzające** , aby otworzyć okienko Wybierz członka lub grupę.

    ![Wybierz okienko elementów członkowskich lub grup, aby wybrać osoby zatwierdzające](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Wybierz co najmniej jednego członka lub grupę, a następnie kliknij przycisk **Wybierz**. Możesz dodać dowolną kombinację elementów członkowskich i grup. Musisz wybrać co najmniej jedną osobę zatwierdzającą. Brak domyślnych osób zatwierdzających.

    Wybrane opcje zostaną wyświetlone na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkich ustawień roli kliknij pozycję **Aktualizuj** , aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
- [Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-alerts.md)
