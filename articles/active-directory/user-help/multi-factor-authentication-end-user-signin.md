---
title: Logowanie przy użyciu uwierzytelniania przy użyciu konta służbowego — usługa Azure AD
description: Dowiedz się, jak zalogować się na konto służbowe przy użyciu różnych metod weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 33cf9e284d2206ea497af7a5da7c3cf4a890cc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064091"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Zaloguj się na konto służbowe przy użyciu metody weryfikacji dwuskładnikowej

> [!NOTE]
> Celem tego artykułu jest przejście przez typowe doświadczenie logowania. Aby uzyskać pomoc dotyczącą logowania się lub rozwiązywania problemów, zobacz [Problemy z uwierzytelnianiem wieloskładnikowym platformy Azure](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Jakie będzie Twoje doświadczenie logowania?
Środowisko logowania różni się w zależności od tego, czego chcesz użyć jako drugiego czynnika: połączenia telefonicznego, aplikacji uwierzytelniającym lub wiadomości SMS. Wybierz opcję, która najlepiej opisuje to, co robisz:

| Jak się logujesz? |
| --- |
| [Połączenie telefoniczne z telefonem komórkowym lub biurowym](#signing-in-with-a-phone-call) |
| [Z tekstem na mój telefon komórkowy](#signing-in-with-a-text-message)
| [Powiadomienia z aplikacji Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Z kodami weryfikacyjnymi z aplikacji Microsoft Authenticator |
| [Z alternatywną metodą, ponieważ nie mogę teraz użyć preferowanej metody](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logowanie się za pomocą połączenia telefonicznego
Poniżej opisano proces weryfikacji dwuetapowej podczas połączenia z telefonem komórkowym lub biurowym.

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365, używając nazwy użytkownika i hasła.  
2. Microsoft dzwoni do Ciebie.  
3. Odbierz telefon i naciśnij klawisz #.  

## <a name="signing-in-with-a-text-message"></a>Logowanie się za pomocą wiadomości tekstowej
Poniżej opisano proces weryfikacji dwuetapowej z wiadomością tekstową na telefon komórkowy:

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365, używając nazwy użytkownika i hasła.
2. Firma Microsoft wysyła wiadomość tekstową zawierającą kod numeru.
3. Wprowadź kod w polu podanym na stronie logowania.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logowanie się za pomocą aplikacji Microsoft Authenticator
W poniższych informacjach opisano środowisko korzystania z aplikacji Microsoft Authenticator do weryfikacji dwuetapowych. Istnieją dwa różne sposoby korzystania z aplikacji. Możesz otrzymywać powiadomienia push na urządzeniu lub możesz otworzyć aplikację, aby uzyskać kod weryfikacyjny.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Aby zalogować się za pomocą powiadomienia z aplikacji Microsoft Authenticator
1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365, używając nazwy użytkownika i hasła.
2. Firma Microsoft wysyła powiadomienie do aplikacji Microsoft Authenticator na urządzeniu użytkownika.

   ![Microsoft wysyła powiadomienie](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Otwórz powiadomienie w telefonie i wybierz klawisz **Zweryfikuj.** Jeśli twoja firma wymaga kodu PIN, wprowadź go tutaj.
4. Teraz powinieneś się zalogować.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Aby zalogować się przy użyciu kodu weryfikacyjnego za pomocą aplikacji Microsoft Authenticator

Jeśli używasz aplikacji Microsoft Authenticator do uzyskania kodów weryfikacyjnych, po otwarciu aplikacji zobaczysz numer pod nazwą konta. Liczba ta zmienia się co 30 sekund, dzięki czemu nie używasz tego samego numeru dwa razy. Gdy pojawi się prośba o podanie kodu weryfikacyjnego, otwórz aplikację i użyj dowolnego numeru, który jest aktualnie wyświetlany.

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365, używając nazwy użytkownika i hasła.
2. Firma Microsoft monituje o podanie kodu weryfikacyjnego.

   ![Wprowadź kod weryfikacyjny](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Otwórz aplikację Microsoft Authenticator w telefonie i wprowadź kod w polu, w którym się logujesz.

## <a name="signing-in-with-an-alternate-method"></a>Logowanie przy za pomocą alternatywnej metody
Czasami nie masz telefonu lub urządzenia skonfigurowane jako preferowana metoda weryfikacji. W takiej sytuacji zaleca się skonfigurowanie metod tworzenia kopii zapasowych dla konta. W poniższej sekcji pokazano, jak zalogować się przy za pomocą metody alternatywnej, gdy metoda podstawowa może nie być dostępna.

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365, używając nazwy użytkownika i hasła.
2. Wybierz **opcję Użyj innej opcji weryfikacji**. Widzisz różne opcje weryfikacji na podstawie liczby ustawień.
3. Wybierz alternatywną metodę i zaloguj się.

   ![Użyj metody alternatywnej](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Następne kroki
- Jeśli masz problemy z logowaniem się za pomocą weryfikacji dwuetapowej, uzyskaj więcej informacji na temat [Problemy z uwierzytelnianiem wieloskładnikowym platformy Azure.](multi-factor-authentication-end-user-troubleshoot.md)

- Dowiedz się, jak [zarządzać ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md).

- Dowiedz się, jak [rozpocząć pracę z aplikacją Microsoft Authenticator,](user-help-auth-app-download-install.md) aby można było używać powiadomień do logowania się zamiast wiadomości TEKSTOWYCH i połączeń telefonicznych.
