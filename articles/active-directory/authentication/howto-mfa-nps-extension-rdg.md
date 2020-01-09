---
title: Integrowanie RDG z rozszerzeniem NPS usługi Azure MFA — Azure Active Directory
description: Integrowanie infrastruktury bramy usług pulpitu zdalnego z usługą Azure MFA przy użyciu rozszerzenia serwera zasad sieciowych dla systemu Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d9b2332d6d78e7bde63d0fa3f5b64b588e576b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425444"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrowanie infrastruktury bramy usług pulpitu zdalnego przy użyciu rozszerzenia serwera zasad sieciowych (NPS) i Azure AD

Ten artykuł zawiera szczegółowe informacje do integrowania infrastruktury bramy usług pulpitu zdalnego za pomocą usługi Azure Multi-Factor Authentication (MFA) przy użyciu rozszerzenia serwera zasad sieciowych (NPS) dla systemu Microsoft Azure.

Rozszerzenia serwera zasad sieciowych (NPS) dla platformy Azure umożliwia klientom w zabezpieczaniu uwierzytelniania klienta usługi Usługa użytkowników zdalnego uwierzytelniania (RADIUS) za pomocą usługi Azure użytkownika oparte na chmurze [Multi-Factor Authentication (MFA)](multi-factor-authentication.md). To rozwiązanie zapewnia weryfikację dwuetapową dla dodając drugą warstwę zabezpieczeń do logowania użytkowników i transakcji.

Ten artykuł zawiera instrukcje krok po kroku do integrowania infrastruktury NPS z usługą Azure MFA przy użyciu rozszerzenia serwera NPS dla platformy Azure. Dzięki temu weryfikacji dla użytkowników próbujących się zalogować się do bramy usług pulpitu zdalnego.

> [!NOTE]
> Ten artykuł nie powinien być używany z wdrożeniami serwera MFA i powinien być używany tylko z wdrożeniami usługi Azure MFA (opartymi na chmurze).

Zasad sieciowych i dostępu do usług (NPS) umożliwia organizacjom wykonaj następujące czynności:

* Zdefiniuj centralne lokalizacje w celu zarządzania i sterowania żądania sieci, określając, kto może się połączyć, jakie razy dziennie połączenia są dozwolone, czas trwania połączeń i poziom zabezpieczeń, które klienci muszą używać do łączenia i tak dalej. Zamiast określać te zasady na każdym serwerze sieci VPN lub bramy usług pulpitu zdalnego (RD), te zasady można określić jeden raz w centralnej lokalizacji. Protokołu RADIUS umożliwia scentralizowane uwierzytelnianie, autoryzacja i Ewidencjonowanie.
* Ustal i Wymuś zasady dotyczące kondycji klienta ochrony dostępu do sieci (NAP), które określają, czy urządzenia są przyznawane nieograniczony lub ograniczony dostęp do zasobów sieciowych.
* Zapewnia sposób wymuszające uwierzytelnianie i autoryzację do uzyskiwania dostępu do 802.1 punkty dostępu bezprzewodowego możliwością x i przełączników Ethernet.

Zazwyczaj organizacje wykorzystują serwer NPS (RADIUS) do uproszczenia i scentralizowania zarządzania zasadami sieci VPN. Jednak w wielu organizacjach również użyć serwera zasad Sieciowych można uproszczenie i scentralizowane zarządzanie zasady autoryzacji połączeń usług pulpitu usług pulpitu zdalnego (RD CAP).

