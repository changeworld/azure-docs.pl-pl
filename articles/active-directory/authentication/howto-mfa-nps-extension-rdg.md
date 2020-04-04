---
title: Integracja usług RDG z rozszerzeniem nps usługi Azure MFA — usługa Azure Active Directory
description: Integracja infrastruktury bramy usług pulpitu zdalnego z usługą Azure MFA przy użyciu rozszerzenia serwera zasad sieciowych dla platformy Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c61bea7f3ca1105edfec54501c5f0725a5a10225
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654111"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integracja infrastruktury bramy usług pulpitu zdalnego przy użyciu rozszerzenia serwera zasad sieciowych (NPS) i usługi Azure AD

Ten artykuł zawiera szczegółowe informacje dotyczące integracji infrastruktury bramy usług pulpitu zdalnego z uwierzytelnianiem wieloskładnikowym (MFA) platformy Azure przy użyciu rozszerzenia serwera zasad sieciowych (NPS) dla platformy Microsoft Azure.

Rozszerzenie serwera zasad sieciowych (NPS) dla platformy Azure umożliwia klientom zabezpieczenie uwierzytelniania klienta usługi radius (Remote Authentication Dial-In User Service) przy użyciu [uwierzytelniania wieloskładnikowego (MFA)](multi-factor-authentication.md)platformy Azure. To rozwiązanie zapewnia weryfikację dwuetapową w celu dodania drugiej warstwy zabezpieczeń do logowania i transakcji użytkowników.

Ten artykuł zawiera instrukcje krok po kroku dotyczące integracji infrastruktury serwera NPS z usługą Azure MFA przy użyciu rozszerzenia NPS dla platformy Azure. Umożliwia to bezpieczną weryfikację dla użytkowników próbujących zalogować się do bramy pulpitu zdalnego.

> [!NOTE]
> Ten artykuł nie powinien być używany z wdrożeniami serwera usługi MFA i powinien być używany tylko z wdrożeniami usługi Azure MFA (cloud-based).

Usługi zasad sieciowych i dostępu (NPS) dają organizacjom możliwość wykonywania następujących czynności:

* Zdefiniuj lokalizacje centralne dla zarządzania i kontroli żądań sieciowych, określając, kto może się łączyć, jakie pory dnia połączenia są dozwolone, czas trwania połączeń i poziom zabezpieczeń, których klienci muszą używać do łączenia się itd. Zamiast określać te zasady na każdym serwerze bramy sieci VPN lub usług pulpitu zdalnego ,zasady te można określić raz w centralnej lokalizacji. Protokół RADIUS zapewnia scentralizowane uwierzytelnianie, autoryzację i księgowanie (AAA).
* Ustanawianie i wymuszanie zasad kondycji klienta ochrony dostępu do sieci (NAP), które określają, czy urządzeniom mają nieograniczony czy ograniczony dostęp do zasobów sieciowych.
* Zapewnij środki wymuszania uwierzytelniania i autoryzacji dostępu do bezprzewodowych punktów dostępu i przełączników Ethernet o wymiarach 802.1x.

Zazwyczaj organizacje używają serwera NPS (RADIUS) do upraszczania i scentralizowania zarządzania zasadami sieci VPN. Jednak wiele organizacji używa również nps, aby uprościć i scentralizować zarządzanie zasadami autoryzacji połączeń pulpitu zdalnego (RD CAPs).

