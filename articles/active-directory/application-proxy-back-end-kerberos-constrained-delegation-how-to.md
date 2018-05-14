---
title: Rozwiązywanie problemów z protokołu Kerberos ograniczonego delegowania konfiguracji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z protokołu Kerberos ograniczonego delegowania konfiguracji serwera Proxy aplikacji.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: harshja
ms.openlocfilehash: 3ba089123198631c443a759ad62cb0ae5ca40ad3
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Rozwiązywanie problemów z protokołu Kerberos ograniczonego delegowania konfiguracji serwera Proxy aplikacji

Do osiągnięcia logowania jednokrotnego do aplikacji opublikowanych może nieco różnić się od jednej aplikacji do innej metody. Jedną z opcji, które serwer Proxy aplikacji Azure oferuje domyślnie jest delegowanie ograniczone protokołu Kerberos (KCD). Możesz skonfigurować łącznik przeprowadzać ograniczone uwierzytelnienia Kerberos do wewnętrznej bazy danych aplikacji, w imieniu użytkowników.

Procedura włączania KCD jest stosunkowo prosta i zwykle wymaga nie więcej niż ogólną wiedzą z różnymi składnikami i przebieg uwierzytelniania, która ułatwia obsługę logowania jednokrotnego. Znalezienie dobre źródła informacje ułatwiające rozwiązywanie problemów z scenariuszy, gdzie KCD logowania jednokrotnego nie działać zgodnie z oczekiwaniami, może być rozrzedzony.

W efekcie próbuje zapewnia jeden punkt odniesienia, które powinny pomóc w Rozwiązywanie problemów i własnym rozwiązania niektórych typowych problemów, które są widoczne w tym artykule. W tym samym czasie oferują dodatkowe wskazówki dotyczące diagnozowania bardziej złożoną i plików implementacji.

W tym artykule sprawia, że następujące założenia:

-   Wdrożenie serwera Proxy aplikacji Azure na [dokumentacji](manage-apps/application-proxy-enable.md) i dostępu do aplikacji z systemem innym niż KCD działa zgodnie z oczekiwaniami.

-   Aplikacja docelowa opublikowanych zależy od usług IIS i przez firmę Microsoft implementacja protokołu Kerberos.

