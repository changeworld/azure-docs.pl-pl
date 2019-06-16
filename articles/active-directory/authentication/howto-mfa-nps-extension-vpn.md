---
title: Integracja sieci VPN z usługą Azure MFA przy użyciu rozszerzenia serwera zasad sieciowych — usługi Azure Active Directory
description: Zintegruj infrastrukturę sieci VPN z usługą Azure MFA przy użyciu rozszerzenia serwera zasad sieciowych dla systemu Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e94b307d562c4317a87713612a62e6da007f9703
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64570644"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrowanie infrastruktury sieci VPN z usługą Azure MFA za pomocą rozszerzenia serwera zasad sieciowych na platformie Azure

## <a name="overview"></a>Omówienie

Rozszerzenia serwera zasad sieciowych (NPS) dla platformy Azure umożliwia organizacjom w zabezpieczaniu uwierzytelniania klienta usługi Usługa użytkowników zdalnego uwierzytelniania (RADIUS) za pomocą opartej na chmurze [usługi Azure Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md), zapewniającą weryfikacji dwuetapowej.

Ten artykuł zawiera instrukcje dotyczące integracji infrastruktury serwera zasad Sieciowych przy użyciu usługi MFA za pomocą rozszerzenia serwera NPS dla platformy Azure. Ten proces umożliwia weryfikację dwuetapową bezpieczny dla użytkowników, którzy spróbują połączyć się z siecią za pośrednictwem sieci VPN.

Usług zasad sieciowych i dostępu zapewnia organizacjom możliwość:

* Przypisz centralnej lokalizacji, zarządzanie i kontrolę nad żądania sieci, aby określić:

  * Kto może się połączyć

  * Jakie razy dziennie połączenia są dozwolone.

  * Czas trwania połączeń

  * Poziom zabezpieczeń, które klienci muszą używać do łączenia z

    Zamiast określać zasady na każdym serwerze sieci VPN lub bramy usług pulpitu zdalnego, to zrobić po znajdują się w centralnej lokalizacji. Protokołu RADIUS jest używany do zapewnienia scentralizowanego uwierzytelniania, autoryzacji i Ewidencjonowanie.

* Ustal i Wymuś zasady dotyczące kondycji klienta ochrony dostępu do sieci (NAP), które określają, czy urządzenia są przyznawane nieograniczony lub ograniczony dostęp do zasobów sieciowych.

* Umożliwiają wymuszające uwierzytelnianie i autoryzację do uzyskiwania dostępu do 802.1 punkty dostępu bezprzewodowego możliwością x i przełączników Ethernet.
  Aby uzyskać więcej informacji, zobacz [serwer zasad sieciowych](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top).

Aby zwiększyć bezpieczeństwo i zapewnia wysoki poziom zgodności, organizacje mogą stosować NPS przy użyciu usługi Azure Multi-Factor Authentication, aby upewnić się, że użytkowników za pomocą weryfikacji dwuetapowej połączyć z portu wirtualnego na serwerze sieci VPN. Użytkownikom można udzielić dostępu należy podać swoją nazwę użytkownika i kombinacja hasła i inne informacje, które kontrolują one. Te informacje musi być zaufany i nie jest łatwo zduplikować. Może to obejmować numer telefonu komórkowego, numer telefonu stacjonarnego lub aplikacji na urządzeniu przenośnym.

Przed dostępność rozszerzenia serwera NPS dla platformy Azure klienci, którzy chcieli do zaimplementowania weryfikacji dwuetapowej dla zintegrowany serwer zasad Sieciowych i środowiska usługi MFA została konfigurowania i konserwacji na oddzielnym serwerze usługi MFA w środowisku lokalnym. Ten typ uwierzytelniania jest oferowany przez bramy usług pulpitu zdalnego i korzystanie z usługi RADIUS serwera Azure Multi-Factor Authentication.

Za pomocą rozszerzenia serwera NPS dla platformy Azure organizacje można zabezpieczyć uwierzytelniania klienta usługi RADIUS, wdrażając rozwiązanie MFA lokalne lub oparte na chmurze rozwiązanie usługi MFA.

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Gdy użytkownicy łączą się portu wirtualnego na serwerze sieci VPN, muszą najpierw zostać uwierzytelnione przy użyciu różnych protokołów. Protokoły Zezwalaj na użycie kombinacji nazwy użytkownika i hasła oraz metod uwierzytelniania opartego na certyfikatach.

Oprócz uwierzytelniania i weryfikacji ich tożsamości, użytkownicy muszą mieć odpowiednie uprawnienia. W prostych implementacji uprawnienia zezwalające na dostęp, są ustawiane bezpośrednio na obiekty użytkownika usługi Active Directory.

![Karta telefonowania w użytkownicy usługi Active Directory i komputery właściwości użytkownika](./media/howto-mfa-nps-extension-vpn/image1.png)

