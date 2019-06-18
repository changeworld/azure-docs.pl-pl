---
title: Usługa Azure MFA Zaloguj się przy użyciu weryfikacji dwuetapowej — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Ta strona udostępnia wskazówki na gdzie można zobaczyć różne logowania metod dostępnych przy użyciu usługi Azure MFA.
keywords: uwierzytelnianie użytkowników, logowania, zaloguj się przy użyciu telefonu komórkowego, zaloguj się przy użyciu telefonu biurowego
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1350b2d86e18f213d99f1c27d64e371451f5f9b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334439"
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Środowisko logowania za pomocą usługi Azure Multi-Factor Authentication
> [!NOTE]
> Ten artykuł ma na celu opisano typowe środowisko logowania. Aby uzyskać pomoc, logowania lub rozwiązywania problemów, zobacz [problemy z usługą Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Jakie będzie środowisko logowania
Środowisko logowania różnią się zależnie od zdecydujesz się używać jako usługi czynnika: połączenie telefoniczne, aplikację uwierzytelniania lub teksty. Wybierz opcję, która najlepiej opisuje, co robią:

| Jak możesz się zarejestrować? |
| --- |
| [Za pomocą rozmowy telefonicznej, aby Mój telefon komórkowy lub pakietu office](#signing-in-with-a-phone-call) |
| [Tekst na Mój telefon komórkowy](#signing-in-with-a-text-message)
| [Powiadomienia z aplikacji Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Przy użyciu kodów weryfikacyjnych z aplikacji Microsoft Authenticator |
| [Za pomocą alternatywnej metody ponieważ nie mogę używać mojej preferowaną metodą teraz](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logowanie się przy użyciu połączenia telefonicznego
Następujące informacje w tym artykule opisano proces weryfikacji dwuetapowej za pomocą wywołania na Twój telefon komórkowy lub pakietu office.

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.  
2. Microsoft wywołań.  
3. Odbierz połączenie i naciśnij klawisz #.  

## <a name="signing-in-with-a-text-message"></a>Logowanie się przy użyciu wiadomości SMS
Następujące informacje w tym artykule opisano proces weryfikacji dwuetapowej za pomocą wiadomości SMS na telefon komórkowy:

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.
2. Firma Microsoft wysyła wiadomość SMS, który zawiera numer kodu.
3. Wprowadź kod w polu dostępnym na stronie logowania.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logowanie się przy użyciu aplikacji Microsoft Authenticator
Poniższe informacje zawierają opis korzystania z aplikacji Microsoft Authenticator dla weryfikacji dwuetapowej. Istnieją dwa różne sposoby korzystania z aplikacji. Może odbierać powiadomienia wypychane na twoim urządzeniu, lub możesz otworzyć aplikację, aby uzyskać kod weryfikacyjny.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Aby zalogować się przy użyciu powiadomienia z aplikacji Microsoft Authenticator
1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.
2. Firma Microsoft wysyła powiadomienie do aplikacji Microsoft Authenticator na urządzeniu.

   ![Firma Microsoft wysyła powiadomienia](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Otwórz powiadomienie na telefonie i wybierz **Sprawdź** klucza. Jeśli Twoja firma wymaga numeru PIN, wprowadź go tutaj.
4. Użytkownik powinien teraz zostać zarejestrowany.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Aby zalogować się przy użyciu kodu weryfikacyjnego z aplikacji Microsoft Authenticator

Jeśli używasz aplikacji Microsoft Authenticator uzyskać kody weryfikacyjne następnie po otwarciu aplikacji jest wyświetlana liczba pod nazwą Twojego konta. Liczba ta zmienia co 30 sekund, tak, aby nie używać tego samego numeru dwa razy. Gdy pojawi się prośba o kod weryfikacyjny, Otwórz aplikację i używać niezależnie od liczby są obecnie wyświetlane.

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.
2. Microsoft wyświetli monit o podanie kodu weryfikacyjnego.

   ![Wprowadź kod weryfikacyjny](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Otwórz aplikację Microsoft Authenticator na telefonie i wprowadź kod w polu, w którym rejestrujesz się.

## <a name="signing-in-with-an-alternate-method"></a>Logowanie się przy użyciu alternatywna metoda
Czasami nie masz telefon lub urządzenia, które można skonfigurować jako Twoja preferowana metoda weryfikacji. Ta sytuacja, dlatego zaleca się skonfigurowanie metody wykonywania kopii zapasowej dla swojego konta. Poniższej sekcji pokazano, jak zalogować się przy użyciu alternatywną metodę, gdy podstawowej metody nie mogą być dostępne.

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.
2. Wybierz **użyć innej opcji weryfikacji**. Zostanie wyświetlony opcje różnych weryfikacji, w oparciu o ile zostało skonfigurowane.
3. Wybierz alternatywną metodę i zaloguj się.

   ![Należy użyć alternatywnej metody](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Kolejne kroki
- Jeśli masz problemy z zarejestrowaniem się przy użyciu weryfikacji dwuetapowej, należy uzyskać więcej informacji o [problemy z usługą Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

- Dowiedz się, jak [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md).

- Dowiedz się, jak [Rozpoczynanie pracy z aplikacją Microsoft Authenticator](user-help-auth-app-download-install.md) tak, aby powiadomienia można użyć do logowania, zamiast tekstów i połączeń telefonicznych.
