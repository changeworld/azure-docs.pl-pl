---
title: Sieć VPN z usługą Azure MFA przy użyciu rozszerzenia NPS — usługa Azure Active Directory
description: Zintegruj infrastrukturę sieci VPN z usługą Azure MFA przy użyciu rozszerzenia serwera zasad sieciowych dla platformy Microsoft Azure.
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
ms.openlocfilehash: ec8d5b66c71c558e56f3d1f48cec96d7cc487552
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654126"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Zintegruj infrastrukturę sieci VPN z usługą Azure MFA przy użyciu rozszerzenia serwera zasad sieciowych dla platformy Azure

Rozszerzenie serwera zasad sieciowych (NPS) dla platformy Azure umożliwia organizacjom ochronę uwierzytelniania klienta usługi radius (Remote Authentication Dial-In User Service) przy użyciu opartego na chmurze [uwierzytelniania wieloskładnikowego azure (MFA),](howto-mfaserver-nps-rdg.md)który zapewnia weryfikację dwuetapową.

Ten artykuł zawiera instrukcje dotyczące integrowania infrastruktury serwera NPS z usługą MFA przy użyciu rozszerzenia NPS dla platformy Azure. Ten proces umożliwia bezpieczną weryfikację dwuetapową dla użytkowników, którzy próbują połączyć się z siecią przy użyciu sieci VPN.

Usługi zasad sieciowych i dostępu zapewniają organizacjom możliwość:

* Przypisz centralną lokalizację do zarządzania żądaniami sieci i kontroli nad nimi, aby określić:

  * Kto może się połączyć

  * Jakie pore dnia połączenia są dozwolone

  * Czas trwania połączeń

  * Poziom zabezpieczeń, którego klienci muszą używać do łączenia

    Zamiast określać zasady na każdym serwerze bramy sieci VPN lub usługi pulpitu zdalnego, zrób to po tym, jak znajdują się w centralnej lokalizacji. Protokół RADIUS służy do dostarczania scentralizowanego uwierzytelniania, autoryzacji i księgowania (AAA).

* Ustanawianie i wymuszanie zasad kondycji klienta ochrony dostępu do sieci (NAP), które określają, czy urządzeniom mają nieograniczony czy ograniczony dostęp do zasobów sieciowych.

* Umożliwia wymuszanie uwierzytelniania i autoryzacji dostępu do punktów dostępu bezprzewodowego i przełączników Ethernet o wymiarach 802.1x.
  Aby uzyskać więcej informacji, zobacz [Serwer zasad sieciowych](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top).

Aby zwiększyć bezpieczeństwo i zapewnić wysoki poziom zgodności, organizacje mogą zintegrować serwer NPS z uwierzytelnianiem wieloskładnikowym platformy Azure, aby upewnić się, że użytkownicy używają weryfikacji dwuetapowej do łączenia się z portem wirtualnym na serwerze sieci VPN. Aby użytkownicy mieli dostęp, muszą podać kombinację nazwy użytkownika i hasła oraz inne informacje, które kontrolują. Te informacje muszą być zaufane i nie łatwo powielać. Może zawierać numer telefonu komórkowego, numer stacjonarny lub aplikację na urządzeniu mobilnym.

Przed dostępnością rozszerzenia serwera NPS dla platformy Azure klienci, którzy chcieli zaimplementować weryfikację dwuetapową dla zintegrowanych środowisk serwera NPS i usługi MFA, musieli skonfigurować i obsługiwać oddzielny serwer usługi MFA w środowisku lokalnym. Ten typ uwierzytelniania jest oferowany przez bramę usług pulpitu zdalnego i serwer uwierzytelniania wieloskładnikowego platformy Azure przy użyciu usługi RADIUS.

Dzięki rozszerzeniu NPS dla platformy Azure organizacje mogą zabezpieczać uwierzytelnianie klienta usługi RADIUS, wdrażając lokalne rozwiązanie usługi MFA lub rozwiązanie usługi MFA oparte na chmurze.

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Gdy użytkownicy łączą się z portem wirtualnym na serwerze sieci VPN, muszą najpierw uwierzytelnić się przy użyciu różnych protokołów. Protokoły umożliwiają użycie kombinacji nazwy użytkownika i hasła oraz metod uwierzytelniania opartych na certyfikatach.

Oprócz uwierzytelniania i weryfikacji ich tożsamości, użytkownicy muszą mieć odpowiednie uprawnienia telefonowania. W prostych implementacjach uprawnienia telefonowania umożliwiające dostęp są ustawiane bezpośrednio w obiektach użytkownika usługi Active Directory.

![Karta Telefonowanie we właściwościach użytkownika Użytkownicy i komputery usługi Active Directory](./media/howto-mfa-nps-extension-vpn/image1.png)

W prostych implementacjach każdy serwer sieci VPN udziela lub odmawia dostępu na podstawie zasad zdefiniowanych na każdym lokalnym serwerze sieci VPN.

