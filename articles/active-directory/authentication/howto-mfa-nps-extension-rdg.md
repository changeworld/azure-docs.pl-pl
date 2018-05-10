---
title: Zdalne integracji bramy usług pulpitu z rozszerzeniem Azure MFA NPS | Dokumentacja firmy Microsoft
description: Integracja z usługą Azure MFA platformy Microsoft Azure przy użyciu rozszerzenia serwera zasad sieciowych infrastrukturę bramy usług pulpitu zdalnego
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: fbca491e845ad96ecafc6afb5f40d46b99c047bf
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrowanie infrastruktury bramy usług pulpitu zdalnego przy użyciu rozszerzenia serwera zasad sieciowych (NPS) i Azure AD

Ten artykuł zawiera szczegółowe informacje dla integrowanie infrastruktury bramy usług pulpitu zdalnego z usługi Azure Multi-Factor Authentication (MFA) przy użyciu rozszerzenia serwera zasad sieciowych (NPS) platformy Microsoft Azure. 

Rozszerzenia serwera zasad sieciowych (NPS) dla usługi Azure umożliwia klientom ochrony uwierzytelniania klienta usługi usługi użytkowników zdalnego uwierzytelniania (RADIUS), za pomocą usługi Azure na chmurze [Multi-Factor Authentication (MFA)](multi-factor-authentication.md). To rozwiązanie zapewnia weryfikację dwuetapową dodawania drugiej warstwy zabezpieczeń używanej do logowania użytkowników i transakcji.

Ten artykuł zawiera instrukcje krok po kroku do integracji z usługą Azure MFA infrastruktury serwera NPS przy użyciu rozszerzenia zasad Sieciowych na platformie Azure. Dzięki temu weryfikacji dla użytkownicy próbują zalogować się do bramy usług pulpitu zdalnego. 

Zasad sieciowych i dostępu do usługi (NPS) umożliwia organizacjom wykonaj następujące czynności:
* Zdefiniuj centralnej lokalizacji, zarządzania i kontroli żądania sieci, określając, kto może się połączyć, kiedy połączeń dnia są dozwolone, czas trwania połączenia i poziom zabezpieczeń klientów musi być używana do łączenia i tak dalej. Zamiast określenia tych zasad na każdym serwerze sieci VPN lub bramy usług pulpitu zdalnego (RD), te zasady można określić jeden raz w centralnej lokalizacji. Protokół RADIUS zapewnia scentralizowane uwierzytelnianie, autoryzację i Ewidencjonowanie. 
* Ustanów i wymuszanie zasad dotyczących kondycji klienta ochrony dostępu do sieci (NAP), które określają, czy urządzenia są przyznawane nieograniczonego lub ograniczonego dostępu do zasobów sieciowych.
* Umożliwiają wymuszające uwierzytelnianie i autoryzację na dostęp metodą 802. 1 punkty dostępu bezprzewodowego z obsługą i przełączników Ethernet.    

Zazwyczaj organizacji, Użyj serwera zasad Sieciowych (RADIUS), aby uprościć i scentralizowane zarządzanie siecią VPN zasad. Jednak w wielu organizacjach także używać serwera NPS do uproszczenia i scentralizowane zarządzanie zasady autoryzacji połączeń usług pulpitu usług pulpitu zdalnego (RD CAP). 

Organizacje można również zintegrować serwera NPS z usługą Azure MFA do zwiększenia poziomu bezpieczeństwa i zapewnienia wysokiego poziomu zgodności. Pomaga to zapewnić, że użytkownicy ustanowić weryfikacji dwuetapowej do logowania się do bramy usług pulpitu zdalnego. Aby użytkownicy mogli uzyskać dostęp użytkownik musi podać ich kombinacja nazwy użytkownika i hasła oraz informacje, które użytkownik ma w ich kontroli. Te informacje musi być zaufany i nie jest łatwo zduplikowany, takie jak numer telefonu komórkowego, numer telefonu stacjonarnego aplikacji na urządzeniu przenośnym i tak dalej.

Wcześniej dostępności rozszerzenia serwera zasad Sieciowych na platformie Azure, klientów, którzy chcieliby implementuje weryfikację dwuetapową dla zintegrowanego środowiska serwera zasad Sieciowych i usługi Azure MFA było konfigurowania i konserwacji osobny serwer usługi MFA w środowisku lokalnym, zgodnie z opisem w [bramy usług pulpitu zdalnego i Azure przy użyciu usługi RADIUS serwera Multi-Factor Authentication](howto-mfaserver-nps-rdg.md).

