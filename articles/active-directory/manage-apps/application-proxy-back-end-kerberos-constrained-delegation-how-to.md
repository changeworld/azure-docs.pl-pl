---
title: Rozwiązywanie problemów z delegowaniem z ograniczeniami protokołu Kerberos — serwer proxy aplikacji
description: Rozwiązywanie problemów z konfiguracjami delegowania ograniczonego protokołu Kerberos dla serwera proxy aplikacji
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5e866f61409960447e17ecb50b035eabd53dc38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275694"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Rozwiązywanie problemów z konfiguracjami delegowania z ograniczeniami protokołu Kerberos dla serwera proxy aplikacji

Metody dostępne do osiągnięcia wpisu SSO do opublikowanych aplikacji mogą się różnić w zależności od aplikacji. Jedną z opcji, które domyślnie oferuje serwer proxy aplikacji usługi Azure Active Directory (Azure AD), jest delegowanie ograniczone do protokołu Kerberos (KCD). Można skonfigurować łącznik, dla użytkowników, aby uruchomić ograniczone uwierzytelnianie Kerberos do aplikacji zaplecza.

Procedura włączania KCD jest prosta. Wymaga nie więcej niż ogólne zrozumienie różnych składników i przepływu uwierzytelniania, które obsługują sytą dla użytkownika. Ale czasami, KCD SSO nie działa zgodnie z oczekiwaniami. Potrzebujesz dobrych źródeł informacji, aby rozwiązać te scenariusze.

Ten artykuł zawiera pojedynczy punkt odniesienia, który pomaga rozwiązywać i samodzielnie rozwiązywać niektóre z najczęstszych problemów. Obejmuje ona również diagnozę bardziej złożonych problemów z wdrażaniem.

W tym artykule przedstawiono następujące założenia:

