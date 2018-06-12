---
title: Rozwiązywanie problemów z konfiguracji delegowania ograniczonego protokołu Kerberos dla serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z konfiguracji ograniczone delegowanie protokołu Kerberos dla serwera Proxy aplikacji
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 933a5c8a9cde9134071a12dd0aed684358cc8696
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292858"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Rozwiązywanie problemów z konfiguracji delegowania ograniczonego protokołu Kerberos dla serwera Proxy aplikacji

Do osiągnięcia logowania jednokrotnego do opublikowanych aplikacji mogą się różnić od jednej aplikacji do innej metody. Jedną z opcji udostępniającej serwera Proxy aplikacji usługi Azure Active Directory (Azure AD), domyślnie jest ograniczone delegowanie protokołu Kerberos (KCD). Możesz skonfigurować łącznik, dla użytkowników, należy uruchomić uwierzytelnianie Kerberos ograniczonego do zaplecza aplikacji.

Procedura włączania KCD jest prosta. Nie wymaga nie więcej niż ogólną wiedzą z różnymi składnikami i przebieg uwierzytelniania, który obsługuje logowania jednokrotnego. Jednak czasami KCD logowania jednokrotnego nie działać zgodnie z oczekiwaniami. Wymagane jest dobre źródła informacji rozwiązywać problemy z tych scenariuszy.

Ten artykuł zawiera pojedynczy punkt odniesienia, który ułatwia rozwiązywanie problemów i własnym rozwiązania niektórych typowych problemów. Obejmuje ona również diagnostyki bardziej złożonych problemów.

W tym artykule sprawia, że następujące założenia:

-   Wdrożenie aplikacji serwera Proxy Azure AD na [Rozpoczynanie pracy z serwera Proxy aplikacji](manage-apps/application-proxy-enable.md) i dostępu do aplikacji z systemem innym niż KCD działa zgodnie z oczekiwaniami.

-   Aplikacja docelowa opublikowanych opiera się na Internet Information Services (IIS) i przez firmę Microsoft implementacją protokołu Kerberos.