Dostępność rozszerzenia serwera zasad Sieciowych na platformie Azure pozwala organizacjom wyboru do wdrożenia rozwiązania MFA lokalne lub rozwiązanie MFA oparte na chmurze do bezpiecznego uwierzytelniania klienta RADIUS.

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Aby użytkownicy mogli uzyskać dostęp do zasobów sieciowych za pośrednictwem bramy usług pulpitu zdalnego muszą spełniać warunki określone w jednej usług pulpitu zdalnego zasady autoryzacji połączeń (RD CAP) i jeden usług pulpitu zdalnego zasady autoryzacji zasobów (RD RAP). Zasady RD CAP Określ, kto jest autoryzowany do nawiązania połączenia bramy usług pulpitu zdalnego. Zasady RD RAP Określ zasobów sieciowych, takich jak usługa Pulpit zdalny lub aplikacji zdalnych, które użytkownik będzie mógł nawiązywać połączenie za pośrednictwem bramy usług pulpitu zdalnego. 

Bramy usług pulpitu zdalnego można skonfigurować do używania magazynu centralne zasady dla RD CAP. Zasady RD RAP nie można użyć centralnych zasad, jak są przetwarzane w bramie usług pulpitu zdalnego. Przykład brama usług pulpitu zdalnego skonfigurowane do używania magazynu centralne zasady dla RD CAP jest klienta RADIUS do innego serwera NPS, który służy do przechowywania centralne zasady.

Gdy rozszerzenia serwera zasad Sieciowych na platformie Azure są zintegrowane z serwera zasad Sieciowych i bramy usług pulpitu zdalnego, przepływ pomyślne uwierzytelnienie jest następujący:

1. Serwer bramy usług pulpitu zdalnego otrzymuje żądanie uwierzytelnienia od użytkownika pulpitu zdalnego nawiązywania połączenia z zasobem, takich jak sesji pulpitu zdalnego. Działając jako klient usługi RADIUS, serwera bramy usług pulpitu zdalnego konwertuje żądanie na komunikat żądania dostępu usługi RADIUS i wysyła wiadomość do serwera RADIUS (NPS), w którym zainstalowano rozszerzenia serwera NPS. 
2. Kombinacja nazwy użytkownika i hasła została zweryfikowana w usłudze Active Directory, a użytkownik jest uwierzytelniony.
3. Jeśli są spełnione wszystkie warunki określone w żądanie połączenia serwera zasad Sieciowych i zasad sieciowych (na przykład pory dnia lub grupy ograniczeniami członkostwa), żądania uwierzytelniania dodatkowego z usługą Azure MFA wyzwala rozszerzenia serwera NPS. 
4. Usługa Azure MFA komunikuje się z usługą Azure AD, pobiera szczegóły użytkownika, a następnie wykonuje dodatkowego uwierzytelniania przy użyciu metody skonfigurowany przez użytkownika (wiadomości tekstowej, aplikacji mobilnych itd.). 
5. Na powodzenie żądanie uwierzytelniania MFA uwierzytelnianie wieloskładnikowe Azure komunikuje się wynik, który ma rozszerzenie serwera NPS.
6. Serwer zasad Sieciowych, w którym zainstalowano rozszerzenia, wysyła komunikat udzielenia dostępu RADIUS zasady RD CAP do serwera bramy usług pulpitu zdalnego.
7. Użytkownik uzyskuje dostęp do zasobu żądanej sieci za pośrednictwem bramy usług pulpitu zdalnego.

## <a name="prerequisites"></a>Wymagania wstępne
Ta sekcja zawiera szczegóły dotyczące wymagań wstępnych konieczne przed Integrowanie usługi Azure MFA z bramy usług pulpitu zdalnego. Przed rozpoczęciem, musi mieć następujące wymagania wstępne w miejscu.  