Organizacje mogą również zintegrować serwery NPS z usługą Azure MFA w celu zwiększenia bezpieczeństwa i zapewnienia wysokiego poziomu zgodności. Pomaga to zapewnić, że użytkownicy ustanawiają weryfikację dwuetapową w celu zalogowania się do bramy usług pulpitu zdalnego. Aby użytkownicy mieli dostęp, muszą podać kombinację nazwy użytkownika i hasła wraz z informacjami, które użytkownik ma pod kontrolą. Informacje te muszą być zaufane i nie można łatwo powielać, takie jak numer telefonu komórkowego, numer stacjonarny, aplikacja na urządzeniu przenośnym i tak dalej. RDG obsługuje obecnie powiadomienia telefoniczne i wypychane z metod aplikacji akcesoracjatora firmy Microsoft dla 2FA. Aby uzyskać więcej informacji na temat obsługiwanych metod uwierzytelniania, zobacz sekcję [Określanie metod uwierzytelniania, z których użytkownicy mogą korzystać](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Przed dostępnością rozszerzenia SERWERA NPS dla platformy Azure klienci, którzy chcieli zaimplementować weryfikację dwuetapową dla zintegrowanych środowisk NPS i usługi Azure MFA, musieli skonfigurować i obsługiwać oddzielny serwer usługi MFA w środowisku lokalnym, zgodnie z dokumentami w [usłudze Brama usług pulpitu zdalnego i serwer uwierzytelniania wieloskładnikowego platformy Azure przy użyciu usługi RADIUS](howto-mfaserver-nps-rdg.md).

Dostępność rozszerzenia serwera NPS dla platformy Azure daje teraz organizacjom możliwość wdrożenia lokalnego rozwiązania usługi MFA lub rozwiązania usługi MFA opartego na chmurze w celu zabezpieczenia uwierzytelniania klienta usługi RADIUS.

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Aby użytkownicy mogli uzyskać dostęp do zasobów sieciowych za pośrednictwem bramy usług pulpitu zdalnego, muszą spełniać warunki określone w jednej zasadie autoryzacji połączeń usług pulpitu zdalnego (RD CAP) i jednej zasadie autoryzacji zasobów usług pulpitu zdalnego (RD RAP). RD CAPs określić, kto jest upoważniony do łączenia się z bramami usług pulpitu zdalnego. Rd RAPs określić zasoby sieciowe, takie jak pulpity zdalne lub aplikacje zdalne, które użytkownik może połączyć się za pośrednictwem bramy usług pulpitu zdalnego.

Bramę usług pulpitu zdalnego można skonfigurować do używania centralnego magazynu zasad dla rd CAPs. Rd RAPs nie można użyć zasad centralnych, ponieważ są one przetwarzane w bramie usług pulpitu zdalnego. Przykładem bramy usług pulpitu zdalnego skonfigurowanej do używania centralnego magazynu zasad dla RD CAPs jest klient radius dla innego serwera NPS, który służy jako centralny magazyn zasad.

Gdy rozszerzenie SERWERA NPS dla platformy Azure jest zintegrowane z usługą NPS i bramą pulpitu zdalnego, pomyślny przepływ uwierzytelniania jest następujący:

1. Serwer bramy usług pulpitu zdalnego otrzymuje żądanie uwierzytelnienia od użytkownika pulpitu zdalnego w celu nawiązania połączenia z zasobem, takim jak sesja pulpitu zdalnego. Działając jako klient RADIUS, serwer bramy usług pulpitu zdalnego konwertuje żądanie na komunikat żądania dostępu USŁUGI RADIUS i wysyła wiadomość do serwera RADIUS (NPS), na którym jest zainstalowane rozszerzenie NPS.
1. Kombinacja nazwy użytkownika i hasła jest weryfikowana w usłudze Active Directory, a użytkownik jest uwierzytelniany.
1. Jeśli spełnione są wszystkie warunki określone w żądaniu połączenia serwera NPS i zasadach sieciowych (na przykład ograniczenia członkostwa o porze dnia lub w grupie), rozszerzenie SERWERA NIENP wyzwala żądanie uwierzytelniania pomocniczego za pomocą usługi Azure MFA.
1. Usługa Azure MFA komunikuje się z usługą Azure AD, pobiera szczegóły użytkownika i wykonuje uwierzytelnianie pomocnicze przy użyciu obsługiwanych metod.
1. Po powodzeniu wyzwania usługi MFA usługa Azure MFA komunikuje wynik z rozszerzeniem serwera NPS.
1. Serwer NPS, na którym jest zainstalowane rozszerzenie, wysyła komunikat radius access-accept dla zasad rd CAP do serwera bramy usług pulpitu zdalnego.
1. Użytkownik otrzymuje dostęp do żądanego zasobu sieciowego za pośrednictwem bramy usług pulpitu zdalnego.

## <a name="prerequisites"></a>Wymagania wstępne

W tej sekcji opisano wymagania wstępne niezbędne przed integracją usługi Azure MFA z bramą pulpitu zdalnego. Przed rozpoczęciem należy mieć następujące wymagania wstępne w miejscu.  

* Infrastruktura usług pulpitu zdalnego (RDS)
* Licencja usługi Azure MFA
* Oprogramowanie systemu Windows Server
* Rola Zasad sieciowych i usług dostępu (NPS)
* Usługa Azure Active Directory zsynchronizowana z lokalną usługą Active Directory
* Identyfikator identyfikatora GUID usługi Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infrastruktura usług pulpitu zdalnego (RDS)

W miejscu musi być działająca infrastruktura usług pulpitu zdalnego (RDS). Jeśli tego nie zrobisz, możesz szybko utworzyć tę infrastrukturę na platformie Azure, korzystając z następującego szablonu szybkiego startu: [Tworzenie wdrożenia kolekcji sesji usług pulpitu zdalnego](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Jeśli chcesz ręcznie utworzyć lokalną infrastrukturę RDS szybko do celów testowych, wykonaj kroki, aby wdrożyć jedną z nich.
**Dowiedz się więcej:** [Wdrażanie usług pulpitu zdalnego za pomocą przewodnika Szybki start platformy Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) i wdrażania [podstawowej infrastruktury usług pulpitu zdalnego.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

### <a name="azure-mfa-license"></a>Licencja usługi Azure MFA

Wymagane jest licencja dla usługi Azure MFA, która jest dostępna za pośrednictwem usługi Azure AD Premium lub innych pakietów, które go zawierają. Licencje oparte na zużyciu dla usługi Azure MFA, takie jak na użytkownika lub licencje uwierzytelniania, nie są zgodne z rozszerzeniem serwera NPS. Aby uzyskać więcej informacji, zobacz [Jak uzyskać uwierzytelnianie wieloskładnikowe platformy Azure](concept-mfa-licensing.md). Do celów testowych można użyć subskrypcji próbnej.

### <a name="windows-server-software"></a>Oprogramowanie systemu Windows Server

Rozszerzenie NPS wymaga dodatku SP1 dla systemu Windows Server 2008 R2 lub nowszego z zainstalowaną usługą roli NPS. Wszystkie kroki opisane w tej sekcji zostały wykonane przy użyciu systemu Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Rola Zasad sieciowych i usług dostępu (NPS)

Usługa roli NPS zapewnia funkcje serwera i klienta RADIUS, a także usługę kondycji zasad dostępu sieciowego. Ta rola musi być zainstalowana na co najmniej dwóch komputerach w infrastrukturze: Brama usług pulpitu zdalnego i inny serwer członkowski lub kontroler domeny. Domyślnie rola jest już obecna na komputerze skonfigurowanym jako brama pulpitu zdalnego.  Należy również zainstalować rolę SERWERA NPS przynajmniej na innym komputerze, takim jak kontroler domeny lub serwer członkowski.

Aby uzyskać informacje dotyczące instalowania usługi roli NPS w systemie Windows Server 2012 lub starszym, zobacz [Instalowanie serwera zasad kondycji ochrony dostępu do sieci](https://technet.microsoft.com/library/dd296890.aspx). Aby uzyskać opis najlepszych rozwiązań dotyczących serwera NPS, w tym zalecenie zainstalowania serwera NPS na kontrolerze domeny, zobacz [Najważniejsze wskazówki dotyczące serwera NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Usługa Azure Active Directory zsynchronizowana z lokalną usługą Active Directory

Aby użyć rozszerzenia SERWERA, użytkownicy lokalni muszą być synchronizowani z usługą Azure AD i włączeni dla usługi MFA. W tej sekcji przyjęto założenie, że użytkownicy lokalni są zsynchronizowani z usługą Azure AD przy użyciu usługi AD Connect. Aby uzyskać informacje na temat połączenia usługi Azure AD, zobacz [Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Identyfikator identyfikatora GUID usługi Azure Active Directory

Aby zainstalować rozszerzenie SERWERA SIECIOWEGO, musisz znać identyfikator GUID usługi Azure AD. Instrukcje dotyczące znajdowania identyfikatora GUID usługi Azure AD znajdują się poniżej.

## <a name="configure-multi-factor-authentication"></a>Konfigurowanie uwierzytelniania wieloskładnikowego

Ta sekcja zawiera instrukcje dotyczące integrowania usługi Azure MFA z bramą pulpitu zdalnego. Jako administrator należy skonfigurować usługę usługi Azure MFA, zanim użytkownicy będą mogli samodzielnie zarejestrować swoje urządzenia lub aplikacje wieloskładnikowe.

Wykonaj kroki opisane w wprowadzenie [do usługi Azure Uwierzytelnianie wieloskładnikowe w chmurze,](howto-mfa-getstarted.md) aby włączyć usługi MFA dla użytkowników usługi Azure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurowanie kont do weryfikacji dwuetapowej

Po włączeniu konta dla usługi MFA nie można zalogować się do zasobów podlega nych zasadom usługi MFA, dopóki nie zostanie pomyślnie skonfigurowane zaufane urządzenie do użycia dla drugiego czynnika uwierzytelniania i uwierzytelnienie za pomocą weryfikacji dwuetapowej.

Wykonaj kroki opisane w [temacie Co oznacza dla mnie uwierzytelnianie wieloskładnikowe platformy Azure?](../user-help/multi-factor-authentication-end-user.md)

## <a name="install-and-configure-nps-extension"></a>Instalowanie i konfigurowanie rozszerzenia NPS

Ta sekcja zawiera instrukcje dotyczące konfigurowania infrastruktury usług pulpitu zdalnego do używania usługi Azure MFA do uwierzytelniania klienta za pomocą bramy usług pulpitu zdalnego.

### <a name="acquire-azure-active-directory-guid-id"></a>Uzyskiwanie identyfikatora GUID usługi Azure Active Directory

W ramach konfiguracji rozszerzenia serwera NPS należy podać poświadczenia administratora i identyfikator usługi Azure AD dla dzierżawy usługi Azure AD. Poniższe kroki pokazują, jak uzyskać identyfikator dzierżawy.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny dzierżawy platformy Azure.
1. W menu portalu Azure wybierz pozycję **Azure Active Directory**lub wyszukaj i wybierz **usługę Azure Active Directory** z dowolnej strony.
1. Wybierz **pozycję Właściwości**.
1. W bloku Właściwości obok identyfikatora katalogu kliknij ikonę **Kopiuj,** jak pokazano poniżej, aby skopiować identyfikator do schowka.

   ![Uzyskiwanie identyfikatora katalogu z witryny Azure portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia NPS

Zainstaluj rozszerzenie NPS na serwerze z zainstalowaną rolą Zasad sieciowych i Usług dostępu (NPS). Działa to jako serwer RADIUS dla twojego projektu.

> [!Important]
> Upewnij się, że rozszerzenie NPS nie jest instalowane na serwerze bramy usług pulpitu zdalnego.
>

1. Pobierz [rozszerzenie NPS](https://aka.ms/npsmfa).
1. Skopiuj plik wykonywalny instalatora (NpsExtnForAzureMfaInstaller.exe) na serwer NPS.
1. Na serwerze NPS kliknij dwukrotnie pozycję **NpsExtnForAzureMfaInstaller.exe**. Jeśli zostanie wyświetlony monit, kliknij przycisk **Uruchom**.
1. W oknie dialogowym Rozszerzenie serwera NPS dla instalacji usługi Azure MFA zapoznaj się z postanowieniami licencyjnymi dotyczących oprogramowania, zaznacz, że **zgadzam się z warunkami licencji i**kliknij przycisk **Zainstaluj**.
1. W oknie dialogowym Rozszerzenie SERWERA NPS dla instalatora usługi Azure MFA kliknij przycisk **Zamknij**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurowanie certyfikatów do użycia z rozszerzeniem NPS przy użyciu skryptu programu PowerShell

Następnie należy skonfigurować certyfikaty do użycia przez rozszerzenie NPS, aby zapewnić bezpieczną komunikację i pewność. Składniki serwera NPS zawierają skrypt programu Windows PowerShell, który konfiguruje certyfikat z podpisem własnym do użytku z serwerem NPS.

Skrypt wykonuje następujące akcje:

* Tworzy certyfikat z podpisem własnym
* Skojarzy klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD
* Przechowuje certyfikat w lokalnym magazynie maszyn
* Udziela użytkownikowi sieci dostępu do klucza prywatnego certyfikatu
* Ponowne uruchamianie usługi serwera zasad sieciowych

Jeśli chcesz użyć własnych certyfikatów, musisz skojarzyć klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD i tak dalej.

Aby użyć skryptu, podaj rozszerzenie z poświadczeniami administratora usługi Azure AD i identyfikatorem dzierżawy usługi Azure AD, który został skopiowany wcześniej. Uruchom skrypt na każdym serwerze NPS, na którym zainstalowano rozszerzenie NPS. Następnie wykonaj poniższe czynności:

1. Otwórz administracyjny monit programu Windows PowerShell.
1. W wierszu programu PowerShell wpisz `cd 'c:\Program Files\Microsoft\AzureMfa\Config'`i naciśnij klawisz **ENTER**.
1. Wpisz `.\AzureMfaNpsExtnConfigSetup.ps1`i naciśnij **klawisz ENTER**. Skrypt sprawdza, czy jest zainstalowany moduł programu PowerShell usługi Azure Active Directory. Jeśli nie jest zainstalowany, skrypt instaluje moduł dla Ciebie.

   ![Uruchamianie pliku AzureMfaNpsExtnConfigSetup.ps1 w programie Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Po sprawdzeniu instalacji modułu programu PowerShell zostanie wyświetlone okno dialogowe modułu programu Azure Active Directory PowerShell. W oknie dialogowym wprowadź poświadczenia administratora usługi Azure AD i hasło, a następnie kliknij przycisk **Zaloguj**się .

   ![Uwierzytelnianie w usłudze Azure AD w programie PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Po wyświetleniu monitu wklej identyfikator katalogu skopiowany wcześniej do schowka i naciśnij klawisz **ENTER**.

   ![Wprowadzanie identyfikatora katalogu w programie PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skrypt tworzy certyfikat z podpisem własnym i wykonuje inne zmiany konfiguracji. Dane wyjściowe powinny przypominać obraz pokazany poniżej.

   ![Dane wyjściowe programu PowerShell z certyfikatem z podpisem własnym](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurowanie składników serwera NPS w bramie usług pulpitu zdalnego

W tej sekcji można skonfigurować zasady autoryzacji połączeń bramy usług pulpitu zdalnego i inne ustawienia usługi RADIUS.

Przepływ uwierzytelniania wymaga wymiany komunikatów RADIUS między bramą usług pulpitu zdalnego a serwerem NPS, na którym jest zainstalowane rozszerzenie NPS. Oznacza to, że należy skonfigurować ustawienia klienta USŁUGI RADIUS zarówno na bramie usług pulpitu zdalnego, jak i na serwerze NPS, na którym jest zainstalowane rozszerzenie NPS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurowanie zasad autoryzacji połączeń bramy usług pulpitu zdalnego w celu używania magazynu centralnego

Zasady autoryzacji połączeń usług pulpitu zdalnego (RD CAPs) określają wymagania dotyczące łączenia się z serwerem bramy usług pulpitu zdalnego. RD CAPs mogą być przechowywane lokalnie (domyślnie) lub mogą być przechowywane w centralnym magazynie RD CAP, który jest uruchomiony nps. Aby skonfigurować integrację usługi Azure MFA z usługą RDS, należy określić użycie magazynu centralnego.

1. Na serwerze bramy usług pulpitu zdalnego otwórz **Menedżera serwera**.
1. W menu kliknij polecenie **Narzędzia**, wskaż pozycję **Usługi pulpitu zdalnego**, a następnie kliknij polecenie **Menedżer bramy usług pulpitu zdalnego**.
1. W Menedżerze bramy usług pulpitu zdalnego kliknij prawym przyciskiem myszy ** \[pozycję Nazwa\] serwera (lokalna)** i kliknij polecenie **Właściwości**.
1. W oknie dialogowym Właściwości wybierz kartę **Sklep RD CAP.**
1. Na karcie Sklep RD CAP wybierz pozycję **Serwer centralny z systemem NPS**. 
1. W polu **Wprowadź nazwę lub adres IP serwera z systemem NPS** wpisz adres IP lub nazwę serwera, na którym zainstalowano rozszerzenie NPS.

   ![Wprowadź nazwę lub adres IP serwera NPS](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Kliknij przycisk **Dodaj**.
1. W oknie dialogowym **Wspólny klucz tajny** wprowadź wspólny klucz tajny, a następnie kliknij przycisk **OK**. Upewnij się, że rejestrujesz ten udostępniony klucz tajny i bezpiecznie przechowujesz rekord.

   >[!NOTE]
   >Wspólny klucz tajny jest używany do ustanawiania zaufania między serwerami i klientami RADIUS. Stwórz długi i złożony sekret.
   >

   ![Tworzenie wspólnego klucza tajnego w celu ustanowienia zaufania](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Kliknij przycisk **OK**, aby zamknąć okno dialogowe.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurowanie wartości limitu czasu usługi RADIUS w serwerze NPS bramy usług pulpitu zdalnego

Aby upewnić się, że jest czas na sprawdzanie poprawności poświadczeń użytkowników, wykonywanie weryfikacji dwuetapowej, odbieranie odpowiedzi i odpowiadanie na komunikaty radius, konieczne jest dostosowanie wartości limitu czasu radius.

1. Na serwerze bramy usług pulpitu zdalnego otwórz Menedżera serwera. W menu kliknij polecenie **Narzędzia**, a następnie kliknij polecenie **Serwer zasad sieciowych**.
1. W konsoli **NPS (Local)** rozwiń węzeł **Klienci i serwery RADIUS**i wybierz pozycję **Remote RADIUS Server**.

   ![Konsola zarządzania serwerem zasad sieciowych z monitorem zdalnego serwera RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

1. W okienku szczegółów kliknij dwukrotnie pozycję **Grupa serwerów bramy usług terminalowych**.

   >[!NOTE]
   >Ta grupa serwerów RADIUS została utworzona podczas konfigurowania serwera centralnego dla zasad serwera NPS. Brama usług pulpitu zdalnego przesyła dalej komunikaty RADIUS do tego serwera lub grupy serwerów, jeśli więcej niż jeden w grupie.
   >

1. W oknie dialogowym **Właściwości grupy serwera bramy usług terminalowych** wybierz adres IP lub nazwę serwera NPS skonfigurowanego do przechowywania RD CAPs, a następnie kliknij przycisk **Edytuj**.

   ![Wybierz adres IP lub nazwę serwera NPS skonfigurowanego wcześniej](./media/howto-mfa-nps-extension-rdg/image13.png)

1. W oknie dialogowym **Edytowanie serwera RADIUS** wybierz kartę **Równoważenie obciążenia.**
1. Na karcie **Równoważenie obciążenia** w polu **Liczba sekund bez odpowiedzi, zanim żądanie zostanie uznane za porzucone,** zmień wartość domyślną z 3 na wartość z 30 do 60 sekund.
1. W polu **Liczba sekund między żądaniami, gdy serwer jest identyfikowany jako niedostępny,** zmień domyślną wartość 30 sekund na wartość równą lub większą niż wartość określona w poprzednim kroku.

   ![Edytowanie ustawień limitu czasu serwera Radius na karcie równoważenia obciążenia](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Kliknij przycisk **OK** dwa razy, aby zamknąć okna dialogowe.

### <a name="verify-connection-request-policies"></a>Weryfikowanie zasad żądania połączenia

Domyślnie podczas konfigurowania bramy usług pulpitu zdalnego do używania centralnego magazynu zasad dla zasad autoryzacji połączeń brama usług pulpitu zdalnego jest skonfigurowana do przesyłania dalej żądań CAP do serwera NPS. Serwer NPS z zainstalowanym rozszerzeniem usługi Azure MFA przetwarza żądanie dostępu RADIUS. W poniższych krokach pokazano, jak zweryfikować domyślne zasady żądania połączenia.  

1. W bramie usług pulpitu zdalnego w konsoli NPS (Local) rozwiń węzeł **Zasady**i wybierz pozycję **Zasady żądań połączeń**.
1. Kliknij dwukrotnie pozycję **Zasady autoryzacji bramy usług terminalowych**.
1. W oknie dialogowym **Właściwości ZASAD AUTORYZACJI BRAMY USŁUG TERMINALOWYCH** kliknij kartę **Ustawienia.**
1. Na karcie **Ustawienia** w obszarze Przesyłanie dalej żądania połączenia kliknij pozycję **Uwierzytelnianie**. Klient usługi RADIUS jest skonfigurowany do przesyłania dalej żądań uwierzytelniania.

   ![Konfigurowanie ustawień uwierzytelniania określających grupę serwerów](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Kliknij przycisk **Anuluj**.

>[!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia zasad żądania połączenia, zobacz artykuł Konfigurowanie dokumentacji [zasad żądań połączeń](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) dla tego samego. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurowanie serwera NPS na serwerze, na którym jest zainstalowane rozszerzenie NPS

Serwer NPS, na którym jest zainstalowane rozszerzenie NPS, musi mieć możliwość wymiany komunikatów RADIUS z serwerem NPS w bramie usług pulpitu zdalnego. Aby włączyć tę wymianę komunikatów, należy skonfigurować składniki serwera NPS na serwerze, na którym jest zainstalowana usługa rozszerzenia NPS.

### <a name="register-server-in-active-directory"></a>Rejestrowanie serwera w usłudze Active Directory

Aby działać poprawnie w tym scenariuszu, serwer NPS musi być zarejestrowany w usłudze Active Directory.

1. Na serwerze NPS otwórz **Menedżera serwera**.
1. W Menedżerze serwera kliknij **narzędzia**, a następnie kliknij przycisk **serwer zasad sieciowych**.
1. W konsoli Serwer zasad sieciowych kliknij prawym przyciskiem myszy **pozycję NPS (Local),** a następnie kliknij polecenie **Zarejestruj serwer w usłudze Active Directory**.
1. Kliknij **przycisk OK** dwa razy.

   ![Rejestrowanie serwera NPS w usłudze Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Pozostaw konsolę otwartą dla następnej procedury.

### <a name="create-and-configure-radius-client"></a>Tworzenie i konfigurowanie klienta USŁUGI RADIUS

Brama usług pulpitu zdalnego musi być skonfigurowana jako klient RADIUS na serwerze NPS.

1. Na serwerze NPS, na którym jest zainstalowane rozszerzenie NPS, w konsoli **NPS (Local)** kliknij prawym przyciskiem myszy **pozycję Klienci RADIUS** i kliknij polecenie **Nowy**.

   ![Tworzenie nowego klienta RADIUS w konsoli NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. W oknie dialogowym **Nowy klient usługi RADIUS** podaj przyjazną nazwę, taką jak _Brama,_ oraz adres IP lub nazwę DNS serwera bramy usług pulpitu zdalnego.
1. W **polach Wspólny klucz tajny** i **Potwierdź udostępniony klucz tajny** wprowadź ten sam klucz tajny, który był wcześniej używany.

   ![Konfigurowanie przyjaznej nazwy oraz adresu IP lub adresu DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Kliknij **przycisk OK,** aby zamknąć okno dialogowe Nowy klient usługi RADIUS.

### <a name="configure-network-policy"></a>Konfigurowanie zasad sieciowych

Przypomnijmy, że serwer NPS z rozszerzeniem usługi Azure MFA jest wyznaczonym centralnym magazynem zasad dla zasad autoryzacji połączeń (CAP). W związku z tym należy zaimplementować CAP na serwerze NPS do autoryzowania prawidłowych żądań połączeń.  

1. Na serwerze NPS otwórz konsolę NPS (Local), rozwiń pozycję **Zasady**i kliknij pozycję **Zasady sieciowe**.
1. Kliknij prawym przyciskiem myszy pozycję **Połączenia z innymi serwerami dostępu**i kliknij polecenie **Zduplikowane zasady**.

   ![Powielanie połączenia z innymi zasadami serwerów dostępu](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Kliknij prawym przyciskiem myszy pozycję **Kopia połączeń z innymi serwerami dostępu,** a następnie kliknij polecenie **Właściwości**.
1. W oknie dialogowym **Kopiowanie połączeń z innymi serwerami dostępu** w **polu Nazwa zasad**wprowadź odpowiednią nazwę, taką jak _RDG_CAP_. Zaznacz **opcję Zasada włączona**i wybierz **pozycję Przyznaj dostęp**. Opcjonalnie w **obszarze Typ serwera dostępu do sieci**wybierz pozycję **Brama pulpitu zdalnego**lub możesz pozostawić ją jako **nieokreśloną**.

   ![Nadawanie nazw zasadom, włączanie i udzielanie dostępu](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Kliknij kartę **Ograniczenia** i zaznacz pole **wyboru Zezwalaj klientom na łączenie się bez negocjowania metody uwierzytelniania**.

   ![Modyfikowanie metod uwierzytelniania w celu umożliwienia klientom łączenia się](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Opcjonalnie kliknij kartę **Warunki** i dodaj warunki, które muszą być spełnione, aby połączenie zostało autoryzowane, na przykład członkostwo w określonej grupie systemu Windows.

   ![Opcjonalnie określ warunki połączenia](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Kliknij przycisk **OK**. Po wyświetleniu monitu o wyświetlenie odpowiedniego tematu Pomocy kliknij przycisk **Nie**.
1. Upewnij się, że nowe zasady znajdują się na początku listy, że zasady są włączone i że udzielają dostępu.

   ![Przenoszenie zasad na górę listy](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Weryfikowanie konfiguracji

Aby zweryfikować konfigurację, należy zalogować się do bramy usług pulpitu zdalnego przy pomocy odpowiedniego klienta RDP. Pamiętaj, aby użyć konta, które jest dozwolone przez zasady autoryzacji połączeń i jest włączone dla usługi Azure MFA.

Jak pokazano na poniższej ilustracji, można użyć strony **Dostęp do sieci Web pulpitu zdalnego.**

![Testowanie w programie Remote Desktop Web Access](./media/howto-mfa-nps-extension-rdg/image25.png)

Po pomyślnym wprowadzeniu poświadczeń do uwierzytelniania podstawowego okno dialogowe Podłączanie pulpitu zdalnego pokazuje stan Inicjowania połączenia zdalnego, jak pokazano poniżej. 

Jeśli pomyślnie uwierzytelnić się przy użyciu metody uwierzytelniania pomocniczego, które zostały wcześniej skonfigurowane w usłudze Azure MFA, są połączone z zasobem. Jeśli jednak uwierzytelnianie pomocnicze nie powiedzie się, odmówiono dostępu do zasobu. 

![Pulpit zdalny Inicjowanie połączenia zdalnego](./media/howto-mfa-nps-extension-rdg/image26.png)

W poniższym przykładzie aplikacja Authenticator na telefonie z systemem Windows jest używana do zapewnienia uwierzytelniania pomocniczego.

![Przykładowa aplikacja do uwierzytelniania systemu Windows Phone z weryfikacją](./media/howto-mfa-nps-extension-rdg/image27.png)

Po pomyślnym uwierzytelnieniu przy użyciu metody uwierzytelniania pomocniczego użytkownik jest zalogowany do bramy pulpitu zdalnego w normalny sposób. Jednak ponieważ wymagane jest użycie metody uwierzytelniania pomocniczego przy użyciu aplikacji mobilnej na zaufanym urządzeniu, proces logowania jest bezpieczniejszy niż w przeciwnym razie.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Wyświetlanie dzienników Podglądu zdarzeń dla pomyślnych zdarzeń logowania

Aby wyświetlić pomyślne zdarzenia logowania w dziennikach Podglądu zdarzeń systemu Windows, można wydać następujące polecenie Programu Windows PowerShell, aby zbadać dzienniki usług terminali systemu Windows i zabezpieczeń systemu Windows.

Aby zbadać pomyślne zdarzenia logowania w dziennikach operacyjnych bramy _(Podgląd zdarzeń\Dzienniki aplikacji i usług\Microsoft\Windows\TerminalServices-Gateway\Operational),_ użyj następujących poleceń programu PowerShell:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* To polecenie wyświetla zdarzenia systemu Windows, które pokazują, że użytkownik spełnia wymagania zasad autoryzacji zasobów (RD RAP) i otrzymał dostęp.

![Wyświetlanie zdarzeń przy użyciu programu PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* To polecenie wyświetla zdarzenia, które pokazują, kiedy użytkownik spełnił wymagania zasad autoryzacji połączenia.

![wyświetlanie zasad autoryzacji połączeń przy użyciu programu PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Można również wyświetlić ten dziennik i filtr na identyfikatory zdarzeń, 300 i 200. Aby zbadać pomyślne zdarzenia logowania w dziennikach przeglądarki zdarzeń zabezpieczeń, użyj następującego polecenia:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* To polecenie można uruchomić na centralnym serwerze NPS lub serwerze bramy usług pulpitu zdalnego.

![Przykładowe pomyślne zdarzenia logowania](./media/howto-mfa-nps-extension-rdg/image30.png)

Można również wyświetlić dziennik zabezpieczeń lub widok niestandardowy zasad sieciowych i usług dostępu, jak pokazano poniżej:

![Podgląd zdarzeń zasad sieciowych i usług dostępu](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serwerze, na którym zainstalowano rozszerzenie NPS dla usługi Azure MFA, można znaleźć dzienniki aplikacji Podglądu zdarzeń specyficzne dla rozszerzenia w _dziennikach aplikacji i usług\Microsoft\AzureMfa_.

![Dzienniki aplikacji AuthZ podglądu zdarzeń](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Przewodnik rozwiązywania problemów

Jeśli konfiguracja nie działa zgodnie z oczekiwaniami, pierwszym miejscem do rozpoczęcia rozwiązywania problemów jest sprawdzenie, czy użytkownik jest skonfigurowany do korzystania z usługi Azure MFA. Niech użytkownik połączy się z [witryną Azure portal](https://portal.azure.com). Jeśli użytkownicy są monitowani o weryfikację pomocniczą i mogą pomyślnie uwierzytelnić, można wyeliminować niepoprawną konfigurację usługi Azure MFA.

Jeśli usługa Azure MFA działa dla użytkowników, należy przejrzeć odpowiednie dzienniki zdarzeń. Należą do nich dzienniki zdarzeń zabezpieczeń, bramy operacyjnej i usługi Azure MFA, które zostały omówione w poprzedniej sekcji.

Poniżej znajduje się przykładowy wynik dziennika zabezpieczeń pokazujący zdarzenie logowania nie powiodło się (identyfikator zdarzenia 6273).

![Próbka zdarzenia logowania nie powiodło się](./media/howto-mfa-nps-extension-rdg/image33.png)

Poniżej znajduje się powiązane zdarzenie z dzienników AzureMFA:

![Przykładowy dziennik usługi Azure MFA w Podglądzie zdarzeń](./media/howto-mfa-nps-extension-rdg/image34.png)

Aby wykonać zaawansowane opcje rozwiązywania problemów, zapoznaj się z plikami dziennika formatu bazy danych SERWERA, w których jest zainstalowana usługa NPS. Te pliki dziennika są tworzone w _folderze %SystemRoot%\System32\Logs_ jako pliki tekstowe rozdzielane przecinkami.

Aby uzyskać opis tych plików dziennika, zobacz [Interpretowanie plików dziennika formatu bazy danych NPS](https://technet.microsoft.com/library/cc771748.aspx). Wpisy w tych plikach dziennika mogą być trudne do interpretacji bez importowania ich do arkusza kalkulacyjnego lub bazy danych. Można znaleźć kilka analizatorów IAS w trybie online, aby pomóc w interpretacji plików dziennika.

Poniższy obraz ekspozytury przedstawia dane wyjściowe jednej z takich [aplikacji shareware](https://www.deepsoftware.com/iasviewer)do pobrania.

![Przykładowy analizator IAS aplikacji Shareware](./media/howto-mfa-nps-extension-rdg/image35.png)

Na koniec, aby uzyskać dodatkowe opcje rozwiązywania problemów, można użyć analizatora protokołów, takiego jak [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

Poniższy obraz eksploratora wiadomości firmy Microsoft przedstawia ruch sieciowy filtrowany na protokole RADIUS zawierającym nazwę użytkownika **CONTOSO\AliceC**.

![Analizator komunikatów firmy Microsoft przedstawiający filtrowany ruch](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Następne kroki

[Jak uzyskać usługę Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS](howto-mfaserver-nps-rdg.md)

[Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