-   Host serwera i aplikacji znajdują się w jednej domenie usługi Azure Active Directory. Aby uzyskać szczegółowe informacje o scenariuszach różnych domen i lasów, zobacz [KCD oficjalny dokument](https://aka.ms/KCDPaper).

-   Aplikacja podmiotu jest publikowana na platformie Azure dzierżawcy z wstępne uwierzytelnianie jest włączone. Użytkownicy mogą uwierzytelniać na platformie Azure przy użyciu uwierzytelniania opartego na formularzach. Scenariusze uwierzytelniania klienta sformatowanego nie są objęte w tym artykule. One mogą być dodane w pewnym momencie w przyszłości.

## <a name="prerequisites"></a>Wymagania wstępne

Serwer Proxy aplikacji usługi Azure AD można wdrożyć na wiele typów infrastruktury lub środowisk. Architektur różnią się od organizacji. Najczęstszymi przyczynami problemów związanych z KCD nie ma tych środowisk. Błędy konfiguracji prostego lub ogólne błędy spowodować większości problemów.

Z tego powodu najlepiej upewnij się, że zostały spełnione wszystkie wymagania wstępne w [przy użyciu logowania jednokrotnego KCD przy użyciu serwera Proxy aplikacji](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) przed rozpoczęciem rozwiązywania problemów. Należy zwrócić uwagę sekcję na temat konfigurowania ograniczonego delegowania protokołu Kerberos na 2012 R2. Ten proces wykorzystuje różne podejścia do konfigurowania KCD w poprzednich wersjach systemu Windows. Można również, mając na uwadze powyższe zagadnienia:

-   Nie jest często serwerem członkowskim domeny otworzyć okno dialogowe bezpiecznego kanału z określonego kontrolera domeny (DC). Następnie serwer może przenieść do innego okna dialogowego w danym momencie. Dlatego hostów łącznika nie są ograniczone do komunikacji z lokacji lokalnej tylko określone kontrolery domeny.

-   Między domenami scenariusze korzystają z odwołań, które bezpośrednie hostem łącznika do kontrolerów domeny, które mogą być poza lokalnej sieci obwodowej. W takich przypadkach jest równie ważne również wysyłać ruch ponownego udostępnienia do kontrolerów domeny, które reprezentują innych domen. W przeciwnym razie delegowania zakończy się niepowodzeniem.

-   Jeśli to możliwe, należy unikać wprowadzania żadnych aktywnych urządzeń adresów IP ani identyfikatorów między hostami łącznika i kontrolery domeny. Urządzenia te są czasami overintrusive i zakłócić core ruch RPC.

Przetestuj delegowania w scenariuszach proste. Wprowadzenie więcej zmiennych, im bardziej może być konieczne będą konkurować o. Aby zaoszczędzić czas, Ogranicz testowanie do pojedynczego łącznika. Dodaj dodatkowe łączników po problem został rozwiązany.

Niektóre środowiska mogą również czynnikach problemu. Aby uniknąć tych czynników, zminimalizować architektura możliwie często podczas testowania. Na przykład nieprawidłowej konfiguracji zapory wewnętrznej listy ACL są często używane. Jeśli to możliwe Wyślij cały ruch z łącznika prosty do kontrolerów domeny i aplikacji zaplecza.

Najlepszym miejscem, aby umieścić łączników jest możliwie najbliżej ich celów. Zapory, która znajduje się wbudowanego podczas testowania dodaje złożoności zbędne i można przedłużyć czas badania sieci.

Co zawiera KCD problem? Istnieje kilka typowych wskazania, że usługa rejestracji Jednokrotnej w ramach delegowania KCD kończy się niepowodzeniem. Pierwsze znaki problemu są wyświetlane w przeglądarce.

   ![Błąd konfiguracji KCD niepoprawne](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Autoryzacja nie powiodła się z powodu brakujących uprawnień](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Pokaż oba te obrazy objawem tego samego: błąd rejestracji Jednokrotnej. Odmowa dostępu użytkownika do aplikacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jak rozwiązać zależy od tego, problem i objawy, które należy obserwować. Przed przejściem do żadnego dalej, Poznaj następujących artykułach. Udostępniają one przydatne informacje dotyczące rozwiązywania problemów:

-   [Rozwiązywanie problemów z serwera Proxy aplikacji i komunikaty o błędach](manage-apps/application-proxy-troubleshoot.md)

-   [Błędy protokołu Kerberos i objawy](manage-apps/application-proxy-troubleshoot.md#kerberos-errors)

-   [Praca z logowania jednokrotnego podczas lokalnej i w chmurze tożsamości nie są identyczne](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Jeśli masz w tym punkcie główny problem istnieje. Aby rozpocząć, oddziel przepływ następujące trzy etapy, które można rozwiązywać problemy.

### <a name="client-pre-authentication"></a>Wstępne uwierzytelnienie klienta 
Użytkownik zewnętrzny uwierzytelnianie na platformie Azure za pośrednictwem przeglądarki. Możliwość wstępnego uwierzytelniania Azure jest niezbędne do logowania jednokrotnego KCD funkcji. Testowanie i adres tę możliwość, jeśli występują problemy. Etap wstępnego uwierzytelniania nie jest związana z KCD lub opublikowanej aplikacji. To proste naprawić niezgodności sprawdzając związane z poprawnością, które istnieje konto podmiotu na platformie Azure. Sprawdź, czy nie jest również wyłączone lub zablokowane. Odpowiedzi na błąd w przeglądarce jest opisowy wyjaśniający przyczynę. Jeśli nie jesteś pewien, sprawdź inne artykuły dotyczące rozwiązywania problemów firmy Microsoft można zweryfikować.

### <a name="delegation-service"></a>Delegowanie usługi 
Łącznik serwera Proxy Azure, który pobiera biletu usługi Kerberos dla użytkowników z protokołu Kerberos klucza dystrybucji Center (KCD).

Zewnętrzne komunikacji między klientem a Azure frontonu nie ma wpływu na KCD. Komunikacja tylko upewnij się, że KCD działa. Usługa serwera Proxy Azure podano prawidłowy identyfikator użytkownika, który jest używany do pobierania biletu protokołu Kerberos. Bez tego Identyfikatora KCD nie jest możliwe i kończy się niepowodzeniem.

Jak wspomniano wcześniej, komunikaty o błędach przeglądarki zawiera niektóre dobrej wskazówek dotyczących Dlaczego elementów nie powiodło się. Upewnij się, że należy zanotować identyfikator działania i sygnaturę czasową w odpowiedzi. Te informacje pomagają skorelowania zachowania rzeczywiste zdarzenia w dzienniku zdarzeń serwera Proxy Azure.

   ![Błąd konfiguracji KCD niepoprawne](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Pokaż odpowiednie wpisy w dzienniku zdarzeń jako zdarzenia 13019 lub 12027. Znajdź łącznik dzienniki zdarzeń w **Dzienniki aplikacji i usług** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt;  **Łącznik** &gt; **Admin**.

   ![Zdarzenie 13019 z dziennika zdarzeń serwera Proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Zdarzenie 12027 z dziennika zdarzeń serwera Proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1.   Użyj **A** rekord w wewnętrzne serwery DNS dla adresu aplikacji nie **CName**.

2.   Potwierdź, że hosta łącznika przyznano prawo do delegowania SPN konta wyznaczonych docelowego. Potwierdź, że **Użyj dowolnego protokołu uwierzytelniania** jest zaznaczone. Aby uzyskać więcej informacji, zobacz [artykułu konfiguracji logowania jednokrotnego](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

3.   Sprawdź, czy jest tylko jedno wystąpienie SPN istniejących w usłudze Azure AD. Problem `setspn -x` z wiersza polecenia na żadnym hoście członek domeny.

4.   Sprawdź, czy zasady domeny są wymuszane ograniczającego [maksymalny rozmiar wystawionych tokenów Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Ta zasada powoduje zatrzymanie łącznik z pobierania tokenu, jeśli okaże się nadmierna.

Śledzenie sieci, który przechwytuje wymiany między hostem łącznika i domeny Centrum dystrybucji KLUCZY jest następnego kroku najlepsze, aby uzyskać więcej szczegółów niskiego poziomu na problemy. Aby uzyskać więcej informacji, zobacz [nowości papieru rozwiązywanie](https://aka.ms/proxytshootpaper).

Jeśli biletów wygląda dobrze, pojawi się zdarzenie w dzienniku informujący o tym uwierzytelnianie nie powiodło się, ponieważ aplikacja zwróciła 401. To zdarzenie oznacza, że aplikacja docelowa odrzucił bilet. Przejdź do kolejnego etapu.

### <a name="target-application"></a>Aplikacja docelowa 
Konsument biletu Kerberos udostępniane przez łącznik. Na tym etapie oczekiwać, że łącznik, aby zostały wysłane bilet usługi protokołu Kerberos do wewnętrznej. Ten bilet jest nagłówkiem w pierwszym żądaniu aplikacji.

1.   Za pomocą wewnętrzny adres URL aplikacji zdefiniowano w portalu, należy sprawdzić, czy aplikacja jest dostępny bezpośrednio z przeglądarki na hoście łącznika. Następnie można zalogować się pomyślnie. Szczegółowe informacje znajdują się w łączniku **rozwiązywanie** strony.

2.   Nadal na hoście łącznika upewnij się, że uwierzytelnianie między przeglądarką a aplikacji przy użyciu protokołu Kerberos. Wykonaj jedną z następujących czynności:

3.  Uruchom DevTools (**F12**) w programie Internet Explorer lub użyj [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hosta łącznika. Przejdź do aplikacji przy użyciu wewnętrznego adresu URL. Sprawdź oferowany nagłówki autoryzacji WWW zwracany w odpowiedzi z aplikacji, aby upewnić się, że albo negocjowania lub występuje protokołu Kerberos. 

    a. Rozpoczyna się od obiektu blob dalej protokołu Kerberos, który jest zwracany w odpowiedzi z przeglądarki do aplikacji **YII**. Te litery informujące, że działa protokołu Kerberos. Microsoft NT LAN Manager (NTLM) z drugiej strony, zawsze rozpoczyna się od **TlRMTVNTUAAB**, która odczytuje NTLM zabezpieczeń Obsługa dostawcy (NTLMSSP) podczas zdekodować z formatu Base64. Jeśli widzisz **TlRMTVNTUAAB** na początku obiektu blob, protokołu Kerberos nie jest dostępna. Jeśli nie widzisz **TlRMTVNTUAAB**, prawdopodobnie Kerberos dostępne.

       > [!NOTE]
       > Jeśli używasz narzędzia Fiddler, ta metoda wymaga tymczasowo wyłączyć ochrona rozszerzona w konfiguracji aplikacji w usługach IIS.

       ![Przeglądarce inspekcji sieci](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    b. Na tym rysunku obiektu blob nie zaczyna się od **TIRMTVNTUAAB**. Dlatego w tym przykładzie protokół Kerberos jest dostępne, a w obiekcie blob protokołu Kerberos nie zaczyna się od **YII**.

4.  Tymczasowe usunięcie NTLM, z listy dostawców w lokacji usług IIS. Dostęp do aplikacji bezpośrednio z programu Internet Explorer na hoście łącznika. Protokół NTLM jest już na liście dostawców. Tylko przy użyciu protokołu Kerberos może uzyskać dostępu do aplikacji. Jeśli nie można uzyskać dostępu, może to być problem z konfiguracją aplikacji. Uwierzytelnianie Kerberos nie działa.

    a. Jeżeli protokół Kerberos nie jest dostępny, sprawdź ustawienia uwierzytelniania aplikacji w usługach IIS. Upewnij się, że **Negotiate** znajduje się u góry, NTLM, po prostu znajdujące się poniżej. Jeśli widzisz **negocjuje**, **protokołu Kerberos lub Negotiate**, lub **protokołu PKU2U**, kontynuować tylko wtedy, jeśli protokół Kerberos jest funkcjonalności.

       ![Dostawców uwierzytelniania systemu Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
    b. Protokół Kerberos i NTLM w miejscu tymczasowo wyłączyć wstępnego uwierzytelniania dla aplikacji w portalu. Spróbuj uzyskać do niego dostęp z Internetu przy użyciu zewnętrznego adresu URL. Zostanie wyświetlony monit o uwierzytelniania. Możesz to zrobić przy użyciu tego samego konta używane w poprzednim kroku. Jeśli nie występuje problem z aplikacją zaplecza, nie KCD.

    c. Włącz ponownie wstępnego uwierzytelniania w portalu. Uwierzytelnianie za pośrednictwem platformy Azure przez próby połączenia aplikacji za pomocą jego zewnętrznego adresu URL. W przypadku niepowodzenia logowania jednokrotnego zostanie wyświetlony komunikat błędu zabronione w przeglądarce i zdarzenie 13022 w dzienniku:

       *Łącznik serwera Proxy aplikacji usługi Microsoft AAD nie może uwierzytelnić użytkownika, ponieważ serwer wewnętrznej bazy danych odpowiada prób uwierzytelnienia Kerberos z powodu błędu HTTP 401.*

       ![Dostęp zabroniony błąd 401 HTTTP](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

    d. Sprawdź aplikacji usług IIS. Upewnij się, że pula aplikacji skonfigurowana i główną nazwę usługi są skonfigurowane do używania tego samego konta w usłudze Azure AD. Przejdź w usługach IIS, jak pokazano na poniższej ilustracji:

       ![Okno konfiguracji aplikacji usług IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

       Po określeniu tożsamości, upewnij się, że to konto jest skonfigurowane z tej nazwy SPN. Może to być na przykład `setspn –q http/spn.wacketywack.com`. Wprowadź następujący tekst w wierszu polecenia:

       ![Okno polecenia SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

    e. Sprawdź nazwę SPN zdefiniowane w odniesieniu do ustawień aplikacji w portalu. Upewnij się, że tej samej nazwy SPN skonfigurowanych w odniesieniu do obiektu docelowego konta usługi Azure AD jest używane przez puli aplikacji.

       ![Konfiguracja SPN w portalu Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
    f. Przejdź do usług IIS i wybierz **edytora konfiguracji** opcję dla aplikacji. Przejdź do **system.webServer/security/authentication/windowsAuthentication**. Upewnij się, że wartość **UseAppPoolCredentials** jest **True**.

       ![Opcja credential pul aplikacji konfiguracji usług IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

       Zmień tę wartość na **True**. Usuń wszystkie buforowane bilety Kerberos z wewnętrznego serwera, uruchamiając następujące polecenie:

       ```powershell
       Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
       ``` 

Aby uzyskać więcej informacji, zobacz [przeczyścić pamięci podręcznej z biletu Kerberos klienta dla wszystkich sesji](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).



Pozostawienie włączono obsługę trybu jądra, poprawia wydajność operacji protokołu Kerberos. Jednak powoduje także biletu żądanej usługi można odszyfrować za pomocą konta komputera. To konto jest również nazywany systemu lokalnego. Ta wartość **True** przerwanie KCD, gdy aplikacja jest hostowana w więcej niż jednym serwerze w farmie.

-   Jako dodatkowe sprawdzanie wyłączyć **rozszerzone** ochrony zbyt. W niektórych scenariuszach **rozszerzone** ochrony spowodowało przerwanie KCD włączenie w konfiguracji. W takich przypadkach aplikacji został opublikowany jako podfolder domyślnej witryny sieci Web. Ta aplikacja jest skonfigurowana dla tylko uwierzytelnianie anonimowe. Okna dialogowe są wygaszone, które sugeruje się, że obiekty podrzędne nie dziedziczy wszystkie aktywne ustawienia. Firma Microsoft zaleca testu, ale pamiętaj przywrócić tę wartość na **włączone**, jeśli jest to możliwe.

    To dodatkowe sprawdzenie umieszcza na ścieżkę, aby korzystać z opublikowanych aplikacji. Użytkownik może pokrętła się dodatkowe łączniki, które także są skonfigurowane do delegowania. Aby uzyskać więcej informacji, przeczytaj więcej informacji na temat omówienie techniczne, [Rozwiązywanie problemów z serwer Proxy aplikacji usługi Azure AD](https://aka.ms/proxytshootpaper).

Jeśli nadal nie możesz postępu, pomocy technicznej firmy Microsoft mogą pomóc. Tworzenie biletu pomocy technicznej bezpośrednio z poziomu portalu. Inżynier skontaktuje się z Tobą.

## <a name="other-scenarios"></a>Inne scenariusze

- Serwer Proxy aplikacji Azure żąda biletu Kerberos przed wysłaniem żądania do aplikacji. Niektóre aplikacje innych firm, takich jak Tableau serwera nie podoba tej metody uwierzytelniania. Te aplikacje spodziewać się bardziej z konwencjonalnej negocjacji została wykonana. Pierwsze żądanie jest anonimowy, który zezwala aplikacji na odpowiedź z typów uwierzytelniania, obsługuje on za pośrednictwem 401.

- Uwierzytelniania z wieloma przeskokami to powszechnie używane w scenariuszach, w którym warstwy aplikacji z frontonu i zaplecza gdy oba wymagają uwierzytelniania, takie jak SQL Server Reporting Services. Aby skonfigurować wieloskokowych scenariusza, zobacz artykuł pomocy technicznej [Kerberos ograniczonego delegowania może wymagać przejścia protokołu w scenariuszach z wieloma przeskokami](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Kolejne kroki
[Skonfiguruj KCD na domeny zarządzanej](../active-directory-domain-services/active-directory-ds-enable-kcd.md).