* Infrastruktura usług pulpitu zdalnego
* Azure MFA License
* Oprogramowanie Windows Server
* Rola zasad sieciowych i dostępu do usługi (NPS)
* Usługa Azure Active Directory zsynchronizowane z lokalną usługą Active Directory
* Identyfikator GUID usługi Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infrastruktura usług pulpitu zdalnego
W miejscu musi mieć działający infrastruktury usług pulpitu zdalnego (RDS). Jeśli nie chcesz, a następnie tej infrastruktury może szybko utworzyć na platformie Azure przy użyciu następującego szablonu szybki start: [wdrażania Utwórz kolekcję sesji pulpitu zdalnego](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Jeśli chcesz ręcznie utworzyć lokalnej infrastruktury usług pulpitu zdalnego dla celów testowych, wykonaj kroki, aby wdrożyć jeden. 
**Dowiedz się więcej**: [wdrożenia usług pulpitu zdalnego z szybki start Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) i [RDS podstawowe wdrożenie infrastruktury](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="azure-mfa-license"></a>Azure MFA License
Wymagana jest licencja dla usługi Azure MFA, który jest dostępny za pośrednictwem usługi Azure AD Premium, Enterprise Mobility plus Security (EMS) lub subskrypcji usługi MFA. Na podstawie zużycia licencji na usługę Azure MFA, takie zgodnie z harmonogramem użytkownika lub licencji uwierzytelniania na nie są zgodne z rozszerzeniem serwera NPS. Aby uzyskać więcej informacji, zobacz [sposobu uzyskania usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md). Do celów testowych, możesz użyć subskrypcji wersji próbnej. 

### <a name="windows-server-software"></a>Oprogramowanie Windows Server
Rozszerzenie serwera NPS wymaga systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszy z zainstalowaną usługą roli serwera NPS. Wszystkie kroki opisane w tej sekcji zostały wykonane przy użyciu systemu Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Rola zasad sieciowych i dostępu do usługi (NPS)
Usługi roli serwera NPS zawiera serwer usługi RADIUS i klienta, funkcje, a także zasady dostępu do sieci usługi kondycji. Musi być zainstalowana rola to na co najmniej dwa komputery w infrastrukturze: bramy usług pulpitu zdalnego i drugi serwer członkowski lub kontroler domeny. Domyślnie roli jest już obecny na komputerze, który został skonfigurowany jako brama usług pulpitu zdalnego.  Można również zainstalować rolę serwera zasad Sieciowych na co najmniej na innym komputerze, takich jak kontroler domeny lub serwer członkowski.

Aby uzyskać informacje na temat instalowania roli serwera NPS usługi systemu Windows Server 2012 lub starszy, zobacz [zainstalować serwer zasad dotyczących kondycji ochrony dostępu do sieci](https://technet.microsoft.com/library/dd296890.aspx). Opis najlepszych rozwiązań dla serwera NPS, w tym zalecenie, aby zainstalować serwer zasad Sieciowych na kontrolerze domeny, zobacz [najlepsze rozwiązania dotyczące serwera NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Usługa Azure Active Directory zsynchronizowane z lokalną usługą Active Directory
Aby użyć rozszerzenia serwera NPS, lokalnych użytkowników musi być zsynchronizowane z usługą Azure AD i włączone dla usługi MFA. W tej sekcji założono, że użytkowników lokalnych są zsynchronizowane z usługą Azure AD za pomocą AD Connect. Aby uzyskać informacje dotyczące usługi Azure AD connect, zobacz [integrację katalogów lokalnych z usługą Azure Active Directory](../connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Identyfikator GUID usługi Azure Active Directory
Aby zainstalować rozszerzenie serwera NPS, musisz znać identyfikator GUID usługi Azure AD. Instrukcje dotyczące znajdowania identyfikator GUID usługi Azure AD znajdują się poniżej.

## <a name="configure-multi-factor-authentication"></a>Skonfiguruj usługę Multi-Factor Authentication 
Ta sekcja zawiera instrukcje dotyczące integrowania usługi Azure MFA z bramy usług pulpitu zdalnego. Jako administrator należy skonfigurować usługę Azure MFA, zanim użytkownicy będą mogli samodzielnie zarejestrować ich wieloskładnikowego urządzenia lub aplikacji.

Postępuj zgodnie z instrukcjami [wprowadzenie do korzystania z usługi Azure Multi-Factor Authentication w chmurze](howto-mfa-getstarted.md) włączyć uwierzytelnianie wieloskładnikowe dla użytkowników usługi Azure AD. 

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurowanie konta na potrzeby weryfikacji dwuetapowej
Po włączeniu konta dla usługi MFA, nie możesz się zarejestrować do zasobów objętych zasady MFA, dopóki nie została pomyślnie skonfigurowana zaufanego urządzenia dla drugiego etapu uwierzytelniania i został uwierzytelniony przy użyciu weryfikacji dwuetapowej.

Postępuj zgodnie z instrukcjami [co oznacza Azure Multi-Factor Authentication dla mnie?](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md) do zrozumienia i poprawnie skonfigurować urządzenia dla usługi MFA z kontem użytkownika.

## <a name="install-and-configure-nps-extension"></a>Instalowanie i Konfigurowanie rozszerzeń serwera NPS
Ta sekcja zawiera instrukcje dotyczące konfigurowania infrastruktury usług pulpitu zdalnego do użycia usługi Azure MFA na potrzeby uwierzytelniania klienta z bramy usług pulpitu zdalnego.

### <a name="acquire-azure-active-directory-guid-id"></a>Uzyskać identyfikator GUID usługi Azure Active Directory

W ramach konfiguracji rozszerzenia serwera NPS musisz podać poświadczenia administratora i identyfikator usługi Azure AD dla dzierżawy usługi Azure AD. Poniższe kroki pokazują jak uzyskać identyfikator dzierżawcy.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator globalny dzierżawy platformy Azure.
2. W lewym obszarze nawigacji, wybierz **usługi Azure Active Directory** ikony.
3. Wybierz **właściwości**.
4. W bloku właściwości, obok identyfikator katalogu kliknij **kopiowania** ikony, jak pokazano poniżej, aby skopiować identyfikator do Schowka.

 ![Właściwości](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Zainstaluj rozszerzenie serwera NPS
Zainstaluj rozszerzenie serwera NPS na serwerze, który ma zainstalowaną rolę zasad sieciowych i dostępu do usługi (NPS). Funkcja ta działa jako serwer RADIUS dla projektu. 

>[!Important]
> Upewnij się, że nie należy instalować rozszerzenia serwera NPS na serwerze bramy usług pulpitu zdalnego.
> 

1. Pobierz [rozszerzenia serwera NPS](https://aka.ms/npsmfa). 
2. Kopiowanie pliku wykonywalnego Instalatora (NpsExtnForAzureMfaInstaller.exe) na serwerze zasad Sieciowych.
3. Na serwerze NPS, kliknij dwukrotnie **NpsExtnForAzureMfaInstaller.exe**. Jeśli zostanie wyświetlony monit, kliknij przycisk **Uruchom**.
4. W oknie dialogowym serwera NPS rozszerzenia dla usługi Azure MFA instalacji, przejrzyj postanowienia licencyjne dotyczące oprogramowania, sprawdź **zgadzam się z warunkami licencji**i kliknij przycisk **zainstalować**.
 
  ![Instalator usługi Azure MFA](./media/howto-mfa-nps-extension-rdg/image2.png)

5. W oknie dialogowym serwera NPS rozszerzenia dla usługi Azure MFA instalacji, kliknij przycisk **Zamknij**. 

  ![Rozszerzenia serwera zasad Sieciowych na potrzeby usługi Azure MFA](./media/howto-mfa-nps-extension-rdg/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurowanie certyfikatów do użytku z rozszerzeniem serwera NPS przy użyciu skryptu programu PowerShell
Następnie należy skonfigurować certyfikatów do użytku przez rozszerzenie serwera NPS do zapewnienia bezpiecznej komunikacji i gwarancji. Składniki serwera NPS obejmują skrypt programu Windows PowerShell, który konfiguruje certyfikatu z podpisem własnym do użytku z serwera NPS. 

Skrypt wykonuje następujące czynności:

* Tworzy certyfikat z podpisem własnym
* Kojarzy klucz publiczny certyfikatu do usługi podmiotu zabezpieczeń w usłudze Azure AD
* Zapisuje certyfikat w magazynie komputera lokalnego
* Zapewnia dostęp do klucza prywatnego certyfikatu użytkownika sieci
* Uruchamia ponownie usługę Serwer zasad sieciowych

Jeśli chcesz użyć własnych certyfikatów, należy skojarzyć klucz publiczny certyfikatu do nazwy głównej usługi w usłudze Azure AD i tak dalej.

Aby użyć skryptu, dostarczają rozszerzenie swoje poświadczenia usługi Azure AD administratora i Identyfikatora dzierżawy usługi Azure AD, które wcześniej zostały skopiowane. Uruchom skrypt na każdym serwerze NPS, w którym zainstalowano rozszerzenia serwera NPS. Następnie wykonaj poniższe czynności:

1. Otwórz administracyjny wiersz środowiska Windows PowerShell.
2. W wierszu polecenia programu PowerShell wpisz `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`i naciśnij klawisz **ENTER**.
3. Typ `.\AzureMfsNpsExtnConfigSetup.ps1`i naciśnij klawisz **ENTER**. Skrypt sprawdza, czy jest zainstalowany moduł programu PowerShell usługi Azure Active Directory. Jeśli nie jest zainstalowany, skrypt zainstaluje moduł.

  ![Program Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
4. Po skrypt sprawdza poprawność instalacji modułu programu PowerShell, wyświetla okno dialogowe modułu programu PowerShell usługi Azure Active Directory. W oknie dialogowym Wprowadź poświadczenia administratora usługi Azure AD i hasło, a następnie kliknij przycisk **logowania**.

  ![Konta programu Powershell](./media/howto-mfa-nps-extension-rdg/image5.png)

5. Po wyświetleniu monitu Wklej identyfikator dzierżawcy wcześniej zostały skopiowane do Schowka i naciśnij klawisz **ENTER**.

  ![Wprowadź identyfikator dzierżawy](./media/howto-mfa-nps-extension-rdg/image6.png)

6. Skrypt tworzy certyfikat z podpisem własnym i wykonuje inne zmiany w konfiguracji. Dane wyjściowe powinny być jak obraz, pokazano poniżej.

  ![Certyfikatu z podpisem własnym](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurowanie składników serwera NPS na bramy usług pulpitu zdalnego
W tej sekcji należy skonfigurować zasady autoryzacji połączeń bramy usług pulpitu zdalnego oraz inne ustawienia usługi RADIUS.

Przepływ uwierzytelniania wymaga, aby komunikaty RADIUS być wymieniane między bramy usług pulpitu zdalnego i serwera NPS, w którym jest zainstalowany serwer zasad Sieciowych. Oznacza to, że zarówno bramy usług pulpitu zdalnego, jak i serwera NPS, w którym zainstalowano rozszerzenia serwera NPS należy skonfigurować ustawienia klientów usługi RADIUS. 

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Skonfiguruj zasady autoryzacji połączeń bramy usług pulpitu zdalnego, aby używał magazynu centralnej
Zasady autoryzacji połączeń usług pulpitu zdalnego (RD CAP) określić wymagania dotyczące połączenia z serwerem bramy usług pulpitu zdalnego. Zasady RD CAP mogą być przechowywane lokalnie (ustawienie domyślne) lub mogą być przechowywane w centralnym magazynie RD CAP, który jest uruchomiony serwer zasad Sieciowych. Aby skonfigurować integrację z usługą Azure MFA z usług pulpitu zdalnego, należy określić centralnego magazynu.

1. Na serwerze bramy usług pulpitu zdalnego Otwórz **Menedżera serwera**. 
2. W menu, kliknij polecenie **narzędzia**, wskaż polecenie **usług pulpitu zdalnego**, a następnie kliknij przycisk **Menedżera bramy usług pulpitu zdalnego**.

  ![Usługi pulpitu zdalnego](./media/howto-mfa-nps-extension-rdg/image8.png)

3. W Menedżer bramy usług pulpitu zdalnego, kliknij prawym przyciskiem myszy  **\[nazwy serwera\] (Local)** i kliknij przycisk **właściwości**.

  ![Nazwa serwera](./media/howto-mfa-nps-extension-rdg/image9.png)

4. W oknie dialogowym właściwości, wybierz **magazynu zasad RD CAP** kartę.
5. Na karcie magazynu zasad RD CAP wybierz **centralnym serwerze NPS**. 
6. W **wprowadź nazwę lub adres IP serwer NPS** wpisz adres IP lub serwera nazwę serwera, w którym zainstalowano rozszerzenia serwera NPS.

  ![Wprowadź nazwę lub adres IP](./media/howto-mfa-nps-extension-rdg/image10.png)
  
7. Kliknij pozycję **Add** (Dodaj).
8. W **wspólny klucz tajny** okno dialogowe, wpisz wspólny klucz tajny, a następnie kliknij przycisk **OK**. Upewnij się, Zapisz ten wspólny klucz tajny i bezpieczne przechowywanie rekordu.

 >[!NOTE]
 >Wspólny klucz tajny jest używany do ustanawiania relacji zaufania między serwerami usługi RADIUS i klientami. Utwórz klucz tajny długich i złożonych.
 >

 ![Wspólny wpis tajny](./media/howto-mfa-nps-extension-rdg/image11.png)

9. Kliknij przycisk **OK**, aby zamknąć okno dialogowe.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Skonfiguruj wartość limitu czasu usługi RADIUS na zdalnego pulpitu bramy serwera zasad Sieciowych
Aby upewnić się, że czasu w celu zweryfikowania poświadczeń użytkowników, przeprowadzenia weryfikacji dwuetapowej, odbierania odpowiedzi i odpowiadać na komunikaty usługi RADIUS, należy dostosować wartość limitu czasu usługi RADIUS.

1. Na serwerze bramy usług pulpitu zdalnego Otwórz Menedżera serwera. W menu, kliknij polecenie **narzędzia**, a następnie kliknij przycisk **serwera zasad sieciowych**. 
2. W **serwer NPS (lokalny)** rozwiń **klientów usługi RADIUS i serwerach**i wybierz **serwera RADIUS**.

 ![Serwer zdalny RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

3. W okienku szczegółów kliknij dwukrotnie **grupy serwerów bramy usług terminalowych**.

 >[!NOTE]
 >Tej grupy serwerów usługi RADIUS został utworzony podczas konfigurowania centralnego serwera dla zasady serwera NPS. Brama usług pulpitu zdalnego przesyła komunikaty RADIUS do serwera lub grupy serwerów, w przypadku więcej niż jednej grupy.
 >

4. W **właściwości grupy serwerów bramy usług terminalowych** oknie dialogowym Wybierz adres IP lub nazwę serwera zasad Sieciowych skonfigurowany do przechowywania informacji o możliwościach usług pulpitu zdalnego, a następnie kliknij przycisk **Edytuj**. 

 ![Grupy serwerów bramy usług terminalowych](./media/howto-mfa-nps-extension-rdg/image13.png)

5. W **edytowanie serwera RADIUS** okno dialogowe, wybierz opcję **równoważenia obciążenia** kartę.
6. W **równoważenia obciążenia** karcie **liczba sekund bez odpowiedzi, zanim żądanie zostanie uznane za** pola, zmień wartość domyślną od 3 do wartość z zakresu od 30 do 60 sekund.
7. W **liczba sekund między żądaniami, gdy serwer jest identyfikowany jako niedostępny** pól, zmienić domyślną wartość wynoszącą 30 sekund na wartość jest równa lub większa niż wartość określona w poprzednim kroku.

 ![Edytowanie serwera Radius](./media/howto-mfa-nps-extension-rdg/image14.png)

8.  Kliknij przycisk **OK** dwa razy, aby zamknąć okno dialogowe.

### <a name="verify-connection-request-policies"></a>Sprawdź zasady żądań połączeń 
Domyślnie podczas konfigurowania bramy usług pulpitu zdalnego do użycia sklep centralne zasady dla zasady autoryzacji połączeń, bramy usług pulpitu zdalnego jest skonfigurowany do przekazywania żądań centralnych zasad dostępu do serwera NPS. Serwer zasad Sieciowych z rozszerzenia usługi Azure MFA, zainstalowane, przetwarza żądanie dostępu usługi RADIUS. Poniższe kroki pokazują, jak sprawdzić domyślnej zasady żądań połączeń. 

1. W bramie usług pulpitu zdalnego, w konsoli serwera NPS (lokalny), rozwiń węzeł **zasady**i wybierz **zasady żądań połączeń**.
2. Kliknij prawym przyciskiem myszy **Połącz zasady żądań**, a następnie kliknij dwukrotnie **zasady autoryzacji bramy serwera terminali**.
3. W **właściwości zasad autoryzacji bramy serwera terminali** okno dialogowe, kliknij przycisk **ustawienia** kartę.
4. Na **ustawienia** kliknij kartę, w obszarze przesyłanie dalej żądania połączenia **uwierzytelniania**. Klienta RADIUS jest skonfigurowany do przesyłania żądań uwierzytelniania.

 ![Ustawienia uwierzytelniania](./media/howto-mfa-nps-extension-rdg/image15.png)
 
5. Kliknij przycisk **anulować**. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurowanie serwera NPS na serwerze, na którym zainstalowano rozszerzenia serwera NPS
Serwer NPS, w którym zainstalowano rozszerzenia serwera zasad Sieciowych musi mieć możliwość wymiany wiadomości usługi RADIUS na serwerze NPS w bramy usług pulpitu zdalnego. Aby włączyć to wiadomości z programem exchange, musisz skonfigurować składniki serwera NPS na serwerze, na którym jest zainstalowana usługa rozszerzenia serwera NPS. 

### <a name="register-server-in-active-directory"></a>Zarejestruj serwer w usłudze Active Directory
Działał prawidłowo, w tym scenariuszu, serwer zasad Sieciowych musi być zarejestrowane w usłudze Active Directory.

1. Na serwerze NPS Otwórz **Menedżera serwera**.
2. W Menedżerze serwera kliknij **narzędzia**, a następnie kliknij przycisk **serwera zasad sieciowych**. 
3. W konsoli serwera zasad sieciowych, kliknij prawym przyciskiem myszy **serwer NPS (lokalny)**, a następnie kliknij przycisk **Zarejestruj serwer w usłudze Active Directory**. 
4. Kliknij przycisk **OK** dwa razy.

 ![Zarejestruj serwer w usłudze AD](./media/howto-mfa-nps-extension-rdg/image16.png)

5. Pozostaw otwarte do następnej procedury konsoli.

### <a name="create-and-configure-radius-client"></a>Tworzenie i konfigurowanie klienta RADIUS 
Bramy usług pulpitu zdalnego musi być skonfigurowany jako klient usługi RADIUS na serwerze zasad Sieciowych. 

1. Na serwerze NPS, w którym zainstalowane jest rozszerzenie serwera NPS, w **serwer NPS (lokalny)** konsoli, kliknij prawym przyciskiem myszy **klientów RADIUS** i kliknij przycisk **nowy**.

 ![Nowi klienci usługi RADIUS](./media/howto-mfa-nps-extension-rdg/image17.png)

2. W **nowy klient RADIUS** okna dialogowego wprowadź przyjazną nazwę, taką jak _bramy_i adres IP lub nazwa DNS serwera bramy usług pulpitu zdalnego. 
3. W **wspólne hasło** i **Potwierdź wspólny klucz tajny** wprowadź to samo hasło, używanego wcześniej.

 ![Nazwy i adresu](./media/howto-mfa-nps-extension-rdg/image18.png)

4. Kliknij przycisk **OK** aby zamknąć okno dialogowe Nowy klient RADIUS.

### <a name="configure-network-policy"></a>Konfigurowanie zasad sieciowych
Odwołaj, że serwer NPS z rozszerzeniem usługi Azure MFA jest magazynu wyznaczonego centralne zasady dla zasady autoryzacji połączeń (CAP). W związku z tym musisz wdrożyć na serwerze NPS można autoryzować żądania połączeń prawidłowe ograniczenie.  

1. W konsoli serwera NPS (lokalny), rozwiń węzeł **zasady**i kliknij przycisk **zasady sieciowe**.
2. Kliknij prawym przyciskiem myszy **połączenia z innymi serwerami dostępu**i kliknij przycisk **zduplikowane zasad**. 

 ![Zduplikowana zasad](./media/howto-mfa-nps-extension-rdg/image19.png)

3. Kliknij prawym przyciskiem myszy **kopiowania połączenia z innymi serwerami dostępu**i kliknij przycisk **właściwości**.

 ![Właściwości sieci](./media/howto-mfa-nps-extension-rdg/image20.png)

4. W **kopiowania połączenia z innymi serwerami dostępu** okna dialogowego, **Nazwa zasady**, wprowadź odpowiednie nazwy, takie jak _RDG_CAP_. Sprawdź **włączone zasady**i wybierz **udzielić dostępu**. Opcjonalnie w **typ serwera dostępu do sieci**, wybierz pozycję **bramy usług pulpitu zdalnego**, można także pozostawić go jako **nieokreślony**.

 ![Kopiowanie połączeń](./media/howto-mfa-nps-extension-rdg/image21.png)

5.  Kliknij przycisk **ograniczenia** i sprawdź **Zezwalaj klientom na łączenie się bez negocjowania metodę uwierzytelniania**.

 ![Zezwalaj klientom na łączenie się](./media/howto-mfa-nps-extension-rdg/image22.png)

6. Opcjonalnie kliknij **warunki** karta i Dodaj warunki, które muszą zostać spełnione dla połączenia autoryzacji, na przykład członkostwa w określonej grupie systemu Windows.

 ![Warunki](./media/howto-mfa-nps-extension-rdg/image23.png)

7. Kliknij przycisk **OK**. Po wyświetleniu monitu, aby wyświetlić odpowiedni temat pomocy, kliknij przycisk **nr**.
8. Upewnij się, że nowych zasad jest na początku listy, że jest włączona, i że przyznaje ona dostęp.

 ![Zasady sieciowe](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Weryfikowanie konfiguracji
Aby sprawdzić konfigurację, należy zalogować się do bramy usług pulpitu zdalnego za pomocą odpowiedniego klienta RDP. Należy używać konta, które jest dozwolona przez użytkownika zasady autoryzacji połączeń i jest włączone dla usługi Azure MFA. 

Pokaż na poniższej ilustracji, można użyć **Remote Web Access pulpitu** strony.

![Dostęp w sieci Web do usług pulpitu zdalnego](./media/howto-mfa-nps-extension-rdg/image25.png)

Po pomyślnym wprowadzania poświadczeń dla uwierzytelniania podstawowego, okno dialogowe połączenie pulpitu zdalnego, wskazuje stan inicjowania połączenia zdalnego, jak pokazano poniżej. 

W przypadku pomyślnego uwierzytelnienia za pomocą metody uwierzytelniania dodatkowego wcześniej skonfigurowane w usłudze Azure MFA, są połączone z zasobem. Jednak dodatkowego uwierzytelniania zakończy się niepowodzeniem, jest odmowa dostępu do zasobu. 

![Inicjowanie połączenia zdalnego](./media/howto-mfa-nps-extension-rdg/image26.png)

W poniższym przykładzie aplikacji uwierzytelniania w systemie Windows phone służy do zapewnienia dodatkowego uwierzytelniania.

![Konta](./media/howto-mfa-nps-extension-rdg/image27.png)

Po zostały pomyślnie uwierzytelniony przy użyciu metody uwierzytelniania dodatkowego, użytkownik jest zalogowany do bramy usług pulpitu zdalnego, jak zwykle. Jednak ponieważ są wymagane do użycia dodatkowej metody uwierzytelniania przy użyciu aplikacji mobilnej na zaufanym urządzeniu, procesu logowania jest bezpieczniejsza niż jest to.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Sprawdź dzienniki Podglądu zdarzeń dla zdarzeń pomyślnego logowania
Aby wyświetlić zdarzeń pomyślnego logowania w dziennikach podglądu zdarzeń systemu Windows, można wydać następujące polecenie środowiska Windows PowerShell do badania dzienników usług terminalowych systemu Windows i zabezpieczeń w systemie Windows.

Kwerenda zdarzeń pomyślnego logowania w operacyjne dzienniki bramy _(Viewer\Applications zdarzeń i usług Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, użyj następujących poleceń programu PowerShell:

* _Get-WinEvent - Nazwa_dziennika Microsoft-Windows-TerminalServices — bramy/operacyjnej_ | gdzie {$_.ID - eq "300"} | FL 
* To polecenie wyświetla zdarzeń systemu Windows, w których wyświetlane są spełnione wymagania zasad autoryzacji zasobów (RD RAP) i przyznano dostęp użytkownika.

![Wyświetl podgląd zdarzeń](./media/howto-mfa-nps-extension-rdg/image28.png)

* _Get-WinEvent - Nazwa_dziennika Microsoft-Windows-TerminalServices — bramy/operacyjnej_ | gdzie {$_.ID - eq "200"} | FL
* To polecenie wyświetla zdarzenia, których wyświetlane, gdy użytkownik spełnione wymagania zasad autoryzacji połączeń.

![Autoryzacja połączeń](./media/howto-mfa-nps-extension-rdg/image29.png)

Można również wyświetlić ten dziennik i filtr na identyfikatorów, 300 i 200 zdarzeń. Aby sprawdzić zdarzeń pomyślnego logowania w dziennikach podglądu zdarzeń zabezpieczeń, użyj następującego polecenia:

* _Get-WinEvent - Nazwa_dziennika zabezpieczeń_ | gdzie {$_.ID - eq "6272"} | FL 
* To polecenie można uruchomić na centralnej serwera NPS lub serwer bramy usług pulpitu zdalnego. 

![Zdarzeń pomyślnego logowania](./media/howto-mfa-nps-extension-rdg/image30.png)

Możesz również wyświetlić dziennik zabezpieczeń lub widok niestandardowy usług zasad sieciowych i dostępu w sposób przedstawiony poniżej:

![Usługi zasad sieciowych i dostępu](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serwerze, na którym zainstalowano rozszerzenia serwera zasad Sieciowych dla usługi Azure MFA, można znaleźć dzienniki Podglądu zdarzeń aplikacji specyficzne dla rozszerzenia w _aplikacji i usług Logs\Microsoft\AzureMfa_. 

![Dzienniki aplikacji Podgląd zdarzeń](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Rozwiązywanie problemów z przewodnika

Jeśli konfiguracja nie działa zgodnie z oczekiwaniami, zacząć rozwiązywać jest Sprawdź, czy użytkownik jest skonfigurowany do używania usługi Azure MFA. Użytkownik powinien połączyć się z [portalu Azure](https://portal.azure.com). Jeśli użytkownicy są monitowani o podanie dodatkowej weryfikacji i można pomyślnie uwierzytelnić, można wyeliminować niepoprawnej konfiguracji usługi Azure MFA.

Jeśli usługi Azure MFA działa dla użytkowników, należy przejrzeć odpowiednich dzienników zdarzeń. Obejmują one zdarzeń zabezpieczeń, operacyjne bramy i dzienniki usługi Azure MFA, które opisano w poprzedniej sekcji. 

Poniżej przedstawiono przykładowe dane wyjściowe dziennika zabezpieczeń przedstawiający zdarzenia logowania nie powiodło się (6273 identyfikator zdarzenia).

![Zdarzenia logowania nie powiodło się](./media/howto-mfa-nps-extension-rdg/image33.png)

Poniżej znajdują się zdarzenia związane z dzienników AzureMFA:

![Dzienników usługi Azure MFA](./media/howto-mfa-nps-extension-rdg/image34.png)

Aby wykonywać zaawansowane rozwiązywanie problemów z opcjami, sprawdź pliki dziennika format bazy danych serwera NPS zainstalowaną usługę NPS. Te pliki dziennika są tworzone w _%SystemRoot%\System32\Logs_ folderu plików tekstowych rozdzielanych przecinkami. 

Aby uzyskać opis tych plików dziennika, zobacz [zinterpretować pliki dziennika Format bazy danych serwera NPS](https://technet.microsoft.com/library/cc771748.aspx). Wpisy w tych plikach dziennika może być trudne zinterpretować, nie importując ich do bazy danych lub arkusz kalkulacyjny. Aby ułatwić interpretowanie pliki dziennika można znaleźć kilka online usługi IAS analizatory składni. 

Na poniższym obrazie przedstawiono dane wyjściowe tego takie do pobrania [aplikacji oprogramowania typu shareware](http://www.deepsoftware.com/iasviewer). 

![Aplikacja "shareware"](./media/howto-mfa-nps-extension-rdg/image35.png)

Na koniec, aby uzyskać dodatkowe Rozwiązywanie problemów z opcjami, możesz użyć analizatora protokołów, takich [programu Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). 

Obraz poniżej z programu Microsoft Message Analyzer przedstawia ruch sieciowy filtrowane wg protokołu RADIUS, który zawiera nazwę użytkownika **Contoso\AliceC**.

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Kolejne kroki
[Jak uzyskać usługę Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS](howto-mfaserver-nps-rdg.md)

[Integrowanie katalogów lokalnych z usługą Azure Active Directory](../connect/active-directory-aadconnect.md)
