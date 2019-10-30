---
title: Administratorzy zarządzają użytkownikami i urządzeniami — Azure MFA — Azure Active Directory
description: Jak Administratorzy mogą zmieniać ustawienia użytkownika, takie jak wymuszenie ponownego uruchomienia procesu weryfikacji przez użytkowników.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 808faaed76ff63d69feb8170eaac72021c7bd49d
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73042119"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Zarządzanie ustawieniami użytkownika przy użyciu usługi Azure Multi-Factor Authentication w chmurze

Jako administrator możesz zarządzać następującymi ustawieniami użytkownika i urządzenia:

* Wymagaj od użytkowników ponownego podania metod kontaktu
* Usuwanie haseł aplikacji
* Wymagaj uwierzytelniania wieloskładnikowego na wszystkich zaufanych urządzeniach

## <a name="manage-authentication-methods"></a>Zarządzanie metodami uwierzytelniania

Jako administrator z przypisaną rolą Administrator uwierzytelniania można wymagać od użytkowników zresetowania hasła, ponownego zarejestrowania się w usłudze MFA lub odwołania istniejących sesji usługi MFA z obiektu użytkownika.

![Zarządzanie metodami uwierzytelniania z poziomu Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie wybierz pozycję **Azure Active Directory**  > **Użytkownicy**  > **Wszyscy użytkownicy**.
1. Wybierz użytkownika, na którym chcesz wykonać akcję, i wybierz pozycję **metody uwierzytelniania**.
   - **Resetowanie hasła** spowoduje zresetowanie hasła użytkownika i przypisanie tymczasowego hasła, które należy zmienić przy następnym logowaniu.
   - **Wymagaj ponownej rejestracji usługi MFA** spowoduje to, że gdy użytkownik zaloguje się w następnym momencie, zostanie poproszony o skonfigurowanie nowej metody uwierzytelniania MFA.
   - **Odwoływanie sesji usługi MFA** czyści sesje usługi MFA zalogowanych przez użytkownika i wymaga ich do wykonania uwierzytelniania MFA przy następnym zażądaniu zasad na urządzeniu.

## <a name="delete-users-existing-app-passwords"></a>Usuń użytkowników istniejące hasła aplikacji

To ustawienie usuwa wszystkie hasła aplikacji utworzone przez użytkownika. Aplikacje nie korzystające z przeglądarki, które zostały skojarzone z tymi hasłami aplikacji, przestają działać do momentu utworzenia nowego hasła aplikacji. Aby wykonać tę akcję, wymagane są uprawnienia administratora globalnego.

### <a name="how-to-delete-users-existing-app-passwords"></a>Jak usunąć użytkowników istniejące hasła aplikacji

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory**  > **Użytkownicy**  > **Wszyscy użytkownicy**.
3. Po prawej stronie wybierz pozycję **Multi-Factor Authentication** na pasku narzędzi. Zostanie otwarta strona uwierzytelnianie wieloskładnikowe.
4. Zaznacz pole wyboru obok użytkownika lub użytkowników, którym chcesz zarządzać. Lista opcji szybkiego kroku pojawia się po prawej stronie.
5. Wybierz pozycję **Zarządzaj ustawieniami użytkownika**.
6. Zaznacz pole wyboru **Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników**.
   ![usunąć wszystkie istniejące hasła aplikacji](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Kliknij pozycję **Zapisz**.
8. Kliknij przycisk **Zamknij**.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat sposobu [konfigurowania ustawień usługi Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Jeśli użytkownicy będą potrzebować pomocy, należy zapoznać się z [podręcznikiem użytkownika w celu weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user.md)
