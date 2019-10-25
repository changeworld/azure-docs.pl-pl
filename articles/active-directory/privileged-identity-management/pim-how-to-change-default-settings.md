---
title: Konfigurowanie ustawień roli usługi Azure AD w Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia roli usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cff298e24ac185767e6290e396818ccece7b9b55
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809152"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurowanie ustawień roli usługi Azure AD w Privileged Identity Management

Administrator ról uprzywilejowanych może dostosować Privileged Identity Management (PIM) w organizacji Azure Active Directory (Azure AD), w tym zmianę środowiska użytkownika, który uaktywnia kwalifikujące się przypisanie roli.

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia roli usługi Azure AD.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **Ustawienia**.

    ![Role usługi Azure AD — ustawienia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Kliknij pozycję **role**.

1. Kliknij rolę, której ustawienia chcesz skonfigurować.

    ![Role usługi Azure AD — role ustawień](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na stronie Ustawienia dla każdej roli istnieje kilka ustawień, które można skonfigurować. Te ustawienia mają wpływ tylko na użytkowników, którzy są **uprawnionymi** przypisaniami, a nie **trwałymi** .

## <a name="activations"></a>Aktywacji

Za pomocą suwaka **aktywacje** można ustawić maksymalny czas (w godzinach), przez który rola pozostaje aktywna przed wygaśnięciem. Ta wartość może należeć do przedziału od 1 do 72 godzin.

## <a name="notifications"></a>Powiadomienia

Użyj przełącznika **powiadomienia** , aby określić, czy administratorzy będą otrzymywać powiadomienia e-mail po aktywowaniu ról. To powiadomienie może być przydatne do wykrywania aktywacji nieautoryzowanych lub illegitimate.

Po wybraniu opcji **Włącz**powiadomienia są wysyłane do:

- Administrator ról uprzywilejowanych
- Administrator zabezpieczeń
- Administrator globalny

Aby uzyskać więcej informacji, zobacz [powiadomienia e-mail w Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Bilet zdarzenia/żądania

Użyj przełącznika **bilet zdarzeń/żądania** , aby wymagać od uprawnionych administratorów dołączenia numeru biletu podczas aktywowania ich roli. To rozwiązanie może zwiększyć efektywność inspekcji dostępu do roli.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Użyj przełącznika **Multi-Factor Authentication** , aby określić, czy użytkownicy muszą weryfikować swoją tożsamość za pomocą usługi MFA, zanim będą mogli aktywować swoje role. Muszą oni tylko weryfikować swoją tożsamość tylko raz na sesję, a nie za każdym razem, gdy aktywują rolę. Po włączeniu usługi MFA należy wziąć pod uwagę dwie wskazówki:

- Użytkownicy, którzy mają konta Microsoft dla swoich adresów e-mail (zwykle @outlook.com, ale nie zawsze), nie mogą zarejestrować się w usłudze Azure Multi-Factor Authentication. Jeśli chcesz przypisać role do użytkowników z kontami Microsoft, należy udostępnić im trwałych administratorów lub wyłączyć uwierzytelnianie wieloskładnikowe dla tej roli.
- Nie można wyłączyć usługi Azure Multi-Factor Authentication dla ról o wysokim poziomie uprawnień dla usług Azure AD i Office 365. Ta funkcja zabezpieczeń pomaga chronić następujące role:  
  
  - Azure Information Protection administrator
  - Administrator rozliczeń
  - Administrator aplikacji w chmurze
  - Administrator zgodności
  - Administrator dostępu warunkowego
  - Administrator systemu Dynamics 365
  - Osoba zatwierdzająca dostęp do skrytki klienta
  - Autorzy katalogów
  - Administrator programu Exchange
  - Administrator globalny
  - Administrator usługi Intune
  - Power BI administrator
  - Administrator ról uprzywilejowanych
  - Administrator zabezpieczeń
  - Administrator programu SharePoint
  - Administrator programu Skype dla firm
  - Administrator użytkowników

Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie wieloskładnikowe i Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Wymagaj zatwierdzenia

Jeśli chcesz delegować wymagane zatwierdzenie w celu aktywowania roli, wykonaj następujące czynności.

1. Ustaw przełącznik **Wymagaj zatwierdzenia** na **włączony**. Okienko rozwija się z opcjami wyboru osób zatwierdzających.

    ![Role usługi Azure AD — ustawienia — wymaga zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Jeśli nie określisz żadnych osób zatwierdzających, administrator ról uprzywilejowanych będzie domyślną osobą zatwierdzającą i jest wymagany do zatwierdzenia wszystkich żądań aktywacji dla tej roli.

1. Aby określić osoby zatwierdzające, kliknij przycisk **Wybierz osoby zatwierdzające**.

    ![Role usługi Azure AD — ustawienia — wymaga zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Wybierz co najmniej jedną osobę zatwierdzającą oprócz roli administrator ról uprzywilejowanych, a następnie kliknij przycisk **Wybierz**. Możesz wybrać użytkowników lub grupy. Zalecamy co najmniej dwie osoby zatwierdzające. Nawet jeśli dodasz Cię jako osobę zatwierdzającą, nie możesz samodzielnie zatwierdzać aktywacji roli. Wybrane opcje zostaną wyświetlone na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkich ustawień roli wybierz pozycję **Zapisz** , aby zapisać zmiany.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD w Privileged Identity Management](pim-how-to-configure-security-alerts.md)
