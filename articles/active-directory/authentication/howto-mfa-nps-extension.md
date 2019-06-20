---
title: Użyj istniejących serwerów zasad Sieciowych, zapewniając Azure MFA — usłudze Azure Active Directory
description: Dodawanie funkcji weryfikacji dwuetapowej oparte na chmurze do istniejącej infrastruktury uwierzytelniania
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97bad4d9cd599890dd5e26cbc77f81156c0f1070
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204674"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrowanie istniejącej infrastruktury NPS przy użyciu usługi Azure Multi-Factor Authentication

Rozszerzenia serwera zasad sieciowych (NPS) dla usługi Azure MFA dodaje oparte na chmurze usługa MFA możliwości do infrastruktury uwierzytelniania przy użyciu istniejących serwerów. Za pomocą rozszerzenia serwera NPS można dodać połączenie telefoniczne, wiadomość SMS lub weryfikacji aplikacji telefonicznej do istniejącego przepływu uwierzytelniania bez konieczności instalowania, konfigurowania i konserwacji nowe serwery. 

To rozszerzenie został utworzony dla organizacji, które chcesz chronić połączenia sieci VPN bez wdrażania serwera usługi Azure MFA. Rozszerzenia serwera NPS działa jako karty między RADIUS i oparte na chmurze usługi Azure MFA, aby zapewnić drugi składnik uwierzytelniania federacyjnego lub zsynchronizowanych użytkowników.

W przypadku używania rozszerzenia serwera NPS dla usługi Azure MFA, przepływ uwierzytelniania obejmuje następujące składniki: 

