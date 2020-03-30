---
title: 'Usługa Azure AD Connect: bezproblemowe logowanie jednokrotne — szybkie uruchamianie | Dokumenty firmy Microsoft'
description: W tym artykule opisano, jak rozpocząć pracę z bezproblemowym logowaniem jednokrotnym usługi Azure Active Directory
services: active-directory
keywords: co to jest usługa Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
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
ms.openlocfilehash: 3ec56d37ca2c0a199968707b3d93f4797be2beca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261205"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Bezproblemowe logowanie usługi Azure Active Directory: szybkie uruchamianie

## <a name="deploy-seamless-single-sign-on"></a>Wdrażanie bezproblemowego logowania jednokrotnego

Usługa Azure Active Directory (Azure AD) Seamless Single Sign-On (Seamless SSO) automatycznie loguje użytkowników, gdy znajdują się na komputerach firmowych połączonych z siecią firmową. Bezproblemowe logowanie jednokrotne zapewnia użytkownikom łatwy dostęp do aplikacji w chmurze bez konieczności stosowania dodatkowych składników lokalnych.

Aby wdrożyć bezproblemowe logowanie jednokrotne, wykonaj następujące kroki.

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Sprawdź wymagania wstępne

Upewnij się, że istnieją następujące wymagania wstępne:

* **Konfigurowanie serwera usługi Azure AD Connect:** Jeśli używasz [uwierzytelniania przekazywania](how-to-connect-pta.md) jako metody logowania, nie jest wymagane żadne dodatkowe sprawdzanie wymagań wstępnych. Jeśli używasz [synchronizacji skrótu hasła](how-to-connect-password-hash-synchronization.md) jako metody logowania i jeśli istnieje zapora między usługą Azure AD Connect i usługą Azure AD, upewnij się, że:
   - Używasz wersji 1.1.644.0 lub nowszej usługi Azure AD Connect. 
   - Jeśli zapora lub serwer proxy zezwala na umieszczanie ** \*** na białej liście DNS, należy umieszczać na białej liście połączenia z adresami URL msappproxy.net za pośrednictwem portu 443. Jeśli nie, zezwalaj na dostęp do [zakresów adresów IP centrum danych platformy Azure,](https://www.microsoft.com/download/details.aspx?id=41653)które są aktualizowane co tydzień. Ten warunek wstępny ma zastosowanie tylko po włączeniu tej funkcji. Nie jest wymagane dla rzeczywistych logowania użytkownika.

    >[!NOTE]
    >Usługa Azure AD Connect w wersjach 1.1.557.0, 1.1.558.0, 1.1.561.0 i 1.1.614.0 ma problem związany z synchronizacją skrótów haseł. Jeśli _nie_ zamierzasz używać synchronizacji skrótów haseł w połączeniu z uwierzytelnianiem przekazywania, przeczytaj [informacje o wersji usługi Azure AD Connect,](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) aby dowiedzieć się więcej.

* **Użyj obsługiwanej topologii usługi Azure AD Connect:** Upewnij się, że używasz jednej z obsługiwanych topologii usługi Azure AD Connect opisanych [w tym miejscu.](plan-connect-topologies.md)

    >[!NOTE]
    >Bezproblemowe logowanie jednokrotne obsługuje wiele lasów usługi AD, niezależnie od tego, czy między nimi znajdują się relacje zaufania usługi AD, czy nie.

* **Konfigurowanie poświadczeń administratora domeny:** Dla każdego lasu usługi Active Directory muszą być wymagane poświadczenia administratora domeny, które:
    * Synchronizacja z usługą Azure AD za pośrednictwem usługi Azure AD Connect.
    * Zawiera użytkowników, których chcesz włączyć dla bezproblemowego logowania jednokrotnego.
    
* **Włącz nowoczesne uwierzytelnianie:** Aby ta funkcja [działała,](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) należy włączyć nowoczesne uwierzytelnianie w dzierżawie.

* **Korzystaj z najnowszych wersji klientów usługi Office 365:** aby uzyskać ciche środowisko logowania z klientami usługi Office 365 (Outlook, Word, Excel i innymi), użytkownicy muszą używać wersji 16.0.8730.xxxx lub nowszej.

## <a name="step-2-enable-the-feature"></a>Krok 2: Włącz funkcję

Włącz bezproblemowe logowanie jednokrotne za pośrednictwem [usługi Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Można również [włączyć bezproblemowe logowanie przy użyciu programu PowerShell,](tshoot-connect-sso.md#manual-reset-of-the-feature) jeśli usługa Azure AD Connect nie spełnia twoich wymagań. Użyj tej opcji, jeśli masz więcej niż jedną domenę na las usługi Active Directory i chcesz być bardziej ukierunkowany na domenę, dla której chcesz włączyć bezproblemowe logowanie jednokrotne.

Jeśli wykonujesz nową instalację usługi Azure AD Connect, wybierz [niestandardową ścieżkę instalacji](how-to-connect-install-custom.md). Na stronie **Logowania użytkownika** wybierz opcję **Włącz logowanie jednokrotne.**

>[!NOTE]
> Opcja będzie dostępna do wyboru tylko wtedy, gdy metodą logowania jest **synchronizacja skrótów haseł** lub **uwierzytelnianie przekazywane.**

![Usługa Azure AD Connect: logowanie użytkownika](./media/how-to-connect-sso-quick-start/sso8.png)

Jeśli masz już instalację usługi Azure AD Connect, wybierz stronę **Zmień logowanie użytkownika w** usłudze Azure AD Connect, a następnie wybierz pozycję **Dalej**. Jeśli używasz usługi Azure AD Connect w wersji 1.1.880.0 lub nowszej, opcja **Włącz znak jednokrotny** zostanie wybrana domyślnie. Jeśli używasz starszych wersji usługi Azure AD Connect, wybierz opcję **Włącz znak jednokrotny na.**

![Usługa Azure AD Connect: zmienianie logowania użytkownika](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Kontynuuj przez kreatora, aż dojdziesz do **włącz znak pojedynczy na** stronie. Podaj poświadczenia administratora domeny dla każdego lasu usługi Active Directory, które:

* Synchronizacja z usługą Azure AD za pośrednictwem usługi Azure AD Connect.
* Zawiera użytkowników, których chcesz włączyć dla bezproblemowego logowania jednokrotnego.

Po zakończeniu pracy kreatora bezproblemowe logowanie jednokrotne jest włączone w dzierżawie.

>[!NOTE]
> Poświadczenia administratora domeny nie są przechowywane w usłudze Azure AD Connect ani w usłudze Azure AD. Są one używane tylko do włączenia funkcji.

Postępuj zgodnie z poniższymi instrukcjami, aby sprawdzić, czy funkcja bezproblemowego logowania jednokrotnego została włączona prawidłowo:

1. Zaloguj się do [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługę Azure Active Directory** w lewym okienku.
3. Wybierz **usługę Azure AD Connect**.
4. Sprawdź, czy funkcja **logowania jednokrotnego jest** wyświetlana jako **włączona**.

![Witryna azure portal: okienko usługi Azure AD Connect](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Bezproblemowe logowanie jednokrotne `AZUREADSSOACC` tworzy konto komputera o nazwie w lokalnej usłudze Active Directory (AD) w każdym lesie usługi AD. Konto `AZUREADSSOACC` komputera musi być silnie chronione ze względów bezpieczeństwa. Tylko administratorzy domeny powinni mieć możliwość zarządzania kontem komputera. Upewnij się, że delegowanie protokołu Kerberos na koncie komputera jest wyłączone i `AZUREADSSOACC` że żadne inne konto w usłudze Active Directory nie ma uprawnień do delegowania na koncie komputera. Przechowuj konto komputera w jednostce organizacyjnej (OU), gdzie są one bezpieczne przed przypadkowymi usunięciami i gdzie tylko administratorzy domeny mają dostęp.

>[!NOTE]
> Jeśli używasz architektury ograniczania skrótu i kradzieży poświadczeń w środowisku lokalnym, `AZUREADSSOACC` należy wprowadzić odpowiednie zmiany, aby upewnić się, że konto komputera nie znajduje się w kontenerze kwarantanny. 

## <a name="step-3-roll-out-the-feature"></a>Krok 3: Rozwałkować funkcję

Możesz stopniowo wdrażać bezproblemowe logowanie jednokrotne dla użytkowników, korzystając z poniższych instrukcji. Możesz zacząć od dodania następującego adresu URL usługi Azure AD do wszystkich lub wybranych ustawień strefy intranetu wybranych użytkowników przy użyciu zasad grupy w usłudze Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Ponadto należy włączyć ustawienie zasad strefy intranetu o nazwie **Zezwalaj na aktualizacje paska stanu za pomocą skryptu** za pośrednictwem zasad grupy. 

>[!NOTE]
> Poniższe instrukcje działają tylko w przypadku programów Internet Explorer i Google Chrome w systemie Windows (jeśli zestaw zaufanych adresów URL witryny jest współużytkowany z programem Internet Explorer). Przeczytaj następną sekcję, aby uzyskać instrukcje dotyczące konfigurowania przeglądarki Mozilla Firefox i Google Chrome na komputerze macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Dlaczego należy zmodyfikować ustawienia strefy intranetowej użytkowników?

Domyślnie przeglądarka automatycznie oblicza poprawną strefę, Internet lub Intranet, z określonego adresu URL. Na przykład `http://contoso/` mapuje do strefy Intranet, podczas gdy `http://intranet.contoso.com/` mapy do strefy Internet (ponieważ adres URL zawiera kropkę). Przeglądarki nie będą wysyłać biletów Protokołu Kerberos do punktu końcowego chmury, takiego jak adres URL usługi Azure AD, chyba że jawnie dodać adres URL do strefy intranetu przeglądarki.

Istnieją dwa sposoby modyfikowania ustawień strefy intranetowej użytkowników:

| Opcja | Uwaga dla administratora | Środowisko użytkownika |
| --- | --- | --- |
| Zasady grupy | Administrator blokuje edycję ustawień strefy intranetu | Użytkownicy nie mogą modyfikować własnych ustawień |
| Preferencje zasad grupy |  Administrator umożliwia edycję ustawień strefy Intranetu | Użytkownicy mogą modyfikować własne ustawienia |

### <a name="group-policy-option---detailed-steps"></a>Opcja "Zasady grupy" — szczegółowe kroki

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytuj zasady grupy zastosowane do niektórych lub wszystkich użytkowników. W tym przykładzie użyto **domyślnych zasad domeny**.
3. Przejdź do**szablonów** > administracyjnych**zasad** >  **konfiguracji** > użytkownika**Składniki systemu Windows** > **Internet** > **FormantOwanie Panelu sterowania** > **.** Następnie wybierz **pozycję Lista przypisania witryny do strefy**.
    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso6.png)
4. Włącz zasadę, a następnie wprowadź następujące wartości w oknie dialogowym:
   - **Nazwa wartości:** Adres URL usługi Azure AD, w którym są przekazywane bilety protokołu Kerberos.
   - **Wartość** (dane): **1** oznacza strefę intranetu.

     Wynik wygląda następująco:

     Nazwa wartości:`https://autologon.microsoftazuread-sso.com`
  
     Wartość (dane): 1

   >[!NOTE]
   > Jeśli chcesz uniemożliwić niektórym użytkownikom korzystanie z bezproblemowego logowania jednokrotnego (na przykład, jeśli ci użytkownicy logują się w kioskach udostępnionych), ustaw poprzednie wartości na **4**. Ta akcja dodaje adres URL usługi Azure AD do strefy z ograniczeniami i nie działa bezproblemowe logowania jednokrotnego przez cały czas.
   >

5. Wybierz przycisk **OK**, a następnie ponownie wybierz przycisk **OK**.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso7.png)

6. Przejdź do**szablonów** > administracyjnych**zasad** >  **konfiguracji** > użytkownika**Składniki systemu Windows** > **Internet Explorer** > Strefa**intranetu****strony** > programu Internet**Control Panel** > . Następnie wybierz pozycję **Zezwalaj na aktualizacje na pasku stanu za pomocą skryptu**.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso11.png)

7. Włącz ustawienie zasad, a następnie wybierz przycisk **OK**.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opcja "Preferencje zasad grupy" — szczegółowe kroki

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytuj zasady grupy zastosowane do niektórych lub wszystkich użytkowników. W tym przykładzie użyto **domyślnych zasad domeny**.
3. Przejdź do**preferencji** >  **konfiguracji** > użytkownika**Ustawienia systemu Windows** > **Rejestr** > rejestru**Nowy** > element**rejestru**.

    ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso15.png)

4. Wprowadź następujące wartości w odpowiednich polach i kliknij przycisk **OK**.
   - **Ścieżka** ***klucza: Oprogramowanie\Microsoft\Windows\CurrentVersion\Ustawienia internetowe\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Nazwa wartości**: ***https***.
   - **Typ wartości**: ***REG_DWORD***.
   - **Dane wartości:** ***00000001***.
 
     ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Zagadnienia dotyczące przeglądarki

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (wszystkie platformy)

Mozilla Firefox nie używa automatycznie uwierzytelniania Kerberos. Każdy użytkownik musi ręcznie dodać adres URL usługi Azure AD do ustawień Firefoksa, wykonując następujące kroki:
1. Uruchom Firefoksa i wprowadź `about:config` na pasku adresu. Odrzuć wszystkie wyświetlane powiadomienia.
2. Wyszukaj preferencje **network.negotiate-auth.trusted-uris.** Ta preferencja zawiera listę zaufanych witryn Firefoksa do uwierzytelniania Kerberos.
3. Kliknij prawym przyciskiem myszy i wybierz polecenie **Modyfikuj**.
4. Wprowadź `https://autologon.microsoftazuread-sso.com` w tym polu.
5. Wybierz **przycisk OK,** a następnie otwórz ponownie przeglądarkę.

#### <a name="safari-macos"></a>Safari (macOS)

Upewnij się, że komputer z systemem macOS jest połączony z usługą AD. Instrukcje dotyczące dołączania do urządzenia z systemem macOS przez ad są poza zakresem tego artykułu.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge oparty na Chromium (wszystkie platformy)

Jeśli zostały zastąpione [AuthNegotiateDelegateAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) lub [AuthServerAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) ustawienia zasad w twoim środowisku, upewnij`https://autologon.microsoftazuread-sso.com`się, że dodasz adres URL usługi Azure AD ( ) do nich, jak również.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge oparty na Chromium (macOS i innych platformach innych niż Windows)

W przypadku przeglądarki Microsoft Edge opartej na chromium w systemie Mac OS i innych platformach innych niż Windows zapoznaj się z programem [Microsoft Edge opartym na liście zasad chromium,](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) aby uzyskać informacje na temat dodawania adresu URL usługi Azure AD do zintegrowanego uwierzytelniania do listy dozwolonych.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (wszystkie platformy)

Jeśli zostały zastąpione [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) lub [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) ustawienia zasad w twoim środowisku, upewnij`https://autologon.microsoftazuread-sso.com`się, że dodasz adres URL usługi Azure AD ( ) do nich, jak również.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS i inne platformy inne niż Windows)

W przypadku przeglądarki Google Chrome na systemie Mac OS i innych platformach innych niż Windows zapoznaj się z [listą zasad projektu Chromium,](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) aby uzyskać informacje na temat umieszczania adresu URL usługi Azure AD w celu zintegrowanego uwierzytelniania.

Korzystanie z rozszerzeń zasad grupy usługi Active Directory innych firm w celu wdrożenia adresu URL usługi Azure AD dla użytkowników Firefoksa i Google Chrome na komputerach Mac wykracza poza zakres tego artykułu.

#### <a name="known-browser-limitations"></a>Znane ograniczenia przeglądarki

Bezproblemowe logowanie jednokrotne nie działa w trybie przeglądania prywatnego w przeglądarkach Firefox i Microsoft Edge. Nie działa również w programie Internet Explorer, jeśli przeglądarka jest uruchomiona w trybie rozszerzonym chronionym. Dla następnej wersji programu Microsoft Edge opartej na Chromium, nie będzie działać w trybie InPrivate i Guest według projektu.

## <a name="step-4-test-the-feature"></a>Krok 4: Przetestuj funkcję

Aby przetestować funkcję dla określonego użytkownika, upewnij się, że obowiązują wszystkie następujące warunki:
  - Użytkownik loguje się na urządzeniu firmowym.
  - Urządzenie zostanie przyłączone do domeny usługi Active Directory. Urządzenie _nie_ musi być [przyłączone](../active-directory-azureadjoin-overview.md)do usługi Azure AD.
  - Urządzenie ma bezpośrednie połączenie z kontrolerem domeny (DC), w sieci przewodowej lub bezprzewodowej firmy lub za pośrednictwem połączenia dostępu zdalnego, takiego jak połączenie sieci VPN.
  - Tę [funkcję wdrożono dla](#step-3-roll-out-the-feature) tego użytkownika za pośrednictwem zasad grupy.

Aby przetestować scenariusz, w którym użytkownik wprowadza tylko nazwę użytkownika, ale nie hasło:
   - Zaloguj się `https://myapps.microsoft.com/` w nowej prywatnej sesji przeglądarki.

Aby przetestować scenariusz, w którym użytkownik nie musi wprowadzać nazwy użytkownika lub hasła, wykonaj jedną z następujących czynności: 
   - Zaloguj się `https://myapps.microsoft.com/contoso.onmicrosoft.com` w nowej prywatnej sesji przeglądarki. Zastąp *contoso* nazwą dzierżawy.
   - Zaloguj się `https://myapps.microsoft.com/contoso.com` w nowej prywatnej sesji przeglądarki. Zastąp *contoso.com* zweryfikowaną domeną (a nie domeną federacjną) w dzierżawie.

## <a name="step-5-roll-over-keys"></a>Krok 5: Przewracaj klawisze

W kroku 2 usługa Azure AD Connect tworzy konta komputerów (reprezentujące usługę Azure AD) we wszystkich lasach usługi Active Directory, na których włączono bezproblemowe logowanie jednokrotne. Aby dowiedzieć się więcej, zobacz [Azure Active Directory Seamless Single Sign-On: Techniczne głębokie nurkowanie](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>Klucz odszyfrowywania protokołu Kerberos na koncie komputera, jeśli wycieknie, może służyć do generowania biletów Protokołu Kerberos dla dowolnego użytkownika w lesie usługi AD. Złośliwi aktorzy mogą następnie personifikować logowania usługi Azure AD dla użytkowników, których bezpieczeństwo zostało naruszone. Zdecydowanie zaleca się okresowe przewracanie tych kluczy odszyfrowywania protokołu Kerberos — co najmniej raz na 30 dni.

Aby uzyskać instrukcje dotyczące przerzucenia kluczy, zobacz [Bezproblemowe logowanie jednokrotne usługi Azure Active Directory: Często zadawane pytania](how-to-connect-sso-faq.md). Pracujemy nad możliwością wprowadzenia automatycznego przerzucenia kluczy.

>[!IMPORTANT]
>Nie musisz wykonywać tego kroku _natychmiast_ po włączeniu tej funkcji. Przerzucaj klucze odszyfrowywania protokołu Kerberos co najmniej raz na 30 dni.

## <a name="next-steps"></a>Następne kroki

- [Głębokie głębokie nurkowanie techniczne:](how-to-connect-sso-how-it-works.md)Dowiedz się, jak działa funkcja bezproblemowego logowania jednokrotnego.
- [Często zadawane pytania:](how-to-connect-sso-faq.md)uzyskaj odpowiedzi na często zadawane pytania dotyczące bezproblemowego logowania jednokrotnego.
- [Rozwiązywanie problemów](tshoot-connect-sso.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją bezproblemowego logowania jednokrotnego.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj forum usługi Azure Active Directory, aby złożyć nowe żądania funkcji.