W większych i bardziej skalowalnych implementacjach zasady, które udzielają lub odmawiają dostępu do sieci VPN, są scentralizowane na serwerach RADIUS. W takich przypadkach serwer sieci VPN działa jako serwer dostępu (klient RADIUS), który przesyła dalej żądania połączeń i komunikaty o koncie do serwera RADIUS. Aby połączyć się z portem wirtualnym na serwerze sieci VPN, użytkownicy muszą być uwierzytelnieni i spełniać warunki, które są zdefiniowane centralnie na serwerach RADIUS.

Gdy rozszerzenie SERWERA SIECIOWEGO dla platformy Azure jest zintegrowane z serwerem NPS, pomyślne wyniki przepływu uwierzytelniania w następujący sposób:

1. Serwer sieci VPN otrzymuje żądanie uwierzytelnienia od użytkownika sieci VPN, które zawiera nazwę użytkownika i hasło do łączenia się z zasobem, takim jak sesja pulpitu zdalnego.
2. Działając jako klient RADIUS, serwer sieci VPN konwertuje żądanie na komunikat *żądania dostępu* PROMIENIA i wysyła je (z zaszyfrowanym hasłem) do serwera RADIUS, na którym jest zainstalowane rozszerzenie NPS.
3. Kombinacja nazwy użytkownika i hasła jest weryfikowana w usłudze Active Directory. Jeśli nazwa użytkownika lub hasło są nieprawidłowe, serwer RADIUS wysyła komunikat *o odrzuceniu programu Access.*
4. Jeśli spełnione są wszystkie warunki określone w żądaniu połączenia serwera NPS i zasadach sieciowych (na przykład ograniczenia członkostwa o porze dnia lub w grupie), rozszerzenie NPS wyzwala żądanie uwierzytelniania pomocniczego za pomocą uwierzytelniania wieloskładnikowego platformy Azure.
5. Uwierzytelnianie wieloskładnikowe platformy Azure komunikuje się z usługą Azure Active Directory, pobiera szczegóły użytkownika i wykonuje uwierzytelnianie pomocnicze przy użyciu metody skonfigurowanej przez użytkownika (połączenie komórkowe, wiadomość tekstowa lub aplikacja mobilna).
6. Gdy wyzwanie usługi MFA zakończy się pomyślnie, uwierzytelnianie wieloskładnikowe platformy Azure komunikuje wynik z rozszerzeniem serwera NPS.
7. Po uwierzytelnieniu i autoryzowaniu próby połączenia serwer NPS, na którym jest zainstalowane rozszerzenie, wysyła komunikat RADIUS *Access-Accept* do serwera sieci VPN (klienta RADIUS).
8. Użytkownik uzyskuje dostęp do portu wirtualnego na serwerze sieci VPN i ustanawia zaszyfrowany tunel sieci VPN.

## <a name="prerequisites"></a>Wymagania wstępne

W tej sekcji opisano wymagania wstępne, które muszą zostać ukończone, aby można było zintegrować uwierzytelnianie wieloskładnikowe z siecią VPN. Przed rozpoczęciem należy mieć następujące wymagania wstępne:

* Infrastruktura SIECI VPN
* Rola Zasady sieciowe i usługi dostępu
* Licencja uwierzytelniania wieloskładnikowego platformy Azure
* Oprogramowanie systemu Windows Server
* Biblioteki
* Usługa Azure Active Directory (Azure AD) synchronizowana z lokalną usługą Active Directory
* Identyfikator identyfikatora GUID usługi Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastruktura SIECI VPN

W tym artykule przyjęto założenie, że istnieje działająca infrastruktura sieci VPN korzystająca z systemu Microsoft Windows Server 2016 i że serwer sieci VPN nie jest obecnie skonfigurowany do przekazywania żądań połączeń do serwera RADIUS. W tym artykule konfigurujesz infrastrukturę sieci VPN do korzystania z centralnego serwera RADIUS.

Jeśli nie masz działającej infrastruktury sieci VPN, możesz ją szybko utworzyć, postępając zgodnie ze wskazówkami zawartymi w wielu samouczkach konfiguracji sieci VPN, które można znaleźć w witrynach firmy Microsoft i innych firm.

### <a name="the-network-policy-and-access-services-role"></a>Rola Zasady sieciowe i usługi dostępu

Usługi zasad sieciowych i dostępu zapewniają funkcje serwera i klienta RADIUS. W tym artykule przyjęto założenie, że rola Zasady sieciowe i Usługi dostępu na serwerze członkowskim lub kontrolerze domeny w danym środowisku została zainstalowana. W tym przewodniku można skonfigurować usługę RADIUS dla konfiguracji sieci VPN. Zainstaluj rolę Zasady sieciowe i Usługi dostępu na serwerze *innym niż* serwer sieci VPN.

