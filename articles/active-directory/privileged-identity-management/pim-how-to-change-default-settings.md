---
title: Konfigurowanie ustawień roli usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 05/31/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9252e3bb8ccddb810074b485f6f073f1bda3f05
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804437"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Konfigurowanie ustawień roli usługi Azure AD w usłudze PIM

Administrator ról uprzywilejowanych może dostosować Azure Active Directory (Azure AD) Privileged Identity Management (PIM) w swojej organizacji, w tym zmianę środowiska użytkownika, który uaktywnia kwalifikujące się przypisanie roli.

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

## <a name="activations"></a>Uaktywnienia

Za pomocą suwaka **aktywacje** można ustawić maksymalny czas (w godzinach), przez który rola pozostaje aktywna przed wygaśnięciem. Ta wartość może należeć do przedziału od 1 do 72 godzin.

## <a name="notifications"></a>Powiadomienia

Użyj przełącznika **powiadomienia** , aby określić, czy administratorzy będą otrzymywać powiadomienia e-mail po aktywowaniu ról. Może to być przydatne do wykrywania aktywacji nieautoryzowanych lub illegitimate.

Po wybraniu opcji **Włącz**powiadomienia są wysyłane do:

- Administrator uprawnionej roli
- Administrator zabezpieczeń
- Administrator globalny

Aby uzyskać więcej informacji, zobacz [powiadomienia e-mail w usłudze PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Bilet zdarzenia/żądania

Aby określić, czy chcesz, aby uprawnieni administratorzy musieli uwzględnić numer biletu podczas aktywowania swojej roli, należy użyć przełącznika **bilet zdarzeń/żądania** . Może to być przydatne w przypadku przeprowadzania inspekcji dostępu do roli.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Użyj przełącznika **Multi-Factor Authentication** , aby określić, czy użytkownicy muszą weryfikować swoją tożsamość za pomocą usługi MFA, zanim będą mogli aktywować swoje role. Muszą oni tylko weryfikować ten raz na sesję, a nie za każdym razem, gdy aktywują rolę. Po włączeniu usługi MFA należy wziąć pod uwagę dwie wskazówki:

* Użytkownicy, którzy mają konta Microsoft dla swoich adresów e-mail ( @outlook.comzazwyczaj ale nie zawsze), nie mogą rejestrować się w usłudze Azure MFA. Jeśli chcesz przypisać role do użytkowników z kontami Microsoft, należy udostępnić im trwałych administratorów lub wyłączyć uwierzytelnianie wieloskładnikowe dla tej roli.
* Nie można wyłączyć usługi MFA dla ról o wysokim poziomie uprawnień dla usług Azure AD i Office 365. Jest to funkcja zabezpieczeń, ponieważ należy uważnie chronić te role:  
  
  * Azure Information Protection administrator
  * Administrator rozliczeń
  * Administrator aplikacji w chmurze
  * Administrator zgodności
  * Administrator dostępu warunkowego
  * Administrator usługi CRM
  * Osoba zatwierdzająca dostęp do skrytki klienta
  * Składniki zapisywania w katalogach
  * Administrator programu Exchange
  * Administrator globalny
  * Administrator usługi Intune
  * Administrator usługi Power BI
  * Administrator uprawnionej roli
  * Administrator zabezpieczeń
  * Administrator usługi SharePoint
  * Administrator programu Skype dla firm
  * Administrator użytkowników

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie wieloskładnikowe (MFA) i program PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Wymagaj zatwierdzenia

Jeśli chcesz wymagać zatwierdzenia w celu aktywowania roli, wykonaj następujące czynności.

1. Ustaw przełącznik **Wymagaj zatwierdzenia** na **włączony**. Okienko rozwija się z opcjami wyboru osób zatwierdzających.

    ![Role usługi Azure AD — ustawienia — wymaga zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Jeśli **nie** określisz żadnych osób zatwierdzających, Administratorzy ról uprzywilejowanych staną się domyślnymi osobami zatwierdzającymi. Administratorzy ról uprzywilejowanych będą musieli zatwierdzić **wszystkie** żądania aktywacji dla tej roli.

1. Aby określić osoby zatwierdzające, kliknij przycisk **Wybierz osoby zatwierdzające**.

    ![Role usługi Azure AD — ustawienia — wymaga zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Wybierz co najmniej jedną osobę zatwierdzającą, a następnie kliknij przycisk **Wybierz**. Możesz wybrać użytkowników lub grupy. Zaleca się co najmniej 2 osób zatwierdzających. Samodzielne zatwierdzanie jest niedozwolone.

    Wybrane opcje zostaną wyświetlone na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkich ustawień roli kliknij pozycję **Zapisz** , aby zapisać zmiany.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD w usłudze PIM](pim-how-to-configure-security-alerts.md)