-   Host serwera i aplikacji znajdują się w jednej domenie usługi Active Directory. Szczegółowe informacje dotyczące wielu domen i lasów scenariuszy znajdują się w [oficjalny dokument KCD](https://aka.ms/KCDPaper).

-   Aplikacja podmiotu jest publikowana na platformie Azure dzierżawcy z wstępne uwierzytelnianie jest włączone, a użytkownicy mogą uwierzytelniać na platformie Azure przy użyciu uwierzytelniania opartego na formularzach. Scenariusze uwierzytelniania klienta sformatowanego nie są objęte w tym artykule, ale można dodać w pewnym momencie w przyszłości.

## <a name="prerequisites"></a>Wymagania wstępne

Serwer Proxy aplikacji Azure można wdrożyć na wiele typów infrastruktury lub środowisk i architektur bez wątpienia różnią się od organizacji. Jedną z najbardziej typowych przyczyn problemów związanych z KCD nie są środowisk, ale zamiast prostego niewłaściwa konfiguracji lub ogólne nadzoru.

Z tego powodu najlepiej zacząć przy tym upewnić się, czy zostały spełnione wszystkie wymagania wstępne określone w [przy użyciu logowania jednokrotnego KCD z artykułem serwera Proxy aplikacji](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) przed rozpoczęciem rozwiązywania problemów.

Szczególnie sekcję na temat konfigurowania KCD na 2012 R2, jak to wykorzystuje podejście różni się w przypadku konfigurowania KCD w poprzednich wersjach systemu Windows, ale także będąc w trosce o kilka innych kwestii:

-   Nie jest często serwerem członkowskim domeny otworzyć okno dialogowe bezpiecznego kanału z określonego kontrolera domeny. Następnie przenieś innego okna dialogowego w dowolnym momencie, aby hosty łącznika nie powinny być ograniczone do możliwość komunikacji z tylko określonej lokalnej lokacji kontrolerów domeny.

-   Podobnie do poprzedniego punktu między domenami, które scenariuszy zależą od odwołań, które bezpośrednie hostem łącznika do kontrolerów domeny, które mogą znajdować się poza lokalnej sieci obwodowej. W tym scenariuszu jest równie ważne upewnij się, że są również możliwość ruchu lub nowszej do kontrolerów domeny, które reprezentują innych domen lub innego delegowania zakończyć się niepowodzeniem.

-   Jeśli to możliwe, należy unikać wprowadzania żadnych aktywnych urządzeń adresy IP/Identyfikatory między hostami łącznika i kontrolery domeny, jak te są czasami za pośrednictwem niepożądanych i zakłócić ruch RPC core

Należy przetestować delegowania w sytuacjach najprostszym. Wprowadzenie więcej zmiennych, im bardziej może być konieczne będą konkurować o. Na przykład ograniczając testowanie do pojedynczego łącznika można zapisać cenny czas i dodatkowe łączników można dodać po rozwiązaniu problemów.

Pewne czynniki środowiska również może być przyczyną problemu. Podczas testowania, zminimalizować architektury do minimum systemu od zera. Aby uniknąć tych czynnikach środowiskowych. Na przykład nie są rzadko nieprawidłowej konfiguracji zapory wewnętrznej listy kontroli dostępu, więc jeśli to możliwe mieć cały ruch z łącznika może bezpośrednio za pomocą kontrolerów domeny i wewnętrznej bazy danych aplikacji. 

Faktycznie bezwzględną najlepszym miejscem, aby umieścić łączniki zbliża się zgodnie z ich celów jako może być. Wbudowany zapory nas o podczas testowania, po prostu dodaje niepotrzebne złożoność i właśnie przedłużyć czas badania sieci.

Tak co stanowi KCD problem mimo to? Istnieją kilka wskazania klasycznego KCD sesji rejestracji jednokrotnej, które nie i pierwszy oznaki problemu zwykle pojawiają się w przeglądarce.

   ![Błąd konfiguracji KCD niepoprawne](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Autoryzacja nie powiodła się z powodu brakujących uprawnień](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

wszystkie które posiadają tej samej objaw nie może wykonać logowanie Jednokrotne i w związku z tym odmowie dostępu użytkownika do aplikacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jak rozwiązać zależy od tego, problem i obserwowanych objawów. Zanim przejdziesz dalej, zapoznaj się następujące łącza, ponieważ zawierają one przydatne informacje, które można mogą nie mieć wszedł między:

-   [Rozwiązywanie problemów z serwera Proxy aplikacji i komunikaty o błędach](active-directory-application-proxy-troubleshoot.md)

-   [Błędy protokołu Kerberos i objawy](active-directory-application-proxy-troubleshoot.md#kerberos-errors)

-   [Praca z logowania jednokrotnego podczas lokalnej i w chmurze tożsamości nie są identyczne](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Jeśli masz daleko, wówczas główny problem ostatecznie istnieje. Uruchom oddzielając przepływu w trzech etapach distinct, które można rozwiązywać problemy.

**Wstępne uwierzytelnienie klienta** — użytkownika zewnętrznego, uwierzytelnianie na platformie Azure za pośrednictwem przeglądarki.

Możliwość wstępnego uwierzytelniania na platformie Azure jest potrzeby logowania jednokrotnego KCD funkcji. Należy przetestować i to adres, jeśli występują problemy. Etap uwierzytelniania wstępnego nie ma związku KCD lub opublikowanej aplikacji. Powinny być dość łatwe naprawić niezgodności przez związane z poprawnością Sprawdzanie konta podmiot istnieje na platformie Azure i że nie jest ono wyłączone/blokowane. Odpowiedzi na błąd w przeglądarce jest dość opisowa, że aby poznać przyczynę. Nasze dokumenty rozwiązywanie można zweryfikować, jeśli nie masz pewności, możesz również sprawdzić.

**Delegowanie usługi** — łącznika serwera Proxy Azure uzyskiwanie bilet usługi protokołu Kerberos w Centrum dystrybucji KLUCZY (Centrum dystrybucji protokołu Kerberos), w imieniu użytkowników.

Zewnętrzne komunikacji między klientem a Azure frontonu powinny nie ma wpływu na KCD, inne niż zapewnienia jego działanie. Jest to tak usługę serwera Proxy Azure można podać prawidłowy identyfikator używany do uzyskania biletu Kerberos. W przeciwnym razie KCD nie jest możliwe, a nie powiedzie się.

Jak wspomniano wcześniej, komunikaty o błędach przeglądarki zwykle zawierają niektóre dobrej wskazówek Dlaczego rzeczy kończą się niepowodzeniem. Upewnij się, że notowanie niezbędnych identyfikator działania i sygnaturę czasową w odpowiedzi podczas to umożliwia korelowanie zachowania rzeczywiste zdarzenia w dzienniku zdarzeń serwera Proxy Azure.

   ![Błąd konfiguracji KCD niepoprawne](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

I odpowiednie pozycje widoczne czy dziennik zdarzeń może być traktowany jako zdarzenia 13019 lub 12027. Można znaleźć w dziennikach zdarzeń łącznika **Dzienniki aplikacji i usług** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Łącznik**&gt;**Admin**.

   ![Zdarzenie 13019 z dziennika zdarzeń serwera Proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Zdarzenie 12027 z dziennika zdarzeń serwera Proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Na użytek rekord A w systemie DNS wewnętrzny adres aplikacji, a nie rekordu CName

-   Potwierdź, że host łącznika przyznano uprawnienia do delegowania wyznaczone konto docelowe głównej nazwy usługi, a **Użyj dowolnego protokołu uwierzytelniania** jest zaznaczone. Aby uzyskać więcej informacji na ten temat, zobacz [artykułu konfiguracji logowania jednokrotnego](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

-   Sprawdź, czy jest tylko jedno wystąpienie SPN istniejących w AD przez wystawienie `setspn -x` z wiersza polecenia na żadnym hoście członek domeny

-   Sprawdź, czy zasady domeny są wymuszane, aby ograniczyć [maksymalny rozmiar wystawionych tokenów Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/), jak to zapobiega łącznik Jeśli token jest nadmierne

Wymiany między hostem łącznika i domeny Centrum dystrybucji KLUCZY przechwytywania śledzenia sieci będzie wówczas następnego kroku najlepsze w uzyskaniu więcej szczegółów niskiego poziomu na problemy. Aby uzyskać więcej informacji zobacz, [nowości papieru rozwiązywanie](https://aka.ms/proxytshootpaper).

Jeśli biletów wygląda dobrze, powinny pojawić się zdarzenia w dzienniku informujący o tym uwierzytelnianie nie powiodło się z powodu aplikacji zwracanie 401. Zwykle wskazuje, że aplikacja docelowa odrzucenia biletu, więc przejść do kolejnego etapu następujące:

**Aplikacji docelowej** -konsumenta biletu Kerberos udostępniane przez łącznik programu

Na tym etapie łącznik powinien wysłali biletu usługi Kerberos z zapleczem jako nagłówek w pierwszym żądaniu aplikacji.

-   Przy użyciu wewnętrzny adres URL aplikacji zdefiniowano w portalu, zweryfikuj, że aplikacja jest dostępny bezpośrednio z przeglądarki na hoście łącznika. Następnie możesz zalogować się pomyślnie. Szczegółowe informacje o tej czynności można znaleźć na stronie Rozwiązywanie problemów dotyczących łącznika.

-   Pracując nadal na hoście łącznika upewnij się, że uwierzytelniania między przeglądarką a aplikacja używa protokołu Kerberos, wykonując jedną z następujących czynności:

1.  Uruchamianie narzędzi dla deweloperów (**F12**) w programie Internet Explorer lub użyj [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hosta łącznika. Przejdź do aplikacji przy użyciu wewnętrznego adresu URL i sprawdzić oferowany nagłówki autoryzacji WWW zwrócił w odpowiedzi z aplikacji, aby upewnić się, że negocjowania lub występuje protokołu Kerberos. Kolejne obiektu blob Kerberos zwracany w odpowiedzi z przeglądarki do aplikacji zaczynają się zwykle **YII**, więc jest to dobry wskazanie protokołu Kerberos w play. NTLM z drugiej strony zawsze rozpoczyna się od **TlRMTVNTUAAB**, która odczytuje NTLMSSP podczas zdekodować z formatu Base64. Jeśli widzisz **TlRMTVNTUAAB** na początku obiektu blob, oznacza to, że protokół Kerberos jest **nie** dostępne. Jeśli widzisz, prawdopodobnie Kerberos dostępne.
    > [!NOTE]
    > Jeśli przy użyciu programu Fiddler, ta metoda wymaga czasowo wyłączyć ochrona rozszerzona na konfiguracji aplikacji w usługach IIS.

     ![Przeglądarce inspekcji sieci](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Rysunek:* ponieważ to nie rozpoczyna się od TIRMTVNTUAAB, jest to przykład, że protokół Kerberos jest dostępne. To jest przykład obiektu Blob protokołu Kerberos, który nie zaczyna się od YII.

2.  Z listy dostawców w lokacji i dostępu do aplikacji IIS bezpośrednio z programu Internet Explorer na hoście łącznika tymczasowe usunięcie NTLM. Z uwierzytelniania NTLM nie jest już na liście dostawców można uzyskać dostęp do aplikacji tylko przy użyciu protokołu Kerberos. Jeśli to się nie powiedzie, następnie które sugeruje, że występuje problem z konfiguracją aplikacji i uwierzytelnianie Kerberos nie działa.

Czy protokołu Kerberos nie jest dostępna, negocjowania ustawienia uwierzytelniania aplikacji w usługach IIS, aby upewnić się, czy są dostępne najwyżej z NTLM tuż poniżej. (Nie negocjowania: Kerberos lub negocjowania: protokołu PKU2U). Nadal tylko, jeśli protokół Kerberos jest funkcjonalności.

   ![Dostawców uwierzytelniania systemu Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Z protokołu Kerberos i NTLM w miejscu umożliwia teraz tymczasowo wyłączyć wstępnego uwierzytelniania dla aplikacji w portalu. Zobacz, czy można do niego dostęp z Internetu przy użyciu zewnętrznego adresu URL. Powinien być monitowani o uwierzytelnienie i powinno być możliwe w tym celu z kontem używanym w poprzednim kroku. Jeśli nie, oznacza to problem z aplikacjami wewnętrznej bazy danych i nie KCD w ogóle.

-   Teraz ponownie włączyć wstępnego uwierzytelniania w portalu i uwierzytelniania za pośrednictwem platformy Azure przez próby połączenia aplikacji za pomocą jego zewnętrznego adresu URL. Logowanie Jednokrotne nie powiodło się, należy Zobacz niedozwolonych komunikat w przeglądarce, a także zdarzenie 13022 w dzienniku:

    *Łącznik serwera Proxy aplikacji usługi Microsoft AAD nie może uwierzytelnić użytkownika, ponieważ serwer wewnętrznej bazy danych odpowiada prób uwierzytelnienia Kerberos z powodu błędu HTTP 401.*

    ![Dostęp zabroniony błąd 401 HTTTP](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Sprawdź aplikacji usług IIS, aby upewnić się, że pula aplikacji skonfigurowana jest skonfigurowany do używania tego samego konta, skonfigurowaną główną nazwę usługi przed w usłudze AD, przechodząc w usługach IIS tak jak na poniższej ilustracji

    ![Okno konfiguracji aplikacji usług IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    Jeśli znasz już tożsamości, należy wydać następujące polecenie w wierszu polecenia, aby upewnić się, że to konto jest ostatecznie skonfigurowany z tej nazwy SPN. Na przykład  `setspn –q http/spn.wacketywack.com`

    ![Okno polecenia SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Sprawdź, czy nazwa SPN zdefiniowane w odniesieniu do ustawień aplikacji w portalu jest tej samej nazwy SPN skonfigurowana przed docelowe AD konto używane przez puli aplikacji

   ![Konfiguracja SPN w portalu Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Przejdź do usług IIS i wybierz **edytora konfiguracji** opcję dla aplikacji, a następnie przejdź do **system.webServer/security/authentication/windowsAuthentication** się upewnić się, że wartość **UseAppPoolCredentials** jest **wartość True**

   ![Opcja credential pul aplikacji konfiguracji usług IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

Po zmianie wartości **True**, wszystkie buforowane Kerberos biletów muszą zostać usunięte z serwera wewnętrznej bazy danych. Można to zrobić, uruchamiając następujące polecenie:

```powershell
Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
``` 

Aby uzyskać więcej informacji, zobacz [przeczyścić pamięci podręcznej z biletu Kerberos klienta dla wszystkich sesji](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).



Gdy są przydatne w zwiększanie wydajności operacji protokołu Kerberos, pozostawiając również włączono obsługę trybu jądra powoduje, że bilet żądanej usługi można odszyfrować za pomocą konta komputera. Jest to również system lokalny, więc po to ustawioną wartość true, podziału KCD w przypadku aplikacji znajduje się na wielu serwerach w farmie.

-   Jako dodatkowe sprawdzanie może również chcesz wyłączyć **rozszerzone** ochrony zbyt. Zostały napotkano scenariuszy, w którym to okazał się break KCD po włączeniu w określonej konfiguracji, gdy aplikacja jest publikowany jako podfolderem folderu domyślnej witryny sieci Web. Ten sam jest skonfigurowany do uwierzytelniania anonimowego, pozostawiając całego okna dialogowe wygaszone co sugeruje, że obiekty podrzędne nie będzie dziedziczy wszystkie aktywne ustawienia. Jednak gdy możliwości zawsze zalecamy posiadanie to włączone, więc wszelkimi środkami testu, ale nie zapomnij Przywróć tutaj, aby włączyć.

Te dodatkowe kontrole powinny umieszczono należy na ścieżkę, aby rozpocząć korzystanie z opublikowanych aplikacji. Można teraz i aż dodatkowe łączniki, które także są skonfigurowane do delegowania, ale jeśli nie musisz kwestie następnie będzie Sugerujemy odczytu naszych bardziej szczegółowym opisem technicznym [kompletny przewodnik po dla serwera Proxy aplikacji usługi AD Rozwiązywanie problemów z platformy Azure](https://aka.ms/proxytshootpaper)

Jeśli nadal nie możesz się postęp problemu, pomocy technicznej będzie więcej niż przyjemnością pomóc i nadal w tym miejscu. Tworzenie biletu pomocy technicznej bezpośrednio z poziomu portalu (inżyniera będzie dotrzeć do użytkownika).

## <a name="other-scenarios"></a>Inne scenariusze

-   Serwer Proxy aplikacji Azure żąda biletu Kerberos przed wysłaniem żądania do aplikacji. Niektóre aplikacje innych firm, takich jak Tableau serwera nie chcesz, aby ta metoda uwierzytelniania, a raczej oczekuje więcej z konwencjonalnej negocjacji odbywa się. Pierwsze żądanie jest anonimowe, co pozwala aplikacji na odpowiedź z typami uwierzytelniania obsługuje za pośrednictwem 401.

-   Podwójny przeskok uwierzytelnianie — często używane w scenariuszach, w którym warstwy aplikacji z wewnętrznej bazy danych i front end wymagające uwierzytelniania, takich jak SQL Reporting Services.

## <a name="next-steps"></a>Kolejne kroki
[Skonfiguruj ograniczone delegowanie protokołu kerberos (KCD) do domeny zarządzanej](../active-directory-domain-services/active-directory-ds-enable-kcd.md)
