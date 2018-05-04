---
title: Integracja sieci VPN z usługą Azure MFA za pomocą rozszerzenia serwera zasad sieciowych | Dokumentacja firmy Microsoft
description: W tym artykule omówiono integrowanie infrastruktury sieci VPN z usługą Azure MFA za pomocą rozszerzenia serwera zasad sieciowych platformy Microsoft Azure.
services: active-directory
keywords: Usługa Azure MFA, integracja sieci VPN, Azure Active Directory, serwer zasad sieciowych rozszerzenia
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 15d69c6b964c0efdc5578d09c5dea8dde85df83f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrowanie infrastruktury sieci VPN z usługą Azure MFA za pomocą rozszerzenia serwera zasad sieciowych na platformie Azure

## <a name="overview"></a>Przegląd

Rozszerzenia serwera zasad sieciowych (NPS) na platformie Azure umożliwia organizacjom chronić uwierzytelniania klienta usługi usługi użytkowników zdalnego uwierzytelniania (RADIUS), za pomocą opartej na chmurze [Azure Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md), zapewniające weryfikacji dwuetapowej.

Ten artykuł zawiera instrukcje dotyczące integrowania infrastruktury serwera NPS za pomocą usługi MFA za pomocą rozszerzenia serwera zasad Sieciowych na platformie Azure. Ten proces umożliwia weryfikację dwuetapową bezpieczne dla użytkowników, którzy próbę nawiązania połączenia z siecią przy użyciu sieci VPN. 

Usługi zasad sieciowych i dostępu umożliwia organizacji:

* Przypisz centralną lokalizację, zarządzania i kontroli żądania sieci, aby określić:

    * Kto może się połączyć 
    
    * Kiedy połączeń dnia są dozwolone 
    
    * Czas trwania połączenia
    
    * Poziom zabezpieczeń, której klienci muszą używać nawiązywania połączenia

    Zamiast określać zasady dotyczące każdego serwera sieci VPN lub bramy usług pulpitu zdalnego, zrobić po są one w centralnej lokalizacji. Aby zapewnić scentralizowane uwierzytelnianie, autoryzację i Ewidencjonowanie jest używany protokół usługi RADIUS. 

* Ustanów i wymuszanie zasad dotyczących kondycji klienta ochrony dostępu do sieci (NAP), które określają, czy urządzenia są przyznawane nieograniczonego lub ograniczonego dostępu do zasobów sieciowych.

