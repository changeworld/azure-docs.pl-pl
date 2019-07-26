---
title: Konfigurowanie informacji zabezpieczających (wersja zapoznawcza) z monitu logowania — Azure Active Directory | Microsoft Docs
description: Informacje na temat sposobu konfigurowania informacji zabezpieczających dla konta służbowego po wyświetleniu monitu na stronie logowania organizacji.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20256fb712d9381ba2adc90e2e68ce4fdc8911a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382782"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>Konfigurowanie informacji zabezpieczających (wersja zapoznawcza) z monitu na stronie logowania

Następujące kroki można wykonać w przypadku wyświetlenia monitu o skonfigurowanie informacji zabezpieczających natychmiast po zalogowaniu się na konto służbowe.

Ten monit jest wyświetlany tylko wtedy, gdy użytkownik nie skonfigurował informacji zabezpieczających wymaganych przez organizację. Jeśli Twoje informacje zabezpieczające zostały już skonfigurowane wcześniej, ale chcesz wprowadzić zmiany, możesz wykonać kroki opisane w artykułach z instrukcjami dotyczącymi różnych metod. Aby uzyskać więcej informacji, zobacz [Dodawanie lub aktualizowanie informacji zabezpieczających — omówienie](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>Logowanie na konto służbowe

Po zalogowaniu się na konto służbowe zobaczysz monit o podanie kolejnych informacji przed uzyskaniem dostępu do konta.

![Monit z pytaniem o kolejne informacje](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Konfigurowanie informacji zabezpieczających za pomocą kreatora

Wykonaj następujące kroki, aby skonfigurować informacje zabezpieczające dla swojego konta służbowego z monitu.

>[!Important]
>Jest to tylko przykład procesu. W zależności od wymagań organizacji administrator mógł ustawić różne metody weryfikacji, które należy skonfigurować w trakcie tego procesu. W tym przykładzie wymagamy dwóch metod: aplikacji Microsoft Authenticator i numeru telefonu komórkowego na potrzeby weryfikacyjnych rozmów telefonicznych lub wiadomości SMS.

1. Po wybraniu pozycji **Dalej** w monicie zostanie wyświetlony **kreator Zabezpiecz swoje konto**, pokazując pierwszą metodę, której skonfigurowanie jest wymagane przez administratora i organizację. W tym przykładzie jest to aplikacja Microsoft Authenticator.

   > [!Note]
   > Jeśli chcesz użyć aplikacji uwierzytelniającej innej niż aplikacja Microsoft Authenticator, wybierz link **Chcę użyć innej aplikacji uwierzytelniania**.
   >
   > Jeśli organizacja umożliwia wybranie innej metody niż aplikacja uwierzytelniająca, możesz wybrać **link Chcę skonfigurować inną metodę**.

    ![Kreator Zabezpiecz swoje konto pokazujący stronę pobierania aplikacji uwierzytelniającej](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Wybierz pozycję **Pobierz teraz**, aby pobrać i zainstalować aplikację Microsoft Authenticator na swoim urządzeniu mobilnym, a następnie wybierz pozycję **Dalej**. Aby uzyskać więcej informacji o pobieraniu i instalowaniu aplikacji, zobacz [Pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Kreator Zabezpiecz swoje konto pokazujący stronę konfigurowania konta programu uwierzytelniającego](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Pozostań na stronie **Konfigurowanie konta** podczas konfigurowania aplikacji Microsoft Authenticator na urządzeniu mobilnym.

4. Otwórz aplikację Microsoft Authenticator, zezwól na powiadomienia (jeśli pojawi się monit), wybierz pozycję **Dodaj konto** z menu ikony **Dostosowywanie i kontrolowanie** w prawym górnym rogu, a następnie wybierz pozycję **Konto służbowe**.

5. Wróć na stronę **Konfigurowanie konta** na komputerze, a następnie wybierz pozycję **Dalej**.

    Zostanie wyświetlona strona **Skanowanie kodu QR**.

    ![Skanowanie kodu QR przy użyciu aplikacji Authenticator](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Zeskanuj podany kod przy użyciu czytnika kodów QR aplikacji Microsoft Authenticator, który pojawił się na urządzeniu mobilnym po utworzeniu konta służbowego w kroku 5.

    Aplikacja Authenticator powinna pomyślnie dodać konto służbowe bez konieczności podawania żadnych dodatkowych informacji przez użytkownika. Jednak jeśli czytnik kodów QR nie może odczytać kodu, możesz wybrać **link Nie mogę zeskanować kodu QR** oraz ręcznie podać kod i adres URL w aplikacji Microsoft Authenticator. Aby uzyskać więcej informacji na temat ręcznego dodawania kodu, zobacz [Ręcznie dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

7. Wybierz pozycję **Dalej** na stronie **Zeskanuj kod QR** na komputerze.

    Do aplikacji Microsoft Authenticator na urządzeniu przenośnym zostanie wysłane powiadomienie o konieczności przetestowania konta.

    ![Testowanie konta za pomocą aplikacji Authenticator](media/security-info/securityinfo-prompt-test-app.png)

8. Zatwierdź powiadomienie w aplikacji Microsoft Authenticator, a następnie wybierz pozycję **Dalej**.

    ![Powiadomienie o powodzeniu łączenia aplikacji z kontem](media/security-info/securityinfo-prompt-auth-app-success.png).

    Informacje zabezpieczające zostały zaktualizowane, tak aby aplikacja Microsoft Authenticator była domyślnie używana do weryfikowania tożsamości podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła.

9. Na stronie konfiguracji **Telefon** określ, czy chcesz otrzymywać wiadomości SMS, czy odbierać rozmowy telefoniczne, a następnie wybierz pozycję **Dalej**. Na potrzeby tego przykładu używamy wiadomości SMS, więc należy użyć numeru telefonu urządzenia, które może przyjmować wiadomości SMS.

    ![Rozpoczynanie konfigurowania numeru telefonu dla wiadomości SMS](media/security-info/securityinfo-prompt-text-msg.png)

    Wiadomości SMS są wysyłane na numer telefonu. Jeśli chcesz korzystać z rozmów telefonicznych, proces jest taki sam. Będziesz jednak otrzymywać rozmowy telefoniczne z instrukcjami zamiast wiadomości SMS.

10. Podaj kod określony w wiadomości SMS wysłanej na urządzenie przenośne, a następnie wybierz pozycję **Dalej**.

    ![Testowanie konta za pomocą wiadomości SMS](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Przejrzyj powiadomienie o powodzeniu, a następnie wybierz pozycję **Gotowe**.

    ![Powiadomienie o powodzeniu](media/security-info/securityinfo-prompt-call-answered-success.png)

    Informacje zabezpieczające zostały zaktualizowane, tak aby wiadomości SMS były używane jako zapasowa metoda weryfikowania tożsamości podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła.

12. Przejrzyj stronę **Powodzenie**, aby zweryfikować, czy aplikacja Microsoft Authenticator i metoda korzystająca z telefonu (wiadomości SMS lub rozmowy telefonicznej) zostały skonfigurowane w ramach informacji zabezpieczających, a następnie wybierz pozycję **Gotowe**.

    ![Strona pomyślnego zakończenia kreatora](media/security-info/securityinfo-prompt-setup-success.png)

## <a name="next-steps"></a>Następne kroki

- Aby zmienić, usunąć lub zaktualizować domyślne metody informacji zabezpieczających, zobacz:

    - [Konfigurowanie informacji zabezpieczających dla aplikacji Authenticator](security-info-setup-auth-app.md).

    - [Konfigurowanie informacji zabezpieczających pod kątem wiadomości SMS](security-info-setup-text-msg.md).

    - [Konfigurowanie informacji zabezpieczających w celu korzystania z rozmów telefonicznych](security-info-setup-phone-number.md).

    - [Konfigurowanie informacji zabezpieczających w celu korzystania z poczty e-mail](security-info-setup-email.md).

    - [Konfigurowanie informacji zabezpieczających w celu korzystania ze wstępnie zdefiniowanych pytań zabezpieczających](security-info-setup-questions.md).

- Aby uzyskać informacje o logowaniu za pomocą określonej metody, zobacz [Jak się zalogować](user-help-sign-in.md).

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](user-help-reset-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
