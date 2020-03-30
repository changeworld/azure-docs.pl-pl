---
title: Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM — Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia roli zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638684"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Konfigurowanie ustawień roli zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi

Podczas konfigurowania ustawień roli zasobów platformy Azure można zdefiniować ustawienia domyślne, które są stosowane do przydziałów ról zasobów platformy Azure w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Poniższe procedury można skonfigurować w przepływie pracy zatwierdzania i określić, kto może zatwierdzać lub odrzucać żądania.

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia roli zasobu platformy Azure.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem w roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **zasoby platformy Azure**.

1. Wybierz zasób, który chcesz zarządzać, taki jak subskrypcja lub grupa zarządzania.

    ![Strona zasobów platformy Azure zawiera listę zasobów, którymi można zarządzać](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Wybierz **pozycję Ustawienia roli**.

    ![Strona ustawień roli z listą ról zasobów platformy Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Wybierz rolę, której ustawienia chcesz skonfigurować.

    ![Strona szczegółów ustawień roli zawierająca listę kilku ustawień przypisania i aktywacji](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Wybierz **pozycję Edytuj,** aby otworzyć okienko **Ustawień roli.** Pierwsza karta umożliwia zaktualizowanie konfiguracji aktywacji roli w zarządzania tożsamościami uprzywilejowanymi.

    ![Strona Edytowanie ustawień roli z otwartą kartą Aktywacja](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Wybierz kartę **Przydział** lub Przycisk **Przypisanie dalej u** dołu strony, aby otworzyć kartę ustawienia przypisania. Te ustawienia kontrolują przypisania ról wprowadzone wewnątrz interfejsu zarządzania tożsamościami uprzywilejowanymi.

    ![Karta Przypisanie roli na stronie ustawień roli](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Użyj karty **Powiadomienie** lub Przycisk **Aktywacja dalej:** u dołu strony, aby przejść do karty ustawień powiadomień dla tej roli. Te ustawienia sterują wszystkimi powiadomieniami e-mail związanymi z tą rolą.

    ![Karta Powiadomienia o rolach na stronie ustawień roli](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    Na karcie **Powiadomienia** na stronie ustawienia roli zarządzanie tożsamościami uprzywilejowanymi umożliwia szczegółową kontrolę nad tym, kto otrzymuje powiadomienia i jakie powiadomienia otrzymują.

    - **Wyłączanie wiadomości e-mail**<br>Możesz wyłączyć określone wiadomości e-mail, czyszcząc domyślne pole wyboru adresata i usuwając dodatkowe adresaci.  

    - **Ograniczanie wiadomości e-mail do określonych adresów e-mail**<br>Wiadomości e-mail wysyłane do adresatów domyślnych można wyłączyć, czyszcząc pole wyboru domyślnego adresata. Następnie można dodać dodatkowe adresy e-mail jako dodatkowych adresatów. Jeśli chcesz dodać więcej niż jeden adres e-mail, oddziel je średnikiem (;).

    - **Wysyłanie wiadomości e-mail zarówno do domyślnych adresatów, jak i do dodatkowych adresatów**<br>Wiadomości e-mail można wysyłać zarówno do adresata domyślnego, jak i do dodatkowego, zaznaczając pole wyboru domyślnego adresata i dodając adresy e-mail dla dodatkowych adresatów.

    - **Tylko krytyczne wiadomości e-mail**<br>Dla każdego typu wiadomości e-mail możesz zaznaczyć to pole wyboru, aby otrzymywać tylko krytyczne wiadomości e-mail. Oznacza to, że zarządzanie tożsamościami uprzywilejowanymi będzie nadal wysyłać wiadomości e-mail do skonfigurowanych adresatów tylko wtedy, gdy wiadomość e-mail wymaga natychmiastowej akcji. Na przykład wiadomości e-mail z prośbą o rozszerzenie przypisania roli nie zostaną wyzwolone, podczas gdy zostaną wyzwolone wiadomości e-mail wymagające od administratorów zatwierdzenia żądania rozszerzenia.

1. Wybierz przycisk **Aktualizuj** w dowolnym momencie, aby zaktualizować ustawienia roli.

## <a name="assignment-duration"></a>Czas trwania przydziału

Podczas konfigurowania ustawień roli można wybrać jedną z dwóch opcji czasu trwania przydziału dla każdego typu przydziału (kwalifikującego się i aktywnego). Te opcje stają się domyślnym maksymalnym czasem trwania, gdy użytkownik jest przypisany do roli w zarządzanie tożsamością uprzywilejowaną.

Możesz wybrać jedną z tych **kwalifikujących się** opcji czasu trwania przydziału:

| | |
| --- | --- |
| **Zezwalaj na stałe kwalifikujące się przydziały** | Administratorzy zasobów mogą przypisywać stałe kwalifikujące się przydziały. |
| **Wygaśnie kwalifikujący się przydział po** | Administratorzy zasobów mogą wymagać, aby wszystkie kwalifikujące się przydziały miały określoną datę rozpoczęcia i zakończenia. |

Możesz też wybrać jedną z tych **aktywnych** opcji czasu trwania przydziału:

| | |
| --- | --- |
| **Zezwalaj na stałe aktywne przypisanie** | Administratorzy zasobów mogą przypisywać stałe aktywne przypisanie. |
| **Wygaśnie aktywne przypisanie po** | Administratorzy zasobów mogą wymagać, aby wszystkie aktywne przydziały miały określoną datę rozpoczęcia i zakończenia. |

> [!NOTE]
> Administratorzy zasobów mogą odnawiać wszystkie przydziały z określoną datą zakończenia. Ponadto użytkownicy mogą inicjować żądania samoobsługi w celu [rozszerzenia lub odnowienia przydziałów ról](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

Zarządzanie tożsamościami uprzywilejowanymi zapewnia opcjonalne wymuszanie uwierzytelniania wieloskładnikowego platformy Azure dla dwóch różnych scenariuszy.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Wymagaj uwierzytelniania wieloskładnikowego przy aktywnym przypisaniu

W niektórych przypadkach można przypisać użytkownika lub grupę do roli na krótki czas (na przykład jeden dzień). W takim przypadku przypisani użytkownicy nie muszą żądać aktywacji. W tym scenariuszu zarządzanie tożsamością uprzywilejowaną nie można wymusić uwierzytelniania wieloskładnikowego, gdy użytkownik używa przypisania roli, ponieważ są już aktywne w roli od momentu, w którym jest przypisany.

Aby upewnić się, że administrator zasobów wypełniający przydział jest tym, kim się wydaje, możesz wymusić uwierzytelnianie wieloskładnikowe przy aktywnym przypisaniu, zaznaczając pole **Wymagaj uwierzytelniania wieloskładnikowego w aktywnym przypisaniu.**

### <a name="require-multi-factor-authentication-on-activation"></a>Wymagaj uwierzytelniania wieloskładnikowego podczas aktywacji

Możesz wymagać od użytkowników, którzy kwalifikują się do roli, aby udowodnić, kogo używają uwierzytelniania wieloskładnikowego platformy Azure, zanim będą mogli aktywować. Uwierzytelnianie wieloskładnikowe zapewnia, że użytkownik jest tym, kim mówią, że są z rozsądną pewnością. Wymuszanie tej opcji chroni krytyczne zasoby w sytuacjach, gdy konto użytkownika mogło zostać naruszone.

Aby wymagać uwierzytelniania wieloskładnikowego przed aktywacją, zaznacz pole **Wymagaj uwierzytelniania wieloskładnikowego w polu aktywacji.**

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie wieloskładnikowe i Zarządzanie tożsamościami uprzywilejowanymi](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maksymalny czas trwania aktywacji

Użyj **suwaka Maksymalny czas trwania aktywacji,** aby ustawić maksymalny czas w godzinach, przez który rola pozostaje aktywna przed jej wygaśnięciem. Ta wartość może wynosić od jednej do 24 godzin.

## <a name="require-justification"></a>Wymagaj uzasadnienia

Podczas aktywacji można wymagać, aby użytkownicy wprowadzali uzasadnienie biznesowe. Aby wymagać uzasadnienia, zaznacz pole **Wymagaj justowania w aktywnym przypisaniu** lub **Pole Wymagaj uzasadnienia w aktywacji.**

## <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia, aby aktywować

Jeśli chcesz wymagać zatwierdzenia, aby aktywować rolę, wykonaj następujące kroki.

1. Zaznacz pole wyboru **Wymagaj zatwierdzenia, aby aktywować.**

1. Wybierz **pozycję Wybierz osoby zatwierdzające,** aby otworzyć stronę Wybierz członka lub **grupę.**

    ![Wybieranie okienka użytkownika lub grupy w celu wybrania osób zatwierdzających](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Zaznacz co najmniej jednego użytkownika lub grupę, a następnie kliknij przycisk **Wybierz**. Można dodać dowolną kombinację użytkowników i grup. Musisz wybrać co najmniej jedną osobę zatwierdzającej. Nie ma żadnych domyślnych osób zatwierdzających.

    Wybrane osoby zatwierdzające pojawią się na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkich ustawień roli wybierz pozycję **Aktualizuj,** aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-assign-roles.md)
- [Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-configure-alerts.md)
