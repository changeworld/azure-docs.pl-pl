---
title: Zarządzanie użytkownikami i urządzeniami usługi Azure MFA — usługa Azure Active Directory
description: Jak administratorzy mogą zmienić ustawienia użytkownika, takie jak zmuszanie użytkowników do ponownego wykonania procesu sprawdzania.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07845bb5b742b1bcfbb22d260457e9a8e16edab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263714"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Manage user settings with Azure Multi-Factor Authentication in the cloud (Zarządzanie ustawieniami użytkownika przy użyciu usługi Azure Multi-Factor Authentication w chmurze)

Jako administrator możesz zarządzać następującymi ustawieniami użytkownika i urządzenia:

* Wymaganie od użytkowników ponownego podania metod kontaktu
* Usuwanie haseł aplikacji
* Wymagaj usługi MFA na wszystkich zaufanych urządzeniach

## <a name="manage-authentication-methods"></a>Zarządzanie metodami uwierzytelniania

Jako administrator przypisany rolę Administrator uwierzytelniania można wymagać od użytkowników, aby zresetować swoje hasło, ponownie zarejestrować się dla usługi MFA lub odwołać istniejące sesje usługi MFA z ich obiektu użytkownika.

![Zarządzanie metodami uwierzytelniania z witryny Azure portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Po lewej stronie wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.
1. Wybierz użytkownika, na którym chcesz wykonać akcję, i wybierz **pozycję Metody uwierzytelniania**.
   - **Resetuj hasło** zresetuje hasło użytkownika i przypisze tymczasowe hasło, które musi zostać zmienione przy następnym loguch.
   - **Wymagaj ponownego rejestrowania usługi MFA** spowoduje, że gdy użytkownik zaloguje się następnym razem, zostaną one poproszone o skonfigurowanie nowej metody uwierzytelniania usługi MFA.
   - **Odwołaj sesje usługi MFA** czyści zapamiętane sesje usługi MFA użytkownika i wymaga od nich wykonywania usługi MFA następnym razem, gdy jest to wymagane przez zasady na urządzeniu.

## <a name="delete-users-existing-app-passwords"></a>Usuwanie istniejących haseł aplikacji przez użytkowników

To ustawienie powoduje usunięcie wszystkich haseł aplikacji utworzonych przez użytkownika. Aplikacje inne niż przeglądarki, które były skojarzone z tymi hasłami aplikacji, przestają działać do momentu utworzenia nowego hasła aplikacji. Uprawnienia administratora globalnego są wymagane do wykonania tej akcji.

### <a name="how-to-delete-users-existing-app-passwords"></a>Jak usunąć użytkowników istniejących haseł aplikacji

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.
3. Po prawej stronie wybierz pozycję **Uwierzytelnianie wieloskładnikowe** na pasku narzędzi. Zostanie otwarta strona uwierzytelniania wieloskładnikowego.
4. Zaznacz pole obok użytkownika lub użytkowników, którymi chcesz zarządzać. Po prawej stronie pojawi się lista opcji szybkiego kroku.
5. Wybierz pozycję **Zarządzaj ustawieniami użytkownika**.
6. Zaznacz pole wyboru **Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników**.
   ![Usuwanie wszystkich istniejących haseł aplikacji](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Kliknij **przycisk Zapisz**.
8. Kliknij **przycisk Zamknij**.

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat [konfigurowania ustawień uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-mfasettings.md)
- Jeśli użytkownicy potrzebują pomocy, skieruj ich w [stronę podręcznika użytkownika w celu weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user.md)
