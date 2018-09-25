---
title: Konfigurowanie ustawień roli w katalogu usługi Azure AD w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia ról katalogu usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 09/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 77a57afda3859de4a246c15d3b5c0b4ba8e2a8c8
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039005"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>Konfigurowanie ustawień roli w katalogu usługi Azure AD w usłudze PIM

Administrator ról uprzywilejowanych, można dostosować usługi Azure AD Privileged Identity Management (PIM) w organizacji, w tym zmian środowiska dla użytkownika, który jest aktywacja kwalifikujące się przypisanie roli.

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia dla ról katalogu usługi Azure AD.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról katalogu usługi Azure AD**.

1. Kliknij przycisk **ustawienia**.

    ![Role katalogu usługi AD systemu Azure — ustawienia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Kliknij przycisk **role**.

1. Kliknij rolę, którego ustawienia chcesz skonfigurować.

    ![Role katalogu usługi AD systemu Azure — ustawień ról](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na stronie Ustawienia dla każdej roli istnieje kilka ustawień, które można skonfigurować. Te ustawienia mają wpływ tylko na użytkowników, którzy są **kwalifikujących się** przypisania, nie **stałe** przypisania.

## <a name="activations"></a>Uaktywnienia

Użyj **aktywacji** suwak, aby ustawić maksymalny czas w godzinach, rola pozostaje aktywna, przed jego wygaśnięciem. Ta wartość może mieć wartość od 1 do 72 godzin.

## <a name="notifications"></a>Powiadomienia

Użyj **powiadomienia** przełącznika, aby określić, czy system wysyła wiadomości e-mail do administratorów potwierdzenie ich uaktywniono rolę. Może to być przydatne w przypadku wykrycia nieautoryzowanego lub nielegalne aktywacji.

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
  * Zapisywanie katalogów
  * Administrator programu Exchange
  * Administrator globalny
  * Administrator usługi Information Protection
  * Administrator usługi Intune
  * Administrator usługi Power BI
  * Administrator ról uprzywilejowanych
  * Administrator zabezpieczeń
  * Administrator usługi programu SharePoint
  * Administrator programu Skype dla firm
  * Administrator użytkowników

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie wieloskładnikowe (MFA) i PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Wymagaj zatwierdzenia

Jeśli ma być wymagane zatwierdzenie, aby aktywować rolę, wykonaj następujące kroki.

1. Ustaw **wymagają zatwierdzenia** przełączyć się do **włączone**. Okienka rozwija się z opcjami, aby wybrać osób zatwierdzających.

    ![Role katalogu usługi Azure AD - Settings - wymagają zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Jeśli użytkownik **nie** Określ wszystkie osoby zatwierdzające, Administratorzy ról uprzywilejowanych stają się osoby zatwierdzające domyślne. Administratorzy ról uprzywilejowanych, które będą wymagane do zatwierdzenia **wszystkich** żądań aktywacji dla tej roli.

1. Aby określić osób zatwierdzających, kliknij przycisk **wybierz osoby zatwierdzające**.

    ![Role katalogu usługi Azure AD - Settings - wymagają zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Wybierz jedną lub więcej osób zatwierdzających, a następnie kliknij przycisk **wybierz**. Możesz wybrać użytkowników lub grup. Zaleca się co najmniej 2 osób zatwierdzających. Samodzielne zatwierdzanie nie jest dozwolone.

    Wybrane opcje pojawią się na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkie Twoje ustawienia ról kliknij **Zapisz** Aby zapisać zmiany.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Kolejne kroki

- [Przypisywanie ról katalogu usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Konfigurowanie alertów zabezpieczeń dla ról katalogu usługi Azure AD w usłudze PIM](pim-how-to-configure-security-alerts.md)
