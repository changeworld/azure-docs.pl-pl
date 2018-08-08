---
title: Jak zarządzać ustawień aktywacji roli | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić ustawienia domyślne uprzywilejowanymi tożsamościami przy użyciu rozszerzenia usługi Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 4ca74c001ba379b287c0c9799d90336eb187b2c2
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619604"
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Jak zarządzać ustawień aktywacji roli w usłudze Azure AD Privileged Identity Management
Administrator ról uprzywilejowanych, można dostosować usługi Azure AD Privileged Identity Management (PIM) w organizacji, w tym zmian środowiska dla użytkownika, który jest aktywacja kwalifikujące się przypisanie roli.

## <a name="manage-the-role-activation-settings"></a>Zarządzanie ustawieniami aktywacji roli
1. Przejdź do [witryny Azure portal](https://portal.azure.com) i wybierz **usługi Azure AD Privileged Identity Management** aplikacji z poziomu pulpitu nawigacyjnego.
2. Wybierz **Zarządzanie rolami uprzywilejowanymi** > **ustawienia** > **ról uprzywilejowanych**.
3. Wybierz rolę, którego ustawienia chcesz zarządzać.

Na stronie Ustawienia dla każdej roli istnieje wiele ustawień, które można skonfigurować. Te ustawienia dotyczą tylko użytkowników, którzy są administratorów uprawnionych administratorów nie trwałych.

**Aktywacje**: czasu w godzinach, które rola pozostaje aktywna, przed jego wygaśnięciem. Może to być z zakresu od 1 do 72 godzin.

**Powiadomienia**: Możesz wybrać, informację określającą, czy system wysyła wiadomości e-mail do administratorów potwierdzenie ich uaktywniono rolę. Może to być przydatne w przypadku wykrycia nieautoryzowanego lub nielegalne aktywacji.

**Bilet zdarzenia/żądania**: można wybrać, czy nie będą musieli uprawnionymi administratorami, aby dołączyć numer biletu podczas aktywacji ich ról. Może to być przydatne, gdy wykonujesz inspekcje dostępu do roli.

**Uwierzytelnianie wieloskładnikowe**: można wybrać, czy wymagać od użytkowników zweryfikować swoją tożsamość za pomocą uwierzytelniania Wieloskładnikowego, zanim użytkownik może dokonać aktywacji ich ról. Mają tylko sprawdzić to raz dla sesji, nie za każdym razem, gdy ich aktywowania roli. Istnieją dwa wskazówki, aby mieć na uwadze, po włączeniu usługi MFA:

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

Aby uzyskać więcej informacji o używaniu MFA za pomocą usługi PIM zobacz [jak wymagać uwierzytelniania Wieloskładnikowego](pim-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

