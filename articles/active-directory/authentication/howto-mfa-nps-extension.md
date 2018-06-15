---
title: Użyj istniejącego serwera NPS, aby zapewnić możliwości usługi Azure MFA | Dokumentacja firmy Microsoft
description: Dodawanie funkcji vericiation dwuetapowej oparte na chmurze do istniejącej infrastruktury uwierzytelniania
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 05/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 57bf8b81d8d7fee6eaee216b9a2e0c52aa625257
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868334"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integracja istniejącej infrastrukturze serwera NPS z usługi Azure Multi-Factor Authentication

Rozszerzenia serwera zasad sieciowych (NPS) dla usługi Azure MFA dodaje możliwości usługi MFA oparte na chmurze do uwierzytelniania przy użyciu istniejących serwerów infrastruktury. Z rozszerzeniem serwera zasad Sieciowych można dodać połączenie telefoniczne, wiadomość tekstowa lub weryfikacji aplikacji telefonicznej z istniejących przepływ uwierzytelniania bez konieczności instalowania, konfigurowania i konserwacji nowe serwery. 

To rozszerzenie został utworzony dla organizacji, które chcesz chronić połączenia sieci VPN bez wdrażania serwera usługi Azure MFA. Rozszerzenia serwera NPS działa jako karty sieciowej między usługi RADIUS i oparte na chmurze usługi Azure MFA, aby zapewnić drugi składnik uwierzytelniania federacyjnego lub zsynchronizowanych użytkowników.

W przypadku używania rozszerzenia serwera zasad Sieciowych dla usługi Azure MFA, przepływ uwierzytelniania obejmuje następujące składniki: 

1. **Serwer NAS lub sieć VPN** odbiera żądania od klientów sieci VPN i konwertuje je na żądań RADIUS do serwera NPS. 
2. **Serwer NPS** łączy do usługi Active Directory w celu przeprowadzenia podstawowego uwierzytelniania dla żądań RADIUS, a na sukces, przekazuje żądanie do wszystkich zainstalowanych rozszerzeń.  
3. **Rozszerzenia serwera NPS** wyzwala żądania do usługi Azure MFA dodatkowego uwierzytelniania. Po rozszerzenia odbiera odpowiedź, a jeśli żądanie uwierzytelniania MFA zakończy się powodzeniem, kończy żądanie uwierzytelniania podając tokeny zabezpieczające, które obejmują oświadczenia MFA na serwerze NPS, wystawiony przez usługę STS Azure.  
4. **Usługa Azure MFA** komunikuje się z usługą Azure Active Directory można pobrać szczegółów użytkownika i wykonuje dodatkowego uwierzytelniania przy użyciu metody weryfikacji skonfigurowane dla użytkownika.

Na poniższym diagramie przedstawiono ten proces żądania uwierzytelniania wysokiego poziomu: 

