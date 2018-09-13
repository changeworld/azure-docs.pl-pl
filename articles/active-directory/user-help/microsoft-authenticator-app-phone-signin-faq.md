---
title: Microsoft Authenticator logowania za pomocą telefonu — konta platformy Azure i Microsoft | Dokumentacja firmy Microsoft
description: Za pomocą telefonu zalogować się do swojego konta Microsoft, zamiast wpisywać hasła. Ten artykuł zawiera odpowiedzi na często zadawane pytania o tej funkcji.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directoary
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 08/12/2017
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 3536c5da7833c32b583f1a510be43864c9107068
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717050"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Logowanie się za pomocą telefonu zamiast hasła
Aplikacja Microsoft Authenticator pomaga chronić Twoje konta, przeprowadzania weryfikacji dwuetapowej, po wprowadzeniu hasła. Ale czy wiesz, że może ona całkowicie zastąpić hasło Twojego osobistego konta Microsoft?

Ta funkcja jest dostępna w systemach iOS i urządzenia z systemem Android i współdziała z osobistych kont Microsoft.
 
## <a name="how-it-works"></a>Jak to działa
Wiele osób na użytek aplikacji Microsoft Authenticator weryfikacji dwuetapowej po zalogowaniu się do swojego konta Microsoft. Możesz wpisać hasło, a następnie przejdź do aplikacji, aby zatwierdzić powiadomienie lub kodu weryfikacyjnego. Za pomocą logowanie za pomocą telefonu Pomiń hasło i wykonaj weryfikację tożsamości na swoim telefonie. Ponieważ logowanie za pomocą telefonu jest typem weryfikacji dwuetapowej, nadal konieczne jest zapewnienie rzeczy, które znasz i rzeczą, musisz zweryfikować swoją tożsamość. Numer telefonu jest nadal rzeczą, jaką masz i numeru PIN lub klucza biometryczne Twój telefon jest kwestią, które znasz.

## <a name="how-to-get-started"></a>Jak zacząć
Aby zalogować się do Twojego osobistego konta Microsoft, za pomocą telefonu, wykonaj następujące kroki:

1. Włącz logowanie za pomocą telefonu dla swojego konta.

    - Jeśli nie masz jeszcze, zainstalować i dodać Twojego osobistego konta Microsoft, zgodnie z instrukcjami w aplikacji Microsoft Authenticator [strony Microsoft Authenticator](microsoft-authenticator-app-how-to.md). Nowo dodane kont są włączane automatycznie, więc wszystko jest gotowe.

    - Jeśli używasz już Microsoft Authenticator weryfikacji dwuetapowej, wybierz konto usługi z poziomu strony głównej aplikacji i wybierz **Włącz logowanie za pomocą telefonu** z menu rozwijanego.

    >[!NOTE]
    >Aby chronić Twoje konto, firma Microsoft wymaga numeru PIN lub biometrycznych blokady na urządzeniu. Pozostawienie telefonu odblokować aplikacji pojawi się żądanie z pytaniem, skonfigurować blokadę przed włączeniem logowanie za pomocą telefonu.

2. Większość strony, gdzie normalnie wprowadzisz hasła do konta Microsoft mają łącze, które mówi **zamiast tego użyj aplikacji**. Wybierz ten link, aby zalogować się przy użyciu telefonu.
 
3. Firma Microsoft wysyła powiadomienie na Twój telefon. Zatwierdź powiadomienie, aby zalogować się do swojego konta.   
 
## <a name="faq"></a>Często zadawane pytania

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Jak jest logowanie przy użyciu Mój telefon bezpieczniejsze niż wpisanie hasła?  
Obecnie większość osób Zaloguj się do witryny sieci web lub aplikacji za pomocą nazwy użytkownika i hasła.  Niestety hasła są często utraty, kradzieży lub odgadnięty przez hakerów. Po skonfigurowaniu Zaloguj się w aplikacji Microsoft Authenticator możemy wygenerować klucz na telefonie, który można odblokować konto. Ten klucz, PIN lub biometrycznych, że używasz już na telefonie są chronione.  Po zalogowaniu się przy użyciu telefonu, ten klucz służy do potwierdzenia tożsamości bezpiecznie z dwóch czynników — telefon, sama i możliwość jego odblokowania.
 
