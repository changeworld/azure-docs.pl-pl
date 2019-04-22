---
title: 'Program Azure AD Connect: Bezproblemowe logowanie jednokrotne — szybki start | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób rozpoczęcia pracy za pomocą usługi Azure Active Directory bezproblemowe logowanie jednokrotne
services: active-directory
keywords: Co to jest program Azure AD Connect, instalowanie usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06566ab81b6af847a7eb174731105b7f43a7197f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680908"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Usługa Azure Active Directory bezproblemowego logowania jednokrotnego: Szybki start

## <a name="deploy-seamless-single-sign-on"></a>Wdrażanie bezproblemowego logowania jednokrotnego

Usługa Azure Active Directory (Azure AD) bezproblemowe logowanie jednokrotne (bezproblemowe logowanie Jednokrotne) automatycznie zaloguje użytkowników znajdujących się na komputerach stacjonarnych firmy, które są podłączone do sieci firmowej. Bezproblemowe logowanie Jednokrotne zapewnia użytkownikom łatwy dostęp do aplikacji w chmurze, bez żadnych dodatkowych lokalnych składników.

Aby wdrożyć bezproblemowe logowanie Jednokrotne, wykonaj następujące kroki.

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Sprawdzanie wymagań wstępnych

Upewnij się, że zostały spełnione następujące wymagania wstępne:

* **Konfigurowanie serwera usługi Azure AD Connect**: Jeśli używasz [uwierzytelniania przekazywanego](how-to-connect-pta.md) jako metody logowania, jest wymagane żadne dodatkowe sprawdzenie wymagań wstępnych. Jeśli używasz [synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) jako metody logowania, a w przypadku zapory między program Azure AD Connect a usługą Azure AD, upewnij się, że:
   - Użyj wersji 1.1.644.0 lub nowszego, programu Azure AD Connect. 
   - Jeśli zapora lub serwer proxy zezwala DNS umieszczania na białej liście, lista dozwolonych połączeń z  **\*. msappproxy.net** adresy URL za pośrednictwem portu 443. Jeśli nie, Zezwalaj na dostęp do [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), która jest aktualizowana co tydzień. To wymaganie wstępne dotyczy tylko wtedy, gdy włączysz tę funkcję. Nie jest wymagana dla logowania rzeczywistych użytkowników.

    >[!NOTE]
    >Usługa Azure AD Connect w wersjach 1.1.557.0, 1.1.558.0, 1.1.561.0 i 1.1.614.0 ma problem związany z synchronizacją skrótów haseł. Jeśli użytkownik _nie_ zamierza się używać synchronizacji skrótów haseł w połączeniu z uwierzytelniania przekazywanego, przeczytaj [informacje o wersji usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) Aby dowiedzieć się więcej.

* **Użyj obsługiwanej topologii program Azure AD Connect**: Upewnij się, że używasz jednego z obsługiwanych topologii Azure AD Connect, opisane [tutaj](plan-connect-topologies.md).

    >[!NOTE]
    >Bezproblemowe logowanie Jednokrotne obsługuje wiele lasów usługi AD, czy istnieją AD relacji zaufania między nimi lub nie.

* **Konfigurowanie poświadczeń administratora domeny**: Musisz mieć poświadczenia administratora domeny, dla każdej usługi Active Directory lasu, który:
    * Można zsynchronizować z usługą Azure AD za pomocą usługi Azure AD Connect.
    * Zawiera użytkowników, dla których chcesz włączyć bezproblemowego logowania jednokrotnego.
    
* **Włączenie nowoczesnego uwierzytelniania**: Należy włączyć [nowoczesnego uwierzytelniania](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) w dzierżawie usługi dla tej funkcji do pracy.

* **Użyj najnowszej wersji klientów usługi Office 365**: Aby dyskretnej logowania jednokrotnego przy użyciu klientów usługi Office 365 (Outlook, Word, Excel i innych), usługi użytkownicy musieli używać 16.0.8730.xxxx wersji lub nowszej.

## <a name="step-2-enable-the-feature"></a>Krok 2: Włącz tę funkcję

