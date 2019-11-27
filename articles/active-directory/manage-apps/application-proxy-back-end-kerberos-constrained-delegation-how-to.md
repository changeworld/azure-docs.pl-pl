---
title: Rozwiązywanie problemów z ograniczonym delegowaniem protokołu Kerberos — serwer proxy aplikacji
description: Rozwiązywanie problemów z konfiguracji ograniczonego delegowania protokołu Kerberos dla serwera Proxy aplikacji
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275694"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Rozwiązywanie problemów z konfiguracjami delegowanie ograniczone protokołu Kerberos dla serwera Proxy aplikacji

Metody dostępne dla osiągnięcia logowania jednokrotnego do opublikowanych aplikacji mogą się różnić od jednej aplikacji do innej. Jedną z opcji, serwer Proxy aplikacji usługi Azure Active Directory (Azure AD) oferuje domyślnie jest ograniczone delegowanie protokołu Kerberos (KCD). Można skonfigurować łącznik dla użytkowników, należy uruchomić uwierzytelnianie Kerberos ograniczone do aplikacji zaplecza.

Procedura włączania ograniczonego delegowania protokołu Kerberos jest bardzo proste. Wymaga to nie więcej niż ogólna wiedza o różnych składników i przepływ uwierzytelniania, która obsługuje logowanie Jednokrotne. Jednak czasami Usługa rejestracji Jednokrotnej ograniczonego delegowania protokołu Kerberos nie działa zgodnie z oczekiwaniami. Konieczne jest dobre źródła informacji pomocnych w rozwiązywaniu tych scenariuszy.

Ten artykuł zawiera pojedynczy punkt odniesienia, który ułatwia rozwiązywanie problemów i samodzielną korektę niektórych typowych problemów. Obejmuje ona również diagnostyki bardziej złożonych problemów implementacji.

W tym artykule sprawia, że następujące założenia:

- Wdrożenie usługi Azure serwer proxy aplikacji usługi Azure AD na [wprowadzenie z serwerem proxy aplikacji](application-proxy-add-on-premises-application.md) i ogólny dostęp do aplikacji innych niż KCD działają zgodnie z oczekiwaniami.
- Aplikacja docelowa opublikowanych opiera się na Internet Information Services (IIS) i przez firmę Microsoft implementacją protokołu Kerberos.
- W ramach jednej domeny usługi Azure Active Directory znajdują się hosty serwera i aplikacji. Aby uzyskać szczegółowe informacje na temat scenariuszy obejmujących wiele domen i lasów, zobacz [oficjalny dokument KCD](https://aka.ms/KCDPaper).
- Aplikacja podmiotu została opublikowana na platformie Azure dzierżawy przy użyciu wstępnego uwierzytelniania włączone. Użytkownicy oczekują uwierzytelniania na platformie Azure przy użyciu uwierzytelniania opartego na formularzach. Scenariusze uwierzytelniania klienta rozbudowane nie są uwzględnione w tym artykule. One może zostać dodane w pewnym momencie w przyszłości.

## <a name="prerequisites"></a>Wymagania wstępne

Serwer Proxy aplikacji usługi Azure AD można wdrożyć na wiele rodzajów środowiskach produkcji wstępnej lub infrastruktury. Architektury różnią się od organizacji. Najczęstszymi przyczynami problemów związanych z ograniczonego delegowania protokołu Kerberos nie ma środowiska. Proste błędów konfiguracji lub ogólne błędy powodować większości problemów.

Z tego powodu najlepiej upewnić się, że zostały spełnione wszystkie wymagania wstępne dotyczące [korzystania z logowania JEDNOkrotnego KCD z serwerem proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md) przed rozpoczęciem rozwiązywania problemów. Należy pamiętać, sekcję na temat konfigurowania ograniczonego delegowania protokołu Kerberos w 2012 R2. Ten proces wykorzystuje innego podejścia do konfigurowania ograniczonego delegowania protokołu Kerberos w poprzednich wersjach systemu Windows. Ponadto można je na uwadze następujące kwestie:

- Nie jest niczym niezwykłym serwerem członkowskim domeny otworzyć okno dialogowe z bezpiecznego kanału z określonego kontrolera domeny (DC). Następnie serwer może przenieść do innego okna dialogowego w danym momencie. Dlatego hosty łącznika nie są ograniczone do komunikacji z lokacją lokalną tylko określone kontrolery domeny.
- Scenariusze międzydomenowe polegają na odwołań, które kierują hostem łącznika do kontrolerów domeny, które mogą być spoza obwodu sieci lokalnej. W takich przypadkach jest równie ważne również przesyłać dane wartości do kontrolerów domeny, które reprezentują w innych domenach. W przeciwnym razie delegowanie nie powiedzie się.
- Jeśli to możliwe, unikaj wprowadzania żadnych aktywnych urządzeń lub Identyfikatory adresów IP między hostami łącznika i kontrolery domeny. Te urządzenia są czasami zbyt inwazyjne i zakłócają podstawowy ruch RPC.

Test delegowania w prostych scenariuszy. Więcej zmiennych, które można wprowadzić, tym bardziej Niewykluczone, że będą konkurować o. Aby zaoszczędzić czas, Ogranicz testowanie do pojedynczego łącznika. Po ten problem został rozwiązany, należy dodać dodatkowe łączniki.

Niektóre środowiska mogą również czynnikach, wystąpił problem. Aby uniknąć tych czynników, zminimalizować architektury jak najszerzej podczas badań. Na przykład nieprawidłowo Zapora wewnętrznej listy ACL są wspólne. Jeśli to możliwe Wyślij cały ruch z łącznika bezpośrednio za pośrednictwem kontrolerów domeny i serwer zaplecza w aplikacji.

Najlepszym miejscem do łączników pozycji jest możliwie najbliżej ich celów. Zaporą, która znajduje się wbudowany podczas testowania zwiększa złożoność zbędne i można przedłużyć czas swoje badania.

Co zawiera problem ograniczonego delegowania protokołu Kerberos? Istnieje kilka typowych wskazania, że usługa rejestracji Jednokrotnej ograniczonego delegowania protokołu Kerberos, kończy się niepowodzeniem. Pierwszy objawy problemu są wyświetlane w przeglądarce.

![Przykład: nieprawidłowy błąd konfiguracji KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Przykład: Autoryzacja nie powiodła się z powodu brakujących uprawnień](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Pokaż obie te obrazy objawem tego samego: błąd rejestracji Jednokrotnej. Odmowa dostępu użytkownika do aplikacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jak rozwiązywać zależy od tego, problem i objawy, którą możesz obserwować. Przed przejściem do dowolnego narzędzia, zapoznaj się z następującymi artykułami. Zapewniają one przydatne informacje dotyczące rozwiązywania problemów:

- [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md)
- [Błędy i objawy protokołu Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Praca z logowaniem jednokrotnym w przypadku tożsamości lokalnych i w chmurze nie jest identyczna](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Jeśli masz z tym punktem głównego problemu istnieje. Aby rozpocząć, należy oddzielić flow w następujących trzech etapów, które można rozwiązać.

### <a name="client-pre-authentication"></a>Wstępne uwierzytelnienie klienta

Użytkownik zewnętrzny uwierzytelnianie na platformie Azure za pośrednictwem przeglądarki. Możliwość wstępnego uwierzytelniania na platformie Azure jest niezbędne do rejestracji Jednokrotnej ograniczonego delegowania protokołu Kerberos do funkcji. Przetestuj i adres tę możliwość, jeśli występują problemy. Etap uwierzytelniania wstępnego nie jest związana z ograniczonego delegowania protokołu Kerberos lub opublikowanej aplikacji. To proste naprawić niezgodności, sprawdzając poprawnością, które istnieje konto podmiotu na platformie Azure. Ponadto upewnij się, że nie jest wyłączone lub zablokowane. Odpowiedzi na błąd w przeglądarce jest opisowy wyjaśniający przyczynę. Jeśli nie jesteś pewien, sprawdź inne artykuły dotyczące rozwiązywania problemów firmy Microsoft, aby sprawdzić.

### <a name="delegation-service"></a>Delegowanie usługi

Łącznik serwera Proxy usługi Azure, który pobiera biletu usługi Kerberos dla użytkowników z protokołu Kerberos klucza dystrybucji Center (ograniczonego delegowania protokołu Kerberos).

Zewnętrzne komunikacji między klientem a Azure frontonu nie ma wpływu na ograniczonego delegowania protokołu Kerberos. Komunikacja tylko upewnij się, że ograniczonego delegowania protokołu Kerberos, działa. Usługa serwera Proxy usługi Azure podano prawidłowy identyfikator użytkownika, który służy do pobrania biletu protokołu Kerberos. Bez tego Identyfikatora ograniczonego delegowania protokołu Kerberos nie jest możliwe, a nie powiedzie się.

Jak wspomniano wcześniej, komunikaty o błędach przeglądarki zapewnia pewne dobre wskazówek dotyczących Dlaczego rzeczy kończy się niepowodzeniem. Koniecznie Zanotuj identyfikator działania i sygnatura czasowa w odpowiedzi. Te informacje pomagają korelować zachowania rzeczywiste zdarzenia w dzienniku zdarzeń serwera Proxy usługi Azure.

![Przykład: nieprawidłowy błąd konfiguracji KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Odpowiednie wpisy w dzienniku zdarzeń są wyświetlane jako zdarzenia 13019 lub 12027. Znajdź dzienniki zdarzeń łącznika w **dziennikach aplikacji i usług** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **administrator**.

![Zdarzenie 13019 z dziennika zdarzeń serwera Proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Zdarzenie 12027 z dziennika zdarzeń serwera Proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Użyj rekordu **A** w wewnętrznym systemie DNS dla adresu aplikacji, a nie **CNAME**.
1. Potwierdź, czy host łącznika zostały przyznane uprawnienia do delegowania do nazwy SPN wyznaczonym docelowe konto. Upewnij się, że wybrano **opcja Użyj dowolnego protokołu uwierzytelniania** . Aby uzyskać więcej informacji, zobacz [artykuł Konfiguracja logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md).
1. Sprawdź, czy jest tylko jedno wystąpienie nazwy SPN istnieje w usłudze Azure AD. Problem `setspn -x` z wiersza polecenia na dowolnym hoście należącym do domeny.
1. Sprawdź, czy zasady domeny są wymuszane, które ograniczają [Maksymalny rozmiar wystawionych tokenów Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Ta zasada zatrzymuje łącznik z uzyskanie tokenu, jeśli okaże się być nadmierne.

Śledzenie sieci, która przechwytuje wymiany między hostem łącznika i domeny Centrum dystrybucji KLUCZY jest kolejnym krokiem najlepsze, aby uzyskać więcej informacji niskiego poziomu dotyczące zagadnień. Aby uzyskać więcej informacji, zobacz [dokument głębokiego rozwiązywania problemów szczegółowe](https://aka.ms/proxytshootpaper).

Jeśli zarządzanie biletami wygląda dobrze, zobaczysz zdarzenia w dziennikach, stwierdzający, że uwierzytelnianie nie powiodło się, ponieważ aplikacja zwrócił 401. To zdarzenie oznacza, że aplikacja docelowa odrzucone biletu. Przejdź do kolejnego etapu.

### <a name="target-application"></a>Aplikacja docelowa

Użytkownik biletu protokołu Kerberos, dostarczone przez łącznik. Na tym etapie należy się spodziewać łącznika aby zostały wysłane bilet usługi protokołu Kerberos do zaplecza. Ten bilet jest nagłówkiem w pierwszego żądania aplikacji.

1. Przy użyciu wewnętrznego adresu URL aplikacji zdefiniowane w portalu, sprawdź, czy aplikacja będzie dostępna bezpośrednio z przeglądarki na hoście łącznika. Następnie można zalogować się pomyślnie. Szczegóły można znaleźć na stronie **Rozwiązywanie problemów** dotyczących łącznika.
1. Pracując nadal na hoście łącznika upewnij się, że uwierzytelnianie między przeglądarką a aplikacji przy użyciu protokołu Kerberos. Wykonaj jedną z następujących czynności:
1. Uruchom program DevTools (**F12**) w programie Internet Explorer lub Użyj [programu Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hosta łącznika. Przejdź do aplikacji przy użyciu wewnętrznego adresu URL. Zbadaj oferowane nagłówki autoryzacji WWW zwracany w odpowiedzi z aplikacji, aby upewnić się, że albo negocjowania lub protokołu Kerberos jest obecny.

   - Następny obiekt BLOB protokołu Kerberos, który jest zwracany w odpowiedzi z przeglądarki do aplikacji rozpoczyna się od **YII**. Te litery informujące, że protokół Kerberos jest uruchomiona. Z drugiej strony program Microsoft NT LAN Manager (NTLM) zawsze zaczyna się od **TlRMTVNTUAAB**, który odczytuje dostawcę obsługi zabezpieczeń NTLM (NTLMSSP) podczas dekodowania z poziomu algorytmu Base64. Jeśli na początku obiektu BLOB widzisz **TlRMTVNTUAAB** , protokół Kerberos nie jest dostępny. Jeśli nie widzisz **TlRMTVNTUAAB**, prawdopodobnie jest dostępny protokół Kerberos.

      > [!NOTE]
      > Jeśli używasz programu Fiddler, ta metoda wymaga tymczasowo wyłączyć ochrony rozszerzonej konfiguracji aplikacji w usługach IIS.

      ![Inspekcja sieci przeglądarce](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Obiekt BLOB na tym rysunku nie zaczyna się od **TIRMTVNTUAAB**. W tym przykładzie jest dostępny protokół Kerberos, a obiekt BLOB protokołu Kerberos nie zaczyna się od **YII**.

1. Tymczasowe usunięcie NTLM, z listy dostawców w lokacji usług IIS. Dostęp do aplikacji bezpośrednio z programu Internet Explorer na hoście łącznika. Uwierzytelnianie NTLM nie jest już na liście dostawców. Aby dostęp do aplikacji, tylko przy użyciu protokołu Kerberos. Jeśli występuje błąd dostępu, może to być problem z konfiguracją aplikacji. Uwierzytelnianie Kerberos nie działa.

   - Jeśli protokół Kerberos nie jest dostępny, sprawdź ustawienia uwierzytelniania aplikacji w usługach IIS. Upewnij się, że w górnej części znajduje się wartość **Negocjuj** z uwierzytelnianiem NTLM tuż poniżej. Jeśli widzisz **nie Negocjuj**, **Kerberos lub Negotiate**lub **protokołu PKU2U**, Kontynuuj tylko wtedy, gdy protokół Kerberos jest funkcjonalny.

     ![Dostawcy uwierzytelniania Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Przy użyciu protokołu Kerberos i NTLM w miejscu tymczasowo wyłączyć wstępnego uwierzytelniania dla aplikacji w portalu. Spróbuj uzyskać do niego dostęp z Internetu za pomocą zewnętrznego adresu URL. Zostanie wyświetlony monit uwierzytelnienia. Możesz to zrobić za pomocą tego samego konta, które są używane w poprzednim kroku. W przeciwnym razie występuje problem z aplikacją zaplecza, nie ograniczonego delegowania protokołu Kerberos.
   - Włącz ponownie wstępnego uwierzytelniania w portalu. Uwierzytelnianie za pośrednictwem platformy Azure, próbując nawiązać połączenia z aplikacji za pośrednictwem jego zewnętrznego adresu URL. Jeśli logowania jednokrotnego nie powiedzie się, zostanie wyświetlony błąd "niedozwolone" wiadomości w przeglądarce i zdarzenie 13022 w dzienniku:

     *Łącznik serwera proxy aplikacji usługi Microsoft AAD nie może uwierzytelnić użytkownika, ponieważ serwer zaplecza odpowiada na próby uwierzytelniania Kerberos z powodu błędu HTTP 401.*

      ![Pokazuje HTTTP 401 — Błąd zabroniony](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Sprawdź aplikacji usług IIS. Upewnij się, że pulę skonfigurowaną aplikację i nazwy SPN są skonfigurowane do używania tego samego konta w usłudze Azure AD. Przejdź w usługach IIS, jak pokazano na poniższej ilustracji:

      ![Okno konfiguracji aplikacji usług IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Po określeniu tożsamości, upewnij się, że to konto jest skonfigurowane przy użyciu tej nazwy SPN. Może to być na przykład `setspn –q http/spn.wacketywack.com`. Wprowadź następujący tekst w wierszu polecenia:

      ![Pokazuje okno poleceń SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Sprawdź nazwę SPN, zdefiniowane w odniesieniu do ustawień aplikacji w portalu. Upewnij się, że tej samej nazwy SPN skonfigurowane w odniesieniu do obiektu docelowego konta usługi Azure AD jest używany przez puli aplikacji.

      ![Konfiguracja główna nazwa usługi w witrynie Azure portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Przejdź do usług IIS i wybierz opcję **Edytor konfiguracji** dla aplikacji. Przejdź do **System. WebServer/Security/Authentication/WindowsAuthentication**. Upewnij się, że wartość **UseAppPoolCredentials** ma wartość **true**.

      ![Pule aplikacji programu IIS konfiguracji poświadczeń — opcja](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Zmień tę wartość na **true**. Usuń wszystkich buforowanych biletów Kerberos z serwerów zaplecza, uruchamiając następujące polecenie:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Aby uzyskać więcej informacji, zobacz [przeczyszczanie pamięci podręcznej biletu klienta protokołu Kerberos dla wszystkich sesji](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Jeśli pozostanie włączony tryb jądra, zwiększa wydajność operacji protokołu Kerberos. Jednak powoduje także biletu dla żądanej usługi, która ma zostać odszyfrowany za pomocą konta komputera. To konto jest również nazywany systemu lokalnego. Ustaw tę wartość na **true** , aby przerwać KCD, gdy aplikacja jest hostowana na więcej niż jednym serwerze w farmie.

- Jako dodatkowe sprawdzenie Wyłącz również ochronę **rozszerzoną** . W niektórych scenariuszach **rozszerzona** ochrona została złamana KCD, gdy została włączona w określonych konfiguracjach. W takich przypadkach aplikacja została opublikowana jako podfolder domyślnej witryny sieci Web. Ta aplikacja jest skonfigurowana tylko uwierzytelniania anonimowego. Okna dialogowe są wyszarzone, co sugeruje, że obiekty podrzędne nie dziedziczą wszystkie aktywne ustawienia. Zalecamy przetestowanie, ale nie zapomni **, aby przywrócić tę wartość, jeśli**jest to możliwe.

  Tego wyboru dodatkową umieszcza na drodze do korzystania z opublikowanych aplikacji. Można zawsze wdrożyć dodatkowe łączniki, które również są skonfigurowane do delegowania. Aby uzyskać więcej informacji, zapoznaj się z bardziej szczegółowym opisem technicznym, [Rozwiązywanie problemów z usługą Azure serwer proxy aplikacji usługi Azure AD](https://aka.ms/proxytshootpaper).

Jeśli nadal nie możesz postępu, pomocy technicznej firmy Microsoft mogą pomóc. Utwórz bilet pomocy technicznej bezpośrednio z poziomu portalu. Inżynier się z Tobą.

## <a name="other-scenarios"></a>Inne scenariusze

- Serwer Proxy aplikacji usługi Azure żąda biletu protokołu Kerberos przed wysłaniem jej żądania do aplikacji. Niektóre aplikacje innych firm nie są podobne do tej metody uwierzytelniania. Te aplikacje oczekiwać bardziej konwencjonalne negocjacji została wykonana. Pierwsze żądanie jest anonimowe, co pozwala aplikacji na odpowiedź z typami uwierzytelniania, obsługuje on za pośrednictwem 401.
- Uwierzytelniania z wieloma przeskokami są często używane w scenariuszach gdzie warstwy aplikacji, za pomocą zaplecza i frontonu, gdy oba wymagają uwierzytelniania, takiego jak SQL Server Reporting Services. Aby skonfigurować scenariusz wielodostępny, zobacz artykuł pomocy technicznej [ograniczone delegowanie protokołu Kerberos może wymagać przejścia do protokołu w scenariuszach z przeskokiem](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Następne kroki

[Skonfiguruj KCD w domenie zarządzanej](../../active-directory-domain-services/deploy-kcd.md).
