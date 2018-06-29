---
title: Azure MFA logowania w trakcie weryfikacji dwuetapowej | Dokumentacja firmy Microsoft
description: Ta strona pozwoli wskazówki na gdzie można wyświetlić różnych logowania dostępne metody za pomocą usługi Azure MFA.
keywords: Uwierzytelnianie użytkownika, logowania, zarejestruj się przy użyciu telefonu komórkowego, zarejestruj się przy użyciu telefonu biurowego
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 5df4af49a6c0251161d619c358ae68eaad3c3660
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102458"
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Środowisko logowania w usłudze Azure Multi-Factor Authentication
> [!NOTE]
> Celem tego artykułu jest przeprowadzenie typowe środowiska logowania. Aby uzyskać pomoc, z zalogowaniem lub rozwiązywania problemów, zobacz [problemy z uwierzytelnianiem wieloskładnikowym Azure](../../../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Jakie będzie środowisko logowania
Środowisko logowania różni się w zależności od wyboru do użycia jako Twoje czynnika: połączenie telefoniczne, aplikacja uwierzytelniania lub tekstów. Wybierz opcję, która najlepiej opisuje czynności:

| Jak możesz się zalogować? |
| --- |
| [Z telefoniczne Mój telefon komórkowy lub pakietu office](#signing-in-with-a-phone-call) |
| [Tekst na Mój telefon komórkowy](#signing-in-with-a-text-message)
| [Z powiadomienia z aplikacji Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Z kodów weryfikacyjnych z aplikacji Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Przy użyciu alternatywne metody ponieważ nie można użyć Moje preferowaną metodą w tej chwili](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logowanie się przy użyciu połączeń telefonicznych
Poniżej opisano środowisko weryfikacji dwuetapowej przy użyciu wywołania na Twój telefon komórkowy lub pakietu office.

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.  
2. Microsoft dzwoni do Ciebie.  
3. Odbierz połączenie i naciśnij klawisz #.  

## <a name="signing-in-with-a-text-message"></a>Logowanie się przy użyciu wiadomości SMS
Poniżej opisano środowisko weryfikacji dwuetapowej z wiadomość SMS na telefon komórkowy:

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.
2. Firma Microsoft wysyła wiadomość tekstową zawierającą numer kodu.
3. Wprowadź kod w polu dostępnym na stronie logowania.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logowanie się przy użyciu aplikacji uwierzytelniania firmy Microsoft
Poniższe informacje zawierają opis korzystania z aplikacji Microsoft Authenticator dla weryfikacji dwuetapowej. Istnieją dwa różne sposoby korzystania z aplikacji. Na urządzeniu może odbierać powiadomienia wypychane, lub możesz otworzyć aplikację, aby kod weryfikacyjny.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Aby zalogować się przy użyciu powiadomienie z aplikacji Microsoft Authenticator
1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.
2. Microsoft wysyła powiadomienie do aplikacji Microsoft Authenticator na urządzeniu.

  ![Firma Microsoft wysyła powiadomienia](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Otwórz powiadomienie na telefon i wybierz **Sprawdź** klucza. Jeśli firma wymaga numeru PIN, wprowadź go tutaj.
4. Powinny teraz być zalogowano.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Aby zalogować się przy użyciu kodu weryfikacyjnego z aplikacji Microsoft Authenticator

Jeśli używasz aplikacji Microsoft Authenticator uzyskać kodów weryfikacji, następnie po otwarciu aplikacji jest wyświetlana liczba pod nazwą konta. Liczba ta zmienia co 30 sekund, aby nie używać tego samego numeru dwa razy. Gdy pojawi się monit o kod weryfikacyjny, Otwórz aplikację i używać dowolnej wartości są obecnie wyświetlane.

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.
2. Microsoft wyświetla monit o podanie kodu weryfikacyjnego.

  ![Wprowadź kod weryfikacyjny](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Otwórz aplikację Microsoft Authenticator na telefonie i wprowadź kod w polu, w których logujesz.

## <a name="signing-in-with-an-alternate-method"></a>Logowanie się przy użyciu alternatywnych — metoda
Czasami nie masz telefon lub urządzenia, które można skonfigurować jako metodę weryfikacji preferowanego. Ta sytuacja jest, dlatego zaleca się skonfigurowanie metody wykonywania kopii zapasowej dla Twojego konta. Poniższej sekcji przedstawiono sposób Zaloguj się przy użyciu alternatywną metodę, gdy podstawowej metody nie mogą być dostępne.

1. Zaloguj się do aplikacji lub usługi, takiej jak Office 365 przy użyciu nazwy użytkownika i hasła.
2. Wybierz **użyć innej opcji weryfikacji**. Widzisz opcji weryfikacji różnych opartych o ile masz Instalatora.
3. Wybierz alternatywną metodę i zaloguj się.

  ![Należy użyć alternatywnej metody](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz problemy z zarejestrowaniem się przy użyciu weryfikacji dwuetapowej, uzyskać więcej informacji o [problemy z uwierzytelnianiem wieloskładnikowym Azure](../../../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-troubleshoot.md).

Dowiedz się, jak [zarządzać ustawieniami weryfikacji dwuetapowej](../../../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md).

Dowiedz się, jak [wprowadzenie do aplikacji Microsoft Authenticator](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) tak, aby powiadomienia można użyć do logowania zamiast teksty i połączeń telefonicznych.
