---
title: Konfigurowanie ustawień roli w usłudze Azure AD w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia roli usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cb8944578caf00fac5ca430e411f044a875f6af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60288718"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Konfigurowanie ustawień roli w usłudze Azure AD w usłudze PIM

Administrator ról uprzywilejowanych, można dostosować usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) w organizacji, w tym zmian środowiska dla użytkownika, który jest aktywacja kwalifikujące się przypisanie roli.

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia dla roli usługi Azure AD.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD**.

1. Kliknij pozycję **Ustawienia**.

    ![Role usługi Azure AD — ustawienia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Kliknij przycisk **role**.

1. Kliknij rolę, którego ustawienia chcesz skonfigurować.

    ![Role usługi Azure AD — ustawień ról](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na stronie Ustawienia dla każdej roli istnieje kilka ustawień, które można skonfigurować. Te ustawienia mają wpływ tylko na użytkowników, którzy są **kwalifikujących się** przypisania, nie **stałe** przypisania.

## <a name="activations"></a>Uaktywnienia

Użyj **aktywacji** suwak, aby ustawić maksymalny czas w godzinach, rola pozostaje aktywna, przed jego wygaśnięciem. Ta wartość może mieć wartość od 1 do 72 godzin.

## <a name="notifications"></a>Powiadomienia

Użyj **powiadomienia** przełącznika, aby określić, czy administratorzy będą otrzymywać powiadomienia e-mail uaktywnienie ról. Może to być przydatne w przypadku wykrycia nieautoryzowanego lub nielegalne aktywacji.

Po ustawieniu **Włącz**, powiadomienia są wysyłane do:

- Administrator uprawnionej roli
- Administrator zabezpieczeń
- Administrator globalny

Aby uzyskać więcej informacji, zobacz [wiadomości E-mail z powiadomieniami w usłudze PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Bilet zdarzenia/żądania

Użyj **bilet zdarzenia/żądania** przełącznika, aby określić, czy będą musieli kwalifikujących się Administratorzy mogą obejmować numer biletu podczas aktywacji ich ról. Może to być przydatne, gdy wykonujesz inspekcje dostępu do roli.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Użyj **uwierzytelnianie wieloskładnikowe** przełącznik, aby określić, czy wymagać od użytkowników zweryfikować swoją tożsamość za pomocą uwierzytelniania Wieloskładnikowego, zanim użytkownik może dokonać aktywacji ich ról. Mają tylko sprawdzić to raz dla sesji, nie za każdym razem, gdy ich aktywowania roli. Istnieją dwa wskazówki, aby mieć na uwadze, po włączeniu usługi MFA:

* Użytkownicy, którzy mają konta Microsoft dla swoich adresów e-mail (zazwyczaj @outlook.com, ale nie zawsze) nie można zarejestrować usługi Azure MFA. Jeśli chcesz przypisać role do użytkowników z kontami Microsoft, możesz zwiększyć ich administratorów trwałych lub wyłączanie usługi MFA dla tej roli.
* Nie można wyłączyć uwierzytelnianie wieloskładnikowe dla ról o wysokim poziomie uprawnień dla usługi Azure AD i Office 365. Jest to funkcja bezpieczeństwa, ponieważ te role powinny być dokładnie chronione:  
  
  * Administrator rozliczeń
  * Administrator aplikacji w chmurze
  * Administrator zgodności
  * Administrator dostępu warunkowego
  * Administrator usługi CRM
  * Osoba zatwierdzająca dostęp do skrytki klienta
  * Składniki zapisywania w katalogach
  * Administrator programu Exchange
  * Administrator globalny
  * Administrator usługi Information Protection
  * Administrator usługi Intune
  * Administrator usługi Power BI
  * Administrator uprawnionej roli
  * Administrator zabezpieczeń
  * Administrator usługi SharePoint
  * Administrator programu Skype dla firm
  * Administrator użytkowników

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie wieloskładnikowe (MFA) i PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Wymagaj zatwierdzenia

Jeśli ma być wymagane zatwierdzenie, aby aktywować rolę, wykonaj następujące kroki.

1. Ustaw **wymagają zatwierdzenia** przełączyć się do **włączone**. Okienka rozwija się z opcjami, aby wybrać osób zatwierdzających.

    ![Role usługi Azure AD - Settings - wymagają zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Jeśli użytkownik **nie** Określ wszystkie osoby zatwierdzające, Administratorzy ról uprzywilejowanych stają się osoby zatwierdzające domyślne. Administratorzy ról uprzywilejowanych, które będą wymagane do zatwierdzenia **wszystkich** żądań aktywacji dla tej roli.

1. Aby określić osób zatwierdzających, kliknij przycisk **wybierz osoby zatwierdzające**.

    ![Role usługi Azure AD - Settings - wymagają zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Wybierz jedną lub więcej osób zatwierdzających, a następnie kliknij przycisk **wybierz**. Możesz wybrać użytkowników lub grup. Zaleca się co najmniej 2 osób zatwierdzających. Samodzielne zatwierdzanie nie jest dozwolone.

    Wybrane opcje pojawią się na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkie Twoje ustawienia ról kliknij **Zapisz** Aby zapisać zmiany.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Kolejne kroki

- [Przypisz role usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD w usłudze PIM](pim-how-to-configure-security-alerts.md)