W prostych implementacji każdy serwer sieci VPN przyznaje dostęp lub odmówi go na podstawie zasad, które są zdefiniowane na każdym lokalnym serwerze sieci VPN.

W implementacji większych i bardziej skalowalna zasady, które udzielić lub odmówić dostępu do sieci VPN są scentralizowane na serwery usługi RADIUS. W takich przypadkach serwer sieci VPN działa jako serwera dostępu (klienta usługi RADIUS), który przekazuje żądania połączenia i wiadomości konta do serwera RADIUS. Aby podłączyć do portu wirtualnego na serwerze sieci VPN, użytkownicy muszą uwierzytelnić się i spełnia warunki, które są definiowane centralnie na serwerach usługi RADIUS.

Gdy rozszerzenia serwera NPS dla platformy Azure jest zintegrowany z serwera NPS, przepływ pomyślne uwierzytelnienie wyniki, w następujący sposób:

1. Serwer sieci VPN odbiera żądanie uwierzytelnienia użytkownika sieci VPN, który zawiera nazwę użytkownika i hasło do łączenia się z zasobem, takich jak sesji pulpitu zdalnego.
2. Działając jako klient usługi RADIUS, serwer sieci VPN konwertuje żądanie względem usługi RADIUS *żądanie dostępu* komunikatu i wysyła je (z zaszyfrowane hasło) do serwera RADIUS zainstalowanym rozszerzenia serwera NPS.
3. Kombinacja nazwy użytkownika i hasła jest weryfikowana w usłudze Active Directory. Jeśli nazwa użytkownika lub hasło jest nieprawidłowe, serwer RADIUS wysyła *odmowy dostępu* wiadomości.
4. Jeśli są spełnione wszystkie warunki określone w żądaniu połączenia serwera zasad Sieciowych i zasad sieci (na przykład, pory dnia lub grupy ograniczeniami członkostwa), rozszerzenia serwera NPS dla wyzwalaczy żądania uwierzytelniania pomocniczego, za pomocą usługi Azure Multi-Factor Authentication.
5. Usługa Azure Multi-Factor Authentication komunikuje się z usługą Azure Active Directory, pobiera szczegóły użytkownika i wykonuje dodatkowego uwierzytelniania przy użyciu metody, który został skonfigurowany przez użytkownika (komórki telefoniczne, wiadomość SMS lub aplikacji mobilnej).
6. Po pomyślnym żądania uwierzytelniania MFA, usługi Azure Multi-Factor Authentication komunikuje się wynik do rozszerzenia serwera NPS.
7. Po próby połączenia są uwierzytelnieni i autoryzowani, serwer zasad Sieciowych, w którym zainstalowano rozszerzenia wysyła PROMIEŃ *udzielenia dostępu* komunikatu na serwerze sieci VPN (klienta usługi RADIUS).
8. Użytkownik uzyskuje dostęp do portu wirtualnego na serwerze sieci VPN i ustanawia szyfrowany tunel sieci VPN.

## <a name="prerequisites"></a>Wymagania wstępne

W tej sekcji przedstawiono wymagania wstępne, które należy wykonać, zanim włączysz usługę MFA z siecią VPN. Przed przystąpieniem do wykonywania, konieczne jest posiadanie następujących wymagań wstępnych w miejscu:

* Infrastrukturę sieci VPN
* Rola usług zasad sieciowych i dostępu
* Licencja na usługę Azure Multi-Factor Authentication
* Oprogramowanie systemu Windows Server
* Biblioteki
* Azure Active Directory (Azure AD) zsynchronizowane z usługą środowiska lokalnego usługi Active Directory
* Identyfikator GUID usługi Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastrukturę sieci VPN

W tym artykule założono, że masz działającej infrastruktury sieci VPN, który używa systemu Microsoft Windows Server 2016 i że serwer sieci VPN aktualnie nie skonfigurowano przesyłanie żądań połączeń do serwera RADIUS. W artykule możesz skonfigurować infrastrukturę sieci VPN do korzystania z centralnego serwera RADIUS.

Jeśli nie masz działającej infrastruktury sieci VPN w miejscu, można szybko można utworzyć jeden, postępując zgodnie ze wskazówkami w wiele samouczków ustawienia sieci VPN, które można znaleźć w Microsoft i innych witryn.

### <a name="the-network-policy-and-access-services-role"></a>Rola usług zasad sieciowych i dostępu

Dostęp do usług zasad sieciowych i oferuje funkcje serwera i klienta usługi RADIUS. W tym artykule założono, że zainstalowano rolę usług zasad sieciowych i dostępu na serwer członkowski lub kontroler domeny w danym środowisku. W tym przewodniku konfigurowania serwera RADIUS konfiguracji sieci VPN. Zainstaluj rolę usług zasad sieciowych i dostępu na serwerze *innych niż* serwer sieci VPN.

