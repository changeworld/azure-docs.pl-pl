---
title: 'Azure AD Connect: Bezproblemowego logowania jednokrotnego — szybki start | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób rozpoczęcia pracy za pomocą usługi Azure Active Directory bezproblemowe logowanie jednokrotne
services: active-directory
keywords: Co to jest program Azure AD Connect, instalowanie usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f0611662dfb0ad2e15f87bbe5ec5559e7d8da57d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185724"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Usługa Azure Active Directory bezproblemowe logowanie jednokrotne: Szybki start

## <a name="deploy-seamless-single-sign-on"></a>Wdrażanie bezproblemowego logowania jednokrotnego

Usługa Azure Active Directory (Azure AD) bezproblemowe logowanie jednokrotne (bezproblemowe logowanie Jednokrotne) automatycznie zaloguje użytkowników znajdujących się na komputerach stacjonarnych firmy, które są podłączone do sieci firmowej. Bezproblemowe logowanie Jednokrotne zapewnia użytkownikom łatwy dostęp do aplikacji w chmurze, bez żadnych dodatkowych lokalnych składników.

Aby wdrożyć bezproblemowe logowanie Jednokrotne, wykonaj następujące kroki.

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Sprawdzanie wymagań wstępnych

Upewnij się, że zostały spełnione następujące wymagania wstępne:

* **Konfigurowanie serwera usługi Azure AD Connect**: Jeśli używasz [uwierzytelniania przekazywanego](active-directory-aadconnect-pass-through-authentication.md) jako metody logowania, jest wymagane żadne dodatkowe sprawdzenie wymagań wstępnych. Jeśli używasz [synchronizacji skrótów haseł](active-directory-aadconnectsync-implement-password-hash-synchronization.md) jako metody logowania, a w przypadku zapory między program Azure AD Connect a usługą Azure AD, upewnij się, że:
   - Użyj wersji 1.1.644.0 lub nowszego, programu Azure AD Connect. 
   - Jeśli zapora lub serwer proxy zezwala DNS umieszczania na białej liście, lista dozwolonych połączeń z  **\*. msappproxy.net** adresy URL za pośrednictwem portu 443. Jeśli nie, Zezwalaj na dostęp do [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), która jest aktualizowana co tydzień. To wymaganie wstępne dotyczy tylko wtedy, gdy włączysz tę funkcję. Nie jest wymagana dla logowania rzeczywistych użytkowników.

    >[!NOTE]
    >Usługa Azure AD Connect w wersjach 1.1.557.0, 1.1.558.0, 1.1.561.0 i 1.1.614.0 ma problem związany z synchronizacją skrótów haseł. Jeśli użytkownik _nie_ zamierza się używać synchronizacji skrótów haseł w połączeniu z uwierzytelniania przekazywanego, przeczytaj [informacje o wersji usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) Aby dowiedzieć się więcej.

* **Konfigurowanie poświadczeń administratora domeny**: musisz mieć poświadczenia administratora domeny, dla każdej usługi Active Directory lasu, który:
    * Można zsynchronizować z usługą Azure AD za pomocą usługi Azure AD Connect.
    * Zawiera użytkowników, dla których chcesz włączyć bezproblemowego logowania jednokrotnego.

## <a name="step-2-enable-the-feature"></a>Krok 2: Włączanie funkcji

Włącz bezproblemowe logowanie Jednokrotne za pomocą [programu Azure AD Connect](active-directory-aadconnect.md).

Jeśli wykonujesz nowej instalacji programu Azure AD Connect, wybierz opcję [niestandardową ścieżkę](active-directory-aadconnect-get-started-custom.md). W **logowania użytkownika** wybierz opcję **Włącz logowanie jednokrotne** opcji.

![Usługi Azure AD Connect: Logowanie użytkownika](./media/active-directory-aadconnect-sso/sso8.png)

Jeśli masz już instalację programu Azure AD Connect, wybierz opcję **zmiana użytkownika logowania** strony w programie Azure AD Connect, a następnie wybierz pozycję **dalej**.

