---
title: Microsoft Authenticator phone logowania — konta platformy Azure i Microsoft | Dokumentacja firmy Microsoft
description: Zaloguj się do swojego konta Microsoft, zamiast wpisywania hasła przy użyciu telefonu. Ten artykuł zawiera odpowiedzi na często zadawane pytania o tej funkcji.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/12/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 063a97fc8f5b0746517919c73094525942418719
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102509"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Zaloguj się przy użyciu telefonu, nie pamiętasz hasła

Aplikacja Microsoft Authenticator pomaga chronić swoje konta, wykonując weryfikację dwuetapową, po wprowadź hasło. Ale czy wiesz, że go całkowicie zastąpić hasło dla swojego osobistego konta Microsoft?

Ta funkcja jest dostępna w systemach iOS i urządzeniach z systemem Android i współpracuje z osobistego konta Microsoft.

## <a name="how-it-works"></a>Jak to działa

Wiele osób korzystać z aplikacji Microsoft Authenticator na potrzeby weryfikacji dwuetapowej, po zalogowaniu się do swojego konta Microsoft. Wpisz hasło, następnie przejdź do aplikacji, aby zatwierdzić powiadomienia albo kod weryfikacyjny. Z telefonu logowania Pomiń hasło i wykonaj weryfikację tożsamości na telefonie. Ponieważ logowanie telefonu jest typem weryfikację dwuetapową, nadal konieczne jest zapewnienie operacją, której znasz i operacją, należy zweryfikować Twoją tożsamość. Telefon jest nadal operacją, której masz i numeru PIN lub klucza biometryczne Twój telefon jest operacją, której należy wiedzieć.

## <a name="how-to-get-started"></a>Jak zacząć

Aby zalogować się na osobiste konto Microsoft z telefonem, wykonaj następujące kroki:

1. Włącz logowanie telefonu dla swojego konta.

  - Jeśli nie masz jeszcze, zainstalować i dodać Twojego osobistego konta Microsoft, zgodnie z procedurą opisaną w aplikacji Microsoft Authenticator [strony Microsoft Authenticator](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md). Nowo dodanego konta są włączane automatycznie, więc wszystko jest gotowe.

  - Jeśli używasz już Authenticator firmy Microsoft na potrzeby weryfikacji dwuetapowej, wybierz konto na stronie głównej aplikacji i wybierz **włączyć logowanie phone** z menu rozwijanego.

  >[!NOTE]
  >Aby chronić swoje konto, wymagamy numeru PIN lub blokady biometrycznego na urządzeniu. Jeśli zachowasz telefonu odblokowane aplikacji pojawia się żądanie pytaniem do skonfigurowania blokady przed włączeniem logowania telefonu.

3. Większość stron, gdzie należy zwykle wprowadzić hasło konta Microsoft ma linku **zamiast tego użyć aplikacji**. Wybierz ten link, aby zalogować się przy użyciu telefonu.

4. Firma Microsoft wysyła powiadomienie na Twój telefon. Zatwierdzenie powiadomienia, aby zalogować się do swojego konta.   

## <a name="faq"></a>Często zadawane pytania

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Jak się zalogować Mój telefon bezpieczniejsze niż wpisanie hasła?  

Obecnie większość użytkowników Zaloguj się do witryny sieci web lub aplikacji przy użyciu nazwy użytkownika i hasła.  Niestety hasła są często utraty, kradzieży lub odgadnięty przez hakerów. Po skonfigurowaniu aplikacji Authenticator firmy Microsoft do logowania, możemy wygenerować klucz na telefonie można odblokować konto. Ten klucz z PIN lub biometryczne już użycie w telefonie są chronione.  Gdy zalogujesz się przy użyciu telefonu, ten klucz służy do potwierdzenia Twojej tożsamości bezpiecznie z dwa czynniki — telefon sam i możliwość jego odblokowania. 

Klucz używany jest podobny do kluczy używanych w Windows Hello i specyfikacje FIDO Alliance UAF. Informacje o danych jest tylko mnie używane do ochrony klucza lokalnie i jest nigdy nie wysyłane do lub przechowywane w chmurze. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Gdzie można używać Mój telefon w celu zastąpienia hasła, a gdy nadal jest potrzebna hasło?  

