---
title: Logowanie jednokrotne za pomocą serwera proxy aplikacji | Dokumenty firmy Microsoft
description: Obejmuje sposób dostarczania logowania jednokrotnego przy użyciu serwera proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5948fba67d3f071d77192f9ad89bc696fdc0c3cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253457"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Delegowanie ograniczone protokołu Kerberos do logowania jednokrotnego w aplikacjach za pomocą serwera proxy aplikacji

Można zapewnić logowanie jednokrotne dla aplikacji lokalnych publikowanych za pośrednictwem serwera proxy aplikacji, które są zabezpieczone za pomocą zintegrowanego uwierzytelniania systemu Windows. Te aplikacje wymagają biletu Kerberos do dostępu. Serwer proxy aplikacji używa delegowania ograniczonego protokołu Kerberos (KCD) do obsługi tych aplikacji. 

Logowanie jednokrotne w aplikacjach przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA) można włączyć, zezwalając łącznikom serwera proxy aplikacji w usłudze Active Directory na personifikowanie użytkowników. Łączniki używają tego uprawnienia do wysyłania i odbierania tokenów w ich imieniu.

## <a name="how-single-sign-on-with-kcd-works"></a>Jak działa logowanie jednokrotne z KCD
Ten diagram wyjaśnia przepływ, gdy użytkownik próbuje uzyskać dostęp do aplikacji lokalnej, która używa IWA.

