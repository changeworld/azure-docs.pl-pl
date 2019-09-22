---
title: 'Program Azure AD Connect: Bezproblemowe logowanie jednokrotne — Szybki Start | Microsoft Docs'
description: W tym artykule opisano, jak zacząć korzystać z Azure Active Directory bezproblemowego logowania jednokrotnego
services: active-directory
keywords: Co to jest Azure AD Connect, zainstaluj Active Directory, wymagane składniki usługi Azure AD, logowania jednokrotnego, rejestracji jednokrotnej
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
ms.openlocfilehash: 29f94d6ff8045b7cae64957eeae00d2460ca3e37
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176822"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory bezproblemowe logowanie jednokrotne: Szybki start

## <a name="deploy-seamless-single-sign-on"></a>Wdróż bezproblemowe logowanie jednokrotne

Azure Active Directory (usługa Azure AD) bezproblemowe logowanie jednokrotne (bezproblemowe logowanie jednokrotne) użytkowników, gdy znajdują się na swoich komputerach firmowych, które są połączone z siecią firmową. Bezproblemowe logowanie jednokrotne zapewnia użytkownikom łatwy dostęp do aplikacji opartych na chmurze bez konieczności używania dodatkowych składników lokalnych.

Aby wdrożyć bezproblemową rejestrację jednokrotną, wykonaj następujące kroki.

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Sprawdzanie wymagań wstępnych

Upewnij się, że są spełnione następujące wymagania wstępne:

* **Skonfiguruj serwer Azure AD Connect**: Jeśli używasz [uwierzytelniania przekazywanego](how-to-connect-pta.md) jako metody logowania, nie jest wymagane żadne dodatkowe Sprawdzanie wymagań wstępnych. Jeśli używasz [synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) jako metody logowania, a jeśli między Azure AD Connect i usługą Azure AD istnieje Zapora, upewnij się, że:
   - Używasz wersji 1.1.644.0 lub nowszej Azure AD Connect. 
   - Jeśli zapora lub serwer proxy zezwala na listy dozwolonych DNS, dozwolonych połączenia  **\*** z adresami URL. msappproxy.NET przez port 443. W przeciwnym razie Zezwól na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są aktualizowane co tydzień. To wymaganie wstępne jest stosowane tylko po włączeniu funkcji. Nie jest to wymagane w przypadku rzeczywistych logowań użytkownika.

    >[!NOTE]
    >Azure AD Connect wersje 1.1.557.0, 1.1.558.0, 1.1.561.0 i 1.1.614.0 mają problem związany z synchronizacją skrótów haseł. Jeśli _nie_ zamierzasz używać synchronizacji skrótów haseł w połączeniu z uwierzytelnianiem przekazującym, przeczytaj informacje o [wersji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) , aby dowiedzieć się więcej.

* **Użyj obsługiwanej topologii Azure AD Connect**: Upewnij się, że używasz jednej z Azure AD Connect obsługiwanych topologii opisanych [tutaj](plan-connect-topologies.md).

    >[!NOTE]
    >Bezproblemowe logowanie jednokrotne obsługuje wiele lasów usługi AD, bez względu na to, czy istnieją relacje zaufania między nimi.

* **Skonfiguruj poświadczenia administratora domeny**: Musisz mieć poświadczenia administratora domeny dla każdego lasu Active Directory, który:
    * Synchronizacja z usługą Azure AD odbywa się za pomocą Azure AD Connect.
    * Zawiera użytkowników, którym chcesz umożliwić bezproblemowe logowanie jednokrotne.
    
* **Włącz nowoczesne uwierzytelnianie**: Aby ta funkcja działała, należy włączyć [nowoczesne uwierzytelnianie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) w dzierżawie.

* **Użyj najnowszych wersji klientów pakietu Office 365**: Aby uzyskać ciche środowisko logowania z klientami z pakietem Office 365 (Outlook, Word, Excel i inne), użytkownicy muszą używać wersji 16.0.8730. xxxx lub nowszej.

## <a name="step-2-enable-the-feature"></a>Krok 2: Włącz funkcję