Włącz bezproblemowe logowanie Jednokrotne za pomocą [programu Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Możesz również [Włącz bezproblemowe logowanie Jednokrotne przy użyciu programu PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) Jeśli program Azure AD Connect nie spełnia Twoich wymagań. Użyj tej opcji, jeśli masz więcej niż jedną domenę dla lasu usługi Active Directory i chcesz więcej przeznaczone są informacje o domenie, aby umożliwić bezproblemowe logowanie Jednokrotne dla.

Jeśli wykonujesz nowej instalacji programu Azure AD Connect, wybierz opcję [niestandardową ścieżkę](how-to-connect-install-custom.md). W **logowania użytkownika** wybierz opcję **Włącz logowanie jednokrotne** opcji.

>[!NOTE]
> Opcja będzie dostępna do wyboru tylko wtedy, gdy metoda rejestracji jednokrotnej **synchronizacji skrótów haseł** lub **uwierzytelniania przekazywanego**.

![Program Azure AD Connect: Logowanie użytkowników](./media/how-to-connect-sso-quick-start/sso8.png)

Jeśli masz już instalację programu Azure AD Connect, wybierz opcję **zmiana użytkownika logowania** strony w programie Azure AD Connect, a następnie wybierz pozycję **dalej**. Jeśli używasz wersji Azure AD Connect 1.1.880.0 lub wyższy, **Włącz logowanie jednokrotne** opcja zostanie zaznaczona domyślnie. Jeśli używasz starszej wersji programu Azure AD Connect, wybierz opcję **Włącz logowanie jednokrotne** opcji.

![Program Azure AD Connect: Zmiany logowania użytkownika](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Kontynuuj pracę z kreatorem, aż dojdziesz do **Włącz logowanie jednokrotne** strony. Zapewniają, że poświadczenia administratora domeny dla każdej usługi Active Directory lasu, który:

* Można zsynchronizować z usługą Azure AD za pomocą usługi Azure AD Connect.
* Zawiera użytkowników, dla których chcesz włączyć bezproblemowego logowania jednokrotnego.

Po zakończeniu działania kreatora bezproblemowe logowanie Jednokrotne jest włączona w dzierżawie.

>[!NOTE]
> Poświadczenia administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Są one używane tylko po to, aby włączyć tę funkcję.

Wykonaj te instrukcje, aby sprawdzić, prawidłowo włączona bezproblemowe logowanie Jednokrotne:

1. Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługi Azure Active Directory** w okienku po lewej stronie.
3. Wybierz **programu Azure AD Connect**.
4. Upewnij się, że **bezproblemowego logowania jednokrotnego** funkcji pojawia się jako **włączone**.

![Azure Portal: Okienko usługi Azure AD Connect](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Bezproblemowe logowanie Jednokrotne tworzy konto komputera o nazwie `AZUREADSSOACC` w swojej lokalnej usłudze Active Directory (AD) w każdym lesie usługi AD. `AZUREADSSOACC` Konto komputera, które wymagają silnie ochrony ze względów bezpieczeństwa. Tylko Administratorzy domeny powinno być możliwe do zarządzania kontem komputera. Upewnij się, wyłączenia delegowanie protokołu Kerberos na konto komputera oraz że żadne inne konto w usłudze Active Directory ma uprawnienia delegowania uprawnień `AZUREADSSOACC` konta komputera. Store konta komputera w organizacji jednostki Organizacyjnej, gdzie są one bezpieczne przypadkowym a tylko Administratorzy domeny mają dostęp.

>[!NOTE]
> Jeśli używane są architektur Pass--Hash i ograniczania ryzyka kradzieży poświadczeń w Twoim środowisku w środowisku lokalnym, wprowadzić odpowiednie zmiany, aby upewnić się, że `AZUREADSSOACC` konto komputera nie znajdą się w kontenerze kwarantanny. 

## <a name="step-3-roll-out-the-feature"></a>Krok 3: Wdrażanie funkcji

Można stopniowo wdrożeniem bezproblemowe logowanie Jednokrotne dla użytkowników wykonując instrukcje podane poniżej. Rozpocznij, dodając następujący adres URL usługi Azure AD do wszystkie lub wybrane ustawienia strefy Intranet użytkowników za pomocą zasad grupy w usłudze Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Ponadto należy włączyć zasadę strefy intranetowej, nosi nazwę **zezwala na aktualizacje na pasku stanu za pomocą skryptu** za pomocą zasad grupy. 

>[!NOTE]
> Poniższe instrukcje działają tylko w przypadku programu Internet Explorer i Google Chrome w Windows (jeśli współużytkuje zestaw adresów URL zaufanych witryn w programie Internet Explorer). Zapoznaj się z sekcją dalej, aby uzyskać instrukcje dotyczące sposobu konfigurowania Mozilla Firefox i Google Chrome w systemie macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Dlaczego należy zmodyfikować ustawienia strefy Intranet użytkowników?

Domyślnie przeglądarka automatycznie oblicza poprawnej strefy Internet lub Intranet, z określonym adresem URL. Na przykład `http://contoso/` mapuje do strefy Intranet, podczas gdy `http://intranet.contoso.com/` mapuje do strefy Internet (ponieważ jest to adres URL zawiera kropkę). Przeglądarki będzie wysyłał bilety protokołu Kerberos do punktu końcowego w chmurze, takich jak usługa Azure AD adres URL, chyba że dodasz do strefy Intranet w przeglądarce adres URL.

Istnieją dwa sposoby, aby zmodyfikować ustawienia strefy Intranet użytkowników:

| Opcja | Kwestia administratora | Środowisko użytkownika |
| --- | --- | --- |
| Zasady grupy | Administrator blokady w dół edytowanie ustawień strefy Intranet | Użytkownicy nie mogą modyfikować własnych ustawień |
| Preferencji zasad grupy |  Admin umożliwia edytowanie ustawień sieci Intranet strefy | Użytkownicy mogą modyfikować własnych ustawień |

### <a name="group-policy-option---detailed-steps"></a>Opcja "Zasad grupy" — szczegółowy opis kroków

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytowanie zasad grupy, która jest stosowana do niektórych lub wszystkich użytkowników. W tym przykładzie użyto **domyślne zasady domeny**.
3. Przejdź do **Konfiguracja użytkownika** > **zasad** > **Szablony administracyjne** > **Windows Składniki** > **programu Internet Explorer** > **internetowy Panel sterowania** > **strony zabezpieczeń**. Następnie wybierz pozycję **strefy Lista przypisywanie witryn do**.
    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso6.png)
4. Włącz zasady, a następnie wprowadź następujące wartości w oknie dialogowym:
   - **Nazwa wartości**: Usługa Azure AD adres URL gdzie bilety protokołu Kerberos są przesyłane dalej.
   - **Wartość** (dane): **1** wskazuje strefy intranetowej.

     Wynik wygląda następująco:

     Nazwa wartości: `https://autologon.microsoftazuread-sso.com`
  
     Wartość (dane): 1

   >[!NOTE]
   > Jeśli chcesz uniemożliwić użytkownikom niektóre za pomocą bezproblemowego logowania jednokrotnego (na przykład, jeśli ci użytkownicy Zaloguj się na udostępnionym kioski), ustaw powyższe wartości **4**. Ta akcja dodaje usługi Azure AD adres URL do strefy z ograniczeniami i kończy się niepowodzeniem bezproblemowe logowanie Jednokrotne przez cały czas.
   >

5. Wybierz **OK**, a następnie wybierz pozycję **OK** ponownie.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso7.png)

6. Przejdź do **Konfiguracja użytkownika** > **Szablony administracyjne** **zasad** > ** > **składników Windows**  >  **Programu Internet Explorer** > **internetowy Panel sterowania** > **strony zabezpieczeń**  >   **Strefy intranetowej**. Następnie wybierz pozycję **zezwala na aktualizacje na pasku stanu za pomocą skryptu**.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso11.png)

