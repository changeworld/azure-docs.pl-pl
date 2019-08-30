---
title: Administratorzy zarządzają użytkownikami i urządzeniami — Azure MFA — Azure Active Directory
description: Jak Administratorzy mogą zmieniać ustawienia użytkownika, takie jak wymuszenie ponownego uruchomienia procesu weryfikacji przez użytkowników.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190d697dca56fa51d92987f32db0146aa79881aa
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162395"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Zarządzanie ustawieniami użytkownika przy użyciu usługi Azure Multi-Factor Authentication w chmurze

Jako administrator możesz zarządzać następującymi ustawieniami użytkownika i urządzenia:

* Wymagaj od użytkowników ponownego podania metod kontaktu
* Usuwanie haseł aplikacji
* Wymagaj uwierzytelniania wieloskładnikowego na wszystkich zaufanych urządzeniach

## <a name="manage-authentication-methods"></a>Zarządzanie metodami uwierzytelniania

Jako administrator z przypisaną rolą Administrator uwierzytelniania można wymagać od użytkowników zresetowania hasła, ponownego zarejestrowania się w usłudze MFA lub odwołania istniejących sesji usługi MFA z obiektu użytkownika.

![Zarządzanie metodami uwierzytelniania z poziomu Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.
1. Wybierz użytkownika, na którym chcesz wykonać akcję, i wybierz pozycję **metody uwierzytelniania**.
   - **Resetowanie hasła** spowoduje zresetowanie hasła użytkownika i przypisanie tymczasowego hasła, które należy zmienić przy następnym logowaniu.
   - **Wymagaj ponownej rejestracji usługi MFA** spowoduje to, że gdy użytkownik zaloguje się w następnym momencie, zostanie poproszony o skonfigurowanie nowej metody uwierzytelniania MFA.
   - **Odwoływanie sesji usługi MFA** czyści sesje usługi MFA zalogowanych przez użytkownika i wymaga ich do wykonania uwierzytelniania MFA przy następnym zażądaniu zasad na urządzeniu.

## <a name="require-users-to-provide-contact-methods-again"></a>Wymagaj od użytkowników ponownego podania metod kontaktu

To ustawienie wymusza ponowną próbę wykonania procesu rejestracji przez użytkownika. Aplikacje nie korzystające z przeglądarki nadal działają, jeśli użytkownik ma dla nich hasła aplikacji.  Hasła aplikacji użytkowników można usunąć, wybierając pozycję **Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Jak wymagać od użytkowników ponownego podania metod kontaktu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.
3. Po prawej stronie wybierz pozycję **Multi-Factor Authentication** na pasku narzędzi. Zostanie otwarta strona uwierzytelnianie wieloskładnikowe.
4. Zaznacz pole wyboru obok użytkownika lub użytkowników, którym chcesz zarządzać. Lista opcji szybkiego kroku pojawia się po prawej stronie.
5. Wybierz pozycję **Zarządzaj ustawieniami użytkownika**.
6. Zaznacz pole wyboru **Wymagaj, aby wybrani użytkownicy mogli ponownie podawać metody kontaktu**.
   ![Wymagaj od użytkowników ponownego podania metod kontaktu](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Kliknij pozycję **Zapisz**.
8. Kliknij przycisk **Zamknij**.

Organizacje mogą wykonać te kroki za pomocą programu PowerShell, używając następującego przewodnika, aby `StrongAuthenticationMethods` wyczyścić Ten atrybut:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Usuń użytkowników istniejące hasła aplikacji

To ustawienie usuwa wszystkie hasła aplikacji utworzone przez użytkownika. Aplikacje nie korzystające z przeglądarki, które zostały skojarzone z tymi hasłami aplikacji, przestają działać do momentu utworzenia nowego hasła aplikacji.

### <a name="how-to-delete-users-existing-app-passwords"></a>Jak usunąć użytkowników istniejące hasła aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.
3. Po prawej stronie wybierz pozycję **Multi-Factor Authentication** na pasku narzędzi. Zostanie otwarta strona uwierzytelnianie wieloskładnikowe.
4. Zaznacz pole wyboru obok użytkownika lub użytkowników, którym chcesz zarządzać. Lista opcji szybkiego kroku pojawia się po prawej stronie.
5. Wybierz pozycję **Zarządzaj ustawieniami użytkownika**.
6. Zaznacz pole wyboru **Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników**.
   ![Usuń wszystkie istniejące hasła aplikacji](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Kliknij pozycję **Zapisz**.
8. Kliknij przycisk **Zamknij**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Przywracanie usługi MFA na wszystkich zapamiętanych urządzeniach dla użytkownika

Jedną z konfigurowalnych funkcji usługi Azure Multi-Factor Authentication jest umożliwienie użytkownikom oznaczania urządzeń jako zaufanych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień usługi Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Użytkownicy mogą zrezygnować z weryfikacji dwuetapowej dla konfigurowalnej liczby dni na ich zwykłych urządzeniach. W przypadku naruszenia zabezpieczeń konta lub utraty zaufanego urządzenia należy mieć możliwość usunięcia zaufanego stanu i ponownego włączenia weryfikacji dwuetapowej.

Po zaznaczeniu tej opcji **Przywróć uwierzytelnianie wieloskładnikowe na wszystkich zapamiętanych urządzeniach** użytkownicy muszą przekonywać weryfikacji dwuetapowej przy następnym logowaniu, nawet jeśli oznaczył swoje urządzenie jako zaufane.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Jak przywrócić uwierzytelnianie wieloskładnikowe na wszystkich urządzeniach zawieszonych dla użytkownika

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.
3. Po prawej stronie wybierz pozycję **Multi-Factor Authentication** na pasku narzędzi. Zostanie otwarta strona uwierzytelnianie wieloskładnikowe.
4. Zaznacz pole wyboru obok użytkownika lub użytkowników, którym chcesz zarządzać. Lista opcji szybkiego kroku pojawia się po prawej stronie.
5. Wybierz pozycję **Zarządzaj ustawieniami użytkownika**.
6. Zaznacz pole wyboru **Przywróć uwierzytelnianie wieloskładnikowe na wszystkich zapamiętanych urządzeniach**
   ![przywracanie uwierzytelniania wieloskładnikowego na wszystkich zapamiętanych urządzeniach](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Kliknij pozycję **Zapisz**.
8. Kliknij przycisk **Zamknij**.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat sposobu [konfigurowania ustawień usługi Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Jeśli użytkownicy będą potrzebować pomocy, należy zapoznać się z [podręcznikiem użytkownika w celu weryfikacji](../user-help/multi-factor-authentication-end-user.md) dwuetapowej