Aby uzyskać informacje o instalowaniu roli usług zasad sieciowych i dostępu usługi Windows Server 2012 lub nowszy, odwiedź [zainstalować serwer zasad dotyczących kondycji ochrony dostępu do sieci](https://technet.microsoft.com/library/dd296890.aspx). Ochrona dostępu do sieci jest przestarzała w systemie Windows Server 2016. Aby uzyskać opis najlepszych rozwiązań dla serwera zasad Sieciowych, w tym zalecenia, aby zainstalować serwer NPS na kontrolerze domeny, zobacz [najlepsze rozwiązania dotyczące serwera NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Azure MFA License

Licencja jest wymagana dla usługi Azure Multi-Factor Authentication i jest dostępna za pośrednictwem usługi Azure AD Premium, pakietu Enterprise Mobility + Security lub autonomiczną licencję usługi Multi-Factor Authentication. Na podstawie użycia licencji na usługę Azure MFA, takich jak dla określonego użytkownika lub uwierzytelnianie licencji na nie są zgodne z rozszerzeniem serwera NPS. Aby uzyskać więcej informacji, zobacz [sposobu uzyskania usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md). Do celów testowych można użyć subskrypcji wersji próbnej.

### <a name="windows-server-software"></a>Oprogramowanie systemu Windows Server

Rozszerzenia serwera NPS wymaga systemu Windows Server 2008 R2 SP1 lub zainstalować później, przy użyciu roli usług zasad sieciowych i dostępu. Wszystkie kroki opisane w tym przewodniku zostały wykonane przy użyciu systemu Windows Server 2016.

### <a name="libraries"></a>Biblioteki

Następujące biblioteki są instalowane automatycznie z rozszerzenia serwera NPS:

-   [Pakiety Visual C++ Redistributable for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory Module for Windows PowerShell w wersji 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Microsoft Azure Active Directory modułu programu PowerShell nie jest już obecny, jest on instalowany przy użyciu skryptu konfiguracji, który uruchamiany jako część procesu instalacji. Nie ma potrzeby do zainstalowania modułu wcześniej, jeśli nie został jeszcze zainstalowany.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Usługa Azure Active Directory synchronizowane z usługą Active Directory w środowisku lokalnym

Aby użyć rozszerzenia serwera NPS, lokalnych użytkowników musi być synchronizowane z usługą Azure Active Directory i włączone dla usługi MFA. W tym przewodniku założono, że lokalnych użytkowników są synchronizowane z usługą Azure Active Directory za pomocą usługi Azure AD Connect. Instrukcje dotyczące włączania uwierzytelniania Wieloskładnikowego użytkownicy znajdują się poniżej.

Aby uzyskać informacje o usłudze Azure AD Connect, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Identyfikator GUID usługi Azure Active Directory

Aby zainstalować rozszerzenia serwera NPS, musisz wiedzieć identyfikator GUID usługi Azure Active Directory. Instrukcje dotyczące znajdowania identyfikatora GUID usługi Azure Active Directory znajdują się w następnej sekcji.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurowanie serwera RADIUS dla połączeń sieci VPN

Po zainstalowaniu roli serwera NPS na serwerze członkowskim, musisz skonfigurować go do uwierzytelniania i autoryzacji klienta sieci VPN, czy żądania połączenia sieci VPN. 

W tej sekcji założono instalacji roli usług zasad sieciowych i dostępu, ale nie skonfigurowano ją do użycia w ramach infrastruktury.

> [!NOTE]
> Jeśli masz już działający serwer sieci VPN używa centralny serwer usługi RADIUS do uwierzytelniania, możesz pominąć tę sekcję.
>

### <a name="register-server-in-active-directory"></a>Zarejestruj serwer w usłudze Active Directory

Aby działać poprawnie, w tym scenariuszu, serwer NPS musi być zarejestrowany w usłudze Active Directory.

1. Otwórz Menedżera serwera.

2. W Menedżerze serwera wybierz **narzędzia**, a następnie wybierz pozycję **serwer zasad sieciowych**.

3. W konsoli serwera zasad sieciowych, kliknij prawym przyciskiem myszy **serwer NPS (lokalny)** , a następnie wybierz pozycję **Zarejestruj serwer w usłudze Active Directory**. Wybierz **OK** dwa razy.

    ![Zarejestruj serwer w opcji menu usługi Active Directory](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Zamykaj konsoli w następnej procedurze.

### <a name="use-wizard-to-configure-the-radius-server"></a>Użyj kreatora, aby skonfigurować serwer usługi RADIUS

Można użyć standardowego (opartej na kreatorze) lub zaawansowanych opcji konfiguracji, aby skonfigurować serwer usługi RADIUS. W tej sekcji założono, że używasz opcji opartej na Kreatorze konfiguracji standardowej.

1. W konsoli serwera zasad sieciowych wybierz **serwer NPS (lokalny)** .

2. W obszarze **standardowej konfiguracji**, wybierz opcję **serwera RADIUS dla połączeń sieci VPN lub Dial-Up**, a następnie wybierz pozycję **Konfigurowanie sieci VPN lub Dial-Up**.

    ![Konfiguracja serwera RADIUS dla połączeń sieci VPN lub Dial-Up](./media/howto-mfa-nps-extension-vpn/image3.png)

3. W **wybierz Dial-up lub typu połączenia sieci prywatnej wirtualnego** okna, wybierz **połączenia wirtualnej sieci prywatnej**, a następnie wybierz pozycję **dalej**.

    ![Konfigurowanie wirtualnej sieci prywatnej połączeń](./media/howto-mfa-nps-extension-vpn/image4.png)

4. W **Określ Dial-Up lub serwer VPN** wybierz **Dodaj**.

5. W **klienta RADIUS nowe** okna, Podaj przyjazną nazwę, wprowadź możliwej do rozpoznania nazwę lub adres IP serwera sieci VPN, a następnie wprowadź udostępnionych tajny. Należy udostępnionych tajny długie i złożone. Zapisz, ponieważ będzie on potrzebny w następnej sekcji.

    ![Utwórz okno klienta RADIUS nowe](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Wybierz **OK**, a następnie wybierz pozycję **dalej**.

7. W **skonfigurować metody uwierzytelniania** okna, Zaakceptuj wybór domyślny (**uwierzytelnianie szyfrowane firmy Microsoft w wersji 2 [MS-CHAPv2])** lub wybierz inną opcję i wybierz pozycję **dalej** .

    > [!NOTE]
    > Jeśli skonfigurujesz protokołu uwierzytelniania rozszerzonego (EAP), należy użyć Microsoft Challenge Handshake Authentication Protocol (CHAPv2) lub uwierzytelniania protokołu PEAP (Protected Extensible). Nie protokołu EAP jest obsługiwane.

8. W **określ grupy użytkowników** wybierz **Dodaj**, a następnie wybierz odpowiednią grupę. Jeśli grupa nie istnieje, pozostaw pustą wartość, aby przyznać dostęp wszystkim użytkownikom wybór.

    ![Określ okno grupy użytkowników, aby udzielić lub odmówić dostępu](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Wybierz opcję **Dalej**.

10. W **określ filtry IP** wybierz **dalej**.

11. W **Określ ustawienia szyfrowania** , zaakceptuj ustawienia domyślne, a następnie wybierz pozycję **dalej**.

    ![W oknie Określanie ustawień szyfrowania](./media/howto-mfa-nps-extension-vpn/image8.png)

12. W **Określ nazwę obszaru** okna, nazwa obszaru jest pusta, zaakceptuj domyślne ustawienie, a następnie wybierz **dalej**.

    ![Określ nazwę obszaru okno](./media/howto-mfa-nps-extension-vpn/image9.png)

13. W **klientów Kończenie nowego telefoniczne lub połączenia wirtualnej sieci prywatnej i RADIUS** wybierz **Zakończ**.

    ![Ukończona konfiguracja okna](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Zweryfikuj konfigurację usługi RADIUS

W tej sekcji przedstawiono utworzonych za pomocą Kreatora konfiguracji.

1. Na serwerze zasad sieciowych w konsoli serwera NPS (lokalny), rozwiń węzeł **klientów RADIUS**, a następnie wybierz pozycję **klientów RADIUS**.

2. W okienku szczegółów kliknij prawym przyciskiem myszy klienta usługi RADIUS, który został utworzony, a następnie wybierz pozycję **właściwości**. Właściwości klienta RADIUS (serwer sieci VPN) powinny być podobne do tych, pokazano poniżej:

    ![Sprawdź właściwości sieci VPN i konfiguracji](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Wybierz **anulować**.

4. Na serwerze zasad sieciowych w konsoli serwera NPS (lokalny), rozwiń węzeł **zasady**, a następnie wybierz pozycję **zasady żądań połączeń**. Zasady połączenia sieci VPN jest wyświetlana, jak pokazano na poniższej ilustracji:

    ![Zasady żądań połączeń, przedstawiający zasady połączenia sieci VPN](./media/howto-mfa-nps-extension-vpn/image12.png)

5. W obszarze **zasady**, wybierz opcję **zasad sieciowych**. Powinny zostać wyświetlone zasady połączenia wirtualnej sieci prywatnej (VPN), które przypomina zasad pokazano na poniższej ilustracji:

    ![Zasady sieciowe przedstawiający zasady połączenia wirtualnej sieci prywatnej](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Skonfiguruj serwer sieci VPN do użycia uwierzytelnianie usługi RADIUS

W tej sekcji skonfigurujesz serwer sieci VPN do użycia uwierzytelnianie usługi RADIUS. Instrukcjach przyjęto założenie, że mają działającej konfiguracji serwera sieci VPN, ale nie skonfigurowano do użycia uwierzytelnianie usługi RADIUS. Po skonfigurowaniu serwera sieci VPN, upewnij się, że konfigurację działa zgodnie z oczekiwaniami.

> [!NOTE]
> Jeśli masz już działającą konfigurację serwera sieci VPN korzysta z uwierzytelniania usługi RADIUS, możesz pominąć tę sekcję.
>

### <a name="configure-authentication-provider"></a>Skonfiguruj dostawcę uwierzytelniania

1. Na serwerze sieci VPN Otwórz Menedżera serwera.

2. W Menedżerze serwera wybierz **narzędzia**, a następnie wybierz pozycję **Routing i dostęp zdalny**.

3. W **Routing i dostęp zdalny** okna, kliknij prawym przyciskiem myszy  **\<nazwa serwera > (local)** , a następnie wybierz pozycję **właściwości**.

4. W  **\<nazwa serwera > (local) właściwości** wybierz **zabezpieczeń** kartę.

5. Na **zabezpieczeń** , w obszarze **dostawcy uwierzytelniania**, wybierz opcję **uwierzytelnianie usługi RADIUS**, a następnie wybierz pozycję **Konfiguruj**.

    ![Konfigurowanie dostawcy uwierzytelniania usługi RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)

6. W **uwierzytelnianie usługi RADIUS** wybierz **Dodaj**.

7. W **Dodawanie serwera RADIUS** okna, wykonaj następujące czynności:

    a. W **nazwy serwera** wprowadź nazwę lub adres IP serwera RADIUS, który został skonfigurowany w poprzedniej sekcji.

    b. Dla **wspólny klucz tajny**, wybierz opcję **zmiany**, a następnie wprowadź udostępnionych tajny, który został utworzony i zarejestrowany wcześniej.

    c. W **limitu czasu (w sekundach)** wybierz wartość z zakresu od **30** za pośrednictwem **60**.  
    Wartość limitu czasu jest niezbędne w celu umożliwienia wystarczająco dużo czasu na ukończenie drugiego składnika uwierzytelniania.

    ![Dodaj okno serwera usługi RADIUS, Konfigurowanie limitu czasu](./media/howto-mfa-nps-extension-vpn/image16.png)

8. Kliknij przycisk **OK**.

### <a name="test-vpn-connectivity"></a>Testowanie łączności sieci VPN

W tej sekcji, potwierdzasz, że klient sieci VPN jest uwierzytelnione i autoryzowane przez serwer usługi RADIUS, gdy próbują nawiązać połączenie z portu wirtualnego sieci VPN. Instrukcjach przyjęto założenie, że używasz systemu Windows 10 jako klienta sieci VPN.

> [!NOTE]
> Jeśli już skonfigurowano klienta sieci VPN w taki sposób, aby połączyć się z serwerem sieci VPN i zapisaniu ustawienia, można pominąć kroki związane z konfigurowania i zapisywania obiektu połączenia sieci VPN.
>

1. Na komputerze klienckim sieci VPN wybierz **Start** przycisk, a następnie wybierz **ustawienia** przycisku.

2. W **ustawienia Windows** wybierz **sieć i Internet**.

3. wybierz pozycję **VPN**.

4. Wybierz **dodać połączenie VPN**.

5. W **dodać połączenie VPN** okna w **dostawcy sieci VPN** wybierz opcję **Windows (wbudowane)** , wykonaj pozostałe pola, zgodnie z potrzebami, a następnie wybierz **Zapisz**.

    ![W oknie "Dodawanie połączenia VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Przejdź do **Panelu sterowania**, a następnie wybierz pozycję **Centrum sieci i udostępniania**.

7. Wybierz **zmiana ustawień karty**.

    ![Centrum sieci i udostępniania — Zmień ustawienia karty sieciowej](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Kliknij prawym przyciskiem myszy połączenie sieci VPN, a następnie wybierz **właściwości**.

9. W oknie dialogowym właściwości sieci VPN wybierz **zabezpieczeń** kartę.

10. Na **zabezpieczeń** kartę, upewnij się, że tylko **Microsoft CHAP Version 2 (MS-CHAP v2)** jest wybrany, a następnie wybierz **OK**.

    ![Opcja "Zezwalaj na tych protokołów"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Kliknij prawym przyciskiem myszy połączenie sieci VPN, a następnie wybierz **Connect**.

12. W **ustawienia** wybierz **Connect**.  
    Pomyślnym nawiązaniu połączenia zostanie wyświetlony w dzienniku zabezpieczeń na serwerze RADIUS, jako zdarzenia 6272 identyfikator, jak pokazano poniżej:

    ![Okno właściwości zdarzeń przedstawiający pomyślne połączenie](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Rozwiązywanie problemów z usługi RADIUS

Załóżmy, że działa konfiguracji sieci VPN przed skonfigurowaniem serwera sieci VPN, aby używany centralny serwer usługi RADIUS do uwierzytelniania i autoryzacji. Jeśli konfiguracja była praca, jest prawdopodobne, że problem jest spowodowany błędnej konfiguracji serwera RADIUS lub użycia nieprawidłowej nazwy użytkownika lub hasło. Na przykład jeśli używasz alternatywny sufiks nazwy UPN w nazwa_użytkownika próba logowania może zakończyć się niepowodzeniem. Użyj tej samej nazwie konta w celu uzyskania najlepszych wyników.

Aby rozwiązać te problemy, doskonale nadaje się do uruchomienia jest zbadanie dzienników zdarzeń zabezpieczeń na serwerze RADIUS. Aby zapisać czasu na wyszukiwanie zdarzeń, można użyć opartej na rolach zasad sieciowych i dostępu do serwera widoku niestandardowego w Podglądzie zdarzeń, jak pokazano poniżej. "Identyfikator zdarzenia 6273" wskazuje zdarzeń, gdzie serwer NPS odmowa dostępu do użytkownika.

![BYŁY wyświetlanie podglądu zdarzeń](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Konfigurowanie uwierzytelniania wieloskładnikowego

Aby uzyskać pomoc w przypadku konfigurowania użytkowników do uwierzytelniania wieloskładnikowego, zobacz artykuły [jak, które wymuszają weryfikację dwuetapową dla użytkownika lub grupy](howto-mfa-userstates.md) i [Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalowanie i konfigurowanie rozszerzenia serwera NPS

Ta sekcja zawiera instrukcje dotyczące konfigurowania sieci VPN, aby używać uwierzytelniania Wieloskładnikowego na potrzeby uwierzytelniania klienta z serwerem sieci VPN.

Po zainstalowaniu i skonfigurowaniu rozszerzenia serwera NPS, wszystkie uwierzytelniania opartego na usłudze RADIUS klienta, który jest przetwarzany przez ten serwer jest wymagany do użycia usługi MFA. Jeśli wszystkich użytkowników sieci VPN nie są zarejestrowane w usłudze Azure Multi-Factor Authentication, możesz wykonać jedną z następujących czynności:

* Konfigurowanie innego serwera RADIUS do uwierzytelniania użytkowników, którzy nie są skonfigurowane do używania uwierzytelniania Wieloskładnikowego.

* Utwórz wpis rejestru, który umożliwia użytkownikom kwestionowane zapewnić drugi składnik uwierzytelniania, jeśli są one rejestrowane w usłudze Azure Multi-Factor Authentication.

Utwórz nową wartość ciągu o nazwie _REQUIRE_USER_MATCH w HKLM\SOFTWARE\Microsoft\AzureMfa_i ustaw wartość *True* lub *False*.

![Ustawienie "Wymagaj dopasowania użytkownika"](./media/howto-mfa-nps-extension-vpn/image34.png)

Jeśli wartość jest równa *True* lub jest pusta, wszystkie żądania uwierzytelniania podlegają żądania uwierzytelniania MFA. Jeśli wartość jest równa *False*, wyzwań MFA są wystawiane tylko dla użytkowników, którzy są zarejestrowane w usłudze Azure Multi-Factor Authentication. Użyj *False* ustawienie tylko podczas testowania lub w środowiskach produkcyjnych w trakcie okresu dołączania.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Uzyskaj identyfikator GUID usługi Azure Active Directory

W ramach konfiguracji rozszerzenia serwera NPS musisz podać poświadczenia administratora i identyfikator dzierżawy usługi Azure AD. Uzyskaj identyfikator, wykonując następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny dzierżawy platformy Azure.

2. W okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

3. Wybierz **właściwości**.

4. Aby skopiować identyfikator usługi Azure AD, zaznacz **kopiowania** przycisku.

    ![Identyfikator katalogu usługi Azure AD w witrynie Azure portal](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia serwera NPS

Rozszerzenia serwera NPS musi być zainstalowany na serwerze zasad sieciowych i zainstalowaną rolą usługi dostępu do tej funkcji jako serwer RADIUS w projekcie. Czy *nie* zainstalować rozszerzenia serwera NPS na serwerze sieci VPN.

1. Pobierz rozszerzenia serwera NPS z [Microsoft Download Center](https://aka.ms/npsmfa).

2. Kopiowanie pliku wykonywalnego Instalatora (*NpsExtnForAzureMfaInstaller.exe*) na serwerze zasad Sieciowych.

3. Na serwerze zasad Sieciowych, kliknij dwukrotnie **NpsExtnForAzureMfaInstaller.exe** i, jeśli zostanie wyświetlony monit, wybierz opcję **Uruchom**.

4. W **serwera NPS dla usługi Azure MFA Instalator rozszerzenia** okna, przejrzyj postanowienia licencyjne dotyczące oprogramowania, wybierz **zgodę na warunki i postanowienia licencyjne** pole wyboru, a następnie wybierz pozycję **zainstalować**.

    ![Okno "Serwera NPS dla usługi Azure MFA Instalator rozszerzenia"](./media/howto-mfa-nps-extension-vpn/image36.png)

5. W **serwera NPS dla usługi Azure MFA Instalator rozszerzenia** wybierz **Zamknij**.  

    !["Ustawienia pomyślnie ukończono" oknie potwierdzenia](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurowanie certyfikatów do użytku z rozszerzeniem serwera NPS przy użyciu skryptu programu PowerShell

W celu zapewnienia bezpiecznej komunikacji i kontrola, należy skonfigurować certyfikaty do użytku przez rozszerzenia serwera NPS. Składniki serwera NPS obejmują skrypt programu Windows PowerShell, który umożliwia skonfigurowanie certyfikatu z podpisem własnym do użytku przy użyciu rozwiązania NPS.

Skrypt wykonuje następujące czynności:

* Tworzy certyfikat z podpisem własnym.
* Kojarzy klucz publiczny certyfikatu do jednostki w usłudze Azure AD usługi.
* Zapisuje certyfikat w magazynie komputera lokalnego.
* Udziela sieci użytkownikowi dostępu do klucza prywatnego certyfikatu.
* Uruchamia ponownie usługę serwera NPS.

Jeśli chcesz użyć własnych certyfikatów, należy skojarzyć klucz publiczny certyfikatu przy użyciu jednostki usługi w usłudze Azure AD i tak dalej.

Aby użyć skryptu, należy podać rozszerzenie przy użyciu poświadczeń administracyjnych usługi Azure Active Directory i identyfikator dzierżawy usługi Azure Active Directory, które wcześniej zostały skopiowane. Uruchom skrypt na każdym serwerze zasad Sieciowych, gdzie zainstalować rozszerzenia serwera NPS.

1. Uruchom program Windows PowerShell jako administrator.

2. W wierszu polecenia programu PowerShell wprowadź **dysk cd "c:\Program Files\Microsoft\AzureMfa\Config"** , a następnie naciśnij klawisz Enter.

3. W następnym wierszu polecenia wprowadź **.\AzureMfaNpsExtnConfigSetup.ps1**, a następnie naciśnij klawisz Enter. Skrypt sprawdza, czy jest zainstalowany moduł Azure AD PowerShell. Jeśli nie jest zainstalowany, skrypt zainstaluje moduł.

    ![Uruchamianie skryptu konfiguracji AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Po skrypt sprawdza poprawność instalacji modułu PowerShell, wyświetla modułu programu PowerShell usługi Azure Active Directory logowania okna.

4. Wprowadź swoje poświadczenia administratora usługi Azure AD i hasło, a następnie wybierz **Zaloguj**.

    ![Uwierzytelnianie usługi Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. W wierszu polecenia Wklej skopiowany wcześniej identyfikator dzierżawy, a następnie naciśnij klawisz Enter.

    ![Wprowadź identyfikator katalogu usługi Azure AD, kopiowane przed](./media/howto-mfa-nps-extension-vpn/image40.png)

    Skrypt tworzy certyfikat z podpisem własnym i wykonuje inne zmiany w konfiguracji. Dane wyjściowe są tak jak na poniższej ilustracji:

    ![Samopodpisany certyfikat przedstawiający okno programu PowerShell](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Uruchom ponownie serwer.

### <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji

Aby sprawdzić konfigurację, należy ustanowić nowe połączenie VPN z serwerem sieci VPN. Po pomyślnym wprowadzeniu poświadczeń dla uwierzytelniania podstawowego, połączenie sieci VPN czeka uwierzytelniania pomocniczego zakończyło się sukcesem, zanim połączenie zostanie nawiązane, jak pokazano poniżej.

![W oknie Ustawienia Windows w sieci VPN](./media/howto-mfa-nps-extension-vpn/image42.png)

W przypadku pomyślnego uwierzytelnienia za pomocą metody dodatkowej weryfikacji, który wcześniej skonfigurowany w usłudze Azure MFA są połączone z zasobem. Jednak w przypadku dodatkowego uwierzytelniania zakończy się niepowodzeniem, są odmowa dostępu do zasobu.

W poniższym przykładzie aplikację Microsoft Authenticator na Windows Phone zawiera uwierzytelniania dodatkowego:

![Przykład uwierzytelniania Wieloskładnikowego wiersz na Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Po pomyślnie uwierzytelniono za pomocą metody pomocnicze są uzyska dostęp do portu wirtualnego na serwerze sieci VPN. Ponieważ wymagane było jej używać dodatkowej metody uwierzytelniania przy użyciu aplikacji mobilnej na zaufanym urządzeniu, proces logowania jest bezpieczniejszy niż Jeśli była używana w niej tylko kombinację nazwy użytkownika i hasła.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Wyświetl dzienniki Podglądu zdarzeń dla zdarzeń pomyślnego logowania

Aby wyświetlić zdarzeń pomyślnego logowania w dziennikach podglądu zdarzeń Windows zapytanie dziennika zabezpieczeń Windows, na serwerze NPS, wprowadzając następujące polecenie programu PowerShell:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Zabezpieczenia środowiska PowerShell programu Podgląd zdarzeń](./media/howto-mfa-nps-extension-vpn/image44.png)

Jak pokazano poniżej, można wyświetlić w dzienniku zabezpieczeń lub widok niestandardowy usług zasad sieciowych i dostępu:

![Przykład serwera zasad sieciowych dziennika](./media/howto-mfa-nps-extension-vpn/image45.png)

Na serwerze, na którym zainstalowano rozszerzenia serwera NPS dla usługi Azure Multi-Factor Authentication, można znaleźć podglądu zdarzeń Dzienniki aplikacji, które są specyficzne dla rozszerzenia w *aplikacji i usług Logs\Microsoft\AzureMfa*.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Okienko dzienników AuthZ podglądu zdarzeń przykład](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Jeśli konfiguracja nie działa zgodnie z oczekiwaniami, należy rozpocząć rozwiązywanie problemów z upewniając się, że użytkownik jest skonfigurowany do używania uwierzytelniania Wieloskładnikowego. Użytkownik powinien połączyć się z [witryny Azure portal](https://portal.azure.com). Jeśli użytkownik jest monitowany o podanie dodatkowego uwierzytelniania i może pomyślnie uwierzytelnienia, można wyeliminować niepoprawnej konfiguracji uwierzytelnianie wieloskładnikowe jako problem.

Jeśli działania usługi MFA dla użytkownika, zapoznaj się odpowiednie dzienniki Podglądu zdarzeń. Dzienniki zawierają zdarzeń zabezpieczeń, operacyjne bramy i dzienniki usługi Azure Multi-Factor Authentication, które zostały omówione w poprzedniej sekcji.

Poniżej przedstawiono przykład dziennika zabezpieczeń, który przedstawia zdarzenia zakończonego niepowodzeniem logowania (zdarzenie 6273 Identyfikatora):

![Dziennik zabezpieczeń przedstawiający nie powiodło się zdarzenia logowania](./media/howto-mfa-nps-extension-vpn/image47.png)

Zdarzenia związane z dziennika usługi Azure Multi-Factor Authentication jest następujący:

![Dzienniki usługi Azure Multi-Factor Authentication](./media/howto-mfa-nps-extension-vpn/image48.png)

Do zaawansowanego rozwiązywania problemów, sprawdź pliki dziennika format bazy danych serwera NPS zainstalowaną usługę serwera NPS. Pliki dziennika są tworzone w _%SystemRoot%\System32\Logs_ folder jako pliki tekstowe rozdzielonych przecinkami. Aby uzyskać opis plików dziennika, zobacz [interpretacji pliki dziennika Format bazy danych serwera NPS](https://technet.microsoft.com/library/cc771748.aspx).

Wpisy w tych plikach dziennika są trudne do interpretacji, chyba że zostaną wyeksportowane do arkusza kalkulacyjnego lub bazy danych. Możesz znaleźć wiele uwierzytelniania internetowego usługi (IAS) podczas analizowania narzędzia w trybie online, aby ułatwić Interpretowanie plików dzienników. Dane wyjściowe tego takich do pobrania [aplikacji "shareware"](https://www.deepsoftware.com/iasviewer) jest następująca:

![Przykładowy "shareware" aplikacji usługi IAS analizatora](./media/howto-mfa-nps-extension-vpn/image49.png)

Aby wykonać dodatkowe procedury rozwiązywania problemów, można użyć analizatora protokołów, takich jak program Wireshark lub [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Na poniższej ilustracji, z programu Wireshark przedstawiono komunikaty RADIUS między serwerem sieci VPN i serwera NPS.

![Microsoft Message Analyzer przedstawiający filtrowane ruchu](./media/howto-mfa-nps-extension-vpn/image50.png)

Aby uzyskać więcej informacji, zobacz [integrowanie istniejącej infrastruktury NPS przy użyciu usługi Azure Multi-Factor Authentication](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Kolejne kroki

[Uzyskaj usługę Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS](howto-mfaserver-nps-rdg.md)

[Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