Organizacje, można również zintegrować serwera NPS z usługą Azure MFA, aby zwiększyć bezpieczeństwo i zapewnia wysoki poziom zgodności. Pozwala to zagwarantować, że użytkownicy ustanawiać weryfikacji dwuetapowej, aby zalogować się do bramy usług pulpitu zdalnego. Użytkownikom można udzielić dostępu musi podać ich kombinacji nazwy użytkownika/hasła, wraz z informacjami, których użytkownik ma w swojej kontrolce. Te informacje, musi być zaufany i nie można łatwo zduplikować, takie jak numer telefonu komórkowego, numer telefonu stacjonarnego, aplikacja na urządzeniu przenośnym i tak dalej. RDG obecnie obsługuje połączenia telefoniczne i powiadomienia wypychane z metod aplikacji Microsoft Authenticator dla funkcji 2FA. Aby uzyskać więcej informacji na temat obsługiwanych metod uwierzytelniania, zobacz sekcję [Określanie metody uwierzytelniania, które służą użytkownikom](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Przed dostępność rozszerzenia serwera NPS dla platformy Azure klienci, którzy chcieliby zaimplementować weryfikację dwuetapową dla zintegrowanych środowisk serwera zasad Sieciowych i usługi Azure MFA miał konfigurowania i konserwacji osobny serwer usługi MFA w środowisku lokalnym, zgodnie z opisem w [ Brama pulpitu zdalnego i korzystanie z usługi RADIUS serwera Azure Multi-Factor Authentication](howto-mfaserver-nps-rdg.md).

Dostępność rozszerzenia serwera NPS dla platformy Azure umożliwia organizacjom wybranej do wdrożenia rozwiązanie MFA na podstawie lokalnych lub opartych na chmurze rozwiązanie usługi MFA do bezpiecznego uwierzytelniania klienta usługi RADIUS.

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Użytkownikom można udzielić dostępu do zasobów sieciowych za pośrednictwem bramy usług pulpitu zdalnego muszą spełniać warunki określone w jednej usług pulpitu zdalnego zasady autoryzacji połączeń (RD CAP) i jeden usług pulpitu zdalnego zasady autoryzacji zasobów (zdalnego RD RAP). RD CAP Określ, kto jest autoryzowany do nawiązania z bramy usług pulpitu zdalnego. RD RAP Określ zasobów sieciowych, takich jak pulpity zdalne lub zdalne aplikacje, które użytkownik może połączyć się za pośrednictwem bramy usług pulpitu zdalnego.

Brama usług pulpitu zdalnego można skonfigurować na potrzeby magazynu centralne zasady RD CAP. RD RAP nie można użyć centralne zasady, jak są przetwarzane w bramie usług pulpitu zdalnego. Przykładem bramy usług pulpitu zdalnego skonfigurowane na potrzeby magazynu centralne zasady RD CAP jest klient usługi RADIUS do innego serwera NPS, który służy do przechowywania centralne zasady.

Gdy rozszerzenia serwera NPS dla platformy Azure jest zintegrowana z serwerem NPS i bramy usług pulpitu zdalnego, przepływ pomyślne uwierzytelnienie jest następująca:

1. Serwer bramy usług pulpitu zdalnego odbiera żądanie uwierzytelnienia od użytkownika pulpitu zdalnego do połączenia z zasobem, takich jak sesji pulpitu zdalnego. Działając jako klient usługi RADIUS, serwera bramy usług pulpitu zdalnego przekształcają żądanie komunikatu żądanie dostępu usługi RADIUS i wysyła wiadomość do serwera RADIUS (NPS), w którym zainstalowano rozszerzenia serwera NPS.
1. Kombinacja nazwy użytkownika i hasła jest weryfikowana w usłudze Active Directory, a użytkownik jest uwierzytelniony.
1. Jeśli są spełnione wszystkie warunki określone w żądaniu połączenia serwera zasad Sieciowych i zasad sieci (na przykład, pory dnia lub grupy ograniczeniami członkostwa), rozszerzenia serwera NPS dla wyzwalaczy żądania uwierzytelniania pomocniczego, za pomocą usługi Azure MFA.
1. Usługa Azure MFA komunikuje się z usługą Azure AD, pobiera szczegóły użytkownika i wykonuje dodatkowego uwierzytelniania przy użyciu obsługiwanych metod.
1. W razie powodzenia żądania uwierzytelniania MFA usługi Azure MFA komunikuje się wynik do rozszerzenia serwera NPS.
1. Serwer zasad Sieciowych, w którym zainstalowane jest rozszerzenie, wysyła komunikat udzielenia dostępu usługi RADIUS dla zasad RD CAP do serwera bramy usług pulpitu zdalnego.
1. Użytkownik otrzymuje dostęp do zasobu żądanej sieci za pośrednictwem bramy usług pulpitu zdalnego.

## <a name="prerequisites"></a>Wymagania wstępne

W tej sekcji przedstawiono wymagania wstępne niezbędne przed Integrowanie usługi Azure MFA z bramą usług pulpitu zdalnego. Przed przystąpieniem do wykonywania, konieczne jest posiadanie następujących wymagań wstępnych w miejscu.  

* Infrastruktury usług pulpitu zdalnego
* Azure MFA License
* Oprogramowanie systemu Windows Server
* Rola zasad sieciowych i dostępu do usług (NPS)
* Usługa Azure Active Directory zsynchronizowane z usługą Active Directory w środowisku lokalnym
* Identyfikator GUID usługi Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infrastruktury usług pulpitu zdalnego

W miejscu, musi mieć działającą infrastrukturę usług pulpitu zdalnego (RDS). Jeśli tego nie zrobisz, możesz szybko utworzyć tę infrastrukturę na platformie Azure przy użyciu następującego szablonu szybkiego startu: [Utwórz wdrożenie kolekcji sesji pulpit zdalny](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Jeśli chcesz ręcznie utworzyć lokalnej infrastruktury usług pulpitu zdalnego, szybkie do celów testowych, postępuj zgodnie z instrukcjami, aby wdrożyć.
**Dowiedz się więcej**: [Wdróż RDS z przewodnikiem Szybki Start platformy Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) i [podstawowym wdrożeniem infrastruktury RDS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-mfa-license"></a>Azure MFA License

Wymagane jest licencję usługi Azure MFA, który jest dostępny za pośrednictwem usługi Azure AD Premium lub inne pakiety, które go zawierają. Na podstawie użycia licencji dla usługi Azure MFA, na przykład dla każdego użytkownika lub licencji uwierzytelniania na nie są zgodne z rozszerzeniem serwera NPS. Aby uzyskać więcej informacji, zobacz [sposobu uzyskania usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md). Do celów testowych można użyć subskrypcji wersji próbnej.

### <a name="windows-server-software"></a>Oprogramowanie systemu Windows Server

Rozszerzenia serwera NPS wymaga systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszy z zainstalowaną usługą roli serwera NPS. Wszystkie kroki opisane w tej sekcji zostały wykonane przy użyciu systemu Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Rola zasad sieciowych i dostępu do usług (NPS)

Usługi roli serwera NPS zawiera usługi RADIUS serwera i klienta, funkcje, a także usługa kondycji zasady dostępu do sieci. Musi być zainstalowana ta rola na co najmniej dwa komputery w infrastrukturze: bramy usług pulpitu zdalnego, a drugi serwer członkowski lub kontroler domeny. Domyślnie roli jest już obecny na komputerze, skonfigurowany jako brama usług pulpitu zdalnego.  Również zainstalowanie roli serwera NPS na co najmniej na innym komputerze, na przykład kontroler domeny lub serwer członkowski.

Aby uzyskać informacje na temat instalowania roli serwera zasad Sieciowych usługi Windows Server 2012 lub starszy, zobacz [zainstalować serwer zasad dotyczących kondycji ochrony dostępu do sieci](https://technet.microsoft.com/library/dd296890.aspx). Aby uzyskać opis najlepszych rozwiązań dla serwera zasad Sieciowych, w tym zalecenia, aby zainstalować serwer NPS na kontrolerze domeny, zobacz [najlepsze rozwiązania dotyczące serwera NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Usługa Azure Active Directory zsynchronizowane z usługą Active Directory w środowisku lokalnym

Aby użyć rozszerzenia serwera NPS, lokalnych użytkowników musi być synchronizowane z usługą Azure AD i włączone dla usługi MFA. W tej sekcji założono, że użytkowników lokalnych są zsynchronizowane z usługą Azure AD za pomocą programu AD Connect. Aby uzyskać informacji na temat usługi Azure AD connect, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Identyfikator GUID usługi Azure Active Directory

Aby zainstalować rozszerzenia serwera NPS, musisz wiedzieć identyfikator GUID usługi Azure AD. Instrukcje dotyczące znajdowania identyfikatora GUID usługi Azure AD znajdują się poniżej.

## <a name="configure-multi-factor-authentication"></a>Konfigurowanie uwierzytelniania wieloskładnikowego

Ta sekcja zawiera instrukcje dotyczące integrowania usługi Azure MFA z bramą usług pulpitu zdalnego. Jako administrator należy skonfigurować usługę Azure MFA, zanim użytkownicy będą mogli samodzielnie zarejestrować swoje urządzenia w usłudze Multi-Factor Authentication lub aplikacji.

Postępuj zgodnie z instrukcjami w [wprowadzenie do usługi Azure Multi-Factor Authentication w chmurze](howto-mfa-getstarted.md) włączyć usługę MFA dla użytkowników usługi Azure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurowanie konta włączono weryfikację dwuetapową

Po włączeniu konta usługi dla usługi MFA nie możesz zalogować się zasoby zarządzane przez zasady MFA, aż pomyślnie skonfigurowano zaufanego urządzenia do użycia dla drugiego składnika uwierzytelniania, a komputer został uwierzytelniony przy użyciu weryfikacji dwuetapowej.

Postępuj zgodnie z instrukcjami w [co usługi Azure Multi-Factor Authentication oznacza dla mnie?](../user-help/multi-factor-authentication-end-user.md) do zrozumienia i poprawnie skonfigurować urządzenia dla usługi MFA z Twoim kontem użytkownika.

## <a name="install-and-configure-nps-extension"></a>Instalowanie i konfigurowanie rozszerzenia serwera NPS

Ta sekcja zawiera instrukcje dotyczące konfigurowania infrastruktury usług pulpitu zdalnego przy użyciu usługi Azure MFA klienta do uwierzytelniania przy użyciu bramy usług pulpitu zdalnego.

### <a name="acquire-azure-active-directory-guid-id"></a>Uzyskiwanie Identyfikatora GUID usługi Azure Active Directory

W ramach konfiguracji rozszerzenia serwera NPS musisz podać poświadczenia administratora i identyfikator usługi Azure AD dla dzierżawy usługi Azure AD. Poniższe kroki pokazują, jak można pobrać identyfikatora dzierżawy.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny dzierżawy platformy Azure.
1. W menu Azure Portal wybierz pozycję **Azure Active Directory**lub Wyszukaj i wybierz pozycję **Azure Active Directory** z dowolnej strony.
1. Wybierz pozycję **Właściwości**.
1. W bloku właściwości, obok Identyfikatora katalogu kliknij **kopiowania** ikony, jak pokazano poniżej, aby skopiować identyfikator do Schowka.

   ![Pobieranie identyfikatora katalogu z Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia serwera NPS

Instalowanie rozszerzenia serwera NPS na serwerze, na którym jest zainstalowana rola zasad sieciowych i dostępu do usług (NPS). Ta opcja działa jako serwer RADIUS dotyczące własnego projektu.

> [!Important]
> Upewnij się, że nie instalują rozszerzenia serwera NPS na serwerze bramy usług pulpitu zdalnego.
>

1. Pobierz [rozszerzenia serwera NPS](https://aka.ms/npsmfa).
1. Kopiowanie pliku wykonywalnego (NpsExtnForAzureMfaInstaller.exe) na serwerze zasad Sieciowych.
1. Na serwerze zasad Sieciowych, kliknij dwukrotnie **NpsExtnForAzureMfaInstaller.exe**. Jeśli zostanie wyświetlony monit, kliknij przycisk **Uruchom**.
1. W oknie dialogowym serwera NPS dla usługi Azure MFA Instalator rozszerzenia Przejrzyj postanowienia licencyjne dotyczące oprogramowania, sprawdź **zgodę na warunki i postanowienia licencyjne**i kliknij przycisk **zainstalować**.
1. W oknie dialogowym serwera NPS dla usługi Azure MFA Instalator rozszerzenia kliknij **Zamknij**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurowanie certyfikatów do użycia przy użyciu rozszerzenia serwera NPS przy użyciu skryptu programu PowerShell

Następnie należy skonfigurować certyfikaty do użytku przez rozszerzenia serwera NPS w celu zapewnienia bezpiecznej komunikacji i kontrola. Składniki serwera NPS obejmują skrypt programu Windows PowerShell, który umożliwia skonfigurowanie certyfikatu z podpisem własnym do użytku przy użyciu rozwiązania NPS.

Skrypt wykonuje następujące czynności:

* Tworzy certyfikat z podpisem własnym
* Kojarzy klucz publiczny certyfikatu do usługi podmiotu zabezpieczeń w usłudze Azure AD
* Zapisuje certyfikat w magazynie komputera lokalnego
* Zapewnia dostęp do klucza prywatnego certyfikatu użytkownika sieci
* Uruchamia ponownie usługę Serwer zasad sieciowych

Aby korzystać z własnych certyfikatów, musisz skojarzyć klucz publiczny certyfikatu do nazwy głównej usługi w usłudze Azure AD i tak dalej.

Aby użyć skryptu, należy podać rozszerzenie przy użyciu poświadczeń administratora usługi Azure AD i identyfikator dzierżawy usługi Azure AD, które wcześniej zostały skopiowane. Uruchom skrypt na każdym serwerze zasad Sieciowych, w którym zainstalowano rozszerzenia serwera NPS. Następnie wykonaj poniższe czynności:

1. Otwórz administracyjny wiersz środowiska Windows PowerShell.
1. W wierszu polecenia programu PowerShell wpisz `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`i naciśnij klawisz **ENTER**.
1. Typ `.\AzureMfaNpsExtnConfigSetup.ps1`i naciśnij klawisz **ENTER**. Skrypt sprawdza, czy zainstalowano modułu programu PowerShell usługi Azure Active Directory. Jeśli nie jest zainstalowany, skrypt zainstaluje moduł.

   ![Uruchamianie AzureMfaNpsExtnConfigSetup. ps1 w programie Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Po skrypt sprawdza poprawność instalacji modułu PowerShell, wyświetla okno dialogowe modułu programu PowerShell usługi Azure Active Directory. W oknie dialogowym Wprowadź poświadczenia administratora usługi Azure AD i hasło, a następnie kliknij przycisk **Sign In**.

   ![Uwierzytelnianie w usłudze Azure AD w programie PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Po wyświetleniu monitu wklej skopiowany wcześniej identyfikator katalogu do schowka, a następnie naciśnij klawisz **Enter**.

   ![Umieszczanie identyfikatora katalogu w programie PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skrypt tworzy certyfikat z podpisem własnym i wykonuje inne zmiany w konfiguracji. Dane wyjściowe powinny być zgodnie z poniższym obrazem.

   ![Dane wyjściowe programu PowerShell zawierające certyfikat z podpisem własnym](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurowanie składników serwera NPS na bramy usług pulpitu zdalnego

W tej sekcji skonfigurujesz zasady autoryzacji połączeń bramy usług pulpitu zdalnego i inne ustawienia usługi RADIUS.

Przepływ uwierzytelniania wymaga wymiany komunikatów usługi RADIUS między bramą Pulpit zdalny a serwerem NPS, na którym jest zainstalowane rozszerzenie serwera NPS. Oznacza to, że zarówno Brama usług pulpitu zdalnego, jak i serwera NPS, w którym zainstalowano rozszerzenia serwera NPS, należy skonfigurować ustawienia klientów usługi RADIUS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Skonfiguruj zasady autoryzacji połączeń bramy usług pulpitu zdalnego, aby używał magazynu centralnego

Zasady autoryzacji połączeń usług pulpitu zdalnego (RD CAP) określenie wymagań dotyczących nawiązywania połączenia z serwerem bramy usług pulpitu zdalnego. Limity usług pulpitu zdalnego, które mogą być przechowywane lokalnie (ustawienie domyślne) lub mogą one być przechowywane w centralnym magazynie RD CAP, który jest uruchomiony serwer zasad Sieciowych. Aby skonfigurować integrację usługi Azure MFA, za pomocą usług pulpitu zdalnego, należy określić korzystanie z magazynu centralnego.

1. Na serwerze bramy usług pulpitu zdalnego Otwórz **Menedżera serwera**.
1. W menu, kliknij polecenie **narzędzia**, wskaż polecenie **usług pulpitu zdalnego**, a następnie kliknij przycisk **Menedżera bramy usług pulpitu zdalnego**.
1. W Menedżerze bramy usług pulpitu zdalnego, kliknij prawym przyciskiem myszy  **\[nazwy serwera\] (Local)** i kliknij przycisk **właściwości**.
1. W oknie dialogowym właściwości wybierz **RD CAP Store** kartę.
1. Na karcie RD CAP Store wybierz **centralny serwer, na którym działa serwer NPS**. 
1. W **wprowadź nazwę lub adres IP serwera, na którym działa serwer NPS** wpisz adres IP lub serwera nazwę serwera, na którym zainstalowałeś rozszerzenia serwera NPS.

   ![Wprowadź nazwę lub adres IP serwera NPS](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Kliknij pozycję **Dodaj**.
1. W **wspólny klucz tajny** okno dialogowe, wpisz wspólny klucz tajny, a następnie kliknij przycisk **OK**. Upewnij się, Zapisz ten wspólny klucz tajny i przechowuj w bezpiecznym miejscu rekordu.

   >[!NOTE]
   >Wspólny klucz tajny jest używany do ustanawiania relacji zaufania między klientami i serwerów usługi RADIUS. Tworzenie wpisu tajnego długie i złożone.
   >

   ![Tworzenie wspólnego klucza tajnego w celu ustanowienia zaufania](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Kliknij przycisk **OK**, aby zamknąć okno dialogowe.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Skonfiguruj wartość limitu czasu usługi RADIUS na serwerze zasad Sieciowych zdalnego pulpitu bramy

Aby upewnić się, że jest czas na przeprowadzenie walidacji poświadczeń użytkowników, weryfikacji dwuetapowej, otrzymywać odpowiedzi i odpowiadanie na wiadomości usługi RADIUS, należy dostosować wartość limitu czasu usługi RADIUS.

1. Na serwerze bramy usług pulpitu zdalnego Otwórz Menedżera serwera. W menu, kliknij polecenie **narzędzia**, a następnie kliknij przycisk **serwer zasad sieciowych**.
1. W **serwer NPS (lokalny)** konsoli, rozwiń **klienci i serwery RADIUS**i wybierz **serwera RADIUS**.

   ![Konsola zarządzania serwerem zasad sieciowych pokazująca zdalny serwer usługi RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

1. W okienku szczegółów kliknij dwukrotnie **Grupa serwera bramy usług terminalowych**.

   >[!NOTE]
   >Ta grupa serwera usługi RADIUS został utworzony podczas konfigurowania centralnego serwera NPS zasad. Brama usług pulpitu zdalnego przekazuje komunikaty RADIUS do serwera lub grupy serwerów, jeśli więcej niż jednej grupy.
   >

1. W **właściwości grupy serwerów bramy usług terminalowych** okna dialogowego Wybierz adres IP lub nazwę serwera serwer zasad Sieciowych skonfigurowany do przechowywania RD CAP, a następnie kliknij przycisk **Edytuj**.

   ![Wybierz adres IP lub nazwę serwera zasad sieciowych skonfigurowany wcześniej](./media/howto-mfa-nps-extension-rdg/image13.png)

1. W **edytowanie serwera RADIUS** okno dialogowe, wybierz opcję **równoważenia obciążenia** kartę.
1. W **równoważenia obciążenia** na karcie **liczba sekund bez odpowiedzi zanim żądanie zostanie uznane za porzucone** pola, zmienić domyślną wartość od 3 do wartość z przedziału od 30 do 60 sekund.
1. W **liczba sekund między żądaniami, gdy serwer jest zidentyfikowany jako niedostępny** pola, zmień wartości domyślnej równej 30 sekund na wartość, która jest równa lub większa niż wartość określona w poprzednim kroku.

   ![Edytowanie ustawień limitu czasu serwera usługi RADIUS na karcie Równoważenie obciążenia](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Kliknij przycisk **OK** dwa razy, aby zamknąć okno dialogowe.

### <a name="verify-connection-request-policies"></a>Sprawdź zasady żądań połączeń

Domyślnie podczas konfigurowania bramy usług pulpitu zdalnego na potrzeby magazynu zasady centralne zasady autoryzacji połączeń, bramy usług pulpitu zdalnego jest skonfigurowany do przekazywania limit żądań do serwera NPS. Serwer zasad Sieciowych przy użyciu rozszerzenia usługi Azure MFA zainstalowano, przetwarza żądanie dostępu usługi RADIUS. Poniższe kroki pokazują jak zweryfikować domyślnej zasady żądań połączeń.  

1. W bramie usług pulpitu zdalnego, w konsoli serwera NPS (lokalny), rozwiń węzeł **zasady**i wybierz **zasady żądań połączeń**.
1. Kliknij dwukrotnie **zasady autoryzacji bramy usług terminalowych**.
1. W **właściwości zasady autoryzacji bramy usług terminalowych** okno dialogowe, kliknij przycisk **ustawienia** kartę.
1. Na **ustawienia** kliknij kartę pod przesyłanie dalej żądania połączenia **uwierzytelniania**. Klient usługi RADIUS jest skonfigurowany do przesyłania żądań uwierzytelniania.

   ![Skonfiguruj ustawienia uwierzytelniania określające grupę serwerów](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Kliknij przycisk **anulować**.

>[!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia zasad żądań połączeń, zobacz artykuł [Konfiguracja zasad żądań połączeń](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) jest taka sama. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurowanie serwera NPS na serwerze, na którym zainstalowano rozszerzenia serwera NPS

Serwer NPS, w którym zainstalowano rozszerzenia serwera NPS musi być w stanie wymieniać komunikaty usługi RADIUS za pomocą serwera NPS na bramy usług pulpitu zdalnego. Aby włączyć ten wymianę komunikatów, należy skonfigurować składniki serwera NPS na serwerze, na którym jest zainstalowana usługa rozszerzenia serwera NPS.

### <a name="register-server-in-active-directory"></a>Zarejestruj serwer w usłudze Active Directory

Aby działać poprawnie, w tym scenariuszu, serwer NPS musi zostać zarejestrowany w usłudze Active Directory.

1. Na serwerze NPS Otwórz **Menedżera serwera**.
1. W Menedżerze serwera kliknij **narzędzia**, a następnie kliknij przycisk **serwer zasad sieciowych**.
1. W konsoli serwera zasad sieciowych, kliknij prawym przyciskiem myszy **serwer NPS (lokalny)** , a następnie kliknij przycisk **Zarejestruj serwer w usłudze Active Directory**.
1. Kliknij przycisk **OK** dwa razy.

   ![Rejestrowanie serwera NPS w Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Zamykaj konsoli w następnej procedurze.

### <a name="create-and-configure-radius-client"></a>Tworzenie i konfigurowanie klienta RADIUS

Brama usług pulpitu zdalnego musi być skonfigurowany jako klient usługi RADIUS serwera NPS.

1. Na serwerze zasad Sieciowych, w którym zainstalowane jest rozszerzenie serwera NPS, w **serwer NPS (lokalny)** konsoli kliknij prawym przyciskiem myszy **klientów RADIUS** i kliknij przycisk **New**.

   ![Tworzenie nowego klienta usługi RADIUS w konsoli serwera NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. W **nowy klient RADIUS** okna dialogowego wprowadź przyjazną nazwę, taką jak _bramy_oraz adres IP lub nazwa DNS serwera bramy usług pulpitu zdalnego.
1. W **wspólny klucz tajny** i **Potwierdź wspólny klucz tajny** wprowadź ten sam klucz tajny, który używanego wcześniej.

   ![Skonfiguruj przyjazną nazwę i adres IP lub DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Kliknij przycisk **OK** aby zamknąć okno dialogowe Nowy klient RADIUS.

### <a name="configure-network-policy"></a>Konfigurowanie zasad sieciowych

Pamiętaj, że serwer zasad Sieciowych przy użyciu rozszerzenia usługi Azure MFA jest magazynem wyznaczonym centralne zasady dla zasad autoryzacji połączeń (CAP). W związku z tym należy zaimplementować limit na serwerze NPS w celu autoryzowania połączeń prawidłowe żądania.  

1. Na serwerze NPS Otwórz konsoli serwera NPS (lokalny), rozwiń węzeł **zasady**i kliknij przycisk **zasad sieciowych**.
1. Kliknij prawym przyciskiem myszy **połączenia z innymi serwerami dostępu**i kliknij przycisk **Duplikuj zasady**.

   ![Duplikowanie połączenia z innymi zasadami serwerów dostępu](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Kliknij prawym przyciskiem myszy **kopiowania połączenia z innymi serwerami dostępu**i kliknij przycisk **właściwości**.
1. W **kopiowania połączenia z innymi serwerami dostępu** okno dialogowe, **nazwa_zasad**, wprowadź odpowiednią nazwę, taką jak _RDG_CAP_. Sprawdź **włączone zasady**i wybierz **udzielić dostępu**. Opcjonalnie w **typ serwera dostępu do sieci**, wybierz opcję **bramy usług pulpitu zdalnego**, można także pozostawić ją jako **nieokreślony**.

   ![Nazwij zasady, Włącz i Udziel dostępu](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Kliknij przycisk **ograniczenia** kartę i sprawdź **Zezwalaj klientom na łączenie się bez negocjowania metodę uwierzytelniania**.

   ![Modyfikowanie metod uwierzytelniania, aby umożliwić klientom nawiązywanie połączeń](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Opcjonalnie kliknij **warunki** karta i Dodaj warunki, które muszą zostać spełnione połączenia autoryzowany, np. członkostwa w określonej grupie Windows.

   ![Opcjonalnie określ warunki połączenia](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Kliknij przycisk **OK**. Gdy zostanie wyświetlony monit, aby wyświetlić odpowiedni temat pomocy, kliknij przycisk **nie**.
1. Upewnij się, że nowe zasady jest na początku listy, czy zasady są włączone, i on przyznanie dostępu.

   ![Przenieś zasady na początek listy](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Zweryfikuj konfigurację

Aby sprawdzić konfigurację, należy zalogować się do bramy usług pulpitu zdalnego za pomocą odpowiedniego klienta RDP. Pamiętaj używać konta, które jest dozwolony przez użytkownika zasady autoryzacji połączeń i jest włączone dla usługi Azure MFA.

Pokaż na poniższej ilustracji, można użyć **dostępu w sieci Web usług pulpitu zdalnego** strony.

![Testowanie w Pulpit zdalny Dostęp w sieci Web](./media/howto-mfa-nps-extension-rdg/image25.png)

Po pomyślnym wprowadzania poświadczeń dla uwierzytelniania podstawowego, okno dialogowe połączenie pulpitu zdalnego, wskazuje stan inicjowania połączenia zdalnego, jak pokazano poniżej. 

W przypadku pomyślnego uwierzytelnienia przy użyciu metody uwierzytelniania pomocniczego, który zostało wcześniej skonfigurowane w usłudze Azure MFA są połączone z zasobem. Jednak w przypadku dodatkowego uwierzytelniania zakończy się niepowodzeniem, są odmowa dostępu do zasobu. 

![Podłączanie pulpitu zdalnego inicjowania połączenia zdalnego](./media/howto-mfa-nps-extension-rdg/image26.png)

W poniższym przykładzie aplikacji Authenticator na urządzeniu z systemem Windows phone jest używany w celu zapewnienia dodatkowego uwierzytelniania.

![Przykład Windows Phone aplikacji Authenticator, która wyświetla weryfikację](./media/howto-mfa-nps-extension-rdg/image27.png)

Po pomyślnym uwierzytelnieniu przy użyciu metody uwierzytelniania pomocniczego, użytkownik jest zalogowany do bramy usług pulpitu zdalnego, jak zwykle. Jednak ze względu na to, że wymagane jest użycie pomocniczej metody uwierzytelniania przy użyciu aplikacji mobilnej na zaufanym urządzeniu, proces logowania jest bezpieczniejszy niż w przeciwnym razie.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Wyświetl dzienniki Podglądu zdarzeń dla zdarzeń pomyślnego logowania

Aby wyświetlić pomyślnych zdarzeń logowania w dziennikach podglądu zdarzeń Windows, mogą wydać następujące polecenie programu Windows PowerShell do wykonywania zapytań w dziennikach usług terminalowych Windows i Windows zabezpieczeń.

Aby wysłać zapytanie zdarzeń pomyślnego logowania w dzienniki operacyjne bramy _(zdarzenie Viewer\Applications i usług Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ , użyj następujących poleceń programu PowerShell:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* To polecenie wyświetla zdarzenia Windows, które pokazują użytkownika zostały spełnione wymagania zasad autoryzacji zasobów (zdalnego RD RAP) i uzyskał dostęp.

![Wyświetlanie zdarzeń przy użyciu programu PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* To polecenie wyświetla zdarzenia, które po użytkownik zostały spełnione wymagania zasad autoryzacji połączeń.

![Wyświetlanie zasad autoryzacji połączeń przy użyciu programu PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Można również wyświetlić ten dziennik i filtr dla zdarzenia identyfikatory, 300 i 200. Aby wysłać zapytanie zdarzeń pomyślnego logowania w dziennikach podglądu zdarzeń zabezpieczeń, użyj następującego polecenia:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* To polecenie można uruchomić na centralny serwer NPS lub serwer bramy usług pulpitu zdalnego.

![Przykładowe zdarzenia pomyślnego logowania](./media/howto-mfa-nps-extension-rdg/image30.png)

Jak pokazano poniżej, można wyświetlić w dzienniku zabezpieczeń lub widok niestandardowy usług zasad sieciowych i dostępu:

![Usługi zasad sieciowych i dostępu sieciowego Podgląd zdarzeń](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serwerze, na którym zainstalowano rozszerzenia serwera NPS dla usługi Azure MFA, można znaleźć podglądu zdarzeń, dzienniki aplikacji specyficzne dla rozszerzenia w _aplikacji i usług Logs\Microsoft\AzureMfa_.

![Podgląd zdarzeń autoryzacji dzienników aplikacji](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Rozwiązywanie problemów z przewodnika

Jeśli konfiguracja nie działa zgodnie z oczekiwaniami, jest pierwsze miejsce, aby rozpocząć rozwiązywanie problemów z, aby sprawdzić, czy użytkownik jest skonfigurowany do używania usługi Azure MFA. Użytkownik powinien połączyć się z [witryny Azure portal](https://portal.azure.com). Jeśli użytkownicy są monitowani o podanie dodatkowej weryfikacji i może pomyślnie uwierzytelnienia, można wyeliminować niepoprawnej konfiguracji usługi Azure MFA.

Jeśli usługi Azure MFA działa dla użytkowników, należy zapoznać się odpowiednie dzienniki zdarzeń. Obejmują one zdarzeń zabezpieczeń, operacyjne bramy i dzienniki usługi Azure MFA, które zostały omówione w poprzedniej sekcji.

Poniżej przedstawiono przykładowe dane wyjściowe dziennika zabezpieczeń wyświetlane zdarzenia logowania nie powiodło się (6273 identyfikator zdarzenia).

![Przykład zdarzenia nieudanego logowania](./media/howto-mfa-nps-extension-rdg/image33.png)

Poniżej przedstawiono zdarzenia związane z dzienników AzureMFA:

![Przykładowy dziennik usługi Azure MFA Podgląd zdarzeń](./media/howto-mfa-nps-extension-rdg/image34.png)

Aby wykonywać zaawansowane rozwiązywanie problemów z opcjami, zapoznaj się z serwera NPS format pliki dziennika bazy danych zainstalowaną usługę serwera NPS. Te pliki dziennika są tworzone w _%SystemRoot%\System32\Logs_ folder jako pliki tekstowe rozdzielonych przecinkami.

Aby uzyskać opis tych plików dziennika, zobacz [interpretacji pliki dziennika Format bazy danych serwera NPS](https://technet.microsoft.com/library/cc771748.aspx). Wpisy w tych plikach dziennika może być trudne do interpretacji, nie importując ich w arkuszu kalkulacyjnym lub bazie danych. Aby ułatwić interpretowanie pliki dziennika można znaleźć kilka online usługi IAS analizatory składni.

Na poniższym obrazie przedstawiono dane wyjściowe tego takich do pobrania [aplikacji "shareware"](https://www.deepsoftware.com/iasviewer).

![Przykładowy Parser usługi IAS dla aplikacji "shareware"](./media/howto-mfa-nps-extension-rdg/image35.png)

Na koniec, aby uzyskać dodatkowe Rozwiązywanie problemów z opcjami, możesz użyć analizatora protokołów, takich [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

Obraz poniżej z Microsoft Message Analyzer przedstawia ruch sieciowy, filtrowanie protokołu RADIUS, który zawiera nazwę użytkownika **CONTOSO\AliceC**.

![Program Microsoft Message Analyzer pokazujący filtrowany ruch](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Następne kroki

[Jak uzyskać usługę Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS](howto-mfaserver-nps-rdg.md)

[Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