![Diagram przepływu uwierzytelniania AAD firmy Microsoft](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Użytkownik wprowadza adres URL, aby uzyskać dostęp do aplikacji lokalnej za pośrednictwem serwera proxy aplikacji.
2. Serwer proxy aplikacji przekierowuje żądanie do usług uwierzytelniania usługi Azure AD w celu wstępnego uwierzytelnienia. W tym momencie usługa Azure AD stosuje wszystkie odpowiednie zasady uwierzytelniania i autoryzacji, takie jak uwierzytelnianie wieloskładnikowe. Jeśli użytkownik zostanie zweryfikowany, usługa Azure AD tworzy token i wysyła go do użytkownika.
3. Użytkownik przekazuje token do serwera proxy aplikacji.
4. Serwer proxy aplikacji sprawdza poprawność tokenu i pobiera z niego nazwę główną użytkownika (UPN), a następnie łącznik ściąga nazwę UPN, a nazwę główną usługi (SPN) za pośrednictwem podwójnie uwierzytelnionego bezpiecznego kanału.
5. Łącznik wykonuje negocjacje protokołu Kerberos Ograniczone delegowanie (KCD) z lokalną usługą AD, personifikując użytkownika, aby uzyskać token Protokołu Kerberos do aplikacji.
6. Usługa Active Directory wysyła token Protokołu Kerberos dla aplikacji do łącznika.
7. Łącznik wysyła oryginalne żądanie do serwera aplikacji przy użyciu tokenu Kerberos odebranego z usługi AD.
8. Aplikacja wysyła odpowiedź do łącznika, który jest następnie zwracany do usługi proxy aplikacji i na koniec do użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z logiem jednokrotnym dla aplikacji IWA upewnij się, że środowisko jest gotowe z następującymi ustawieniami i konfiguracjami:

* Aplikacje, takie jak aplikacje sieci Web programu SharePoint, są skonfigurowane do używania zintegrowanego uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [Włączanie obsługi uwierzytelniania Kerberos](https://technet.microsoft.com/library/dd759186.aspx)lub program SharePoint w programie [SharePoint zobacz Planowanie uwierzytelniania Kerberos w programie SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Wszystkie aplikacje mają [nazwy główne usługi](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Serwer z systemem Łącznik i serwer z uruchomiona aplikacją są przyłączone do domeny i stanowią część tej samej domeny lub domen ufających. Aby uzyskać więcej informacji na temat dołączania do domeny, zobacz [Dołączanie komputera do domeny](https://technet.microsoft.com/library/dd807102.aspx).
* Serwer z systemem Łącznik ma dostęp do odczytu TokenGroupsGlobalAndUniversal atrybut dla użytkowników. To ustawienie domyślne mogło mieć wpływ na zabezpieczenie środowiska.

### <a name="configure-active-directory"></a>Konfigurowanie usługi Active Directory
Konfiguracja usługi Active Directory różni się w zależności od tego, czy łącznik serwera proxy aplikacji i serwer aplikacji znajdują się w tej samej domenie, czy nie.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Łącznik i serwer aplikacji w tej samej domenie
1. W usłudze Active Directory przejdź do pozycji**Użytkownicy i komputery** **narzędzia** > .
2. Wybierz serwer z uruchomionym łącznikiem.
3. Kliknij prawym przyciskiem myszy i wybierz polecenie**Delegowanie** **właściwości** > .
4. Wybierz pozycję **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. 
5. Wybierz pozycję **Użyj dowolnego protokołu uwierzytelniania**.
6. W obszarze **Usługi, do których to konto może przedstawiać poświadczenia delegowane,** dodaj wartość dla tożsamości nazwy SPN serwera aplikacji. Dzięki temu łącznik serwera proxy aplikacji personifikować użytkowników w ucho przeciwko aplikacjom zdefiniowanym na liście.

   ![Zrzut ekranu okna Właściwości łącznika-SVR](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Serwer łączników i aplikacji w różnych domenach
1. Aby uzyskać listę wymagań wstępnych do pracy z KCD w różnych domenach, zobacz [Delegowanie ograniczone kerberos między domenami](https://technet.microsoft.com/library/hh831477.aspx).
2. Użyj `principalsallowedtodelegateto` właściwości konta usługi (komputera lub dedykowanego konta użytkownika domeny) aplikacji sieci web, aby włączyć delegowanie uwierzytelniania Kerberos z serwera proxy aplikacji (łącznika). Serwer aplikacji działa w kontekście `webserviceaccount` serwera delegującego, a serwer delegowany jest `connectorcomputeraccount`. Uruchom poniższe polecenia na kontrolerze domeny (z systemem Windows Server 2012 R2 lub nowszym) `webserviceaccount`w domenie programu . Użyj nazw płaskich (nie nazwy UPN) dla obu kont.

   Jeśli `webserviceaccount` jest to konto komputera, użyj następujących poleceń:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Jeśli `webserviceaccount` jest to konto użytkownika, użyj następujących poleceń:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Konfigurowanie logowania jednokrotnego 
1. Opublikuj aplikację zgodnie z instrukcjami opisanymi w [publikacji aplikacji za pomocą serwera proxy aplikacji](application-proxy-add-on-premises-application.md). Upewnij się, że jako **metodę preauthentication**wybierz pozycję **Azure Active Directory** .
2. Po wyświetleniu aplikacji na liście aplikacji przedsiębiorstwa zaznacz ją i kliknij pozycję **Logowanie jednokrotne**.
3. Ustaw tryb logowania jednokrotnego na **Zintegrowane uwierzytelnianie systemu Windows**.  
4. Wprowadź **nazwę SPN aplikacji wewnętrznej** serwera aplikacji. W tym przykładzie spn dla naszej opublikowanej aplikacji jest http/www.contoso.com. Ten spn musi znajdować się na liście usług, do których łącznik może przedstawić delegowane poświadczenia. 
5. Wybierz **delegowaną tożsamość logowania** dla łącznika, który ma być używany w imieniu użytkowników. Aby uzyskać więcej informacji, zobacz [Praca z różnymi tożsamościami lokalnymi i tożsamościami w chmurze](#working-with-different-on-premises-and-cloud-identities)

   ![Zaawansowana konfiguracja aplikacji](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Samoso dla aplikacji innych niż Windows

Przepływ delegowania protokołu Kerberos w usłudze Azure AD Application Proxy rozpoczyna się, gdy usługa Azure AD uwierzytelnia użytkownika w chmurze. Po odebraniu żądania lokalnie łącznik serwera proxy aplikacji usługi Azure AD wystawia bilet protokołu Kerberos w imieniu użytkownika przez interakcję z lokalną usługą Active Directory. Ten proces jest określany jako delegowanie ograniczone kerberos (KCD). W następnej fazie żądanie jest wysyłane do aplikacji wewnętrznej bazy danych z tym biletem Kerberos. 

Istnieje kilka protokołów, które określają sposób wysyłania takich żądań. Większość serwerów innych niż Windows oczekuje negocjacji z SPNEGO. Ten protokół jest obsługiwany w usłudze Azure AD Application Proxy, ale jest domyślnie wyłączony. Serwer można skonfigurować dla SPNEGO lub standardowego KCD, ale nie dla obu.

Jeśli skonfigurujesz komputer łącznika dla spnego, upewnij się, że wszystkie inne łączniki w tej grupie łączników są również skonfigurowane z spnego. Aplikacje oczekujące standardowego KCD powinny być kierowane przez inne złącza, które nie są skonfigurowane dla spnego.
 

Aby włączyć spnego:

1. Otwórz wiersz polecenia, który działa jako administrator.
2. W wierszu polecenia uruchom następujące polecenia na serwerach łączników, które wymagają spnego.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Aby uzyskać więcej informacji na temat protokołu Kerberos, zobacz [Wszystko, co chcesz wiedzieć o delegowaniu ograniczeń protokołu Kerberos (KCD).](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)

Aplikacje inne niż Windows zazwyczaj nazwy użytkowników lub nazwy kont SAM zamiast adresów e-mail domeny. Jeśli ta sytuacja dotyczy aplikacji, należy skonfigurować pole tożsamości delegowanego logowania, aby połączyć tożsamości w chmurze z tożsamościami aplikacji. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Praca z różnymi tożsamościami lokalnymi i chmurowymi
Serwer proxy aplikacji zakłada, że użytkownicy mają dokładnie taką samą tożsamość w chmurze i lokalnie. Jednak w niektórych środowiskach, ze względu na zasady firmowe lub zależności aplikacji, organizacje mogą być musiały używać alternatywnych identyfikatorów do logowania. W takich przypadkach nadal można używać KCD do logowania jednokrotnego. Skonfiguruj **delegowaną tożsamość logowania** dla każdej aplikacji, aby określić, która tożsamość powinna być używana podczas wykonywania logowania jednokrotnego.  

Ta funkcja umożliwia wielu organizacjom, które mają różne tożsamości lokalne i tożsamości w chmurze, aby mieć logowanie typu SSO z chmury do aplikacji lokalnych bez konieczności wprowadzania przez użytkowników różnych nazw użytkowników i haseł. Obejmuje to organizacje, które:

* Mieć wiele domen wewnętrzniejoe@us.contoso.com joe@eu.contoso.com( , ) i jednejjoe@contoso.comdomeny w chmurze ( ).
* Mieć nie-routable nazwę domenyjoe@contoso.usawewnętrznie ( ) i prawnych jeden w chmurze.
* Nie używaj nazw domen wewnętrznie (joe)
* Używaj różnych aliasów lokalnie i w chmurze. Na przykład joe-johns@contoso.com w porównaniu zjoej@contoso.com  

Za pomocą serwera proxy aplikacji można wybrać tożsamość, której chcesz użyć, aby uzyskać bilet Protokołu Kerberos. To ustawienie jest dla aplikacji. Niektóre z tych opcji są odpowiednie dla systemów, które nie akceptują formatu adresu e-mail, inne są przeznaczone do alternatywnego logowania.

![Zrzut ekranu parametru tożsamości delegowanego logowania](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Jeśli używana jest delegowana tożsamość logowania, wartość może nie być unikatowa we wszystkich domenach lub lasach w organizacji. Można uniknąć tego problemu, publikując te aplikacje dwa razy przy użyciu dwóch różnych grup łączników. Ponieważ każda aplikacja ma inną grupę odbiorców użytkowników, można dołączyć do jej łączników do innej domeny.

### <a name="configure-sso-for-different-identities"></a>Konfigurowanie identyfikatora SSO dla różnych tożsamości
1. Skonfiguruj ustawienia usługi Azure AD Connect, aby główną tożsamością był adres e-mail (poczta). Odbywa się to w ramach procesu dostosowywania, zmieniając główne pole **Nazwa użytkownika** w ustawieniach synchronizacji. Te ustawienia określają również sposób logowania użytkowników do usługi Office365, urządzeń z systemem Windows10 i innych aplikacji, które używają usługi Azure AD jako magazynu tożsamości.  
   ![Zrzut ekranu identyfikujący użytkowników — główna nazwa użytkownika](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. W ustawieniach konfiguracji aplikacji, którą chcesz zmodyfikować, wybierz **delegowaną tożsamość logowania,** która ma być używana:

   * Główna nazwa użytkownika (na przykład joe@contoso.com)
   * Alternatywna nazwa głównego użytkownika joed@contoso.local(na przykład )
   * Nazwa użytkownika w nazwie głównej użytkownika (na przykład joe)
   * Nazwa użytkownika w nazwach głównych alternatywnych użytkowników (na przykład joed)
   * Lokalna nazwa konta SAM (w zależności od konfiguracji kontrolera domeny)

### <a name="troubleshooting-sso-for-different-identities"></a>Rozwiązywanie problemów z identyfikatorem SSO dla różnych tożsamości
Jeśli wystąpił błąd w procesie logowania przy logowaniu logowania, pojawia się on w dzienniku zdarzeń maszyny łącznika, jak wyjaśniono w [polu Rozwiązywanie problemów](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Jednak w niektórych przypadkach żądanie jest pomyślnie wysyłane do aplikacji wewnętrznej bazy danych, podczas gdy ta aplikacja odpowiada w różnych innych odpowiedzi HTTP. Rozwiązywanie problemów z tymi przypadkami należy rozpocząć od zbadania numeru zdarzenia 24029 na komputerze łącznika w dzienniku zdarzeń sesji serwera proxy aplikacji. Tożsamość użytkownika, która została użyta do delegowania pojawia się w polu "użytkownik" w szczegółach zdarzenia. Aby włączyć dziennik sesji, wybierz pozycję **Pokaż dzienniki analityczne i debugowanie** w menu widoku podglądu zdarzeń.

## <a name="next-steps"></a>Następne kroki

* [Jak skonfigurować aplikację serwera proxy aplikacji do używania delegowania ograniczonego protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md)


Aby zapoznać się z najnowszymi informacjami i aktualizacjami, zobacz [blog dotyczący serwera proxy aplikacji](https://blogs.technet.com/b/applicationproxyblog/)