Zapewnij bezproblemowe logowanie jednokrotne za pomocą [Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Możesz również [włączyć bezproblemowe logowanie jednokrotne przy użyciu programu PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) , jeśli Azure AD Connect nie spełnia wymagań. Użyj tej opcji, jeśli masz więcej niż jedną domenę na Active Directory Las, a chcesz mieć większą wartość dowolną dla domeny, dla której chcesz włączyć bezproblemowe logowanie jednokrotne.

Jeśli wykonujesz nową instalację Azure AD Connect, wybierz [niestandardową ścieżkę instalacji](how-to-connect-install-custom.md). Na stronie **logowania użytkownika** wybierz opcję **Włącz logowanie jednokrotne** .

>[!NOTE]
> Opcja będzie dostępna do wybrania tylko wtedy, gdy metoda logowania jest **synchronizacją skrótu hasła** lub **uwierzytelnianiem przekazującym**.

![Program Azure AD Connect: Logowanie użytkowników](./media/how-to-connect-sso-quick-start/sso8.png)

Jeśli masz już instalację Azure AD Connect, wybierz stronę **logowania użytkownika** w Azure AD Connect, a następnie wybierz przycisk **dalej**. Jeśli używasz Azure AD Connect wersji 1.1.880.0 lub nowszej, opcja **Włącz logowanie** jednokrotne zostanie domyślnie wybrana. Jeśli używasz starszych wersji Azure AD Connect, zaznacz opcję **Włącz logowanie jednokrotne** .

![Program Azure AD Connect: Zmiana logowania użytkownika](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Kontynuuj pracę z kreatorem, dopóki nie uzyskasz na stronie **Włączanie logowania** jednokrotnego. Podaj poświadczenia administratora domeny dla każdego lasu Active Directory, który:

* Synchronizacja z usługą Azure AD odbywa się za pomocą Azure AD Connect.
* Zawiera użytkowników, którym chcesz umożliwić bezproblemowe logowanie jednokrotne.

Po zakończeniu pracy kreatora w dzierżawie jest włączone bezproblemowe logowanie jednokrotne.

>[!NOTE]
> Poświadczenia administratora domeny nie są przechowywane w Azure AD Connect ani w usłudze Azure AD. Są one używane tylko w celu włączenia tej funkcji.

Postępuj zgodnie z tymi instrukcjami, aby sprawdzić, czy włączono bezproblemową rejestrację logowania jednokrotnego:

1. Zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. W lewym okienku wybierz pozycję **Azure Active Directory** .
3. Wybierz **Azure AD Connect**.
4. Upewnij się, że funkcja **bezproblemowego logowania jednokrotnego** jest wyświetlana jako **włączona**.

![Azure Portal: Okienko Azure AD Connect](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Bezproblemowe logowanie jednokrotne tworzy konto `AZUREADSSOACC` komputera o nazwie w lokalnym Active Directory (AD) w każdym lesie usługi AD. Konto `AZUREADSSOACC` komputera musi być silnie chronione ze względów bezpieczeństwa. Tylko Administratorzy domeny powinni mieć możliwość zarządzania kontem komputera. Upewnij się, że delegowanie Kerberos na koncie komputera jest wyłączone i że żadne inne konto w Active Directory nie ma uprawnień do `AZUREADSSOACC` delegowania na koncie komputera. Przechowywanie konta komputera w jednostce organizacyjnej (OU), w którym są bezpieczne przed przypadkowym usunięciem i gdzie tylko Administratorzy domeny mają dostęp.

>[!NOTE]
> Jeśli w środowisku lokalnym są używane architektury łagodzenia przed mieszaniem i kradzieżą poświadczeń, wprowadź odpowiednie zmiany, aby upewnić się, że `AZUREADSSOACC` konto komputera nie kończy się w kontenerze kwarantanny. 

## <a name="step-3-roll-out-the-feature"></a>Krok 3: Wycofaj funkcję

Możesz stopniowo wdrożyć bezproblemową rejestrację jednokrotną dla użytkowników, korzystając z instrukcji podanych poniżej. Aby rozpocząć, Dodaj następujący adres URL usługi Azure AD do ustawień strefy intranetowej wszystkich lub wybranych użytkowników za pomocą zasady grupy w Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Ponadto należy włączyć ustawienie zasad strefy intranet o nazwie **Zezwalaj na aktualizacje na pasku stanu za pośrednictwem skryptu** za pośrednictwem zasady grupy. 

>[!NOTE]
> Poniższe instrukcje dotyczą tylko programu Internet Explorer i Google Chrome w systemie Windows (jeśli udostępnia zestaw adresów URL zaufanej witryny w programie Internet Explorer). Zapoznaj się z następną sekcją, aby uzyskać instrukcje dotyczące sposobu konfigurowania przeglądarki Mozilla Firefox i Google Chrome w systemie macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Dlaczego należy zmodyfikować ustawienia strefy intranetowej użytkowników?

Domyślnie przeglądarka automatycznie oblicza poprawną strefę, Internet lub intranet, z określonego adresu URL. Na przykład `http://contoso/` Usługa mapuje do strefy intranet, a `http://intranet.contoso.com/` następnie mapuje ją na strefę internetową (ponieważ adres URL zawiera kropkę). Przeglądarki nie będą wysyłać biletów protokołu Kerberos do punktu końcowego w chmurze, takiego jak adres URL usługi Azure AD, chyba że jawnie dodasz adres URL do strefy intranetowej przeglądarki.

Istnieją dwa sposoby modyfikacji ustawień strefy intranetowej użytkowników:

| Opcja | Zagadnienie dla administratorów | Środowisko użytkownika |
| --- | --- | --- |
| Zasady grupy | Administrator blokuje Edytowanie ustawień strefy intranetowej | Użytkownicy nie mogą modyfikować własnych ustawień |
| Preferencja zasad grupy |  Administrator umożliwia edytowanie ustawień strefy intranetowej | Użytkownicy mogą modyfikować własne ustawienia |

### <a name="group-policy-option---detailed-steps"></a>Opcja "zasady grupy" — szczegółowe kroki

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytuj zasady grupy, które są stosowane do niektórych lub wszystkich użytkowników. Ten przykład używa **domyślnych zasad domeny**.
3. Przejdź do > **zasad** **konfiguracji użytkownika** **Szablony administracyjne**składnikisystemu > **Windows**InternetExplorerinternetowy >  >  >  > **Strona zabezpieczeń**panelu sterowania. Następnie wybierz pozycję **lokacja do przypisywania stref**.
    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso6.png)
4. Włącz zasady, a następnie wprowadź następujące wartości w oknie dialogowym:
   - **Nazwa wartości**: Adres URL usługi Azure AD, gdzie są przekazywane bilety protokołu Kerberos.
   - **Wartość** (Dane): **1** oznacza strefę intranetową.

     Wynik będzie wyglądać następująco:

     Nazwa wartości:`https://autologon.microsoftazuread-sso.com`
  
     Wartość (dane): 1

   >[!NOTE]
   > Aby uniemożliwić niektórym użytkownikom korzystanie z bezproblemowego logowania jednokrotnego (na przykład jeśli użytkownicy logują się do współużytkowanych kiosków), należy ustawić powyższe wartości na **4**. Ta akcja powoduje dodanie adresu URL usługi Azure AD do strefy z ograniczeniami i bezproblemowy dostęp do logowania jednokrotnego.
   >

5. Wybierz **przycisk OK**, a następnie ponownie wybierz przycisk **OK** .

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso7.png)

6. Przejdź do > **zasad** **konfiguracji użytkownika** **Szablony administracyjne**składnikisystemu > **Windows**InternetExplorerinternetowy >  >  >  > **Strefa sieci intranet**w panelu > sterowania. Następnie wybierz pozycję **Zezwalaj na aktualizacje na pasku stanu za pomocą skryptu**.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso11.png)