Aby uzyskać informacje dotyczące instalowania usługi roli Zasady sieciowe i usługi dostępu w systemie Windows Server 2012 lub [nowszym,](https://technet.microsoft.com/library/dd296890.aspx)zobacz Instalowanie serwera zasad kondycji ochrony dostępu . Funkcja napap jest przestarzała w systemie Windows Server 2016. Aby uzyskać opis najlepszych rozwiązań dotyczących serwera NPS, w tym zalecenie zainstalowania serwera NPS na kontrolerze domeny, zobacz [Najważniejsze wskazówki dotyczące serwera NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Licencja usługi Azure MFA

Licencja jest wymagana dla uwierzytelniania wieloskładnikowego platformy Azure i jest dostępna za pośrednictwem usługi Azure AD Premium, Enterprise Mobility + Security lub licencji autonomicznej uwierzytelniania wieloskładnikowego. Licencje oparte na zużyciu dla usługi Azure MFA, takie jak licencje na użytkownika lub uwierzytelniania, nie są zgodne z rozszerzeniem serwera NPS. Aby uzyskać więcej informacji, zobacz [Jak uzyskać uwierzytelnianie wieloskładnikowe platformy Azure](concept-mfa-licensing.md). Do celów testowych można użyć subskrypcji próbnej.

### <a name="windows-server-software"></a>Oprogramowanie systemu Windows Server

Rozszerzenie serwera NPS wymaga dodatku SP1 dla systemu Windows Server 2008 R2 lub nowszego z zainstalowaną rolą Zasady sieciowe i usługi dostępu. Wszystkie kroki opisane w tym przewodniku zostały wykonane w systemie Windows Server 2016.

### <a name="libraries"></a>Biblioteki

Następujące biblioteki są instalowane automatycznie z rozszerzeniem NPS:

-    [Pakiety redystrybucyjne programu Visual C++ dla programu Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Moduł usługi Microsoft Azure Active Directory dla systemu Windows PowerShell w wersji 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Jeśli moduł programu PowerShell usługi Microsoft Azure Active Directory nie jest jeszcze obecny, jest on instalowany ze skryptem konfiguracji uruchamianym w ramach procesu instalacji. Nie ma potrzeby instalowania modułu z wyprzedzeniem, jeśli nie jest jeszcze zainstalowany.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Usługa Azure Active Directory synchronizowana z lokalną usługą Active Directory

Aby użyć rozszerzenia serwera NPS, użytkownicy lokalni muszą być synchronizowani z usługą Azure Active Directory i włączeni dla usługi MFA. W tym przewodniku przyjęto założenie, że użytkownicy lokalni są synchronizowani z usługą Azure Active Directory za pośrednictwem usługi Azure AD Connect. Instrukcje dotyczące włączania użytkowników usługi MFA znajdują się poniżej.

Aby uzyskać informacje o usłudze Azure AD Connect, zobacz [Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Identyfikator identyfikatora GUID usługi Azure Active Directory

Aby zainstalować rozszerzenie serwera NPS, musisz znać identyfikator GUID usługi Azure Active Directory. Instrukcje dotyczące znajdowania identyfikatora GUID usługi Azure Active Directory znajdują się w następnej sekcji.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurowanie usługi RADIUS dla połączeń sieci VPN

Jeśli rola serwera NPS została zainstalowana na serwerze członkowskim, należy skonfigurować ją do uwierzytelniania i autoryzowania klienta sieci VPN, który żąda połączeń sieci VPN. 

W tej sekcji przyjęto założenie, że zainstalowano rolę Zasady sieciowe i Usługi dostępu, ale nie skonfigurowano jej do użycia w infrastrukturze.

> [!NOTE]
> Jeśli masz już działający serwer sieci VPN, który używa scentralizowanego serwera RADIUS do uwierzytelniania, możesz pominąć tę sekcję.
>

### <a name="register-server-in-active-directory"></a>Rejestrowanie serwera w usłudze Active Directory

Aby w tym scenariuszu serwer NPS działał poprawnie, musi być zarejestrowany w usłudze Active Directory.

1. Otwórz Menedżera serwera.

2. W Menedżerze serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Serwer zasad sieciowych**.

3. W konsoli Serwer zasad sieciowych kliknij prawym przyciskiem myszy **pozycję NPS (Local),** a następnie wybierz polecenie **Zarejestruj serwer w usłudze Active Directory**. Wybierz **przycisk OK** dwa razy.

    ![Opcja menu Rejestrowanie serwera w usłudze Active Directory](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Pozostaw konsolę otwartą dla następnej procedury.

### <a name="use-wizard-to-configure-the-radius-server"></a>Konfigurowanie serwera RADIUS za pomocą kreatora

Do skonfigurowania serwera RADIUS można użyć opcji standardowej (opartej na kreatorze) lub zaawansowanej konfiguracji. W tej sekcji przyjęto założenie, że używasz standardowej opcji konfiguracji opartej na kreatorze.

1. W konsoli Serwer zasad sieciowych wybierz pozycję **NPS (Local)**.

2. W obszarze **Konfiguracja standardowa**wybierz pozycję **SERWER RADIUS dla połączeń telefonicznych lub sieci VPN**, a następnie wybierz pozycję **Konfiguruj sieć VPN lub Dial-up**.

    ![Konfigurowanie serwera RADIUS dla połączeń telefonicznych lub połączeń sieci VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. W oknie **Wybierz typ połączeń telefonicznych lub wirtualnych połączeń sieci prywatnej** wybierz pozycję **Połączenia wirtualnej sieci prywatnej**, a następnie wybierz pozycję **Dalej**.

    ![Konfigurowanie połączeń wirtualnej sieci prywatnej](./media/howto-mfa-nps-extension-vpn/image4.png)

4. W oknie **Określanie opcji Telefonowanie lub Serwer sieci VPN** wybierz pozycję **Dodaj**.

5. W oknie **Nowy klient RADIUS** podaj przyjazną nazwę, wprowadź rozpoznawaną nazwę lub adres IP serwera sieci VPN, a następnie wprowadź wspólne tajne hasło. Pokonuj wspólne tajne hasło długie i złożone. Nagraj go, ponieważ będziesz go potrzebować w następnej sekcji.

    ![Tworzenie nowego okna klienta USŁUGI RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Wybierz **przycisk OK**, a następnie wybierz pozycję **Dalej**.

7. W oknie **Konfigurowanie metod uwierzytelniania** zaakceptuj domyślny wybór **(Uwierzytelnianie szyfrowane firmy Microsoft w wersji 2 [MS-CHAPv2])** lub wybierz inną opcję, a następnie wybierz pozycję **Dalej**.

    > [!NOTE]
    > W przypadku konfigurowania protokołu EAP (Extensible Authentication Protocol) należy użyć protokołu uwierzytelniania uzgadniania microsoft challenge-handshake (CHAPv2) lub chronionego protokołu uwierzytelniania rozszerzalnego (PEAP). Żaden inny protokołu EAP nie jest obsługiwany.

8. W oknie **Określanie grup użytkowników** wybierz pozycję **Dodaj**, a następnie wybierz odpowiednią grupę. Jeśli nie istnieje żadna grupa, pozostaw zaznaczenie puste, aby udzielić dostępu wszystkim użytkownikom.

    ![Określanie okna Grup użytkowników w celu zezwalania lub odrzucania dostępu](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Wybierz **pozycję Dalej**.

10. W oknie **Określanie filtrów IP** wybierz pozycję **Dalej**.

11. W oknie **Określ ustawienia szyfrowania** zaakceptuj ustawienia domyślne, a następnie wybierz pozycję **Dalej**.

    ![Okno Określ ustawienia szyfrowania](./media/howto-mfa-nps-extension-vpn/image8.png)

12. W oknie **Określanie nazwy obszaru** pozostaw nazwę obszaru pustą, zaakceptuj ustawienie domyślne, a następnie wybierz pozycję **Dalej**.

    ![Okno Określ nazwę obszaru](./media/howto-mfa-nps-extension-vpn/image9.png)

13. W oknie **Kończenie nowych połączeń telefonicznych lub wirtualnych połączeń sieci prywatnej i klientów RADIUS** wybierz pozycję **Zakończ**.

    ![Ukończone okno konfiguracji](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Sprawdź konfigurację usługi RADIUS

W tej sekcji opisano konfigurację utworzoną za pomocą kreatora.

1. Na serwerze zasad sieciowych w konsoli NPS (lokalny) rozwiń węzeł **Klienci usługi RADIUS**, a następnie wybierz pozycję **Klienci RADIUS**.

2. W okienku szczegółów kliknij prawym przyciskiem myszy utworzony klient RADIUS, a następnie wybierz polecenie **Właściwości**. Właściwości klienta RADIUS (serwera sieci VPN) powinny być podobne do tych pokazanych tutaj:

    ![Weryfikowanie właściwości i konfiguracji sieci VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Wybierz pozycję **Anuluj**.

4. Na serwerze zasad sieciowych w konsoli NPS (lokalny) rozwiń węzeł **Zasady**, a następnie wybierz pozycję **Zasady żądań połączeń**. Zasady połączeń sieci VPN są wyświetlane w sposób pokazany na poniższej ilustracji:

    ![Zasady żądania połączenia przedstawiające zasady połączenia sieci VPN](./media/howto-mfa-nps-extension-vpn/image12.png)

5. W obszarze **Zasady**wybierz pozycję **Zasady sieciowe**. Powinna zostać wyświetlona zasada połączeń wirtualnej sieci prywatnej (VPN), która przypomina zasady pokazane na poniższej ilustracji:

    ![Zasady sieciowe przedstawiające zasady połączenia wirtualnej sieci prywatnej](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurowanie serwera sieci VPN do używania uwierzytelniania RADIUS

W tej sekcji można skonfigurować serwer sieci VPN do używania uwierzytelniania RADIUS. Instrukcje zakładają, że masz działającą konfigurację serwera sieci VPN, ale nie skonfigurowano go do używania uwierzytelniania RADIUS. Po skonfigurowaniu serwera sieci VPN upewnij się, że konfiguracja działa zgodnie z oczekiwaniami.

> [!NOTE]
> Jeśli masz już działającą konfigurację serwera sieci VPN, która używa uwierzytelniania RADIUS, możesz pominąć tę sekcję.
>

### <a name="configure-authentication-provider"></a>Konfigurowanie dostawcy uwierzytelniania

1. Na serwerze sieci VPN otwórz Menedżera serwera.

2. W Menedżerze serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Routing i Dostęp zdalny**.

3. W oknie **Routing i dostęp zdalny** kliknij prawym przyciskiem myszy ** \<nazwę serwera> (lokalny),** a następnie wybierz polecenie **Właściwości**.

4. W oknie ** \<> nazwa serwera (lokalne)** wybierz kartę **Zabezpieczenia.**

5. Na karcie **Zabezpieczenia** w obszarze **Dostawca uwierzytelniania**wybierz pozycję **Uwierzytelnianie PROMIENIOWE**, a następnie wybierz pozycję **Konfiguruj**.

    ![Konfigurowanie dostawcy uwierzytelniania RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)

6. W oknie **Uwierzytelnianie promieniowe** wybierz pozycję **Dodaj**.

7. W oknie **Dodawanie serwera RADIUS** wykonaj następujące czynności:

    a. W polu **Nazwa serwera** wprowadź nazwę lub adres IP serwera RADIUS skonfigurowanego w poprzedniej sekcji.

    b. W przypadku **klucza udostępnionego**wybierz pozycję **Zmień**, a następnie wprowadź udostępnione tajne hasło utworzone i zarejestrowane wcześniej.

    d. W polu **Limit czasu (sekundy)** wprowadź wartość **30**.  
    Wartość limitu czasu jest konieczne, aby dać wystarczająco dużo czasu, aby zakończyć drugi czynnik uwierzytelniania.

    ![Dodawanie okna serwera RADIUS z konfigurowaniem limit czasu](./media/howto-mfa-nps-extension-vpn/image16.png)

8. Kliknij przycisk **OK**.

### <a name="test-vpn-connectivity"></a>Testowanie łączności VPN

W tej sekcji można potwierdzić, że klient sieci VPN jest uwierzytelniany i autoryzowany przez serwer RADIUS podczas próby nawiązania połączenia z portem wirtualnym sieci VPN. Instrukcje zakładają, że używasz systemu Windows 10 jako klienta sieci VPN.

> [!NOTE]
> Jeśli klient sieci VPN został już skonfigurowany do łączenia się z serwerem sieci VPN i zapisałeś ustawienia, można pominąć kroki związane z konfigurowaniem i zapisywaniem obiektu połączenia sieci VPN.
>

1. Na komputerze klienckim sieci VPN wybierz przycisk **Start,** a następnie wybierz przycisk **Ustawienia.**

2. W oknie **Ustawienia systemu Windows** wybierz pozycję Sieć & **Internet**.

3. wybierz pozycję **VPN**.

4. Wybierz **pozycję Dodaj połączenie sieci VPN**.

5. W oknie **Dodawanie połączenia sieci VPN** w polu Dostawca sieci **VPN** wybierz pozycję **Windows (wbudowany)**, wypełnij pozostałe pola, odpowiednio, a następnie wybierz pozycję **Zapisz**.

    ![Okno "Dodawanie połączenia sieci VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Przejdź do **Panelu sterowania**, a następnie wybierz pozycję Centrum sieci **i udostępniania**.

7. Wybierz **pozycję Zmień ustawienia karty**.

    ![Centrum sieci i udostępniania — zmienianie ustawień karty](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Kliknij prawym przyciskiem myszy połączenie sieci VPN, a następnie wybierz polecenie **Właściwości**.

9. W oknie właściwości sieci VPN wybierz kartę **Zabezpieczenia.**

10. Na karcie **Zabezpieczenia** upewnij się, że wybrano tylko **protokół Microsoft CHAP w wersji 2 (MS-CHAP v2),** a następnie wybierz przycisk **OK**.

    ![Opcja "Zezwalaj na te protokoły"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Kliknij prawym przyciskiem myszy połączenie sieci VPN, a następnie wybierz polecenie **Połącz**.

12. W oknie **Ustawienia** wybierz pozycję **Połącz**.  
    Pomyślne połączenie pojawia się w dzienniku zabezpieczeń na serwerze RADIUS jako identyfikator zdarzenia 6272, jak pokazano poniżej:

    ![Okno Właściwości zdarzenia z pomyślnym połączeniem](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Rozwiązywanie problemów z radem RADIUs

Załóżmy, że konfiguracja sieci VPN działała przed skonfigurowaniem serwera sieci VPN do używania scentralizowanego serwera RADIUS do uwierzytelniania i autoryzacji. Jeśli konfiguracja działała, jest prawdopodobne, że problem jest spowodowany błędną konfiguracją serwera RADIUS lub użyciem nieprawidłowej nazwy użytkownika lub hasła. Na przykład jeśli używasz alternatywnego sufiksu nazwy UŻYTKOWNIKA w nazwie użytkownika, próba logowania może zakończyć się niepowodzeniem. Użyj tej samej nazwy konta, aby uzyskać najlepsze wyniki.

Aby rozwiązać te problemy, idealnym miejscem do rozpoczęcia jest sprawdzenie dzienników zdarzeń zabezpieczeń na serwerze RADIUS. Aby zaoszczędzić czas na wyszukiwaniu zdarzeń, można użyć widoku niestandardowego zasad sieciowych opartych na rolach i serwera dostępu w Podglądzie zdarzeń, jak pokazano poniżej. "Event ID 6273" wskazuje zdarzenia, w których serwer NPS odmówił dostępu do użytkownika.

![Podgląd zdarzeń z zdarzeniami NPAS](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Konfigurowanie uwierzytelniania wieloskładnikowego

Aby uzyskać pomoc dotyczącą konfigurowania użytkowników do uwierzytelniania wieloskładnikowego, zobacz artykuły [Planowanie wdrożenia uwierzytelniania wieloskładnikowego platformy Azure w chmurze](howto-mfa-getstarted.md#create-conditional-access-policy) i [konfigurowanie konta do weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalowanie i konfigurowanie rozszerzenia NPS

Ta sekcja zawiera instrukcje konfigurowania sieci VPN do używania usługi MFA do uwierzytelniania klienta za pomocą serwera sieci VPN.

Po zainstalowaniu i skonfigurowaniu rozszerzenia NPS do korzystania z usługi MFA wymagane jest uwierzytelnianie klienta oparte na radius, które jest przetwarzane przez ten serwer. Jeśli wszyscy użytkownicy sieci VPN nie są zarejestrowani w usłudze Azure Multi-Factor Authentication, możesz wykonać jedną z następujących czynności:

* Skonfiguruj inny serwer RADIUS, aby uwierzytelnić użytkowników, którzy nie są skonfigurowani do używania usługi MFA.

* Utwórz wpis rejestru, który umożliwia użytkownikom wyzywanym zapewnienie drugiego czynnika uwierzytelniania, jeśli są zarejestrowani w usłudze Azure Multi-Factor Authentication.

Utwórz nową wartość ciągu o nazwie _REQUIRE_USER_MATCH w HKLM\SOFTWARE\Microsoft\AzureMfa_i ustaw wartość *True* lub *False*.

![Ustawienie "Wymagaj dopasowania użytkownika"](./media/howto-mfa-nps-extension-vpn/image34.png)

Jeśli wartość jest *ustawiona* na True lub jest pusta, wszystkie żądania uwierzytelniania podlegają wyzwaniu usługi MFA. Jeśli wartość jest ustawiona na *False,* wyzwania usługi MFA są wydawane tylko użytkownikom, którzy są zarejestrowani w usłudze Azure Uwierzytelnianie wieloskładnikowe. Użyj *false* ustawienie tylko w testowaniu lub w środowiskach produkcyjnych w okresie dołączania.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Uzyskiwanie identyfikatora GUID usługi Azure Active Directory

W ramach konfiguracji rozszerzenia serwera NPS należy podać poświadczenia administratora i identyfikator dzierżawy usługi Azure AD. Uzyskaj identyfikator, wykonując następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny dzierżawy platformy Azure.

2. W menu portalu Azure wybierz pozycję **Azure Active Directory**lub wyszukaj i wybierz **usługę Azure Active Directory** z dowolnej strony.

3. Wybierz **pozycję Właściwości**.

4. Aby skopiować identyfikator usługi Azure AD, wybierz przycisk **Kopiuj.**

    ![Identyfikator usługi Azure AD Directory w witrynie Azure portal](./media/howto-mfa-nps-extension-vpn/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia NPS

Rozszerzenie NPS musi być zainstalowane na serwerze z zainstalowaną rolą Zasady sieciowe i Usługi dostępu i który działa jako serwer RADIUS w projekcie. *Nie* należy instalować rozszerzenia NPS na serwerze sieci VPN.

1. Pobierz rozszerzenie NPS z [Centrum pobierania Microsoft](https://aka.ms/npsmfa).

2. Skopiuj plik wykonywalny instalatora (*NpsExtnForAzureMfaInstaller.exe*) na serwer NPS.

3. Na serwerze NPS kliknij dwukrotnie pozycję **NpsExtnForAzureMfaInstaller.exe,** a jeśli zostanie wyświetlony monit, wybierz pozycję **Uruchom**.

4. W oknie **Ustawienia rozszerzenia serwera NPS dla usługi Azure MFA** przejrzyj postanowienia licencyjne dotyczące oprogramowania, zaznacz pole wyboru **Zgadzam się z warunkami licencji,** a następnie wybierz pozycję **Zainstaluj**.

    ![Okno "Rozszerzenie nps dla instalatora usługi Azure MFA"](./media/howto-mfa-nps-extension-vpn/image36.png)

5. W oknie **Rozszerzenie NPS dla usługi Azure MFA** **Close**  

    ![Okno potwierdzenia "Konfiguracja zakończyła się pomyślnie"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurowanie certyfikatów do użycia z rozszerzeniem NPS przy użyciu skryptu programu PowerShell

Aby zapewnić bezpieczną komunikację i pewność, skonfiguruj certyfikaty do użycia przez rozszerzenie NPS. Składniki serwera NPS zawierają skrypt programu Windows PowerShell, który konfiguruje certyfikat z podpisem własnym do użytku z serwerem NPS.

Skrypt wykonuje następujące akcje:

* Tworzy certyfikat z podpisem własnym.
* Kojarzy klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD.
* Przechowuje certyfikat w lokalnym magazynie maszyn.
* Udziela użytkownikowi sieci dostępu do klucza prywatnego certyfikatu.
* Uruchamia ponownie usługę NPS.

Jeśli chcesz używać własnych certyfikatów, musisz skojarzyć klucz publiczny certyfikatu z podmiotem usługi w usłudze Azure AD itd.

Aby użyć skryptu, podaj rozszerzenie poświadczeniami administracyjnymi usługi Azure Active Directory i identyfikatorem dzierżawy usługi Azure Active Directory skopiowanym wcześniej. Uruchom skrypt na każdym serwerze NPS, na którym instalujesz rozszerzenie NPS.

1. Uruchom program Windows PowerShell jako administrator.

2. W wierszu polecenia programu PowerShell wprowadź **dysk CD "c:\Program Files\Microsoft\AzureMfa\Config",** a następnie wybierz pozycję Enter.

3. W następnym wierszu polecenia wprowadź **.\AzureMfaNpsExtnConfigSetup.ps1**, a następnie wybierz pozycję Enter. Skrypt sprawdza, czy moduł programu Azure AD PowerShell jest zainstalowany. Jeśli nie jest zainstalowany, skrypt instaluje moduł dla Ciebie.

    ![Uruchamianie skryptu konfiguracyjnego AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Po sprawdzeniu instalacji modułu programu PowerShell zostanie wyświetlone okno logowania modułu programu Azure Active Directory PowerShell.

4. Wprowadź poświadczenia i hasło administratora usługi Azure AD, a następnie wybierz pozycję **Zaloguj się**.

    ![Uwierzytelnij się w programie Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. W wierszu polecenia wklej identyfikator dzierżawy skopiowany wcześniej, a następnie wybierz pozycję Enter.

    ![Wprowadzanie identyfikatora usługi Azure AD Directory skopiowanego przed](./media/howto-mfa-nps-extension-vpn/image40.png)

    Skrypt tworzy certyfikat z podpisem własnym i wykonuje inne zmiany konfiguracji. Dane wyjściowe są podobne do tego na poniższym obrazie:

    ![Okno programu PowerShell z certyfikatem z podpisem własnym](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Uruchom ponownie serwer.

### <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji

Aby zweryfikować konfigurację, należy ustanowić nowe połączenie sieci VPN z serwerem sieci VPN. Po pomyślnym wprowadzeniu poświadczeń do uwierzytelniania podstawowego połączenie sieci VPN czeka na pomyślne uwierzytelnienie pomocnicze przed nawiązaniem połączenia, jak pokazano poniżej.

![Okno Sieci VPN ustawień systemu Windows](./media/howto-mfa-nps-extension-vpn/image42.png)

Jeśli pomyślnie uwierzytelnić za pomocą metody weryfikacji pomocniczej, który został wcześniej skonfigurowany w usłudze Azure MFA, są połączone z zasobem. Jednak jeśli uwierzytelnianie pomocnicze nie powiedzie się, odmówiono dostępu do zasobu.

W poniższym przykładzie aplikacja Microsoft Authenticator na windows phone zapewnia uwierzytelnianie pomocnicze:

![Przykładowy monit usługi MFA w systemie Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Po pomyślnym uwierzytelnienia przy użyciu metody pomocniczej, otrzymasz dostęp do portu wirtualnego na serwerze sieci VPN. Ponieważ wymagane było użycie metody uwierzytelniania pomocniczego przy użyciu aplikacji mobilnej na zaufanym urządzeniu, proces logowania jest bezpieczniejszy niż w przypadku używania tylko kombinacji nazwy użytkownika i hasła.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Wyświetlanie dzienników Podglądu zdarzeń w celu uzyskania pomyślnych zdarzeń logowania

Aby wyświetlić pomyślne zdarzenia logowania w dziennikach Podglądu zdarzeń systemu Windows, wysyłaj na serwer serwera NPS dziennik zabezpieczeń systemu Windows, wprowadzając następujące polecenie programu PowerShell:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Przeglądarka zdarzeń zabezpieczeń programu PowerShell](./media/howto-mfa-nps-extension-vpn/image44.png)

Można również wyświetlić dziennik zabezpieczeń lub widok niestandardowy zasad sieciowych i usług dostępu, jak pokazano poniżej:

![Przykładowy dziennik serwera zasad sieciowych](./media/howto-mfa-nps-extension-vpn/image45.png)

Na serwerze, na którym zainstalowano rozszerzenie NPS dla uwierzytelniania wieloskładnikowego platformy Azure, można znaleźć dzienniki aplikacji Podglądu zdarzeń, które są specyficzne dla rozszerzenia, w *dziennikach aplikacji i usług\Microsoft\AzureMfa*.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Przykładowe okienko Dzienniki AuthZ podglądu zdarzeń](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Jeśli konfiguracja nie działa zgodnie z oczekiwaniami, rozpocznij rozwiązywanie problemów, sprawdzając, czy użytkownik jest skonfigurowany do używania usługi MFA. Niech użytkownik połączy się z [witryną Azure portal](https://portal.azure.com). Jeśli użytkownik jest monitowany o uwierzytelnianie pomocnicze i może pomyślnie uwierzytelnić, można wyeliminować niepoprawną konfigurację usługi MFA jako problem.

Jeśli usługa MFA działa dla użytkownika, przejrzyj odpowiednie dzienniki Podglądu zdarzeń. Dzienniki obejmują zdarzenia zabezpieczeń, bramy operacyjnej i azure wieloskładnikowe dzienniki uwierzytelniania, które zostały omówione w poprzedniej sekcji.

W tym miejscu pokazano przykład dziennika zabezpieczeń, w który jest wyświetlany nieupowiony błąd logowania (identyfikator zdarzenia 6273):

![Dziennik zabezpieczeń pokazujący zdarzenie logowania, które nie powiodło się](./media/howto-mfa-nps-extension-vpn/image47.png)

Pokrewne zdarzenie z dziennika uwierzytelniania wieloskładnikowego platformy Azure jest pokazane w tym miejscu:

![Dzienniki uwierzytelniania wieloskładnikowego platformy Azure](./media/howto-mfa-nps-extension-vpn/image48.png)

Aby wykonać zaawansowane rozwiązywanie problemów, zapoznaj się z plikami dziennika formatu bazy danych SERWERA, w których jest zainstalowana usługa NPS. Pliki dziennika są tworzone w folderze _%SystemRoot%\System32\Logs_ jako pliki tekstowe rozdzielane przecinkami. Aby uzyskać opis plików dziennika, zobacz [Interpretowanie plików dziennika formatu bazy danych NPS](https://technet.microsoft.com/library/cc771748.aspx).

Wpisy w tych plikach dziennika są trudne do zinterpretowania, chyba że zostaną wyeksportowane do arkusza kalkulacyjnego lub bazy danych. Wiele narzędzi do analizowania usługi uwierzytelniania internetowego (IAS) można znaleźć w trybie online, aby ułatwić interpretację plików dziennika. Dane wyjściowe jednej z takich [aplikacji shareware](https://www.deepsoftware.com/iasviewer) do pobrania są pokazane tutaj:

![Przykładowy analizator IAS aplikacji Shareware](./media/howto-mfa-nps-extension-vpn/image49.png)

Aby wykonać dodatkowe rozwiązywanie problemów, można użyć analizatora protokołów, takiego jak Wireshark lub [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Na poniższej ilustracji z programu Wireshark przedstawiono komunikaty RADIUS między serwerem sieci VPN a serwerem NPS.

![Analizator komunikatów firmy Microsoft przedstawiający filtrowany ruch](./media/howto-mfa-nps-extension-vpn/image50.png)

Aby uzyskać więcej informacji, zobacz [Integrowanie istniejącej infrastruktury serwera NPS z uwierzytelnianiem wieloskładnikowym platformy Azure.](howto-mfa-nps-extension.md)

## <a name="next-steps"></a>Następne kroki

[Uzyskaj uwierzytelnianie wieloskładnikowe platformy Azure](concept-mfa-licensing.md)

[Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS](howto-mfaserver-nps-rdg.md)

[Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