* Umożliwiają wymuszające uwierzytelnianie i autoryzację dostępu metodą 802. 1 punkty dostępu bezprzewodowego z obsługą i przełączników Ethernet.   
Aby uzyskać więcej informacji, zobacz [serwera zasad sieciowych](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Aby zwiększyć bezpieczeństwo i zapewniają wysoki poziom zgodności, organizacje mogą stosować serwera NPS z usługi Azure Multi-Factor Authentication, aby upewnić się, czy użytkownicy przy użyciu weryfikacji dwuetapowej nawiązać połączenia z portem wirtualnym na serwerze sieci VPN. Aby użytkownicy mogli uzyskać dostęp użytkownik musi podać swoją nazwę użytkownika i kombinacja hasła i inne informacje, które kontrolują one. Te informacje musi być zaufany i nie jest zduplikowany. Może uwzględniać numer telefonu komórkowego, numer telefonu stacjonarnego lub aplikacji na urządzeniu przenośnym.

Przed dostępności rozszerzenia serwera zasad Sieciowych na platformie Azure klientów, którzy chcieli implementuje weryfikację dwuetapową dla zintegrowane serwera zasad Sieciowych i środowisk usługi MFA została konfigurowania i konserwacji osobny serwer usługi MFA w środowisku lokalnym. Ten typ uwierzytelniania jest oferowana przez bramy usług pulpitu zdalnego i Azure przy użyciu usługi RADIUS serwera Multi-Factor Authentication.

Z rozszerzeniem serwera zasad Sieciowych na platformie Azure organizacje można zabezpieczyć uwierzytelniania klienta usługi RADIUS, wdrażając rozwiązanie MFA lokalne lub rozwiązanie MFA oparte na chmurze.
 
## <a name="authentication-flow"></a>Przepływ uwierzytelniania
Gdy użytkownicy nawiązują połączenia dla portu wirtualnego na serwerze sieci VPN, muszą najpierw zostać uwierzytelnione przy użyciu różnych protokołów. Protokoły Zezwalaj na używanie kombinacji nazwy użytkownika i hasła oraz metod uwierzytelniania opartego na certyfikatach. 

Oprócz uwierzytelniania i weryfikacji tożsamości, użytkownicy muszą mieć odpowiednie uprawnienia. W przypadku prostych implementacji uprawnienia umożliwiające dostęp są ustawiane bezpośrednio na obiekty użytkownika usługi Active Directory. 

![Właściwości użytkownika](./media/howto-mfa-nps-extension-vpn/image1.png)

W przypadku prostych implementacji każdy serwer sieci VPN lub go przydziela nie zezwala na dostęp na podstawie zasad zdefiniowanych każdego lokalnego serwera sieci VPN.

W implementacji większych i bardziej skalowalne zasad, które Udziel lub Odmów dostępu do sieci VPN są scentralizowane na serwerach usługi RADIUS. W takich przypadkach serwer sieci VPN działa jako serwer dostępu (klienta RADIUS), która przekazuje żądania połączenia i konta wiadomości do serwera usługi RADIUS. Aby połączyć się portu wirtualnego na serwerze sieci VPN, użytkownicy musi zostać uwierzytelniony i spełnia warunki, które są definiowane centralnie na serwerach usługi RADIUS. 

Rozszerzenia serwera zasad Sieciowych na platformie Azure jest zintegrowany z serwer NPS, przepływ pomyślne uwierzytelnienie przekłada się na, w następujący sposób:

1. Serwer sieci VPN otrzymuje żądanie uwierzytelnienia od użytkownik sieci VPN, który zawiera nazwę użytkownika i hasło do nawiązywania połączenia z zasobem, takich jak sesji pulpitu zdalnego. 

2. Działając jako klient usługi RADIUS, serwer sieci VPN konwertuje żądanie promień *żądanie dostępu* wiadomości i wysyła je (zaszyfrowanym hasłem) do serwera RADIUS zainstalowanym rozszerzenia serwera NPS. 

3. Kombinacja nazwy użytkownika i hasła została zweryfikowana w usłudze Active Directory. Jeśli nazwa użytkownika lub hasło jest nieprawidłowe, serwer RADIUS wysyła *odmowy dostępu* wiadomości. 

4. Jeśli są spełnione wszystkie warunki określone w żądanie połączenia serwera zasad Sieciowych i zasad sieci (na przykład pory dnia lub grupy ograniczeniami członkostwa), żądania uwierzytelniania dodatkowego w usłudze Azure Multi-Factor Authentication wyzwala rozszerzenia serwera NPS. 

5. Uwierzytelnianie wieloskładnikowe platformy Azure komunikuje się z usługą Azure Active Directory, pobiera szczegóły użytkownika, a następnie wykonuje dodatkowego uwierzytelniania przy użyciu metody, który został skonfigurowany przez użytkownika (komórki połączenie telefoniczne, wiadomość tekstowa lub aplikacji mobilnej). 

6. Po pomyślnym zakończeniu operacji żądanie uwierzytelniania MFA, uwierzytelnianie wieloskładnikowe Azure komunikuje się wynik, który ma rozszerzenie serwera NPS.

7. Po próba połączenia zarówno uwierzytelnieniu i autoryzacji, serwer zasad Sieciowych, w którym zainstalowano rozszerzenia wysyła PROMIEŃ *udzielenia dostępu* wiadomość do serwera sieci VPN (klienta RADIUS).

8. Użytkownik uzyskuje dostęp do wirtualnego portu na serwerze sieci VPN i ustanawia szyfrowane tunel VPN.

## <a name="prerequisites"></a>Wymagania wstępne
Ta sekcja zawiera szczegóły dotyczące wymagań wstępnych, które należy wykonać, zanim będzie można zintegrować usługę MFA z bramy usług pulpitu zdalnego. Przed rozpoczęciem, musi mieć następujące wymagania wstępne spełnione:

* Infrastrukturę sieci VPN.
* Rola usług zasad sieciowych i dostępu
* Licencja uwierzytelnianie wieloskładnikowe Azure
* Oprogramowanie Windows Server
* Biblioteki
* Azure Active Directory (Azure AD) synchronizowane z lokalnej usługi Active Directory 
* Identyfikator GUID usługi Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastrukturę sieci VPN.
W tym artykule przyjęto założenie, że masz pracy infrastrukturę sieci VPN, który używa programu Microsoft Windows Server 2016 i że serwer sieci VPN aktualnie nie skonfigurowano przesyłanie żądań połączeń do serwera usługi RADIUS. W artykule można skonfigurować infrastrukturę sieci VPN do korzystania z centralnego serwera RADIUS.

Jeśli nie masz infrastrukturę sieci VPN pracę w miejscu, można szybko można utworzyć jedną z zachowaniem wskazówek zamieszczonych w wiele samouczki ustawienia sieci VPN, które znajdują się na firmy Microsoft i innych witryn. 
            
### <a name="the-network-policy-and-access-services-role"></a>Rola usług zasad sieciowych i dostępu

Usługi zasad sieciowych i dostępu udostępnia funkcje serwera i klienta RADIUS. W tym artykule przyjęto założenie, że zainstalowano rolę usług zasad sieciowych i dostępu na serwer członkowski lub kontroler domeny w danym środowisku. W tym przewodniku należy skonfigurować RADIUS dla konfiguracji sieci VPN. Zainstaluj rolę usług zasad sieciowych i dostępu na serwerze *innych niż* serwer sieci VPN.

Aby uzyskać informacje o instalowaniu roli usług zasad sieciowych i dostępu usługi systemu Windows Server 2012 lub nowszy, odwiedź [zainstalować serwer zasad dotyczących kondycji ochrony dostępu do sieci](https://technet.microsoft.com/library/dd296890.aspx). Ochrony dostępu do sieci jest przestarzała w systemie Windows Server 2016. Opis najlepszych rozwiązań dla serwera NPS, w tym zalecenie, aby zainstalować serwer zasad Sieciowych na kontrolerze domeny, zobacz [najlepsze rozwiązania dotyczące serwera NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Azure MFA License

Licencja jest wymagane uwierzytelnianie wieloskładnikowe Azure i jest dostępny za pośrednictwem usługi Azure AD Premium, Enterprise Mobility + zabezpieczeń lub licencji autonomicznej usługi Multi-Factor Authentication. Na podstawie zużycia licencji dla usługi Azure MFA, takich jak dla każdego użytkownika lub uwierzytelnianie licencji na nie są zgodne z rozszerzeniem serwera NPS. Aby uzyskać więcej informacji, zobacz [sposobu uzyskania usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md). Do celów testowych, możesz użyć subskrypcji wersji próbnej.

### <a name="windows-server-software"></a>Oprogramowanie Windows Server

Rozszerzenie serwera NPS wymaga systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszym z rolą usługi zasad sieciowych i dostępu zainstalowany. Wszystkie kroki opisane w tym przewodniku zostały wykonane z systemem Windows Server 2016.

### <a name="libraries"></a>Biblioteki

Następujące biblioteki są automatycznie instalowane z rozszerzeniem serwera NPS:

-   [Pakiety redystrybucyjne języka Visual C++ dla programu Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory modułu dla Windows PowerShell w wersji 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Microsoft Azure Active Directory modułu programu PowerShell nie jest już istnieje, jest instalowana za pomocą skryptu konfiguracji, który uruchamiany jako część procesu instalacji. Nie istnieje potrzeba aby zainstalować moduł wcześniejsze, jeśli nie jest już zainstalowany.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Usługa Azure Active Directory synchronizowane z lokalnej usługi Active Directory 

Aby użyć rozszerzenia serwera NPS, lokalnych użytkowników musi być zsynchronizowane z usługą Azure Active Directory i włączone dla usługi MFA. W tym przewodniku założono, że lokalnych użytkowników są synchronizowane z usługą Azure Active Directory za pośrednictwem usługi Azure AD Connect. Instrukcje dotyczące włączania uwierzytelniania Wieloskładnikowego użytkownicy znajdują się poniżej.

Informacje dotyczące usługi Azure AD Connect, zobacz [integrację katalogów lokalnych z usługą Azure Active Directory](../connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Identyfikator GUID usługi Azure Active Directory 

Aby zainstalować rozszerzenie serwera NPS, musisz znać identyfikator GUID usługi Azure Active Directory. Instrukcje dotyczące znajdowania identyfikator GUID usługi Azure Active Directory znajdują się w następnej sekcji.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurowanie serwera RADIUS dla połączeń sieci VPN

Po zainstalowaniu roli serwera NPS na serwerze członkowskim, należy ją skonfigurować do uwierzytelniania i autoryzacji klienta sieci VPN, czy żądania połączenia sieci VPN. 

W tej sekcji założono instalacji roli usług zasad sieciowych i dostępu, ale nie skonfigurowano go do użytku w infrastrukturze.

> [!NOTE]
> Jeśli masz już działający serwer sieci VPN do uwierzytelniania używa centralny serwer usługi RADIUS, możesz pominąć tę sekcję.
>

### <a name="register-server-in-active-directory"></a>Zarejestruj serwer w usłudze Active Directory
Aby działać poprawnie, w tym scenariuszu, należy zarejestrować serwer zasad Sieciowych w usłudze Active Directory.

1. Otwórz Menedżera serwera.

2. W Menedżerze serwera wybierz **narzędzia**, a następnie wybierz **serwera zasad sieciowych**. 

3. W konsoli serwera zasad sieciowych, kliknij prawym przyciskiem myszy **serwer NPS (lokalny)**, a następnie wybierz **Zarejestruj serwer w usłudze Active Directory**. Wybierz **OK** dwa razy.

    ![Serwer zasad sieciowych](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Pozostaw otwarte do następnej procedury konsoli.

### <a name="use-wizard-to-configure-the-radius-server"></a>Użyj kreatora, aby skonfigurować serwer usługi RADIUS
Można użyć standardowego (opartej na kreatorze) lub zaawansowanych opcji konfiguracji, aby skonfigurować serwer usługi RADIUS. W tej sekcji założono, że używasz opcji opartej na Kreatorze konfiguracji standardowej.

1. W konsoli serwera zasad sieciowych wybierz **serwer NPS (lokalny)**.

2. W obszarze **standardowej konfiguracji**, wybierz pozycję **serwera RADIUS w celu telefonicznych i połączeń sieci VPN**, a następnie wybierz **Konfigurowanie sieci VPN lub Dial-Up**.

    ![Konfigurowanie sieci VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. W **wybierz telefonicznych lub typu połączenia sieci prywatnej wirtualnego** okna, wybierz **połączenia wirtualnej sieci prywatnej**, a następnie wybierz **dalej**.

    ![Wirtualna sieć prywatna](./media/howto-mfa-nps-extension-vpn/image4.png)

4. W **Określ Dial-Up lub serwer VPN** wybierz **Dodaj**.

5. W **klienta RADIUS nowe** okna, Podaj przyjazną nazwę, wprowadź rozpoznawalną nazwę lub adres IP serwera sieci VPN, a następnie wprowadź tajny wspólne hasło. Należy wspólne hasło tajny długich i złożonych. Zapisz go, ponieważ będzie on potrzebny w następnej sekcji.

    ![Nowy klient RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Wybierz **OK**, a następnie wybierz **dalej**.

7. W **Konfigurowanie metod uwierzytelniania** okna, Zaakceptuj wybór domyślny (**uwierzytelnianie szyfrowane firmy Microsoft w wersji 2 [MS-CHAPv2])** lub wybierz inną opcję i zaznacz **dalej** .

    > [!NOTE]
    > Jeśli konfigurujesz protokołu uwierzytelniania rozszerzonego (EAP), możesz korzystać Microsoft Challenge Handshake Authentication Protocol (CHAPv2) lub chronionego protokołu uwierzytelniania rozszerzonego (PEAP). Nie protokołu EAP jest obsługiwana.
 
8. W **określić grupy użytkowników** wybierz **Dodaj**, a następnie wybierz odpowiednią grupę. Jeśli grupa nie istnieje, należy pozostawić wybór puste, aby udzielić dostępu do wszystkich użytkowników.

    ![Okno określić grupy użytkowników](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Wybierz opcję **Dalej**.

10. W **określ filtry IP** wybierz **dalej**.

11. W **Określ ustawienia szyfrowania** , zaakceptuj ustawienia domyślne, a następnie wybierz **dalej**.

    ![Określ ustawienia szyfrowania okna](./media/howto-mfa-nps-extension-vpn/image8.png)

12. W **Określ nazwę obszaru** okna, pozostaw puste pole Nazwa obszaru, zaakceptuj ustawienia domyślne, a następnie wybierz **dalej**.

    ![Określ nazwę obszaru okna](./media/howto-mfa-nps-extension-vpn/image9.png)

13. W **klientów Kończenie nowego telefoniczne lub połączenia wirtualnej sieci prywatnej i RADIUS** wybierz **Zakończ**.

    ![Okno "wykonywanie nowych połączeń lub wirtualnej sieci prywatnej połączeń i RADIUS klientów"](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Sprawdź konfigurację usługi RADIUS
Ta sekcja zawiera szczegóły dotyczące utworzonego za pomocą Kreatora konfiguracji.

1. Na serwerze zasad sieciowych w konsoli serwera NPS (lokalny), rozwiń węzeł **klientów RADIUS**, a następnie wybierz **klientów RADIUS**.

2. W okienku szczegółów kliknij prawym przyciskiem myszy klienta RADIUS, który zostanie utworzony, a następnie wybierz **właściwości**. Właściwości klienta RADIUS (serwer sieci VPN) powinny być jak pokazano poniżej:

    ![Właściwości sieci VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Wybierz **anulować**.

4. Na serwerze zasad sieciowych w konsoli serwera NPS (lokalny), rozwiń węzeł **zasady**, a następnie wybierz **zasady żądań połączeń**. Zasady połączeń sieci VPN jest wyświetlane, jak pokazano na poniższej ilustracji:

    ![Żądania połączeń](./media/howto-mfa-nps-extension-vpn/image12.png)

5. W obszarze **zasady**, wybierz pozycję **zasady sieciowe**. Powinny pojawić się zasady połączenia wirtualnej sieci prywatnej (VPN), podobny zasad pokazano na poniższej ilustracji:

    ![Zasady sieciowe](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Skonfiguruj serwer sieci VPN, aby użyć uwierzytelniania RADIUS
W tej sekcji należy skonfigurować serwer sieci VPN do uwierzytelniania RADIUS. Instrukcjach przyjęto założenie, mają działającej konfiguracji serwera sieci VPN, ale nie została skonfigurowana do uwierzytelniania RADIUS. Po skonfigurowaniu serwera sieci VPN, należy upewnić się, że konfigurację działa zgodnie z oczekiwaniami.

> [!NOTE]
> Jeśli masz już działającej konfiguracji serwera sieci VPN korzysta z uwierzytelniania usługi RADIUS, możesz pominąć tę sekcję.
>

### <a name="configure-authentication-provider"></a>Konfigurowanie dostawcy uwierzytelniania
1. Na serwerze sieci VPN Otwórz Menedżera serwera.

2. W Menedżerze serwera wybierz **narzędzia**, a następnie wybierz **Routing i dostęp zdalny**.

3. W **Routing i dostęp zdalny** okna, kliknij prawym przyciskiem myszy  **\<nazwa serwera > (local)**, a następnie wybierz **właściwości**.

    ![Okno Routing i dostęp zdalny](./media/howto-mfa-nps-extension-vpn/image14.png)
 
4. W  **\<nazwa serwera > właściwości (lokalnego)** wybierz **zabezpieczeń** kartę. 

5. Na **zabezpieczeń** , w obszarze **dostawcy uwierzytelniania**, wybierz pozycję **uwierzytelnianie usługi RADIUS**, a następnie wybierz **Konfiguruj**.

    ![Uwierzytelnianie usługi RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)
 
6. W **uwierzytelnianie usługi RADIUS** wybierz **Dodaj**.

7. W **Dodawanie serwera RADIUS** okna, wykonaj następujące czynności:

    a. W **nazwy serwera** wprowadź nazwę lub adres IP serwera usługi RADIUS, skonfigurowanego w poprzedniej sekcji.

    b. Dla **wspólne hasło**, wybierz pozycję **zmiany**, a następnie wpisz wspólne hasło tajny, który został utworzony i zarejestrowane wcześniej.

    c. W **limit czasu (w sekundach)** wybierz wartość z zakresu od **30** za pośrednictwem **60**.  
    Wartość limitu czasu jest niezbędne w celu umożliwienia wystarczająco dużo czasu na ukończenie drugi składnik uwierzytelniania.
 
    ![Okno Dodawanie serwera RADIUS](./media/howto-mfa-nps-extension-vpn/image16.png)
 
8. Kliknij przycisk **OK**.

### <a name="test-vpn-connectivity"></a>Przetestowanie łączności z siecią VPN
W tej sekcji upewnieniu się, że klient VPN uwierzytelnieniu i autoryzacji przez serwer usługi RADIUS podczas próby nawiązania połączenia sieci VPN portu wirtualnego. Instrukcjach przyjęto założenie, że używasz systemu Windows 10 jako klient sieci VPN. 

> [!NOTE]
> Jeśli skonfigurowano klienta sieci VPN w taki sposób, aby połączyć się z serwerem sieci VPN i zapisano ustawienia, można pominąć kroki dotyczące konfigurowania i zapisywanie obiektu połączenia sieci VPN.
>

1. Na komputerze klienckim sieci VPN wybierz **Start** przycisk, a następnie wybierz **ustawienia** przycisku.

2. W **ustawienia systemu Windows** wybierz **sieć i Internet**.

3. Wybierz **VPN**.

4. Wybierz **dodać połączenie VPN**.

5. W **dodać połączenie VPN** okna w **dostawcy sieci VPN** wybierz opcję **systemu Windows (wbudowane)**, wypełnij pozostałe pola, zależnie od potrzeb, a następnie wybierz **Zapisać**. 

    ![Okno "Dodaj połączenie VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)
 
6. Przejdź do **Panelu sterowania**, a następnie wybierz **Centrum sieci i udostępniania**.

7. Wybierz **Zmień ustawienia karty**.

    ![Zmień ustawienia karty sieciowej](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Kliknij prawym przyciskiem myszy połączenie sieci VPN, a następnie wybierz **właściwości**. 

    ![Właściwości sieci VPN](./media/howto-mfa-nps-extension-vpn/image19.png)

9. W oknie właściwości sieci VPN wybierz **zabezpieczeń** kartę. 

10. Na **zabezpieczeń** karcie, upewnij się, że tylko **Microsoft CHAP Version 2 (MS-CHAP v2)** jest wybrany, a następnie wybierz **OK**.

    ![Opcji "Zezwalaj na te protokoły"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Kliknij prawym przyciskiem myszy połączenie sieci VPN, a następnie wybierz **Connect**.

12. W **ustawienia** wybierz **Connect**.  
    Udane połączenie pojawia się w dzienniku zabezpieczeń na serwerze RADIUS jako zdarzenia 6272 identyfikator, jak pokazano poniżej:

    ![Okno właściwości zdarzenia](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Rozwiązywanie problemów z usługi RADIUS

Załóżmy, że przed skonfigurowaniem serwera sieci VPN do użycia centralny serwer usługi RADIUS do uwierzytelniania i autoryzacji pracy konfigurację sieci VPN. Konfiguracja została pracy, jest prawdopodobne, że przyczyną problemu jest błąd konfiguracji serwera usługi RADIUS lub użycia nieprawidłowej nazwy użytkownika lub hasło. Na przykład jeśli używasz alternatywny sufiks nazwy UPN użytkownika, próba logowania może zakończyć się niepowodzeniem. Użyj tej samej nazwy konta w celu uzyskania najlepszych wyników. 

Aby rozwiązać te problemy, doskonale nadaje się do uruchomienia jest zbadanie dziennikami zdarzeń zabezpieczeń na serwerze RADIUS. Aby zapisać czas wyszukiwania dla zdarzeń, służy opartej na rolach zasad sieciowych i dostępu do serwera widok niestandardowy w Podglądzie zdarzeń, jak pokazano poniżej. "Identyfikator zdarzenia 6273" wskazuje zdarzeń, gdy serwer NPS odmowy dostępu dla użytkownika. 

![Podgląd zdarzeń](./media/howto-mfa-nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Skonfiguruj usługę Multi-Factor Authentication

Aby uzyskać pomoc w konfigurowaniu użytkowników w usłudze Multi-Factor Authentication, zobacz artykuły [sposobu wymagać weryfikacji dwuetapowej dla użytkownika lub grupy](howto-mfa-userstates.md) i [Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalowanie i Konfigurowanie rozszerzeń serwera NPS

Ta sekcja zawiera instrukcje dotyczące konfigurowania sieci VPN do użycia usługi MFA na potrzeby uwierzytelniania klienta z serwerem sieci VPN.

Po zainstalowaniu i skonfigurowaniu rozszerzenia serwera NPS, wszystkie uwierzytelnianie klienta usługi RADIUS, który jest przetwarzany przez ten serwer jest wymagana do używać uwierzytelniania Wieloskładnikowego. Jeśli użytkownicy sieci VPN nie są zarejestrowane w usłudze Azure Multi-Factor Authentication, możesz zrobić z następujących czynności:

* Konfigurowanie innego serwera RADIUS w celu uwierzytelniania użytkowników, którzy nie są skonfigurowane do korzystania z usługi MFA. 

* Utwórz wpis rejestru, który umożliwia użytkownikom kwestionowanych zapewnić drugi składnik uwierzytelniania, jeśli są one rejestrowane w usłudze Azure Multi-Factor Authentication. 

Utwórz nową wartość ciągu o nazwie _REQUIRE_USER_MATCH w HKLM\SOFTWARE\Microsoft\AzureMfa_i ustaw wartość *True* lub *False*. 

![Ustawienie "Wymagaj dopasowania użytkownika"](./media/howto-mfa-nps-extension-vpn/image34.png)
 
Jeśli wartość jest równa *True* lub jest pusty, wszystkie żądania uwierzytelniania są poddawane żądanie uwierzytelniania MFA. Jeśli wartość jest równa *False*, wyzwania MFA są wystawiane tylko do użytkowników, którzy są zarejestrowane w usłudze Azure Multi-Factor Authentication. Użyj *False* ustawienie tylko podczas testowania lub w środowiskach produkcyjnych w okresie dołączania.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Uzyskaj identyfikator GUID usługi Azure Active Directory

W ramach konfiguracji rozszerzenia serwera NPS musisz podać poświadczenia administratora i identyfikator dzierżawy usługi Azure AD. Uzyskaj identyfikator w następujący sposób:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator globalny dzierżawy platformy Azure.

2. W okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

3. Wybierz **właściwości**.

4. Aby skopiować identyfikator użytkownika usługi Azure AD, wybierz **kopiowania** przycisku.
 
    ![Identyfikator usługi Azure AD](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Zainstaluj rozszerzenie serwera NPS
Rozszerzenia serwera zasad Sieciowych musi być zainstalowany na serwerze zasad sieciowych i zainstalowaną rolą usług dostępu i która działa jako serwer RADIUS w projekcie. Czy *nie* Zainstaluj rozszerzenie serwera NPS na serwerze usług pulpitu zdalnego.

1. Pobierz rozszerzenie serwera NPS z [Microsoft Download Center](https://aka.ms/npsmfa). 

2. Kopiowanie pliku wykonywalnego (*NpsExtnForAzureMfaInstaller.exe*) na serwerze zasad Sieciowych.

3. Na serwerze NPS, kliknij dwukrotnie **NpsExtnForAzureMfaInstaller.exe** i po wyświetleniu monitu wybierz **Uruchom**.

4. W **NPS dla usługi Azure MFA Instalator rozszerzenia** okna, przejrzyj postanowienia licencyjne dotyczące oprogramowania, zaznaczyć **zgadzam się z warunkami licencji** pole wyboru, a następnie wybierz **zainstalować**.

    ![Okno "NPS rozszerzenia dla usługi Azure MFA instalacji"](./media/howto-mfa-nps-extension-vpn/image36.png)
 
5. W **NPS dla usługi Azure MFA Instalator rozszerzenia** wybierz **Zamknij**.  

    !["Ustawienia pomyślnie ukończono" okno potwierdzenia](./media/howto-mfa-nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurowanie certyfikatów do użytku z rozszerzeniem serwera NPS przy użyciu skryptu środowiska PowerShell
W celu zapewnienia bezpiecznej komunikacji i gwarantują, należy skonfigurować certyfikaty do użycia przez rozszerzenie serwera NPS. Składniki serwera NPS obejmują skrypt programu Windows PowerShell, który konfiguruje certyfikatu z podpisem własnym do użytku z serwera NPS. 

Skrypt wykonuje następujące czynności:

* Tworzy certyfikat z podpisem własnym.
* Kojarzy klucz publiczny certyfikatu z usługą główną w usłudze Azure AD.
* Zapisuje certyfikat w magazynie komputera lokalnego.
* Udziela użytkownikowi dostępu sieciowego do klucza prywatnego certyfikatu.
* Uruchamia ponownie usługę NPS.

Jeśli chcesz użyć własnych certyfikatów, należy skojarzyć klucz publiczny certyfikatu z nazwy głównej usługi w usłudze Azure AD i tak dalej.

Aby użyć skryptu, dostarczają rozszerzenie poświadczeń administracyjnych usługi Azure Active Directory i Identyfikatora dzierżawy usługi Azure Active Directory, które wcześniej zostały skopiowane. Uruchom skrypt na każdym serwerze NPS, w którym zainstalowano rozszerzenia serwera NPS.

1. Uruchom program Windows PowerShell jako administrator.

2. W wierszu polecenia programu PowerShell, wprowadź **cd "c:\Program Files\Microsoft\AzureMfa\Config"**, a następnie wybierz Enter.

3. W następnym wierszu polecenia, wprowadź **.\AzureMfsNpsExtnConfigSetup.ps1**, a następnie wybierz Enter. Skrypt sprawdza, czy jest zainstalowany moduł Azure AD PowerShell. Jeśli nie jest zainstalowany, skrypt zainstaluje moduł.
 
    ![PowerShell](./media/howto-mfa-nps-extension-vpn/image38.png)
 
    Po skrypt sprawdza poprawność instalacji modułu programu PowerShell, wyświetla modułu programu PowerShell usługi Azure Active Directory logowania okna. 

4. Wprowadź poświadczenia administratora usługi Azure AD i hasło, a następnie wybierz **Zaloguj**. 
 
    ![Okno programu PowerShell logowania](./media/howto-mfa-nps-extension-vpn/image39.png)
 
5. W wierszu polecenia Wklej skopiowany wcześniej identyfikator dzierżawy, a następnie wybierz Enter. 

    ![Identyfikator dzierżawy](./media/howto-mfa-nps-extension-vpn/image40.png)

    Skrypt tworzy certyfikat z podpisem własnym i wykonuje inne zmiany w konfiguracji. Wynik jest tak jak na poniższej ilustracji:

    ![Certyfikat z podpisem własnym](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Uruchom ponownie serwer.

### <a name="verify-the-configuration"></a>Sprawdź konfigurację
Aby sprawdzić konfigurację, należy ustanowić nowe połączenie sieci VPN z serwerem sieci VPN. Po pomyślnym wprowadzeniu poświadczeń dla uwierzytelniania podstawowego, połączenie sieci VPN czeka dodatkowego uwierzytelniania się powodzeniem przed połączenie zostanie nawiązane, jak pokazano poniżej. 

![Okno sieci VPN ustawienia systemu Windows](./media/howto-mfa-nps-extension-vpn/image42.png)

Jeśli zostanie pomyślnie uwierzytelniony przy użyciu metody dodatkowej weryfikacji, który wcześniej skonfigurowany w usłudze Azure MFA, są połączone z zasobem. Jednak dodatkowego uwierzytelniania zakończy się niepowodzeniem, jest odmowa dostępu do zasobu. 

W poniższym przykładzie aplikacji Microsoft Authenticator na Windows Phone zawiera dodatkowego uwierzytelniania:

![Sprawdź konto](./media/howto-mfa-nps-extension-vpn/image43.png)

Po pomyślnie uwierzytelniono za pomocą metody pomocnicze są uzyska dostęp do wirtualnego portu na serwerze sieci VPN. Ponieważ trzeba użyć metody dodatkowego uwierzytelniania przy użyciu aplikacji mobilnej na zaufanym urządzeniu, procesu logowania jest bezpieczniejsza niż jeśli były używane tylko kombinacja nazwy użytkownika i hasła.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Sprawdź dzienniki Podglądu zdarzeń dla zdarzeń pomyślnego logowania
Aby wyświetlić zdarzeń pomyślnego logowania w dziennikach podglądu zdarzeń systemu Windows, zapytanie zabezpieczenia systemu Windows dziennika na serwerze NPS, wprowadzając następujące polecenie programu PowerShell:

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![Zabezpieczenia programu PowerShell Podgląd zdarzeń](./media/howto-mfa-nps-extension-vpn/image44.png)
 
Możesz również wyświetlić dziennik zabezpieczeń lub widok niestandardowy usług zasad sieciowych i dostępu w sposób pokazany poniżej:

![Dziennik serwera zasad sieciowych](./media/howto-mfa-nps-extension-vpn/image45.png)

Na serwerze, na którym zainstalowano rozszerzenia serwera NPS uwierzytelnianie wieloskładnikowe Azure, możesz znaleźć dzienniki Podglądu zdarzeń aplikacji, które są specyficzne dla rozszerzenia w *aplikacji i usług Logs\Microsoft\AzureMfa*. 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![Podgląd zdarzeń "Liczba zdarzeń" okienko](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów
Jeśli konfiguracja nie działa zgodnie z oczekiwaniami, należy rozpocząć rozwiązywania problemów, upewniając się, że użytkownik jest skonfigurowany do używania uwierzytelniania Wieloskładnikowego. Użytkownik powinien połączyć się z [portalu Azure](https://portal.azure.com). Jeśli użytkownik jest monitowany o dodatkowego uwierzytelniania i można pomyślnie uwierzytelnić, można wyeliminować niepoprawnej konfiguracji MFA jako rozwiązaniem problemu.

Jeśli uwierzytelnianie wieloskładnikowe działa dla użytkownika, przejrzyj odpowiedni dzienniki Podglądu zdarzeń. Dzienniki zawierają zdarzeń zabezpieczeń, operacyjne bramy i dzienniki usługi Azure Multi-Factor Authentication, które opisano w poprzedniej sekcji. 

Przykład dziennika zabezpieczeń, który wyświetla nie powiodło się logowania zdarzenie (event ID 6273) znajduje się w tym miejscu:

![Dziennik zabezpieczeń przedstawiający nie powiodło się zdarzenia logowania](./media/howto-mfa-nps-extension-vpn/image47.png)

Zdarzenia związane z dziennika Azure Multi-Factor Authentication jest następujący:

![Dzienniki usługi Azure Multi-Factor Authentication](./media/howto-mfa-nps-extension-vpn/image48.png)

Do zaawansowanego rozwiązywania problemów, sprawdź pliki dziennika format bazy danych serwera NPS zainstalowaną usługę NPS. Pliki dziennika są tworzone w _%SystemRoot%\System32\Logs_ folderu plików tekstowych rozdzielanych przecinkami. Opis plików dziennika, zobacz [zinterpretować pliki dziennika Format bazy danych serwera NPS](https://technet.microsoft.com/library/cc771748.aspx). 

Wpisy w tych plikach dziennika są trudne do interpretowania, chyba że zostaną wyeksportowane do arkusza kalkulacyjnego lub bazy danych. Można znaleźć wiele uwierzytelniania internetowego usługi (IAS) podczas analizowania narzędzia w trybie online, aby ułatwić Interpretowanie plików dziennika. Dane wyjściowe tego takie do pobrania [aplikacji "shareware"](http://www.deepsoftware.com/iasviewer) jest następujący: 

![Aplikacja "shareware"](./media/howto-mfa-nps-extension-vpn/image49.png)

Należy uzyskać dalszą pomoc, można użyć analizatora protokołów, takich jak Wireshark lub [programu Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Na poniższej ilustracji z programu Wireshark przedstawiono komunikaty RADIUS między serwerem sieci VPN i serwer zasad Sieciowych.

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

Aby uzyskać więcej informacji, zobacz [integracji istniejącej infrastrukturze serwera NPS z usługi Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). 

## <a name="next-steps"></a>Kolejne kroki
[Pobierz uwierzytelnianie wieloskładnikowe platformy Azure](concept-mfa-licensing.md)

[Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS](howto-mfaserver-nps-rdg.md)

[Integrowanie katalogów lokalnych z usługą Azure Active Directory](../connect/active-directory-aadconnect.md)