![Diagram przepływu uwierzytelniania](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planowanie wdrożenia

Rozszerzenia serwera zasad Sieciowych automatycznie obsługuje nadmiarowość, więc nie trzeba specjalnej konfiguracji.

Można utworzyć dowolną liczbę serwerów NPS z włączoną usługą Azure MFA. W przypadku instalowania wielu serwerów, należy użyć certyfikatu klienta różnicy dla każdego z nich. Tworzenie certyfikatu dla każdego serwera oznacza indywidualnie zaktualizować każdego certyfikatu, a nie martw się o przestoju na wszystkich serwerach.

Serwery sieci VPN trasy żądania uwierzytelniania, dlatego należy zwrócić uwagę na nowe serwery NPS z włączoną usługą Azure MFA.

## <a name="prerequisites"></a>Wymagania wstępne

Rozszerzenia serwera NPS jest przeznaczona do pracy z istniejącej infrastruktury. Upewnij się, że zostały spełnione następujące wymagania wstępne, przed rozpoczęciem.

### <a name="licenses"></a>Licencje

Rozszerzenia serwera zasad Sieciowych dla usługi Azure MFA jest dostępny dla klientów z [licencji dla usługi Azure Multi-Factor Authentication](multi-factor-authentication.md) (dołączony do usługi Azure AD Premium, EMS lub licencji autonomicznej usługi MFA). Na podstawie zużycia licencji dla usługi Azure MFA, takich jak dla każdego użytkownika lub uwierzytelnianie licencji na nie są zgodne z rozszerzeniem serwera NPS. 

### <a name="software"></a>Oprogramowanie

Windows Server 2008 R2 z dodatkiem SP1 lub nowszej.

### <a name="libraries"></a>Biblioteki

Te biblioteki są automatycznie instalowane z rozszerzeniem.

-   [Pakiety redystrybucyjne języka Visual C++ dla programu Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory modułu dla Windows PowerShell w wersji 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Microsoft Azure Active Directory modułu dla środowiska Windows PowerShell jest zainstalowany, jeśli nie jest już obecny za pomocą skryptu konfiguracji uruchomienia w ramach procesu instalacji. Nie istnieje potrzeba do zainstalowania tego modułu wcześniejsze, jeśli nie jest już zainstalowany.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Wszyscy przy użyciu rozszerzenia zasad Sieciowych musi być synchronizowane z usługi Azure Active Directory za pomocą usługi Azure AD Connect, a musi być zarejestrowana w usłudze MFA.

Po zainstalowaniu rozszerzenia należy poświadczenia identyfikator i administratora katalogu dla dzierżawy usługi Azure AD. Można znaleźć Identyfikatora katalogu w [portalu Azure](https://portal.azure.com). Zaloguj się jako administrator, wybierz opcję **usługi Azure Active Directory** ikony po lewej stronie, następnie wybierz **właściwości**. Skopiuj identyfikator GUID w **identyfikator katalogu** i zapisz go. Należy użyć tego identyfikatora GUID jako Identyfikatora dzierżawy, po zainstalowaniu rozszerzenia serwera NPS.

![Znajdź swój identyfikator katalogu we właściwościach usługi Azure Active Directory](./media/howto-mfa-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

Przed zainstalowaniem rozszerzenia serwera zasad Sieciowych, które chcesz przygotować możesz środowiska do obsługi ruchu uwierzytelniania.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Włączanie roli serwera NPS na serwerze przyłączonym do domeny

Serwer zasad Sieciowych łączy do usługi Azure Active Directory i uwierzytelnia żądania usługi MFA. Należy wybrać serwer dla tej roli. Firma Microsoft zaleca, wybierając serwer, który nie obsłuży żądania od innych usług, ponieważ rozszerzenia serwera NPS zgłasza błędów dla żądań, które nie są usługi RADIUS. Serwer zasad Sieciowych musi zostać skonfigurowany jako serwer uwierzytelniania podstawowego i pomocniczego dla danego środowiska; nie jest serwer proxy żądań RADIUS do innego serwera.

1. Na serwerze otwórz **Kreatora dodawania ról i funkcji** w menu Menedżera serwera — Szybki Start.
2. Wybierz **Instalacja roli lub funkcji** dla danego typu instalacji.
3. Wybierz **usług zasad sieciowych i dostępu** roli serwera. Okno wyskakujące może informujące o wymaganych funkcji, aby uruchomić tę rolę.
4. Kontynuuj pracę z kreatorem, aż do strony potwierdzenia. Wybierz **zainstalować**.

Teraz, gdy masz serwer wyznaczony serwera zasad sieciowych, należy także skonfigurować ten serwer do obsługi przychodzących żądań RADIUS z rozwiązania sieci VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Skonfiguruj rozwiązanie sieci VPN do komunikacji z serwerem zasad Sieciowych

W zależności od tego, które rozwiązanie sieci VPN, należy użyć różnią się kroki, aby skonfigurować zasady uwierzytelniania usługi RADIUS. Konfigurowanie tej zasady, aby wskazywały serwer usługi RADIUS serwera NPS.

### <a name="sync-domain-users-to-the-cloud"></a>Użytkownicy domeny synchronizacji w chmurze

Ten krok może już zostać ukończone na swojej dzierżawy, ale warto dokładnie sprawdzić, czy usługa Azure AD Connect został zsynchronizowany baz danych.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Wybierz **usługi Azure Active Directory** > **programu Azure AD Connect**
3. Sprawdź, czy stan synchronizacji **włączone** i który ostatniej synchronizacji był mniejszy niż godzinę temu.

Jeśli chcesz rozpocząć poza nowe round synchronizacji, nam zgodnie z instrukcjami w [synchronizacja programu Azure AD Connect: harmonogram](../connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Określić, które użytkownicy mogą używać metody uwierzytelniania

Istnieją dwa czynniki wpływające na które metody uwierzytelniania są dostępne z wdrażaniem rozszerzenia serwera NPS:

1. Algorytm szyfrowania hasła używane między klientem RADIUS (sieć VPN, serwer Netscaler lub innych) i serwerach NPS.
   - **PAP** obsługuje wszystkie metody uwierzytelniania usługi Azure MFA w chmurze: połączenie telefoniczne, wiadomość tekstowa jednokierunkowe powiadomienie aplikacji mobilnej i kod weryfikacyjny z aplikacji mobilnej.
   - **CHAPV2** i **EAP** obsługi połączeń telefonicznych i powiadomienia aplikacji mobilnej.
2. Metody wejściowe który aplikacja kliencka (VPN, serwer Netscaler lub innych) może obsłużyć. Na przykład klient sieci VPN mają niektóre oznacza Zezwalaj użytkownikowi na wpisz kod weryfikacyjny z aplikacji mobilnej lub tekst?

Podczas wdrażania rozszerzenia serwera NPS, użyj te czynniki do oceny, które metody są dostępne dla użytkowników. Jeśli klienta RADIUS obsługą protokołu PAP, ale klient UX nie ma pól wejściowych dla kod weryfikacyjny, następnie połączeń telefonicznych i powiadomienia aplikacji mobilnej są dwie opcje są obsługiwane.

Możesz [wyłączyć metody uwierzytelniania nieobsługiwany](howto-mfa-mfasettings.md#selectable-verification-methods) na platformie Azure.

### <a name="register-users-for-mfa"></a>Rejestrowanie użytkowników dla usługi MFA

Przed wdrożeniem i rozszerzenie serwera zasad Sieciowych, użytkowników, które będą wymagane do przeprowadzenia weryfikacji dwuetapowej należy zarejestrować dla usługi MFA. Więcej natychmiast po jej wdrożeniu, należy przetestować rozszerzenia, musisz mieć konto co najmniej jeden test, pełni zarejestrowany w usłudze Multi-Factor Authentication.

Uzyskanie testowe konto uruchomiona, wykonaj następujące kroki:
1. Zaloguj się do [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) przy użyciu konta testowego. 
2. Postępuj zgodnie z monitami, aby skonfigurować metodę weryfikacji.
3. Utwórz zasady dostępu warunkowego lub [zmiany stanu użytkownika](howto-mfa-userstates.md) do wymagania weryfikacji dwuetapowej dla konta testowego. 

Użytkownicy muszą wykonaj następujące kroki, aby zarejestrować przed uwierzytelniają z rozszerzeniem serwera NPS.

## <a name="install-the-nps-extension"></a>Zainstaluj rozszerzenie serwera NPS

> [!IMPORTANT]
> Zainstaluj rozszerzenie serwera NPS na innym serwerze niż punkt dostępu do sieci VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Pobierz i zainstaluj rozszerzenie serwera zasad Sieciowych dla usługi Azure MFA

1.  [Pobierz rozszerzenie serwera NPS](https://aka.ms/npsmfa) z Centrum pobierania Microsoft.
2.  Skopiuj plik binarny do serwera zasad sieciowych, które chcesz skonfigurować.
3.  Uruchom *setup.exe* i postępuj zgodnie z instrukcjami instalacji. Jeśli wystąpią błędy, dokładnie dwie biblioteki, w sekcji wymagań wstępnych zostały pomyślnie zainstalowane.

### <a name="run-the-powershell-script"></a>Uruchom skrypt programu PowerShell

Instalator tworzy skrypt programu PowerShell w tej lokalizacji: `C:\Program Files\Microsoft\AzureMfa\Config` (gdzie C:\ to dysk instalacji). Ten skrypt programu PowerShell są wykonywane następujące czynności:

-   Tworzenie certyfikatu z podpisem własnym.
-   Kojarzenie klucza publicznego certyfikatu z usługą główną w usłudze Azure AD.
-   Przechowywanie certyfikat w magazynie certyfikatów komputera lokalnego.
-   Udziel dostępu do klucza prywatnego certyfikatu użytkownika sieci.
-   Uruchom ponownie serwer NPS.

Jeśli nie chcesz używać certyfikatów (zamiast certyfikatami z podpisem własnym generowanych skrypt programu PowerShell), uruchom skrypt programu PowerShell, aby zakończyć instalację. Po zainstalowaniu rozszerzenia na wielu serwerach, każdy z nich ma własny certyfikat.

1. Uruchom program Windows PowerShell jako administrator.
2. Zmień katalog.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Uruchom skrypt programu PowerShell utworzone przez Instalatora.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Zaloguj się do usługi Azure AD jako administrator.
5. PowerShell monit o podanie identyfikatora dzierżawcy Użyj Identyfikatora GUID katalogu, który został skopiowany z portalu Azure w sekcji wymagania wstępne.
6. PowerShell wyświetla komunikat z potwierdzeniem po zakończeniu działania skryptu.  

Powtórz te czynności na wszystkie dodatkowe serwery NPS, które chcesz skonfigurować Równoważenie obciążenia sieciowego.

>[!NOTE]
>Jeśli używasz własne certyfikaty zamiast generowania certyfikatów przy użyciu skryptu programu PowerShell, upewnij się, że są wyrównane do konwencji nazewnictwa serwera NPS. Nazwa podmiotu musi być **CN =\<TenantID\>, OU = Microsoft NPS rozszerzenia**. 

## <a name="configure-your-nps-extension"></a>Skonfiguruj rozszerzenie serwera NPS

Ta sekcja obejmuje zagadnienia dotyczące projektowania oraz sugestie dotyczące pomyślnego wdrożenia rozszerzenia serwera NPS.

### <a name="configuration-limitations"></a>Ograniczenia konfiguracji

- Rozszerzenia serwera zasad Sieciowych dla usługi Azure MFA nie zawiera narzędzia do migracji użytkowników i ustawienia z serwera usługi MFA w chmurze. Z tego powodu zaleca się nowych wdrożeń zamiast istniejącego wdrożenia przy użyciu rozszerzenia. Jeśli używasz rozszerzenia na istniejące wdrożenie, użytkownicy mają do wykonania w górę dowód ponownie, aby wypełnić ich szczegóły usługi MFA w chmurze.  
- Rozszerzenia serwera zasad Sieciowych używa nazwy UPN z lokalnej usługi Active directory do identyfikacji użytkownika na usługę Azure MFA do wykonywania dodatkowej uwierzytelniania Rozszerzenie można skonfigurować do używania innego identyfikatora alternatywnego Identyfikatora logowania lub pole niestandardowe usługi Active Directory niż głównej nazwy użytkownika. Zobacz [zaawansowane opcje konfiguracji dla rozszerzenia serwera NPS uwierzytelnianie wieloskładnikowe](howto-mfaserver-nps-vpn.md) Aby uzyskać więcej informacji.
- Nie wszystkie protokoły szyfrowania obsługuje wszystkie metody weryfikacji.
   - **PAP** obsługuje połączenie telefoniczne, wiadomość tekstowa jednokierunkowe powiadomienie aplikacji mobilnej i kod weryfikacyjny z aplikacji mobilnej
   - **CHAPV2** i **EAP** obsługi połączeń telefonicznych i powiadomienia aplikacji mobilnej

### <a name="control-radius-clients-that-require-mfa"></a>Formant klientów usługi RADIUS, które wymagają usługi MFA

Po włączeniu usługi MFA dla klienta RADIUS przy użyciu rozszerzenia zasad Sieciowych wszystkie uwierzytelnienia dla tego klienta są wymagane do przeprowadzenia uwierzytelniania Wieloskładnikowego. Jeśli chcesz włączyć usługę MFA dla niektórych klientów usługi RADIUS, a innych nie można skonfigurować dwa serwery NPS i zainstaluj rozszerzenie na tylko jeden z nich. Konfigurowanie klientów usługi RADIUS, które chcesz wymagać uwierzytelniania Wieloskładnikowego w celu wysyłania żądań do serwera zasad Sieciowych skonfigurowano rozszerzenie i innych klientów RADIUS do serwera NPS nie jest skonfigurowany z rozszerzeniem.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Przygotowanie do użytkowników, którzy nie są rejestrowane w usłudze MFA

Jeśli masz użytkowników, którzy nie są rejestrowane w usłudze MFA, można określić, co się dzieje podczas próby uwierzytelnienia. Użyj ustawienia rejestru *REQUIRE_USER_MATCH* w ścieżce rejestru *HKLM\Software\Microsoft\AzureMFA* do sterowania zachowaniem funkcji. To ustawienie nie ma opcji konfiguracji pojedynczego:

| Klucz | Wartość | Domyślne |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | PRAWDA/FAŁSZ | Nieustawione (równoważne TRUE) |

Celem tego ustawienia jest ustalenie, co należy zrobić, gdy użytkownik nie jest zarejestrowane w usłudze MFA. Po klucz nie istnieje, nie jest ustawiony lub jest ustawiony na wartość TRUE, a użytkownik nie jest zarejestrowany, a następnie rozszerzenia nie powiodło się żądanie uwierzytelniania MFA. Gdy jest ustawiona na wartość FALSE, a użytkownik nie jest zarejestrowany, uwierzytelnianie będzie kontynuowane bez wykonywania MFA. Jeśli użytkownik jest zarejestrowany w MFA, muszą zostać uwierzytelnione za pomocą usługi MFA nawet wtedy, gdy REQUIRE_USER_MATCH ma wartość FALSE.

Możesz utworzyć ten klucz i ustawiona na FALSE, gdy użytkownicy są dołączania, a może nie wszystkie zarejestrowane na potrzeby usługi Azure MFA jeszcze. Jednak ponieważ ustawienie klucza pozwala na użytkowników, którzy nie są rejestrowane w usłudze MFA do logowania, należy usunąć ten klucz przed przejściem do środowiska produkcyjnego.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Jak sprawdzić, czy certyfikat klienta jest zainstalowany, zgodnie z oczekiwaniami?

Wyszukaj certyfikat z podpisem własnym utworzonych przez Instalatora w magazynie certyfikatów i sprawdź, czy klucz prywatny ma uprawnienia przyznane użytkownikowi **usługi SIECIOWEJ**. Certyfikat ma nazwę podmiotu **CN \<tenantid\>, OU = Microsoft rozszerzenia serwera NPS**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Jak sprawdzić, czy Moje certyfikatu klienta jest skojarzona z mojej dzierżawy w usłudze Azure Active Directory?

Otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenia:

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Te polecenia Drukuj wszystkie certyfikaty skojarzenie dzierżawcy z wystąpienia rozszerzenia serwera NPS w sesji programu PowerShell. Wyszukaj certyfikat przez wyeksportowanie Twojego certyfikatu klienta w formacie "X.509(.cer) algorytmem Base-64" bez klucza prywatnego i porównania go z listy z programu PowerShell.

Nieprawidłowa-z i prawidłowe-dopóki sygnatury czasowe, które są w formie czytelny dla człowieka, może służyć do odfiltrowywania misfits oczywiste, jeśli polecenie zwraca więcej niż jeden certyfikat.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Dlaczego moje żądania kończą się niepowodzeniem z błędem tokenu ADAL?

Ten błąd może wynikać z kilku powodów. Do rozwiązywania problemów, wykonaj następujące kroki:

1. Uruchom ponownie serwer NPS.
2. Sprawdź, czy ten certyfikat klienta jest zainstalowany, zgodnie z oczekiwaniami.
3. Sprawdź, czy certyfikat jest skojarzony z dzierżawą w usłudze Azure AD.
4. Sprawdź, czy https://login.microsoftonline.com/ jest dostępny z serwera z uruchomionym programem rozszerzenia.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Dlaczego uwierzytelniania nie ze względu na błąd w dziennikach HTTP z informacją, że użytkownik nie zostanie znaleziony?

Sprawdź, czy AD Connect jest uruchomiona i czy użytkownik musi być obecny w usłudze Active Directory systemu Windows i usługi Azure Active Directory.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Dlaczego widzę HTTP błędy w dziennikach Uzyskuj dostęp do mojej uwierzytelnienia się niepowodzeniem?

Sprawdź, czy https://adnotifications.windowsazure.com jest dostępny z serwera z uruchomionym programem rozszerzenia serwera NPS.


## <a name="next-steps"></a>Kolejne kroki

- Konfigurowanie alternatywnych identyfikatorów logowania lub konfigurowania listy wyjątek dla adresów IP, która nie należy przeprowadzać weryfikację dwuetapową w [zaawansowane opcje konfiguracji dla rozszerzenia serwera NPS uwierzytelnianie wieloskładnikowe](howto-mfa-nps-extension-advanced.md)

- Dowiedz się, jak zintegrować [bramy usług pulpitu zdalnego](howto-mfa-nps-extension-rdg.md) i [serwery sieci VPN](howto-mfa-nps-extension-vpn.md) przy użyciu rozszerzenia zasad Sieciowych

- [Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Rozstrzyganie komunikatów o błędach z rozszerzenia serwera NPS dotyczących usługi Azure Multi-Factor Authentication)