![Azure AD Connect: Zmień logowanie użytkownika](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

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

![Witryna Azure portal: okienko program Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Krok 3: Wdrażanie tej funkcji

Aby wdrożyć tę funkcję dla użytkowników, należy dodać następujący adres URL usługi Azure AD do ustawienia strefy Intranet użytkowników za pomocą zasad grupy w usłudze Active Directory:

- https://autologon.microsoftazuread-sso.com


Ponadto należy włączyć zasadę strefy intranetowej, nosi nazwę **zezwala na aktualizacje na pasku stanu za pomocą skryptu** za pomocą zasad grupy. 

>[!NOTE]
> Poniższe instrukcje działają tylko w przypadku programu Internet Explorer i Google Chrome w Windows (jeśli współużytkuje zestaw adresów URL zaufanych witryn w programie Internet Explorer). Zapoznaj się z sekcją dalej, aby uzyskać instrukcje dotyczące sposobu konfigurowania Mozilla Firefox i Google Chrome dla komputerów Macintosh.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Dlaczego należy zmodyfikować ustawienia strefy Intranet użytkowników?

Domyślnie przeglądarka automatycznie oblicza poprawnej strefy Internet lub Intranet, z określonym adresem URL. Na przykład "http://contoso/"mapuje do strefy Intranet, podczas gdy"http://intranet.contoso.com/" mapuje do strefy Internet (ponieważ jest to adres URL zawiera kropkę). Przeglądarki będzie wysyłał bilety protokołu Kerberos do punktu końcowego w chmurze, takich jak usługa Azure AD adres URL, chyba że dodasz do strefy Intranet w przeglądarce adres URL.

### <a name="detailed-steps"></a>Szczegółowe procedury

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytowanie zasad grupy, która jest stosowana do niektórych lub wszystkich użytkowników. W tym przykładzie użyto **domyślne zasady domeny**.
3. Przejdź do **Konfiguracja użytkownika** > **Szablony administracyjne** > **składników Windows**  >   **Program Internet Explorer** > **internetowy Panel sterowania** > **strony zabezpieczeń**. Następnie wybierz pozycję **strefy Lista przypisywanie witryn do**.
    ![Logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso6.png)
4. Włącz zasady, a następnie wprowadź następujące wartości w oknie dialogowym:
   - **Nazwa wartości**: gdzie bilety protokołu Kerberos są przekazywane adres URL usług Azure AD.
   - **Wartość** (dane): **1** wskazuje strefy intranetowej.

    Wynik wygląda następująco:

    Wartość:https://autologon.microsoftazuread-sso.com
  
    Dane: 1

   >[!NOTE]
   > Jeśli chcesz uniemożliwić użytkownikom niektóre za pomocą bezproblemowego logowania jednokrotnego (na przykład, jeśli ci użytkownicy Zaloguj się na udostępnionym kioski), ustaw powyższe wartości **4**. Ta akcja dodaje usługi Azure AD adres URL do strefy z ograniczeniami i kończy się niepowodzeniem bezproblemowe logowanie Jednokrotne przez cały czas.
   >

5. Wybierz **OK**, a następnie wybierz pozycję **OK** ponownie.

    ![Logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso7.png)

6. Przejdź do **Konfiguracja użytkownika** > **Szablony administracyjne** > **składników Windows**  >   **Program Internet Explorer** > **internetowy Panel sterowania** > **strony zabezpieczeń** > **strefy intranetowej**. Następnie wybierz pozycję **zezwala na aktualizacje na pasku stanu za pomocą skryptu**.

    ![Logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso11.png)

7. Włącz ustawienie zasad, a następnie wybierz **OK**.

    ![Logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Zagadnienia dotyczące przeglądarki

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (wszystkie platformy)

Mozilla Firefox automatycznie nie korzysta z uwierzytelniania Kerberos. Każdy użytkownik należy ręcznie dodać usługi Azure AD adres URL do ustawień przeglądarki Firefox wykonując następujące kroki:
1. Uruchom przeglądarkę Firefox, a następnie wprowadź `about:config` na pasku adresu. Odrzuć wszelkie powiadomienia, które są wyświetlane.
2. Wyszukaj **network.negotiate-auth.trusted — identyfikatory URI** preferencji. Ta opcja wyświetla listę zaufanych witryn w przeglądarce Firefox uwierzytelniania Kerberos.
3. Kliknij prawym przyciskiem myszy i wybierz **Modyfikuj**.
4. Wprowadź https://autologon.microsoftazuread-sso.com w polu.
5. Wybierz **OK** i ponownym otwarciu przeglądarki.

#### <a name="safari-mac-os"></a>Safari (Mac OS)

Upewnij się, że maszyny z systemem Mac OS jest przyłączony do usługi AD. Aby uzyskać instrukcje dotyczące dołączania AD, zobacz [najlepsze rozwiązania dotyczące integracji OS X z usługą Active Directory](http://www.isaca.org/Groups/Professional-English/identity-management/GroupDocuments/Integrating-OS-X-with-Active-Directory.pdf).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (wszystkie platformy)

Jeśli zastępowano [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) lub [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) ustawienia zasad w danym środowisku, upewnij się, sytuacja: dodajesz usługę Azure AD adres URL (https://autologon.microsoftazuread-sso.com) do nich również.

#### <a name="google-chrome-mac-os-only"></a>Google Chrome (tylko system Mac OS)

Google Chrome w systemie Mac OS i innych platform innych niż Windows, można znaleźć [chrom projektu Policy List](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) instrukcje dotyczące sposobu do listy dozwolonych adresów URL usługi Azure AD dla zintegrowanego uwierzytelniania.

Korzystanie z innych rozszerzeń zasad grupy usługi Active Directory do wdrożenia usługi Azure AD adres URL w usłudze Firefox i Google Chrome dla użytkowników komputerów Mac znajduje się poza zakres tego artykułu.

#### <a name="known-browser-limitations"></a>Przeglądarka znane ograniczenia

Bezproblemowe logowanie Jednokrotne nie działa w trybie przeglądania prywatnym w przeglądarkach Firefox i Microsoft Edge. Ponadto nie działa w programie Internet Explorer Jeśli przeglądarka działa w trybie chronionym rozszerzone.

## <a name="step-4-test-the-feature"></a>Krok 4: Testowanie funkcji

Aby przetestować funkcję dla określonego użytkownika, upewnij się, że zostały spełnione wszystkie następujące warunki:
  - Użytkownik loguje się na urządzenie firmowe.
  - Urządzenie jest przyłączone do domeny usługi Active Directory.
  - Urządzenie ma bezpośrednie połączenie z usługą kontrolera domeny (DC) w sieci firmowej sieci przewodowej lub bezprzewodowej lub za pośrednictwem połączenia dostępu zdalnego, takich jak połączenia sieci VPN.
  - Masz [udostępniona funkcja](##step-3-roll-out-the-feature) do tego użytkownika za pomocą zasad grupy.

Aby przetestować scenariusz, w którym użytkownik musi wprowadzić tylko nazwę użytkownika, ale nie hasło:
   - Zaloguj się do https://myapps.microsoft.com/ w nowej sesji przeglądarki prywatnych.

Aby przetestować scenariusz, gdzie użytkownik nie musi wprowadzać nazwy użytkownika lub hasło, należy użyć jednej z następujących czynności: 
   - Zaloguj się do https://myapps.microsoft.com/contoso.onmicrosoft.com w nowej sesji przeglądarki prywatnych. Zastąp *contoso* nazwą Twojej dzierżawy.
   - Zaloguj się do https://myapps.microsoft.com/contoso.com w nowej sesji przeglądarki prywatnych. Zastąp *contoso.com* zweryfikowanej domeny (nie domeny federacyjnej) w dzierżawie.

## <a name="step-5-roll-over-keys"></a>Krok 5: Przerzucić klucze

W kroku 2 Azure AD Connect tworzy konta komputerów (reprezentowanie usługi Azure AD) w lasach usługi Active Directory na których włączono bezproblemowe logowanie Jednokrotne. Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory bezproblemowe logowanie jednokrotne: techniczne](active-directory-aadconnect-sso-how-it-works.md). Ze względów bezpieczeństwa zaleca się okresowo przerzucaniu protokołu Kerberos klucze odszyfrowujące tych kont komputerów. Aby uzyskać instrukcje na temat sposobu przerzucić klucze, zobacz [usługi Azure Active Directory bezproblemowe logowanie jednokrotne: często zadawane pytania dotyczące](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Nie trzeba wykonać ten krok _natychmiast_ po włączeniu tej funkcji. Przechodzą kluczy odszyfrowywania protokołu Kerberos co najmniej raz na 30 dni.

## <a name="next-steps"></a>Kolejne kroki

- [Rozbudowana technicznie](active-directory-aadconnect-sso-how-it-works.md): zrozumienie sposobu działania funkcji bezproblemowego logowania jednokrotnego.
- [Często zadawane pytania dotyczące](active-directory-aadconnect-sso-faq.md): odpowiedzi na często zadawane pytania dotyczące bezproblemowego logowania jednokrotnego.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-sso.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją bezproblemowego logowania jednokrotnego.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj Forum usługi Azure Active Directory do pliku sugestie dotyczące nowych funkcji.
