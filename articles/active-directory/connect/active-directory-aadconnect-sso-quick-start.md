---
title: 'Azure AD Connect: Bezproblemowe logowanie jednokrotne — szybki start | Dokumentacja firmy Microsoft'
description: W tym artykule opisano, jak rozpocząć pracę z usługi Azure Active Directory bezproblemowe logowanie jednokrotne
services: active-directory
keywords: Co to jest usługa Azure AD Connect, zainstaluj usługę Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: billmath
ms.openlocfilehash: d026009151f85a01f14ce4dd8a510f60ff407da1
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory bezproblemowe logowanie jednokrotne: Szybki start

## <a name="deploy-seamless-single-sign-on"></a>Wdrażanie bezproblemowe logowanie jednokrotne

Azure Active Directory (Azure AD) bezproblemowe logowanie jednokrotne (SSO bezproblemowe) automatycznie użytkownikom zalogowanie się, gdy są one na komputerach stacjonarnych firmowych, które są podłączone do sieci firmowej. Bezproblemowe logowanie Jednokrotne zapewnia użytkownikom łatwy dostęp do aplikacji opartej na chmurze bez konieczności żadnych składników dodatkowych lokalnych.

Aby wdrożyć bezproblemowe logowanie Jednokrotne, wykonaj następujące kroki.

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Sprawdź wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

* **Konfigurowanie serwera usługi Azure AD Connect**: Jeśli używasz [uwierzytelniania przekazywanego](active-directory-aadconnect-pass-through-authentication.md) jako metody logowania, jest wymagane nie dodatkowe wymagania wstępne. Jeśli używasz [synchronizacji skrótu hasła](active-directory-aadconnectsync-implement-password-hash-synchronization.md) jako metody logowania, a w przypadku zapory między Azure AD Connect i Azure AD, upewnij się, że:
   - Użyj wersji 1.1.644.0 lub nowszej programu Azure AD Connect. 
   - Jeśli zapora lub serwer proxy zezwala listę dozwolonych podobnej DNS, dozwolonych połączeń z  **\*. msappproxy.net** adresów URL za pośrednictwem portu 443. Jeśli nie, Zezwalaj na dostęp do [zakresy IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653), która jest aktualizowana co tydzień. To wymaganie wstępne ma zastosowanie tylko wtedy, gdy zostanie włączona funkcja. Nie jest wymagane do rzeczywistego użytkownika logowania.

    >[!NOTE]
    >Wersje usługi Azure AD Connect, 1.1.557.0, 1.1.558.0 1.1.561.0 i 1.1.614.0 ma problem związany z synchronizacji skrótu hasła. Jeśli użytkownik _nie_ ma być używany w połączeniu z uwierzytelniania przekazywanego, przeczytaj synchronizacji skrótu hasła [wersji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) Aby dowiedzieć się więcej.

* **Konfigurowanie poświadczeń administratora domeny**: musisz mieć poświadczenia administratora domeny, dla każdej usługi Active Directory lasu, który:
    * Możesz zsynchronizować z usługą Azure AD za pomocą usługi Azure AD Connect.
    * Zawiera użytkowników, dla których chcesz włączyć dla bezproblemowego logowania jednokrotnego.

## <a name="step-2-enable-the-feature"></a>Krok 2: Włączanie funkcji

Włącz bezproblemowe logowania jednokrotnego za pośrednictwem [programu Azure AD Connect](active-directory-aadconnect.md).

Podczas wykonywania nową instalację programu Azure AD Connect, jeśli [niestandardową ścieżkę](active-directory-aadconnect-get-started-custom.md). W **logowania użytkownika** wybierz pozycję **włączenia funkcji logowania jednokrotnego w** opcji.

![Usługi Azure AD Connect: Logowanie użytkownika](./media/active-directory-aadconnect-sso/sso8.png)

Jeśli masz już zainstalowany program Azure AD Connect, wybierz **zmienić logowania użytkownika** w programie Azure AD Connect, a następnie wybierz **dalej**.