7. Włącz ustawienie zasad, a następnie wybierz przycisk **OK**.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opcja "preferencja zasad grupy" — szczegółowe kroki

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytuj zasady grupy, które są stosowane do niektórych lub wszystkich użytkowników. Ten przykład używa **domyślnych zasad domeny**.
3.  > Przejdź do pozycji**Preferencje** > konfiguracjiużytkownika > **Rejestr**ustawieńsystemuWindows > nowyelementrejestru. > 

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

Mozilla Firefox nie korzysta automatycznie z uwierzytelniania Kerberos. Każdy użytkownik musi ręcznie dodać adres URL usługi Azure AD do ustawień przeglądarki Firefox, wykonując następujące czynności:
1. Uruchom przeglądarkę Firefox i `about:config` wprowadź na pasku adresu. Odrzuć wszystkie wyświetlone powiadomienia.
2. Wyszukaj preferencje **Network. Negocjuj-auth. Trusted-URI** . To preferencje zawiera Zaufane witryny programu Firefox do uwierzytelniania Kerberos.
3. Kliknij prawym przyciskiem myszy i wybierz polecenie **Modyfikuj**.
4. Wprowadź `https://autologon.microsoftazuread-sso.com` wartość w polu.
5. Wybierz przycisk **OK** , a następnie ponownie otwórz przeglądarkę.

#### <a name="safari-macos"></a>Safari (macOS)

Upewnij się, że maszyna, na której działa macOS, jest przyłączona do usługi AD. Instrukcje dotyczące dołączania urządzenia z systemem macOS są poza zakresem tego artykułu.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (wszystkie platformy)

Jeśli ustawienia zasad [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) lub [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) zostały zastąpione w Twoim środowisku, upewnij się, że dodano do nich adres URL (`https://autologon.microsoftazuread-sso.com`) usługi Azure AD.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS i inne platformy inne niż Windows)