Obecnie funkcja logowania telefonu działa tylko z aplikacji sieci web i usług, które są obsługiwane przez osobistego konta Microsoft, iOS lub aplikacje dla systemu Android korzystających z osobistego konta Microsoft i aplikacji w systemie Windows 10, które używają osobistego konta Microsoft. Po zalogowaniu się do jednego z tych witryn sieci web lub aplikacji, na stronie, których zwykle wprowadź hasło to łącze, które mówi **zamiast tego użyć aplikacji**. 

Logowanie telefonu nie może służyć do odblokowania komputera z systemem Windows, konsoli XBOX lub wszystkie pulpitu wersje aplikacji firmy Microsoft, takich jak aplikacje pakietu Office, w tym momencie.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>To zastępuje weryfikację dwuetapową? Należy wyłączyć go?   

Czasami. Pracujemy nad poszerzenia zakresu logowania telefon, ale obecnie nadal istnieją miejsca w ekosystemie firmy Microsoft, które nie obsługują tego. W tych miejscach nadal używamy weryfikacji dwuetapowej dla bezpieczne logowanie. Z tego powodu nie, użytkownik nie należy wyłączyć weryfikację dwuetapową dla konta.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>Zgoda Jeśli aktualizować włączona dla mojego konta włączono weryfikację dwuetapową, muszę zatwierdzić dwa powiadomienia?

Nie, nie. Logowanie do konta Microsoft z telefonem traktowana jako weryfikacji dwuetapowej. Zamiast wpisywać swoje hasło, a następnie zatwierdzanie powiadomienie potwierdzenia tożsamości za odblokowania telefonu, a następnie zatwierdzanie powiadomienie. Firma Microsoft nie wyśle powiadomienie drugi do zatwierdzenia.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Co w przypadku utraty Mój telefon, lub nie ma go przy użyciu elementu me, jak można uzyskać dostęp Moje konto?  

Zawsze można kliknąć przycisk **zamiast tego użyć hasła** na stronie logowania, aby wrócić do przy użyciu hasła. Należy pamiętać, że użycie weryfikację dwuetapową, nadal należy drugiej metody, aby sprawdzić logowanie. Dlatego zdecydowanie zalecamy mieć informacje dodatkowe, aktualne zabezpieczeń na Twoim koncie. Możesz zarządzać informacje zabezpieczające pod https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Jak zatrzymać za pomocą tej funkcji i wróć do wprowadzania hasła?

Kliknij przycisk **zamiast tego użyć hasła** po zalogowaniu. Firma Microsoft Pamiętaj najnowszych wybór i oferty, które domyślnie przy następnym logowaniu. Jeśli kiedykolwiek zajdzie potrzeba Przejdź wstecz, aby zalogować się Twój telefon, kliknij przycisk **zamiast tego użyć aplikacji**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Aby zalogować się do wszystkich kont z firmą Microsoft można używać aplikacji?   
Ta funkcja jest dostępna dla osobistego konta Microsoft tylko w tej chwili. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Można zarejestrować na komputer z Mój telefon?  
Dla komputera zaleca się logowanie się przy użyciu usługi Windows Hello w systemie Windows 10 przy użyciu Twojej krój, odcisk palca lub numeru PIN.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Można zalogować się przy użyciu mojej Windows Phone?  
W tej chwili nie opracowywana tę funkcję dla Microsoft Authenticator na Windows Phone. 

## <a name="next-steps"></a>Kolejne kroki
Jeśli nie zostały pobrane aplikacji Authenticator firmy Microsoft, należy go wyewidencjonować. Aplikacja jest dostępna dla [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), i logowania telefonu jest dostępny w aplikacji Microsoft Authenticator dla [Android](http://go.microsoft.com/fwlink/?Linkid=825072) i [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Jeśli masz pytania dotyczące aplikacji ogólnie rzecz biorąc, Przyjrzyjmy się [uwierzytelniania firmy Microsoft — często zadawane pytania](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-faq.md)