![Azure AD Connect: Zmień logowanie użytkowników](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Kontynuuj pracę z kreatorem, aż do **włączenia funkcji logowania jednokrotnego w** strony. Podaj poświadczenia administratora domeny dla każdej usługi Active Directory lasu, który:
    * Możesz zsynchronizować z usługą Azure AD za pomocą usługi Azure AD Connect.
    * Zawiera użytkowników, dla których chcesz włączyć dla bezproblemowego logowania jednokrotnego.

Po zakończeniu działania kreatora bezproblemowe rejestracji Jednokrotnej jest włączona na dzierżawy.

>[!NOTE]
> Poświadczenia administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Są one używane tylko do włączenia tej funkcji.

Wykonaj te instrukcje, aby sprawdzić prawidłowo włączone bezproblemowe rejestracji Jednokrotnej:

1. Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługi Azure Active Directory** w okienku po lewej stronie.
3. Wybierz **programu Azure AD Connect**.
4. Sprawdź, czy **bezproblemowe logowanie jednokrotne** funkcji jest wyświetlany jako **włączone**.

![Portalu Azure: okienko Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Krok 3: Wdrażanie funkcji

Aby wdrożyć funkcję dla użytkowników, należy dodać następujący adres URL usługi Azure AD do ustawienia strefy Intranet użytkowników za pomocą zasad grupy w usłudze Active Directory:

- https://autologon.microsoftazuread-sso.com


Ponadto należy włączyć zasadę strefy Intranet nosi nazwę **Zezwalaj na pasku stanu za pomocą skryptu aktualizacji** za pomocą zasad grupy. 

>[!NOTE]
> Poniższe instrukcje działa tylko w przypadku programu Internet Explorer i Google Chrome w systemie Windows (Jeśli zestaw adresów URL zaufanych witryn współużytkuje z programu Internet Explorer). Przeczytaj następnej sekcji, aby uzyskać instrukcje dotyczące sposobu konfigurowania Mozilla Firefox i Google Chrome na komputerach Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Dlaczego należy zmodyfikować ustawienia strefy Intranet użytkowników?

Domyślnie przeglądarka automatycznie oblicza poprawnej strefy Internecie lub intranecie, z określonym adresem URL. Na przykład "http://contoso/"mapy do strefy Intranet, podczas gdy"http://intranet.contoso.com/" mapuje strefie Internet (ponieważ adres URL zawiera kropkę). Przeglądarki nie wyśle biletów Kerberos do punktu końcowego w chmurze, takich jak Azure AD adresu URL, chyba że dodasz do strefy Intranet w przeglądarce adres URL.

### <a name="detailed-steps"></a>Szczegółowe procedury

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytowanie zasad grupy, która jest stosowana do niektórych lub wszystkich użytkowników. W tym przykładzie użyto **domyślne zasady domeny**.
3. Przejdź do **Konfiguracja użytkownika** > **Szablony administracyjne** > **składniki systemu Windows**  >   **Program Internet Explorer** > **internetowy Panel sterowania** > **Strona zabezpieczeń**. Następnie wybierz **witrynę do strefy przypisania listy**.
    ![Logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso6.png)
4. Włączanie zasad, a następnie wprowadź następujące wartości w oknie dialogowym:
   - **Nazwa wartości**: programu Azure AD URL gdzie bilety Kerberos są przesyłane dalej.
   - **Wartość** (dane): **1** wskazuje strefy intranetowej.

    Wynik wygląda następująco:

    Wartość:https://autologon.microsoftazuread-sso.com
  
    Dane: 1

   >[!NOTE]
   > Jeśli chcesz uniemożliwić użytkownikom niektóre przy użyciu łatwego logowania jednokrotnego (na przykład, jeśli ci użytkownicy Zaloguj się na udostępnionym kioski), wartość poprzednimi wartościami **4**. Ta akcja dodaje adres URL usługi Azure AD do ograniczonej strefy, a nie powiedzie się bezproblemowe logowanie Jednokrotne cały czas.
   >

5. Wybierz **OK**, a następnie wybierz **OK** ponownie.

    ![Logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso7.png)

6. Przejdź do **Konfiguracja użytkownika** > **Szablony administracyjne** > **składniki systemu Windows**  >   **Program Internet Explorer** > **internetowy Panel sterowania** > **Strona zabezpieczeń** > **strefy Intranet**. Następnie wybierz **Zezwalaj na pasku stanu za pomocą skryptu aktualizacji**.

    ![Logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso11.png)

7. Włącz ustawienie zasad, a następnie wybierz **OK**.

    ![Logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Zagadnienia dotyczące przeglądarki

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (wszystkie platformy)

Mozilla Firefox nie są automatycznie używane uwierzytelnianie Kerberos. Każdy użytkownik musi ręcznie dodać adres URL usługi Azure AD ustawienia przeglądarki Firefox przy użyciu następujących kroków:
1. Uruchom program Firefox, a następnie wprowadź `about:config` na pasku adresu. Odrzucić żadnych powiadomień, które są wyświetlane.
2. Wyszukaj **network.negotiate-auth.trusted URI** preferencji. Ta opcja wyświetla listę zaufanych witryn w programie Firefox uwierzytelniania Kerberos.
3. Kliknij prawym przyciskiem myszy i wybierz **Modyfikuj**.
4. Wprowadź https://autologon.microsoftazuread-sso.com w polu.
5. Wybierz **OK** , a następnie ponownie przeglądarkę.

#### <a name="safari-mac-os"></a>Safari (Mac OS)

Upewnij się, że maszynę z systemem Mac OS jest przyłączony do usługi AD. Aby uzyskać instrukcje na dołączenie AD, zobacz [najlepsze rozwiązania dotyczące integrowania OS X z usługą Active Directory](http://www.isaca.org/Groups/Professional-English/identity-management/GroupDocuments/Integrating-OS-X-with-Active-Directory.pdf).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (wszystkie platformy)

Jeśli masz zastąpiona [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) lub [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) ustawień zasad w środowisku, upewnij się, że Dodaj adres URL usługi Azure AD (https://autologon.microsoftazuread-sso.com) dla nich oraz.

#### <a name="google-chrome-mac-os-only"></a>Google Chrome (tylko Mac OS)

Google Chrome na innych platform z systemem innym niż Windows i Mac OS, można znaleźć w temacie [listy zasad projektu chromu](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) Aby uzyskać informacje dotyczące sposobu listą dozwolonych adresów IP, adres URL usługi Azure AD zintegrowane uwierzytelnianie.

Korzystanie z innych firm rozszerzenia zasad grupy usługi Active Directory do wdrażanie Azure AD adres URL do przeglądarki Firefox i Google Chrome na użytkowników komputerów Mac jest poza zakres tego artykułu.

#### <a name="known-browser-limitations"></a>Ograniczenia dotyczące znanych przeglądarki

Bezproblemowe logowanie Jednokrotne nie działa w trybie prywatnym przeglądania na przeglądarki Firefox i krawędzi. Również nie działa w programie Internet Explorer Jeśli przeglądarka jest uruchomiony w trybie rozszerzonego chronione.

## <a name="step-4-test-the-feature"></a>Krok 4: Testowanie funkcji

Aby przetestować tę funkcję dla określonego użytkownika, upewnij się, że zostały spełnione wszystkie następujące warunki:
  - Użytkownik loguje się na urządzeniach firmowych.
  - Urządzenie jest przyłączony do domeny usługi Active Directory.
  - Urządzenie ma bezpośredniego połączenia kontrolera domeny (DC) w sieci firmowej sieci przewodowej lub bezprzewodowej lub za pośrednictwem połączenia dostępu zdalnego, takich jak połączenia sieci VPN.
  - Masz [wprowadzanie funkcję](##step-3-roll-out-the-feature) do tego użytkownika za pomocą zasad grupy.

Do przetestowania tego scenariusza, w którym użytkownik musi wprowadzić tylko nazwę użytkownika, ale nie hasło:
   - Zaloguj się do https://myapps.microsoft.com/ w nowej sesji przeglądarki prywatnych.

Do przetestowania tego scenariusza, w którym użytkownik nie musi wprowadzić nazwę użytkownika lub hasło, użyj jednej z następujących czynności: 
   - Zaloguj się do https://myapps.microsoft.com/contoso.onmicrosoft.com w nowej sesji przeglądarki prywatnych. Zastąp *contoso* nazwą swojej dzierżawy.
   - Zaloguj się do https://myapps.microsoft.com/contoso.com w nowej sesji przeglądarki prywatnych. Zastąp *contoso.com* z zweryfikowanej domeny (nie federacyjnych domeny) na dzierżawy.

## <a name="step-5-roll-over-keys"></a>Krok 5: Przerzucane kluczy

W kroku 2 Azure AD Connect tworzy konta komputerów (reprezentujący usługi Azure AD) we wszystkich lasach usługi Active Directory, na których włączono bezproblemowe logowania jednokrotnego. Aby dowiedzieć się więcej, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne: techniczne nowości](active-directory-aadconnect-sso-how-it-works.md). Ze względów bezpieczeństwa zaleca się, że należy okresowo przerzucane Kerberos kluczy odszyfrowywania tych kont komputerów. Aby uzyskać instrukcje dotyczące sposobu przerzucane kluczy, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne: często zadawane pytania](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Nie trzeba wykonać ten krok _natychmiast_ po włączeniu funkcji. Przerzuć klucze Kerberos odszyfrowywania co najmniej raz na 30 dni.

## <a name="next-steps"></a>Kolejne kroki

- [Nowości techniczne](active-directory-aadconnect-sso-how-it-works.md): zrozumieć sposób działania funkcji bezproblemowe logowanie jednokrotne.
- [Często zadawane pytania](active-directory-aadconnect-sso-faq.md): odpowiedzi na często zadawane pytania dotyczące bezproblemowe logowanie jednokrotne.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-sso.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją bezproblemowe logowanie jednokrotne.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): plik nowego żądania funkcji za pomocą na Forum usługi Azure Active Directory.
