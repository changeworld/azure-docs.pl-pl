---
title: Jak zarządzać ustawienia aktywacji dla roli | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić ustawienia domyślne dla uprzywilejowanymi tożsamościami przy rozszerzenia usługi Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: 972fd1e322e578516073307d01548132473bc52c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Jak zarządzać ustawienia roli aktywacji w usłudze Azure AD Privileged Identity Management
Administrator ról uprzywilejowanych można dostosować w usłudze Azure AD Privileged Identity Management (PIM) w organizacji, łącznie ze zmianą środowisko dla użytkownika, który jest aktywowanie przypisania roli kwalifikujących się.

## <a name="manage-the-role-activation-settings"></a>Zarządzanie ustawieniami aktywacji roli
1. Przejdź do [portalu Azure](https://portal.azure.com) i wybierz **Azure AD Privileged Identity Management** aplikacji z poziomu pulpitu nawigacyjnego.
2. Wybierz **Zarządzanie ról uprzywilejowanych** > **ustawienia** > **ról uprzywilejowanych**.
3. Wybierz rolę, którego ustawienia chcesz zarządzać.

Na stronie Ustawienia dla każdej roli istnieje wiele ustawień, które można skonfigurować. Te ustawienia dotyczą tylko użytkownicy, którzy są kwalifikujących się Administratorzy, Administratorzy nie trwałych.

**Aktywacje**: czas, w godzinach, które roli pozostaje aktywne, przed jego wygaśnięciem. Może to być w przedziale od 1 do 72 godzin.

**Powiadomienia**: można wybrać, czy system wysyła wiadomości e-mail do administratorów potwierdzenie ich uaktywniono rolę. Może to być przydatne do wykrywania nieautoryzowanego lub nielegalne aktywacji.

**Zdarzenie/żądania biletu**: można wybrać, czy należy wymagać Administratorzy kwalifikujących się do dołączenia numer biletu, gdy aktywują ich roli. Może to być przydatne podczas wykonywania inspekcji dostępu do roli.

**Uwierzytelnianie wieloskładnikowe**: można wybrać, czy należy wymagać od użytkowników zweryfikować swoją tożsamość za pomocą usługi MFA aktywować ich ról. Mają można zweryfikować tego raz dla sesji, nie za każdym razem, gdy ich aktywowania roli. Istnieją dwa porady należy wziąć pod uwagę podczas włączania uwierzytelniania Wieloskładnikowego:

* Użytkownicy, którzy mają konta Microsoft do ich adresów e-mail (zazwyczaj @outlook.com, ale nie zawsze) nie można zarejestrować w usłudze Azure MFA. Jeśli chcesz przypisać role do użytkowników z kontami Microsoft, możesz były administratorów trwałych lub Wyłącz uwierzytelnianie wieloskładnikowe dla tej roli.
* Nie można wyłączyć usługi MFA dla wysoko uprzywilejowane ról dla usługi Azure AD i usługi Office 365. Jest to zabezpieczenie, ponieważ te role powinny być starannie chronione:  
  
  * Administrator aplikacji
  * Administrator serwera Proxy aplikacji
  * Administrator rozliczeń  
  * Administrator do spraw zgodności  
  * Administrator programu CRM usługi
  * Osoba zatwierdzająca dostępu skrytki klienta
  * Składnik zapisywania katalogu  
  * Administrator programu Exchange  
  * Administrator globalny
  * Administrator usługi Intune
  * Skrzynki pocztowej administratora  
  * Pomoc techniczna dla partnerów (warstwa 1)  
  * Pomoc techniczna dla partnerów (warstwa 2)  
  * Administrator roli uprzywilejowanej   
  * Administrator zabezpieczeń  
  * Administrator programu SharePoint  
  * Administrator programu Skype dla firm  
  * Administrator konta użytkownika  

Aby uzyskać więcej informacji na temat przy użyciu usługi MFA w usłudze PIM zobacz [sposobu wymagać uwierzytelniania MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