- Wdrażanie serwera proxy aplikacji usługi Azure AD na [wprowadzenie z serwerem proxy aplikacji](application-proxy-add-on-premises-application.md) i ogólny dostęp do aplikacji innych niż KCD działa zgodnie z oczekiwaniami.
- Opublikowana aplikacja docelowa jest oparta na internetowych usługach informacyjnych (IIS) i implementacji protokołu Kerberos firmy Microsoft.
- Hosty serwerów i aplikacji znajdują się w jednej domenie usługi Azure Active Directory. Aby uzyskać szczegółowe informacje na temat scenariuszy międzydomenowych i leśnych, zobacz [oficjalny dokument KCD](https://aka.ms/KCDPaper).
- Aplikacja podmiotu jest publikowana w dzierżawie platformy Azure z włączonym uwierzytelnianiem wstępnym. Użytkownicy mają uwierzytelnić się na platformie Azure za pomocą uwierzytelniania opartego na formularzach. Scenariusze uwierzytelniania klienta zaawansowanego nie są objęte tym artykułem. Mogą one zostać dodane w pewnym momencie w przyszłości.

## <a name="prerequisites"></a>Wymagania wstępne

Usługi Azure AD Application Proxy można wdrożyć w wielu typach infrastruktury lub środowisk. Architektury różnią się w zależności od organizacji. Najczęstszymi przyczynami problemów związanych z KCD nie są środowiska. Większość problemów powoduje proste błędy lub błędy ogólne.

Z tego powodu przed rozpoczęciem rozwiązywania problemów należy upewnić się, że wszystkie wymagania wstępne w aplikacji [KCD SSO przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md) zostały spełnione. Zanotuj sekcję dotyczącą konfigurowania delegowania z ograniczeniami protokołu Kerberos w 2012R2. Ten proces wykorzystuje inne podejście do konfigurowania KCD w poprzednich wersjach systemu Windows. Należy również pamiętać o następujących kwestiach:

- Nie jest rzadkością, że serwer członkowski domeny otwiera okno dialogowe bezpiecznego kanału z określonym kontrolerem domeny. Następnie serwer może przejść do innego okna dialogowego w danym momencie. Hosty łączników nie są ograniczone do komunikacji tylko z określonymi kontrolerami domeny lokacji lokalnej.
- Scenariusze między domenami opierają się na odwołaniach, które kierują hosta łącznika do kontrolerów domeny, które mogą znajdować się poza obwodem sieci lokalnej. W takich przypadkach równie ważne jest również wysyłanie ruchu dalej do kontrolerów domeny, które reprezentują inne odpowiednie domeny. Jeśli nie, delegowanie nie powiedzie się.
- W miarę możliwości należy unikać umieszczania aktywnych urządzeń IPS lub IDS między hostami łączników i kontrolerami domeny. Urządzenia te są czasami zbyt inwazyjne i zakłócają podstawowy ruch RPC.

Przetestuj delegowanie w prostych scenariuszach. Im więcej zmiennych wprowadzisz, tym więcej będziesz musiał zmierzyć się z. Aby zaoszczędzić czas, ogranicz testowanie do jednego łącznika. Dodaj dodatkowe łączniki po rozwiązaniu problemu.

Niektóre czynniki środowiskowe mogą również przyczynić się do problemu. Aby uniknąć tych czynników, należy zminimalizować architekturę jak najwięcej podczas testowania. Na przykład nieprawidłowo skonfigurowane listy ACL zapory wewnętrznej są wspólne. Jeśli to możliwe, wyślij cały ruch ze łącznika bezpośrednio do kontrolerów domeny i aplikacji zaplecza.

Najlepsze miejsce do pozycjonować łączniki jest jak najbliżej ich celów. Zapora, która siedzi wbudowana podczas testowania dodaje niepotrzebnej złożoności i może przedłużyć badania.

Co pokazuje problem KCD? Istnieje kilka typowych wskazań, że KCD SSO nie działa. Pierwsze oznaki problemu pojawiają się w przeglądarce.

![Przykład: Niepoprawny błąd konfiguracji KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Przykład: Autoryzacja nie powiodła się z powodu brakujących uprawnień](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Oba te obrazy przedstawiają ten sam objaw: błąd jednopisu. Odmowa dostępu użytkownika do aplikacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Sposób rozwiązywania problemów zależy od problemu i obserwowanych objawów. Zanim przejdziesz dalej, zapoznaj się z poniższami artykułami. Dostarczają one przydatnych informacji na temat rozwiązywania problemów:

- [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md)
- [Błędy i objawy protokołu Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Praca z identyfikatorem SSO, gdy tożsamości lokalne i tożsamości w chmurze nie są identyczne](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Jeśli doszedłeś do tego punktu, to twój główny problem istnieje. Aby rozpocząć, należy oddzielić przepływ do następujących trzech etapów, które można rozwiązać.

### <a name="client-pre-authentication"></a>Wstępne uwierzytelnianie klienta

Użytkownik zewnętrzny uwierzytelniający się na platformie Azure za pośrednictwem przeglądarki. Możliwość wstępnego uwierzytelnienia na platformie Azure jest niezbędna do działania funkcji SSO KCD. Przetestuj i rozwiąj tę możliwość, jeśli wystąpią jakiekolwiek problemy. Etap wstępnego uwierzytelniania nie jest związany z KCD ani opublikowaną aplikacją. Łatwo jest poprawić wszelkie rozbieżności, sprawdzając, czy konto podmiotu istnieje na platformie Azure. Sprawdź również, czy nie jest wyłączona lub zablokowana. Odpowiedź na błąd w przeglądarce jest wystarczająco opisowa, aby wyjaśnić przyczynę. Jeśli nie masz pewności, sprawdź inne artykuły firmy Microsoft dotyczące rozwiązywania problemów, aby zweryfikować.

### <a name="delegation-service"></a>Służba delegowania

Łącznik serwera proxy platformy Azure, który pobiera bilet usługi Kerberos dla użytkowników z Centrum dystrybucji kluczy Kerberos (KCD).

Komunikacja zewnętrzna między klientem a frontona platformy Azure nie ma wpływu na KCD. Te komunikaty tylko upewnij się, że KCD działa. Usługa azure proxy jest podana prawidłowy identyfikator użytkownika, który jest używany do uzyskania biletu Kerberos. Bez tego identyfikatora KCD nie jest możliwe i kończy się niepowodzeniem.

Jak wspomniano wcześniej, komunikaty o błędach przeglądarki zawiera kilka dobrych wskazówek na temat dlaczego rzeczy nie. Pamiętaj, aby zanotować identyfikator działania i sygnaturę czasową w odpowiedzi. Te informacje pomagają skorelować zachowanie z rzeczywistymi zdarzeniami w dzienniku zdarzeń usługi Azure Proxy.

![Przykład: Niepoprawny błąd konfiguracji KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Odpowiednie wpisy widoczne w dzienniku zdarzeń są wyświetlane jako zdarzenia 13019 lub 12027. Znajdź dzienniki zdarzeń łącznika w **dziennikach** &gt; aplikacji i usług **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **Admin**.

![Zdarzenie 13019 z dziennika zdarzeń serwera proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Zdarzenie 12027 z dziennika zdarzeń serwera proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Użyj rekordu **A** w wewnętrznym systemie DNS dla adresu aplikacji, a nie **CName**.
1. Ponownie potwierdz, że host łącznika otrzymał prawo do delegowania do nazwy SPN wyznaczonego konta docelowego. Ponownie potwierdz, że wybrano **dowolny protokół uwierzytelniania.** Aby uzyskać więcej informacji, zobacz [artykuł konfiguracji logowania przyusznikowego](application-proxy-configure-single-sign-on-with-kcd.md).
1. Sprawdź, czy istnieje tylko jedno wystąpienie nazwy SPN w usłudze Azure AD. Problem `setspn -x` z wiersza polecenia na dowolnym hoście członka domeny.
1. Sprawdź, czy zasady domeny są wymuszane, co ogranicza [maksymalny rozmiar wystawionych tokenów Protokołu Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Ta zasada zatrzymuje łącznika z coraz token, jeśli okaże się, że nadmierne.

Śledzenia sieci, który przechwytuje wymiany między hosta łącznika i domeny KDC jest następnym najlepszym krokiem, aby uzyskać więcej szczegółów niskiego poziomu w tych problemach. Aby uzyskać więcej informacji, zobacz [papier rozwiązywania problemów z głębokim nurkowaniem](https://aka.ms/proxytshootpaper).

Jeśli ticketing wygląda dobrze, w dziennikach zostanie wyświetlone zdarzenie stwierdzające, że uwierzytelnianie nie powiodło się, ponieważ aplikacja zwróciła 401. To zdarzenie wskazuje, że aplikacja docelowa odrzuciła bilet. Przejdź do następnego etapu.

### <a name="target-application"></a>Aplikacja docelowa

Konsument biletu Protokołu Kerberos dostarczonego przez łącznik. Na tym etapie należy oczekiwać, że łącznik wysłał bilet usługi Kerberos do zaplecza. Ten bilet jest nagłówkiem w pierwszym żądaniu aplikacji.

1. Korzystając z wewnętrznego adresu URL aplikacji zdefiniowanego w portalu, sprawdź, czy aplikacja jest dostępna bezpośrednio z przeglądarki na hoście łącznika. Następnie możesz zalogować się pomyślnie. Szczegóły można znaleźć na stronie rozwiązywanie problemów z **łącznikiem.**
1. Nadal na hoście łącznika upewnij się, że uwierzytelnianie między przeglądarką a aplikacją używa protokołu Kerberos. Wykonaj jedno z następujących działań:
1. Uruchom devtools **(F12**) w programie Internet Explorer lub użyj [fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hosta łącznika. Przejdź do aplikacji przy użyciu wewnętrznego adresu URL. Sprawdź oferowane nagłówki autoryzacji WWW zwrócone w odpowiedzi z aplikacji, aby upewnić się, że negocjuje lub Kerberos jest obecny.

   - Następny obiekt blob Protokołu Kerberos, który jest zwracany w odpowiedzi z przeglądarki do aplikacji rozpoczyna się od **YII**. Te litery informują, że protokół Kerberos jest uruchomiony. Microsoft NT LAN Manager (NTLM), z drugiej strony, zawsze zaczyna się **od TlRMTVNTUAAB**, który odczytuje NTLM Security Support Provider (NTLMSSP) po dekodowane z Base64. Jeśli na początku obiektu blob zostanie wyświetlony **tlRMTVNTUAAB,** protokół Kerberos jest niedostępny. Jeśli nie widzisz **TlRMTVNTUAAB**, Kerberos jest prawdopodobnie dostępny.

      > [!NOTE]
      > Jeśli używasz Fiddler, ta metoda wymaga tymczasowego wyłączenia rozszerzonej ochrony w konfiguracji aplikacji w usługach IIS.

      ![Okno inspekcji sieci przeglądarki](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Obiekt blob na tej ilustracji nie zaczyna się od **TIRMTVNTUAAB**. Tak więc w tym przykładzie protokół Kerberos jest dostępny, a obiekt blob Protokołu Kerberos nie zaczyna się od **YII**.

1. Tymczasowo usuń ntlm z listy dostawców w witrynie usług IIS. Dostęp do aplikacji bezpośrednio z programu Internet Explorer na hoście łącznika. NTLM nie jest już na liście dostawców. Dostęp do aplikacji można uzyskać tylko przy użyciu protokołu Kerberos. Jeśli dostęp nie powiedzie się, może to być problem z konfiguracją aplikacji. Uwierzytelnianie Kerberos nie działa.

   - Jeśli protokół Kerberos nie jest dostępny, sprawdź ustawienia uwierzytelniania aplikacji w usługach IIS. Upewnij **się,** że negotiate jest wymieniony u góry, z NTLM tuż pod nim. Jeśli widzisz **nie negocjuj**, **Kerberos lub Negocjuj**lub **PKU2U**, kontynuuj tylko wtedy, gdy protokół Kerberos działa.

     ![Dostawcy uwierzytelniania systemu Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Po umieszczeniu protokołu Kerberos i NTLM tymczasowo wyłączanie uwierzytelniania wstępnego dla aplikacji w portalu. Spróbuj uzyskać do niego dostęp z Internetu za pomocą zewnętrznego adresu URL. Zostanie wyświetlony monit o uwierzytelnienie. Możesz to zrobić za pomocą tego samego konta, które jest używane w poprzednim kroku. Jeśli nie, występuje problem z aplikacją zaplecza, a nie KCD.
   - Ponownie włącz wstępne uwierzytelnianie w portalu. Uwierzytelnij się za pośrednictwem platformy Azure, próbując połączyć się z aplikacją za pośrednictwem zewnętrznego adresu URL. Jeśli logowanie sypowe nie powiedzie się, w przeglądarce zostanie wyświetlony zabroniony komunikat o błędzie i zdarzenie 13022 w dzienniku:

     *Łącznik serwera proxy aplikacji AAD firmy Microsoft nie może uwierzytelnić użytkownika, ponieważ serwer wewnętrznej bazy danych odpowiada na próby uwierzytelnienia Kerberos z błędem HTTP 401.*

      ![Pokazuje błąd zabroniony HTTTP 401](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Sprawdź aplikację usług IIS. Upewnij się, że skonfigurowana pula aplikacji i spn są skonfigurowane do używania tego samego konta w usłudze Azure AD. Nawigacja w programach IIS, jak pokazano na poniższej ilustracji:

      ![Okno konfiguracji aplikacji usług IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Po poznaniu tożsamości upewnij się, że to konto jest skonfigurowane z daną spn. Może to być na przykład `setspn –q http/spn.wacketywack.com`. Wprowadź następujący tekst w wierszu polecenia:

      ![Pokazuje okno polecenia SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Sprawdź nazwę SPN zdefiniowaną w ustawieniach aplikacji w portalu. Upewnij się, że ten sam spn skonfigurowany względem docelowego konta usługi Azure AD jest używany przez pulę aplikacji.

      ![Konfiguracja nazwy SPN w witrynie Azure portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Przejdź do usług IIS i wybierz opcję **Edytor konfiguracji** dla aplikacji. Przejdź do **pliku system.webServer/security/authentication/windowsAuthentication**. Upewnij się, że wartość **UseAppPoolCredentials** jest **true**.

      ![Opcja poświadczeń aplikacji konfiguracji usług IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Zmień tę wartość na **True**. Usuń wszystkie buforowane bilety protokołu Kerberos z serwera zaplecza, uruchamiając następujące polecenie:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Aby uzyskać więcej informacji, zobacz [Czyszczenie pamięci podręcznej biletów klienta Protokołu Kerberos dla wszystkich sesji](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Jeśli tryb jądra zostanie włączony, zwiększy wydajność operacji protokołu Kerberos. Ale powoduje również, że bilet dla żądanej usługi do odszyfrowania przy użyciu konta komputera. To konto jest również nazywane systemem lokalnym. Ustaw tę wartość **true,** aby przerwać KCD, gdy aplikacja jest hostowana na więcej niż jednym serwerze w farmie.

- Jako dodatkową kontrolę wyłącz również ochronę **rozszerzoną.** W niektórych scenariuszach **ochrona rozszerzona** złamał KCD, gdy została włączona w określonych konfiguracjach. W takich przypadkach aplikacja została opublikowana jako podfolder domyślnej witryny sieci Web. Ta aplikacja jest skonfigurowana tylko do uwierzytelniania anonimowego. Wszystkie okna dialogowe są wyszarzone, co sugeruje, że obiekty podrzędne nie dziedziczą żadnych aktywnych ustawień. Zalecamy przetestowanie, ale nie zapomnij przywrócić tej wartości do **włączonej**, jeśli to możliwe.

  Ta dodatkowa kontrola umożliwia korzystanie z opublikowanej aplikacji. Można rozkręcić dodatkowe łączniki, które są również skonfigurowane do delegowania. Aby uzyskać więcej informacji, przeczytaj bardziej szczegółowe informacje techniczne— [rozwiązywanie problemów z serwerem proxy aplikacji usługi Azure AD](https://aka.ms/proxytshootpaper).

Jeśli nadal nie możesz poczynić postępów, pomoc techniczna firmy Microsoft może Ci pomóc. Utwórz bilet pomocy technicznej bezpośrednio w portalu. Skontaktuje się z Tobą inżynier.

## <a name="other-scenarios"></a>Inne scenariusze

- Serwer proxy aplikacji platformy Azure żąda biletu Protokołu Kerberos przed wysłaniem żądania do aplikacji. Niektóre aplikacje innych firm nie lubią tej metody uwierzytelniania. Aplikacje te oczekują, że będą toczyć bardziej konwencjonalne negocjacje. Pierwsze żądanie jest anonimowe, co umożliwia aplikacji odpowiadać za pomocą typów uwierzytelniania, które obsługuje za pośrednictwem 401.
- Uwierzytelnianie wieloskokowe jest często używane w scenariuszach, w których aplikacja jest warstwowa, z zaplecza i frontonu, gdzie oba wymagają uwierzytelniania, takich jak SQL Server Reporting Services. Aby skonfigurować scenariusz z wieloma przeskokami, zobacz artykuł pomocy technicznej [Kerberos Constrained Delegation may require Protocol Transition in Multi-hop Scenarios](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Następne kroki

[Skonfiguruj KCD w domenie zarządzanej](../../active-directory-domain-services/deploy-kcd.md).