7. Włącz ustawienie zasad, a następnie wybierz **OK**.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opcja "Grupa preferencji zasad" — szczegółowy opis kroków

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytowanie zasad grupy, która jest stosowana do niektórych lub wszystkich użytkowników. W tym przykładzie użyto **domyślne zasady domeny**.
3. Przejdź do **Konfiguracja użytkownika** > **preferencje** > **ustawienia Windows** > **rejestru**  >  **New** > **element rejestru**.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso15.png)

4. Wprowadź następujące wartości w odpowiednich polach, a następnie kliknij przycisk **OK**.
   - **Ścieżka klucza**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Nazwa wartości**: ***https***.
   - **Typ wartości**: ***REG_DWORD***.
   - **Dane wartości**: ***00000001***.
 
     ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Zagadnienia dotyczące przeglądarki

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (wszystkie platformy)

Mozilla Firefox automatycznie nie korzysta z uwierzytelniania Kerberos. Każdy użytkownik należy ręcznie dodać usługi Azure AD adres URL do ustawień przeglądarki Firefox wykonując następujące kroki:
1. Uruchom przeglądarkę Firefox, a następnie wprowadź `about:config` na pasku adresu. Odrzuć wszelkie powiadomienia, które są wyświetlane.
2. Wyszukaj **network.negotiate-auth.trusted — identyfikatory URI** preferencji. Ta opcja wyświetla listę zaufanych witryn w przeglądarce Firefox uwierzytelniania Kerberos.
3. Kliknij prawym przyciskiem myszy i wybierz **Modyfikuj**.
4. Wprowadź `https://autologon.microsoftazuread-sso.com` w polu.
5. Wybierz **OK** i ponownym otwarciu przeglądarki.

#### <a name="safari-macos"></a>Safari (macOS)

Upewnij się, że maszyny z systemem macOS jest przyłączony do usługi AD. Instrukcje dotyczące przyłączania do usługi AD urządzenia z systemem macOS wykracza poza zakres tego artykułu.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (wszystkie platformy)

Jeśli zastępowano [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) lub [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) ustawienia zasad w danym środowisku, upewnij się, sytuacja: dodajesz usługę Azure AD adres URL (`https://autologon.microsoftazuread-sso.com`) do nich również.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (z systemem macOS i innych platform innych niż Windows)