W przypadku usługi Google Chrome na Mac OS i innych platformach innych niż Windows Zapoznaj się z [listą zasad projektu chrom](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) , aby uzyskać informacje na temat dozwolonych adresu URL usługi Azure AD na potrzeby uwierzytelniania zintegrowanego.

Korzystanie z rozszerzeń zasady grupy Active Directory innych firm do wdrożenia adresu URL usługi Azure AD w programie Firefox i Google Chrome na komputerach Mac wykracza poza zakres tego artykułu.

#### <a name="known-browser-limitations"></a>Znane ograniczenia przeglądarki

Bezproblemowe logowanie jednokrotne nie działa w trybie przeglądania prywatnego w przeglądarkach Firefox i Microsoft Edge. Nie działa również w programie Internet Explorer, jeśli przeglądarka działa w trybie rozszerzonym chronionym.

## <a name="step-4-test-the-feature"></a>Krok 4: Testowanie funkcji

Aby przetestować funkcję dla określonego użytkownika, upewnij się, że zostały spełnione wszystkie następujące warunki:
  - Użytkownik loguje się na urządzeniu firmowym.
  - Urządzenie jest przyłączone do domeny Active Directory. Urządzenie _nie_ musi być [przyłączone do usługi Azure AD](../active-directory-azureadjoin-overview.md).
  - Urządzenie ma bezpośrednie połączenie z kontrolerem domeny (DC) w sieci przewodowej lub bezprzewodowej firmy lub za pośrednictwem połączenia dostępu zdalnego, takiego jak połączenie sieci VPN.
  - Funkcja dla tego użytkownika została [przeprowadzona](##step-3-roll-out-the-feature) za pomocą zasady grupy.

W celu przetestowania scenariusza, w którym użytkownik wprowadza tylko nazwę użytkownika, ale nie hasło:
   - Zaloguj się do `https://myapps.microsoft.com/` programu w nowej sesji przeglądarki prywatnej.

W celu przetestowania scenariusza, w którym użytkownik nie musi wprowadzać nazwy użytkownika ani hasła, wykonaj jedną z następujących czynności: 
   - Zaloguj się do `https://myapps.microsoft.com/contoso.onmicrosoft.com` programu w nowej sesji przeglądarki prywatnej. Zamień *contoso* na nazwę dzierżawy.
   - Zaloguj się do `https://myapps.microsoft.com/contoso.com` programu w nowej sesji przeglądarki prywatnej. Zastąp *contoso.com* z zweryfikowaną domeną (nie domeną federacyjną) w dzierżawie.

## <a name="step-5-roll-over-keys"></a>Krok 5. Przewinięcie kluczy

W kroku 2 Azure AD Connect tworzy konta komputerów (reprezentujące usługę Azure AD) we wszystkich lasach Active Directory, w których włączono bezproblemowe logowanie jednokrotne. Aby dowiedzieć się więcej [, zobacz Azure Active Directory bezproblemowe logowanie jednokrotne: Głębokie szczegółowe](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>Klucz odszyfrowujący protokołu Kerberos na koncie komputera, w przypadku przecieków, może służyć do generowania biletów protokołu Kerberos dla dowolnego użytkownika w jego lesie usługi AD. Złośliwe podmioty mogą następnie personifikować logowania do usługi Azure AD dla użytkowników, których dotyczy naruszenie. Zdecydowanie zalecamy okresowe przeprowadzenie tych kluczy odszyfrowywania Kerberos — co najmniej raz na 30 dni.

Aby uzyskać instrukcje dotyczące sposobu zestawiania kluczy, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne: Często zadawane pytania](how-to-connect-sso-faq.md). Pracujemy nad możliwością wprowadzenia zautomatyzowanego przejmowania kluczy.

>[!IMPORTANT]
>Nie musisz wykonywać tego kroku _natychmiast_ po włączeniu tej funkcji. Przewinięcie kluczy odszyfrowywania Kerberos co najmniej raz na 30 dni.

## <a name="next-steps"></a>Następne kroki

- [Głębokie szczegółowea techniczna](how-to-connect-sso-how-it-works.md): Zapoznaj się z działaniem funkcji bezproblemowego logowania jednokrotnego.
- [Często zadawane pytania](how-to-connect-sso-faq.md): Uzyskaj odpowiedzi na często zadawane pytania dotyczące bezproblemowego logowania jednokrotnego.
- [Rozwiązywanie problemów](tshoot-connect-sso.md): Dowiedz się, jak rozwiązywać typowe problemy przy użyciu funkcji bezproblemowego logowania jednokrotnego.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj forum Azure Active Directory, aby wykonać nowe żądania funkcji.