1. **Serwer sieci VPN naNAS/** odbiera żądania od klientów sieci VPN i konwertuje je na serwery NPS żądania usługi RADIUS. 
2. **Serwer NPS** nawiązanie połączenia z usługi Active Directory w celu przeprowadzenia podstawowego uwierzytelniania dla żądań usługi RADIUS, a w razie powodzenia, przekazuje żądanie do wszystkich zainstalowanych rozszerzeń.  
3. **Rozszerzenia serwera NPS** wyzwala żądania do usługi Azure MFA uwierzytelniania pomocniczego. Po rozszerzenie odbiera odpowiedź, a jeśli żądanie uwierzytelniania MFA zakończy się powodzeniem, kończy żądanie uwierzytelniania, zapewniając serwera NPS przy użyciu tokenów zabezpieczających, które zawierają oświadczenia usługi MFA, wystawiony przez usługę STS platformy Azure.  
4. **Usługa Azure MFA** komunikuje się z usługą Azure Active Directory, aby pobrać szczegóły użytkownika i wykonuje dodatkowego uwierzytelniania przy użyciu metody weryfikacji, skonfigurowane dla użytkownika.

Na poniższym diagramie przedstawiono ten przepływ żądania uwierzytelniania wysokiego poziomu: 

![Diagram przepływu uwierzytelniania](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planowanie wdrożenia

Rozszerzenia serwera NPS automatycznie obsługuje nadmiarowość, więc nie trzeba specjalnej konfiguracji.

Można utworzyć dowolną liczbę serwerów NPS z obsługą usługi Azure MFA. Instalacji wielu serwerów, należy używać certyfikatu klienta różnicy dla każdego z nich. Tworzenie certyfikatu dla każdego serwera oznacza indywidualnie zaktualizować każdego certyfikatu, a nie martwić się o przestoje na wszystkich serwerach.

Serwery sieci VPN trasy żądań uwierzytelniania, w związku z czym muszą znać nowe serwery NPS z obsługą usługi Azure MFA.

## <a name="prerequisites"></a>Wymagania wstępne

Rozszerzenia serwera NPS jest przeznaczony do pracy z istniejącą infrastrukturą. Upewnij się, że masz następujące wymagania wstępne, przed przystąpieniem do wykonywania.

### <a name="licenses"></a>Licencje

Rozszerzenia serwera NPS dla usługi Azure MFA jest dostępna dla klientów z [licencji dla usługi Azure Multi-Factor Authentication](multi-factor-authentication.md) (dołączone do usługi Azure AD Premium, EMS lub licencję autonomiczną usługi MFA). Na podstawie użycia licencji na usługę Azure MFA, takich jak dla określonego użytkownika lub uwierzytelnianie licencji na nie są zgodne z rozszerzeniem serwera NPS. 

### <a name="software"></a>Oprogramowanie

Windows Server 2008 R2 z dodatkiem SP1 lub nowszego.

### <a name="libraries"></a>Biblioteki

Te biblioteki są instalowane automatycznie z rozszerzeniem.

- [Pakiety Visual C++ Redistributable for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory Module for Windows PowerShell w wersji 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Microsoft Azure Active Directory Module for Windows PowerShell jest zainstalowany, jeśli nie jest jeszcze obecna, za pomocą skryptu konfiguracji, który można uruchomić jako część procesu instalacji. Nie ma potrzeby do zainstalowania tego modułu wcześniej, jeśli nie został jeszcze zainstalowany.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Wszyscy przy użyciu rozszerzenia serwera NPS musi być synchronizowane z usługą Azure Active Directory za pomocą usługi Azure AD Connect, a musi być zarejestrowana na potrzeby uwierzytelniania Wieloskładnikowego.

Podczas instalowania rozszerzenia potrzebne poświadczenia Identyfikatora i administratora katalogu dla dzierżawy usługi Azure AD. Można znaleźć Identyfikatora katalogu w [witryny Azure portal](https://portal.azure.com). Zaloguj się jako administrator, wybierz opcję **usługi Azure Active Directory** ikonę po lewej stronie, następnie wybierz pozycję **właściwości**. Skopiuj identyfikator GUID w **identyfikator katalogu** pole, a następnie zapisz go. Użyjesz tego identyfikatora GUID jako identyfikator dzierżawy, po zainstalowaniu rozszerzenia serwera NPS.

![Znajdź swój identyfikator katalogu, w obszarze właściwości usługi Azure Active Directory](./media/howto-mfa-nps-extension/find-directory-id.png)

### <a name="network-requirements"></a>Wymagania dotyczące sieci

Serwer NPS musi być w stanie komunikować się z następujących adresów URL przez porty 80 i 443.

* https:\//adnotifications.windowsazure.com  
* https:\//login.microsoftonline.com

Ponadto do ukończenia jest wymagana łączność z następującymi adresami URL [Instalatora karty przy użyciu dostarczonego skryptu programu PowerShell](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

Przed zainstalowaniem rozszerzenia serwera NPS, chcesz Państwu w przygotowaniu środowiska do obsługi ruchu uwierzytelniania.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Włącz rolę serwera NPS na serwerze przyłączonym do domeny

Serwer NPS nawiązuje połączenie z usługą Azure Active Directory i uwierzytelnianie żądań usługi MFA. Wybierz jeden serwer dla tej roli. Firma Microsoft zaleca, wybierając serwer, który nie obsługuje żądania od innych usług, ponieważ rozszerzenia serwera NPS zgłasza błędy dla wszystkich żądań, które nie są usługi RADIUS. Serwer NPS musi być skonfigurowany jako serwer uwierzytelniania podstawowego i pomocniczego dla środowiska; nie jest żądań usługi RADIUS serwera proxy na inny serwer.

1. Na serwerze, otwórz **Kreatora dodawania ról i funkcji** menu Menedżera serwera Przewodnik Szybki Start.
2. Wybierz **Instalacja oparta na rolach lub oparta na funkcjach** dla danego typu instalacji.
3. Wybierz **usług zasad sieciowych i dostępu** roli serwera. Okno wyskakujące może poinformować wymaganych funkcji, aby uruchomić tę rolę.
4. Kontynuuj pracę z kreatorem, aż strona potwierdzenia. Wybierz pozycję **Zainstaluj**.

Teraz, gdy serwer wyznaczony serwera zasad sieciowych, należy także skonfigurować ten serwer do obsługi przychodzących żądań usługi RADIUS z rozwiązanie sieci VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfiguruj rozwiązanie sieci VPN do komunikowania się za pomocą serwera NPS

W zależności od których możesz użyć rozwiązania sieci VPN różnią się kroki, aby skonfigurować zasady uwierzytelniania usługi RADIUS. Skonfiguruj te zasady, aby wskazywały serwer usługi RADIUS serwera NPS.

### <a name="sync-domain-users-to-the-cloud"></a>Użytkownicy domeny synchronizacji w chmurze

Ten krok może już być ukończone w dzierżawie, ale warto dokładnie sprawdzić, czy program Azure AD Connect został zsynchronizowany później baz danych.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Select **Azure Active Directory** > **Azure AD Connect**
3. Sprawdź, czy stan usługi synchronizacji **włączone** i które ostatniej synchronizacji było krócej niż godzinę temu.

Jeśli potrzebujesz Konferencję nowe działanie synchronizacji, nam instrukcje w [synchronizacji programu Azure AD Connect: Harmonogram](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Określanie użytkowników można użyć metod uwierzytelniania

Istnieją dwa czynniki wpływające na metody uwierzytelniania, które są dostępne z wdrożeniem rozszerzenia serwera NPS:

1. Algorytm szyfrowania hasła, które są używane między klientem RADIUS (sieci VPN, serwer Netscaler lub innych) i serwerach NPS.
   - **PAP** obsługuje wszystkie metody uwierzytelniania usługi Azure MFA w chmurze: połączenie telefoniczne, wiadomość tekstowa jednokierunkowe, powiadomienie aplikacji mobilnej i kod weryfikacyjny aplikacji mobilnej.
   - **CHAPV2** i **EAP** obsługi połączeń telefonicznych i powiadomienia przez aplikacje mobilne.
2. Metody wprowadzania danych, aplikacja kliencka (sieci VPN, serwer Netscaler lub innych) może obsługiwać. Na przykład klient sieci VPN może mieć metod, aby umożliwić użytkownika o wpisanie kodu weryfikacyjnego z aplikacji mobilnej lub tekst?

Podczas wdrażania rozszerzenia serwera NPS, należy użyć tych czynników do oceny, metody, które są dostępne dla użytkowników. Jeśli Twój klient RADIUS obsługą protokołu PAP, ale klienta UX nie ma pola wejściowe, aby uzyskać kod weryfikacyjny, następnie połączeń telefonicznych i powiadomienia przez aplikacje mobilne są dwie opcje obsługiwane.

Możesz [Wyłącz metod uwierzytelniania nieobsługiwany](howto-mfa-mfasettings.md#verification-methods) na platformie Azure.

### <a name="register-users-for-mfa"></a>Zarejestrowania użytkowników na potrzeby usługi MFA

Przed wdrożeniem i użyć rozszerzenia serwera NPS dla użytkowników, które są wymagane do przeprowadzenia weryfikacji dwuetapowej wymagają rejestracji usługi MFA. Więcej od razu Aby przetestować rozszerzenie, zgodnie z wdrożeniem, musisz mieć konto co najmniej jeden test, w pełni zarejestrowany do uwierzytelniania wieloskładnikowego.

Aby uzyskać konto test pracy, wykonaj następujące kroki:
1. Zaloguj się do [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) za pomocą konta testowego. 
2. Postępuj zgodnie z monitami, aby skonfigurować metodę weryfikacji.
3. Utwórz zasady dostępu warunkowego lub [zmiany stanu użytkownika](howto-mfa-userstates.md) które wymuszają weryfikację dwuetapową dla konta testu. 

Użytkownicy muszą wykonaj następujące kroki, aby zarejestrować przed ich mogą uwierzytelniać za pomocą rozszerzenia serwera NPS.

## <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia serwera NPS

> [!IMPORTANT]
> Instalowanie rozszerzenia serwera NPS na innym serwerze niż punkt dostępu do sieci VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Pobieranie i instalowanie rozszerzenia serwera NPS dla usługi Azure MFA

1. [Pobierz rozszerzenia serwera NPS](https://aka.ms/npsmfa) z Centrum pobierania Microsoft.
2. Skopiuj plik binarny serwera zasad sieciowych, którą chcesz skonfigurować.
3. Uruchom *setup.exe* i postępuj zgodnie z instrukcjami instalacji. Jeśli wystąpią błędy, należy dokładnie dwie biblioteki z sekcji wymagań wstępnych zostały pomyślnie zainstalowane.

#### <a name="upgrade-the-nps-extension"></a>Uaktualnij rozszerzenia serwera NPS

Podczas uaktualniania istniejącego rozszerzenia serwera NPS dla instalacji, aby uniknąć ponownego uruchomienia serwera bazowego wykonaj następujące czynności:

1. Odinstalować istniejącą wersję
1. Uruchamianie nowego Instalatora
1. Uruchom ponownie usługę serwera zasad sieciowych (IAS)

### <a name="run-the-powershell-script"></a>Uruchom skrypt programu PowerShell

Instalator tworzy skrypt programu PowerShell w tej lokalizacji: `C:\Program Files\Microsoft\AzureMfa\Config` (gdzie C:\ to dysk instalacji). Ten skrypt programu PowerShell wykonuje następujące akcje za każdym razem, gdy jest uruchomiony:

- Utwórz certyfikat z podpisem własnym.
- Kojarzenie klucz publiczny certyfikatu do jednostki w usłudze Azure AD usługi.
- Store certyfikat w magazynie certyfikatów komputera lokalnego.
- Udzielanie dostępu do klucza prywatnego certyfikatu do sieci użytkownika.
- Uruchom ponownie serwer NPS.

Jeśli nie chcesz używać certyfikatów (zamiast certyfikatów z podpisem własnym, które generuje skrypt programu PowerShell), uruchom skrypt programu PowerShell, aby ukończyć instalację. Po zainstalowaniu rozszerzenia na wielu serwerach każdy z nich powinna mieć własny certyfikat.

1. Uruchom program Windows PowerShell jako administrator.
2. Zmień katalogi.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Uruchom skrypt programu PowerShell, utworzony przez Instalatora.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Zaloguj się do usługi Azure AD jako administrator.
5. Program PowerShell monituje o podanie identyfikatora dzierżawy. Użyj katalogu identyfikator GUID, który został skopiowany z witryny Azure portal w sekcji wymagania wstępne.
6. Po zakończeniu działania skryptu, PowerShell wyświetla komunikat o powodzeniu.  

Powtórz te kroki dla pozostałych serwerów zasad Sieciowych, które chcesz skonfigurować Równoważenie obciążenia sieciowego.

Jeśli wygasł poprzedniego certyfikat komputera, a nowy certyfikat został wygenerowany, należy usunąć wszelkie wygasłych certyfikatów. Posiadanie wygasłych certyfikatów może spowodować problemy z rozszerzeniem serwera NPS, uruchamianie.

> [!NOTE]
> Jeśli używasz własnych certyfikatów zamiast generowania certyfikatów za pomocą skryptu programu PowerShell, upewnij się, zostaną wyrównane do konwencji nazewnictwa serwera NPS. Nazwa podmiotu musi być **CN =\<TenantID\>, OU = rozszerzenia serwera NPS Microsoft**. 

## <a name="configure-your-nps-extension"></a>Konfigurowanie rozszerzenia serwera NPS

Ta sekcja obejmuje zagadnienia dotyczące projektowania i sugestie dotyczące pomyślnych wdrożeniach rozszerzenia serwera NPS.

### <a name="configuration-limitations"></a>Ograniczenia konfiguracji

- Rozszerzenia serwera NPS dla usługi Azure MFA nie obejmuje narzędzia do migracji użytkowników i ustawienia z serwera usługi MFA w chmurze. Z tego powodu zaleca się korzystanie z rozszerzenia dla nowych wdrożeń zamiast istniejącego wdrożenia. Użycie rozszerzenia na istniejące wdrożenie, użytkownicy muszą wykonać w górę dowód ponownie, aby wypełnić ich szczegóły uwierzytelniania MFA w chmurze.  
- Rozszerzenia serwera NPS używa nazwy UPN w lokalnej usłudze Active directory do identyfikacji użytkownika w usłudze Azure MFA do wykonywania uwierzytelniania pomocniczego Aby użyć innego identyfikatora alternatywnego Identyfikatora logowania lub pole niestandardowe usługi Active Directory niż nazwa UPN można skonfigurować rozszerzenia. Aby uzyskać więcej informacji, zapoznaj się z artykułem [opcji zaawansowanej konfiguracji dla rozszerzenia serwera NPS do uwierzytelniania wieloskładnikowego](howto-mfa-nps-extension-advanced.md).
- Nie wszystkie protokoły szyfrowania obsługuje wszystkie metody weryfikacji.
   - **PAP** obsługuje połączenie telefoniczne, wiadomość tekstowa jednokierunkowe, powiadomienie aplikacji mobilnej i kod weryfikacyjny aplikacji mobilnej
   - **CHAPV2** i **EAP** obsługi połączeń telefonicznych i powiadomienia przez aplikacje mobilne

### <a name="control-radius-clients-that-require-mfa"></a>Klienci usługi RADIUS kontrolki, które wymagają usługi MFA

Po włączeniu usługi MFA dla klienta usługi RADIUS, za pomocą rozszerzenia serwera NPS wszystkie uwierzytelnienia dla tego klienta są wymagane do przeprowadzenia uwierzytelniania Wieloskładnikowego. Jeśli chcesz włączyć usługę MFA dla niektórych klientów usługi RADIUS, a innych nie można skonfigurować dwa serwery NPS i zainstaluj rozszerzenie na tylko jeden z nich. Konfigurowanie klientów RADIUS, które chcesz wymagać uwierzytelniania Wieloskładnikowego w celu wysyłania żądań do serwera zasad Sieciowych skonfigurowano rozszerzenie i innych klientów RADIUS, serwer NPS nie jest skonfigurowany z rozszerzeniem.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Przygotuj dla użytkowników, które nie zostały zarejestrowane na potrzeby usługi MFA

Jeśli masz użytkowników, którzy nie są zarejestrowane na potrzeby usługi MFA, można określić, co się dzieje, gdy użytkownik próbuje uwierzytelnić. Użyj ustawienia rejestru *REQUIRE_USER_MATCH* w ścieżce rejestru *HKLM\Software\Microsoft\AzureMFA* do sterowania zachowaniem funkcji. To ustawienie jest dostępna opcja jednej konfiguracji:

| Klucz | Wartość | Domyślne |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | PRAWDA/FAŁSZ | Nieustawione (odpowiada to TRUE) |

To ustawienie ma na celu ustalić, co należy zrobić, gdy użytkownik nie zostanie zarejestrowane na potrzeby usługi MFA. Jeśli klucz nie istnieje, nie jest ustawiona lub jest ustawiona na TRUE i użytkownik nie jest zarejestrowany, a następnie rozszerzenie kończy się niepowodzeniem żądania uwierzytelniania MFA. Gdy jest ustawiona na wartość FALSE, a użytkownik nie jest zarejestrowany, uwierzytelnianie będzie kontynuowane bez przeprowadzenia uwierzytelniania Wieloskładnikowego. Jeśli użytkownik jest zarejestrowany w usłudze MFA, muszą zostać uwierzytelnione za pomocą usługi MFA nawet wtedy, gdy REQUIRE_USER_MATCH jest ustawiona na wartość FALSE.

Można utworzyć ten klucz i ustawić wartość FALSE, gdy użytkownicy są dołączania, a może nie wszystkie ono zarejestrowane na potrzeby usługi Azure MFA jeszcze. Jednak ponieważ klucza pozwala na użytkowników, którzy nie są zarejestrowane na potrzeby usługi MFA do logowania, należy je usunąć ten klucz przed przejściem do środowiska produkcyjnego.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Jak sprawdzić, czy certyfikat klienta został zainstalowany, zgodnie z oczekiwaniami?

Wyszukaj certyfikat z podpisem własnym utworzony przez Instalatora w magazynie certyfikatów i upewnij się, że klucz prywatny, ma uprawnienia przyznane użytkownikowi **Usługa sieciowa**. Certyfikat ma nazwę podmiotu **CN \<tenantid\>, OU = rozszerzenia serwera NPS firmy Microsoft**

Certyfikaty z podpisem własnym generowanych przez *AzureMfaNpsExtnConfigSetup.ps1* skryptu również mieć okresu istnienia ważności na dwa lata. Podczas sprawdzania, czy certyfikat jest zainstalowany, należy także sprawdzić, czy certyfikat nie wygasł.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Jak zweryfikować, że moje certyfikat klienta jest skojarzony z dzierżawą usługi Azure Active Directory?

Otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenia:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Te polecenia drukowania wszystkie certyfikaty skojarzenie dzierżawy z wystąpieniem usługi rozszerzenia serwera NPS w sesji programu PowerShell. Znajdź certyfikat, eksportując Twojego certyfikatu klienta w formacie "X.509(.cer) algorytmem Base-64" bez klucza prywatnego i porównaj je z listy za pomocą programu PowerShell.

Poniższe polecenie utworzy plik o nazwie "npscertificate" na dysku "C:" w formacie cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Po uruchomieniu tego polecenia, przejdź do dysku C, zlokalizuj plik i kliknij go dwukrotnie. Przejdź do szczegółów i przewiń w dół do "odciskiem palca", porównać odcisk palca certyfikatu zainstalowanego na serwerze do wskazanego. Odciski palców certyfikatu powinien być zgodny.

Nieprawidłowa-z i ważne — aż sygnatury czasowe, które znajdują się w postaci czytelnej dla człowieka, można odfiltrować misfits oczywiste, jeśli polecenie zwraca więcej niż jeden certyfikat.

---

### <a name="why-cant-i-sign-in"></a>Dlaczego niedomiaru mogę się zarejestrować?

Upewnij się, że hasło nie wygasło. Rozszerzenia serwera NPS nie obsługuje zmieniania hasła jako część przepływu pracy logowania. Aby uzyskać dalszą pomoc, skontaktuj się z pracownikami działu IT Twojej organizacji.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Dlaczego moje żądania kończą się niepowodzeniem z powodu błędu tokenu biblioteki ADAL?

Ten błąd może być spowodowane jedną z kilku powodów. Wykonaj następujące kroki, aby ułatwić rozwiązywanie problemów:

1. Uruchom ponownie serwer NPS.
2. Sprawdź, czy ten certyfikat klienta został zainstalowany zgodnie z oczekiwaniami.
3. Sprawdź, czy certyfikat jest skojarzony z dzierżawą w usłudze Azure AD.
4. Upewnij się, że adres https://login.microsoftonline.com/ jest dostępny z serwera, na którym działa rozszerzenie.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Dlaczego uwierzytelnianie nie powiódł się z powodu błędu w dziennikach HTTP z informacją, że użytkownik nie został znaleziony?

Sprawdź, czy AD Connect jest uruchomiona, a użytkownik musi być obecny w usłudze Azure Active Directory i usługi Windows Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Dlaczego widzę HTTP skupionej błędy w dziennikach Moje uwierzytelnień kończy się niepowodzeniem?

Upewnij się, że adres https://adnotifications.windowsazure.com jest osiągalny z serwera, na którym działa rozszerzenie NPS.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Dlaczego uwierzytelnianie nie działa, mimo obecności ważnego certyfikatu?

Jeśli wygasł poprzedniego certyfikat komputera, a nowy certyfikat został wygenerowany, należy usunąć wszelkie wygasłych certyfikatów. Posiadanie wygasłych certyfikatów może spowodować problemy z rozszerzeniem serwera NPS, uruchamianie.

Aby sprawdzić, czy mają prawidłowy certyfikat, sprawdź Store certyfikat konta komputera lokalnego za pomocą programu MMC, a następnie upewnij się, że certyfikat nie został przekazany datę jego wygaśnięcia. Aby wygenerować certyfikat nowo, ponownie uruchom kroki opisane w sekcji "[uruchomienia skryptu programu PowerShell](#run-the-powershell-script)"

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Zarządzanie protokołami TLS/SSL i zestawami szyfrowania

Zalecane jest, że mechanizmów szyfrowania starsze, słabszej je wyłączyć lub usunąć, chyba że wymagane przez Twoją organizację. Informacje o tym, jak wykonać to zadanie, można znaleźć w artykule [Managing SSL/TLS Protocols and Cipher Suites for AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) (Zarządzanie protokołami SSL/TLS i zestawami szyfrowania dla usług AD FS).

## <a name="next-steps"></a>Kolejne kroki

- Konfigurowanie alternatywnych identyfikatorów logowania lub Ustaw listy wyjątków dla adresów IP, który nie należy wykonywać weryfikacji dwuetapowej w [opcji zaawansowanej konfiguracji dla rozszerzenia serwera NPS do uwierzytelniania wieloskładnikowego](howto-mfa-nps-extension-advanced.md)

- Dowiedz się, jak zintegrować [bramy usług pulpitu zdalnego](howto-mfa-nps-extension-rdg.md) i [serwerów sieci VPN](howto-mfa-nps-extension-vpn.md) przy użyciu rozszerzenia serwera NPS

- [Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Rozstrzyganie komunikatów o błędach z rozszerzenia serwera NPS dotyczących usługi Azure Multi-Factor Authentication)