Google Chrome w systemie Mac OS i innych platform innych niż Windows, można znaleźć [chrom projektu Policy List](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) instrukcje dotyczące sposobu do listy dozwolonych adresów URL usługi Azure AD dla zintegrowanego uwierzytelniania.

Korzystanie z innych rozszerzeń zasad grupy usługi Active Directory do wdrożenia usługi Azure AD adres URL w usłudze Firefox i Google Chrome dla użytkowników komputerów Mac znajduje się poza zakres tego artykułu.

#### <a name="known-browser-limitations"></a>Przeglądarka znane ograniczenia

Bezproblemowe logowanie Jednokrotne nie działa w trybie przeglądania prywatnym w przeglądarkach Firefox i Microsoft Edge. Ponadto nie działa w programie Internet Explorer Jeśli przeglądarka działa w trybie chronionym rozszerzone.

## <a name="step-4-test-the-feature"></a>Krok 4: Testowanie funkcji

Aby przetestować funkcję dla określonego użytkownika, upewnij się, że zostały spełnione wszystkie następujące warunki:
  - Użytkownik loguje się na urządzenie firmowe.
  - Urządzenie jest przyłączone do domeny usługi Active Directory. Urządzenie _nie_ muszą być [usługi Azure AD dołączono](../active-directory-azureadjoin-overview.md).
  - Urządzenie ma bezpośrednie połączenie z usługą kontrolera domeny (DC) w sieci firmowej sieci przewodowej lub bezprzewodowej lub za pośrednictwem połączenia dostępu zdalnego, takich jak połączenia sieci VPN.
  - Masz [udostępniona funkcja](##step-3-roll-out-the-feature) do tego użytkownika za pomocą zasad grupy.

Aby przetestować scenariusz, w którym użytkownik musi wprowadzić tylko nazwę użytkownika, ale nie hasło:
   - Zaloguj się do `https://myapps.microsoft.com/` w nowej sesji przeglądarki prywatnych.

Aby przetestować scenariusz, gdzie użytkownik nie musi wprowadzać nazwy użytkownika lub hasło, należy użyć jednej z następujących czynności: 
   - Zaloguj się do `https://myapps.microsoft.com/contoso.onmicrosoft.com` w nowej sesji przeglądarki prywatnych. Zastąp *contoso* nazwą Twojej dzierżawy.
   - Zaloguj się do `https://myapps.microsoft.com/contoso.com` w nowej sesji przeglądarki prywatnych. Zastąp *contoso.com* zweryfikowanej domeny (nie domeny federacyjnej) w dzierżawie.

## <a name="step-5-roll-over-keys"></a>Krok 5. Przerzucić klucze

W kroku 2 Azure AD Connect tworzy konta komputerów (reprezentowanie usługi Azure AD) w lasach usługi Active Directory na których włączono bezproblemowe logowanie Jednokrotne. Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory bezproblemowego logowania jednokrotnego: Rozbudowana technicznie](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>Klucz odszyfrowywania protokołu Kerberos na koncie komputera, jeśli wyciek, może służyć do generowania bilety protokołu Kerberos dla dowolnego użytkownika w lesie usługi AD. Uczestników złośliwych działań można następnie personifikację logowania usługi Azure AD dla użytkowników, których bezpieczeństwo zostało naruszone. Zdecydowanie zaleca się, że okresowo wdrożysz za pośrednictwem tych kluczy odszyfrowywania protokołu Kerberos — co najmniej raz na 30 dni.

Aby uzyskać instrukcje na temat sposobu przerzucić klucze, zobacz [usługi Azure Active Directory bezproblemowego logowania jednokrotnego: Często zadawane pytania dotyczące](how-to-connect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account). Pracujemy nad możliwość wprowadzenia automatyczne wdrożenie za pośrednictwem kluczy.

>[!IMPORTANT]
>Nie trzeba wykonać ten krok _natychmiast_ po włączeniu tej funkcji. Przechodzą kluczy odszyfrowywania protokołu Kerberos co najmniej raz na 30 dni.

## <a name="next-steps"></a>Kolejne kroki

- [Rozbudowana technicznie](how-to-connect-sso-how-it-works.md): Dowiedz się, jak działa funkcja bezproblemowego logowania jednokrotnego.
- [Często zadawane pytania dotyczące](how-to-connect-sso-faq.md): Uzyskaj odpowiedzi na często zadawane pytania dotyczące bezproblemowego logowania jednokrotnego.
- [Rozwiązywanie problemów z](tshoot-connect-sso.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją bezproblemowego logowania jednokrotnego.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Forum usługi Azure Active Directory umożliwia pliku sugestie dotyczące nowych funkcji.
