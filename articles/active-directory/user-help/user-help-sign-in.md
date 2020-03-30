---
title: Logowanie się przy użyciu informacji o uwierzytelnianiu tożsamości — usługa Azure AD
description: Dowiedz się, jak zalogować się przy użyciu różnych metod weryfikacji tożsamości w informacjach zabezpieczających.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 7d1d27cede6c593150d3937bb3aa0ca59394eea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062170"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Zaloguj się przy użyciu weryfikacji dwuetapowej lub informacji zabezpieczających

Po skonfigurowaniu weryfikacji dwuetapowej lub informacji zabezpieczających możesz zalogować się na swoje konto przy użyciu określonej metody weryfikacji.

> [!Note]
> Jeśli nadal korzystasz z weryfikacji dwuetapowej, musisz skonfigurować metody weryfikacji, postępując zgodnie z instrukcjami zawartymi w artykule [Konfigurowanie mojego konta dla weryfikacji dwuetapowej.](multi-factor-authentication-end-user-first-time.md)
>
> Jeśli administrator włączył informacje zabezpieczające, musisz ustawić metody weryfikacji przy użyciu następujących artykułów krok po kroku:<ul><li>[Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniajnej](security-info-setup-auth-app.md)</li><li>[Set up security info to use text messaging](security-info-setup-text-msg.md) (Konfigurowanie informacji zabezpieczających w celu korzystania z wiadomości tekstowych)</li><li>[Konfigurowanie informacji zabezpieczających do korzystania z połączenia telefonicznego](security-info-setup-phone-number.md)</li><li>[Konfigurowanie informacji zabezpieczających do używania klucza zabezpieczeń](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Logowanie się przy użyciu powiadomienia o aplikacji uwierzytelniającego na urządzeniu przenośnym

1. Zaloguj się na swoje konto za pomocą nazwy użytkownika i hasła.

2. Wybierz **zatwierdź** z powiadomienia o zatwierdzeniu wysłanego na urządzenie przenośne.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Logowanie się przy użyciu kodu aplikacji uwierzytelniającego na urządzeniu przenośnym

1. Zaloguj się na swoje konto za pomocą nazwy użytkownika i hasła.

2. Otwórz aplikację uwierzytelniacza i wpisz losowo wygenerowany kod swojego konta w polu **Wprowadź kod.**

## <a name="sign-in-using-your-phone-number"></a>Zaloguj się przy użyciu swojego numeru telefonu

1. Zaloguj się na swoje konto za pomocą nazwy użytkownika i hasła.

2. Odbierz telefon i postępuj zgodnie z instrukcjami.

## <a name="sign-in-using-a-text-message"></a>Logowanie się przy użyciu wiadomości tekstowej

1. Zaloguj się na swoje konto za pomocą nazwy użytkownika i hasła.

2. Otwórz wiadomość tekstową i wpisz kod z wiadomości tekstowej w polu **Wprowadź kod.**

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Logowanie się przy użyciu klucza zabezpieczeń na ekranie blokady

1. Po zarejestrowaniu klucza zabezpieczeń wybierz obraz klucza zabezpieczeń z ekranu blokady systemu Windows 10.

2. Włóż klucz zabezpieczeń do portu USB urządzenia i zaloguj się do systemu Windows przy użyciu numeru PIN klucza zabezpieczeń.

    ![Logowanie klucza zabezpieczeń na ekranie blokady systemu Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Logowanie się przy użyciu klucza zabezpieczeń i przeglądarki Microsoft Edge

1. Po zarejestrowaniu klucza zabezpieczeń otwórz przeglądarkę Microsoft Edge.

2. Po wyświetleniu monitu o zalogowanie włóż klucz zabezpieczeń do portu USB urządzenia i zaloguj się do systemu Windows przy użyciu numeru PIN klucza zabezpieczeń.

    ![Logowanie klucza zabezpieczeń za pomocą przeglądarki Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Aby uzyskać informacje dotyczące logowania się przy użyciu aplikacji Microsoft Authenticator, zobacz artykuł [Zaloguj się do kont za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Zaloguj się przy użyciu innej metody weryfikacji

Jeśli z jakiegoś powodu nie możesz użyć podstawowej metody logowania, możesz użyć innej wcześniej skonfigurowaną metodę weryfikacji.

1. Zaloguj się normalnie na swoje konto, a następnie wybierz link **Zaloguj się w inny sposób** na stronie Weryfikacji **dwuetapowej.**

    ![Zmienianie metody weryfikacji logowania](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Jeśli nie widzisz linku **Zaloguj w inny sposób,** oznacza to, że nie skonfigurowałeś żadnych innych metod weryfikacji i musisz skontaktować się z administratorem, aby uzyskać pomoc w zalogowaniu się na konto. Gdy administrator pomoże Ci się zalogować, upewnij się, że dodasz dodatkowe metody weryfikacji. Aby uzyskać więcej informacji na temat dodawania metod weryfikacji, zobacz artykuł [Zarządzanie ustawieniami weryfikacji dwuetapowej.](multi-factor-authentication-end-user-manage-settings.md)
    >
    >Jeśli widzisz link **Zaloguj w inny sposób,** ale nadal nie widzisz żadnych innych metod weryfikacji, skontaktuj się z administratorem, aby uzyskać pomoc w zalogowaniu się na konto.

2. Wybierz alternatywną metodę weryfikacji i kontynuuj proces weryfikacji dwuetapowej.

3. Po powrocie na konto możesz zaktualizować metody weryfikacji (jeśli to konieczne). Aby uzyskać więcej informacji na temat dodawania lub zmieniania metod, zobacz artykuł [Zarządzanie ustawieniami weryfikacji dwuetapowej.](multi-factor-authentication-end-user-manage-settings.md)

## <a name="next-steps"></a>Następne kroki

- Informacje o informacjach zabezpieczających w [artykule Omówienie informacji o zabezpieczeniach (wersja zapoznawcza).](user-help-security-info-overview.md)

- Dowiedz się więcej o weryfikacji dwuetapowej w [artykule Omówienie weryfikacji dwuetapowej.](user-help-two-step-verification-overview.md)

- Resetowanie hasła w przypadku jego utraty lub zapomnienia w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/)

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