Klucz używany jest podobny do kluczy używanych w Windows Hello i specyfikacje FIDO Alliance UAF. Informacje o mnie jest tylko danych użytych do ochrony klucza lokalnie, nigdy nie wysyłane do i przechowywane w chmurze. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Gdzie mogę używać mojego telefonu celu zastąpienia hasła, a gdy nadal muszę hasło?  
Już dziś funkcja logowania telefonu działa tylko z aplikacji sieci web i usług, które są obsługiwane przez osobistych kont Microsoft, iOS lub aplikacje dla systemu Android korzystających z osobistego konta Microsoft i aplikacje w systemie Windows 10, które używają osobistego konta Microsoft. Po zalogowaniu się do jednej z tych witryn sieci web lub aplikacji na stronie, w których zwykle wprowadź hasło to link, który jest wyświetlany komunikat, **zamiast tego użyj aplikacji**. 

Logowanie za pomocą telefonu nie może służyć do odblokowania komputera z systemem Windows, konsoli XBOX lub wszystkie pulpitu wersje aplikacji firmy Microsoft, takich jak aplikacje pakietu Office, w tym momencie.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>To zastępuje weryfikację dwuetapową? Należy wyłączyć je?   
Czasami. Pracujemy nad rozszerzeniem zasięgu zakres logowanie telefonem, ale na razie nadal istnieją miejsc w ekosystemie Microsoft, które go nie obsługują. W tych miejscach firma Microsoft nadal korzysta z weryfikacji dwuetapowej dla bezpiecznego logowania. Z tego powodu nie, użytkownik nie należy wyłączyć weryfikację dwuetapową dla konta usługi.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>To wszystko Jeśli włączono weryfikację dwuetapową dla mojego konta mogę zachować, czy muszę zatwierdzić dwa powiadomienia?
Nie, nie. Logowanie do konta Microsoft, za pomocą telefonu jest liczona jako weryfikacji dwuetapowej. Zamiast wpisywać hasło, a następnie zatwierdzanie powiadomienie potwierdzenia tożsamości, wiedza, jak do odblokowania telefonu, a następnie zatwierdzanie powiadomienie. Firma Microsoft nie wyśle powiadomienie drugiego do zatwierdzenia.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Co zrobić, jeśli utracą Mój telefon lub go nie masz mi, jak mogę skorzystać z mojego konta?  
Zawsze można kliknąć przycisk **zamiast tego użyj hasła** na stronie logowania, aby wrócić do używał Twojego hasła. Należy pamiętać o tym, jeśli korzystasz z weryfikacji dwuetapowej, nadal należy drugiej metody, aby sprawdzić, logowanie. Dlatego zdecydowanie zalecamy, aby upewnić się, ponieważ ma dodatkowy, aktualne informacje zabezpieczające na Twoim koncie. Możesz zarządzać zabezpieczające na stronie https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Jak uniemożliwić korzystanie z tej funkcji i wróć do wprowadzania hasła?
Kliknij przycisk **zamiast tego użyj hasła** po zalogowaniu. Firma Microsoft należy pamiętać, wybór najbardziej aktualne i ofertę, która domyślnie przy następnym logowaniu. Jeśli kiedykolwiek chcesz wrócić do logujesz się przy użyciu telefonu, kliknij przycisk **zamiast tego użyj aplikacji**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Aby zalogować się do wszystkich kont z firmą Microsoft można używać aplikacji?   
Ta funkcja jest dostępna dla osobistych kont Microsoft tylko w tej chwili. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Mogę się zalogować do komputera za pomocą Mój telefon?  
Na komputerze zaleca się logujesz się przy użyciu Windows Hello w systemie Windows 10 za pomocą usługi face, odcisk palca lub numeru PIN.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Mogę zalogować się przy użyciu mojego Windows Phone?  
W tej chwili nie opracowywana tej funkcji Microsoft Authenticator na Windows Phone. 

## <a name="next-steps"></a>Kolejne kroki
Jeśli nie zostały pobrane aplikacji Microsoft Authenticator, należy go wyewidencjonować. Aplikacja jest dostępna dla [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), a logowanie za pomocą telefonu jest dostępna w aplikacji Microsoft Authenticator dla [Android](http://go.microsoft.com/fwlink/?Linkid=825072) i [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Jeśli masz pytania dotyczące aplikacji ogólnie rzecz biorąc, Przyjrzyj się [Microsoft Authenticator często zadawane pytania](microsoft-authenticator-app-faq.md)