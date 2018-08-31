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
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 20a704a0d5b61134a61b5cbf02a1c71dbc7039e1
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189341"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>Konfigurowanie ustawień roli w katalogu usługi Azure AD w usłudze PIM

Administrator ról uprzywilejowanych, można dostosować usługi Azure AD Privileged Identity Management (PIM) w organizacji, w tym zmian środowiska dla użytkownika, który jest aktywacja kwalifikujące się przypisanie roli.

## <a name="open-role-settings"></a>Otwórz ustawienia roli

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról katalogu usługi Azure AD**.

1. Kliknij przycisk **ustawienia**.

    ![Role katalogu usługi AD systemu Azure — ustawienia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Kliknij przycisk **role**.

1. Kliknij rolę, którego ustawienia chcesz skonfigurować.

    ![Role katalogu usługi AD systemu Azure — ustawień ról](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na stronie Ustawienia dla każdej roli istnieje kilka ustawień, które można skonfigurować. Te ustawienia mają wpływ tylko na użytkowników, którzy są **kwalifikujących się** przypisania, nie **stałe** przypisania.

## <a name="activations"></a>Uaktywnienia

**Aktywacji** suwak jest maksymalny czas w godzinach, które rola pozostaje aktywna, przed jego wygaśnięciem. Ta wartość może mieć wartość od 1 do 72 godzin.

## <a name="notifications"></a>Powiadomienia

**Powiadomienia** przełącznik umożliwia wybranie, czy system wysyła wiadomości e-mail do administratorów potwierdzenie ich uaktywniono rolę. Może to być przydatne w przypadku wykrycia nieautoryzowanego lub nielegalne aktywacji.

## <a name="incidentrequest-ticket"></a>Bilet zdarzenia/żądania

**Bilet zdarzenia/żądania** przełącznik umożliwia wybranie, czy należy wymagać kwalifikujących się Administratorzy mogą obejmować numer biletu podczas aktywacji ich ról. Może to być przydatne, gdy wykonujesz inspekcje dostępu do roli.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

**Uwierzytelnianie wieloskładnikowe** przełącznik umożliwia wybranie, czy wymagać od użytkowników zweryfikować swoją tożsamość za pomocą uwierzytelniania Wieloskładnikowego, zanim użytkownik może dokonać aktywacji ich ról. Mają tylko sprawdzić to raz dla sesji, nie za każdym razem, gdy ich aktywowania roli. Istnieją dwa wskazówki, aby mieć na uwadze, po włączeniu usługi MFA:

* Użytkownicy, którzy mają konta Microsoft dla swoich adresów e-mail (zazwyczaj @outlook.com, ale nie zawsze) nie można zarejestrować usługi Azure MFA. Jeśli chcesz przypisać role do użytkowników z kontami Microsoft, możesz zwiększyć ich administratorów trwałych lub wyłączanie usługi MFA dla tej roli.
* Nie można wyłączyć uwierzytelnianie wieloskładnikowe dla ról o wysokim poziomie uprawnień dla usługi Azure AD i Office 365. Jest to funkcja bezpieczeństwa, ponieważ te role powinny być dokładnie chronione:  
  
  * Administrator aplikacji
  * Administrator serwera Proxy aplikacji
  * Administrator rozliczeń  
  * Administrator do spraw zgodności  
  * Administrator usługi CRM
  * Osoba zatwierdzająca dostęp do skrytki klienta
  * Składnik zapisywania katalogu  
  * Administrator programu Exchange  
  * Administrator globalny
  * Administrator usługi Intune
  * Administrator skrzynki pocztowej  
  * Pomoc techniczna dla partnerów (warstwa 1)  
  * Pomoc techniczna dla partnerów (warstwa 2)  
  * Administrator roli uprzywilejowanej
  * Administrator zabezpieczeń  
  * Administrator programu SharePoint  
  * Administrator programu Skype dla firm  
  * Administrator kont użytkowników  

Aby uzyskać więcej informacji o używaniu MFA za pomocą usługi PIM, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla ról katalogu usługi Azure AD w usłudze PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Wymagaj zatwierdzenia

**Wymagają zatwierdzenia** przełącznik umożliwia wybranie, czy wymaga zatwierdzenia do aktywowania tej roli.

1. Po ustawieniu przełącznika na **włączone**, okienka rozwija się z opcjami, aby wybrać osób zatwierdzających.

    ![Role katalogu usługi Azure AD - Settings - wymagają zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Jeśli użytkownik **nie** Określ wszystkie osoby zatwierdzające, Administratorzy ról uprzywilejowanych stają się osoby zatwierdzające domyślne. Administratorzy ról uprzywilejowanych, które będą wymagane do zatwierdzenia **wszystkich** żądań aktywacji dla tej roli.

1. Aby określić osób zatwierdzających, kliknij przycisk **wybierz osoby zatwierdzające**.

    ![Role katalogu usługi Azure AD - Settings - wymagają zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Wybierz jedną lub więcej osób zatwierdzających, a następnie kliknij przycisk **wybierz**. Możesz wybrać użytkowników lub grup. Zaleca się co najmniej 2 osób zatwierdzających. Samodzielne zatwierdzanie nie jest dozwolone.

    Wybrane opcje pojawią się na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkie Twoje ustawienia ról kliknij **Zapisz** Aby zapisać zmiany.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Kolejne kroki

- [Wymagaj uwierzytelniania wieloskładnikowego dla ról katalogu usługi Azure AD w usłudze PIM](pim-how-to-require-mfa.md)
- [Konfigurowanie alertów zabezpieczeń dla ról katalogu usługi Azure AD w usłudze PIM](pim-how-to-configure-security-alerts.md)
