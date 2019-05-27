---
title: Logowanie jednokrotne przy użyciu serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Omówiono sposób zapewnienia logowania jednokrotnego przy użyciu serwera Proxy aplikacji usługi Azure AD.
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
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 545906af882be6e53297bf7a9ff2cd12e86d55f0
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859623"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Ograniczone delegowanie protokołu Kerberos do logowania jednokrotnego do aplikacji przy użyciu serwera Proxy aplikacji

Możesz podać jednokrotne logowanie lokalne aplikacje opublikowane za pośrednictwem serwera Proxy aplikacji, które są zabezpieczone przy użyciu zintegrowanego uwierzytelniania Windows. Te aplikacje wymagają biletu protokołu Kerberos, aby uzyskać dostęp. Serwer Proxy aplikacji używa delegowanie ograniczone protokołu Kerberos (KCD) do obsługi tych aplikacji. 

Można włączyć logowanie jednokrotne do aplikacji przy użyciu zintegrowanego Windows Authentication (Zintegrowane), zapewniając uprawnieniu łączników serwera Proxy aplikacji w usłudze Active Directory na personifikowanie użytkowników. Łączniki Użyj tego uprawnienia do wysyłania i odbierania tokenów w ich imieniu.

## <a name="how-single-sign-on-with-kcd-works"></a>Jak logowanie jednokrotne za pomocą działania ograniczonego delegowania protokołu Kerberos
Ten diagram opisano przepływ, gdy użytkownik próbuje uzyskać dostęp w aplikacji lokalnej, która używa IWA.

![Diagram przepływu uwierzytelniania AAD firmy Microsoft](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Użytkownik musi wprowadzić adres URL, aby uzyskać dostęp w aplikacji lokalnej za pośrednictwem serwera Proxy aplikacji.
2. Serwer Proxy aplikacji przekierowuje żądanie do usług uwierzytelniania usługi Azure AD, aby preauthenticate. W tym momencie usługa Azure AD ma zastosowanie wszelkich mających zastosowanie uwierzytelniania i zasady autoryzacji, takie jak uwierzytelnianie wieloskładnikowe. Jeśli użytkownik zostanie zweryfikowana, usługi Azure AD tworzy token i wysyła go do użytkownika.
3. Użytkownik przekazuje token do serwera Proxy aplikacji.
4. Serwer Proxy aplikacji weryfikuje token i pobiera główną nazwę użytkownika (UPN) z niego, a następnie Connector pobiera nazwy UPN i głównej nazwy usługi (SPN) za pośrednictwem uwierzytelnionych dually bezpiecznego kanału.
5. Łącznik negocjowanie delegowanie ograniczone protokołu Kerberos (KCD) przy użyciu usługi AD, personifikacji użytkownika w celu pobrania tokenu protokołu Kerberos do aplikacji lokalnych.
6. Usługi Active Directory wysyła ten token protokołu Kerberos, do łącznika aplikacji.
7. Łącznik wysyła oryginalne żądanie do serwera aplikacji przy użyciu tokenu protokołu Kerberos, otrzymanego z usługi AD.
8. Aplikacja wysyła odpowiedź do łącznika, która jest zwracana do serwera Proxy aplikacji usługi a na koniec do użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z logowania jednokrotnego dla aplikacji IWA, upewnij się, że środowisko jest gotowe przy użyciu następujących ustawień i konfiguracji:

* Aplikacji, takich jak aplikacje sieci Web programu SharePoint są ustawione do korzystania ze zintegrowanego uwierzytelniania Windows. Aby uzyskać więcej informacji, zobacz [Włączanie obsługi uwierzytelniania Kerberos](https://technet.microsoft.com/library/dd759186.aspx), lub dla programu SharePoint, zobacz [planowanie uwierzytelniania Kerberos w programie SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Wszystkie Twoje aplikacje mają [główne nazwy usług](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Serwer z uruchomionym łącznikiem usługi i serwerze z uruchomioną aplikację, są przyłączone do domeny i jest częścią tej samej domenie lub domenach ufających. Aby uzyskać więcej informacji na temat przyłączania do domeny, zobacz [przyłączyć komputer do domeny](https://technet.microsoft.com/library/dd807102.aspx).
* Serwer z uruchomionym łącznikiem usługi ma dostęp do odczytu atrybutu TokenGroupsGlobalAndUniversal dla użytkowników. To ustawienie domyślne może wpływ przez wzmacnianie ochrony środowiska zabezpieczeń.

### <a name="configure-active-directory"></a>Konfigurowanie usługi Active Directory
Konfiguracja usługi Active Directory różni się w zależności od tego, czy Twój łącznik serwera Proxy aplikacji i serwera aplikacji znajdują się w tej samej domenie lub nie.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Łącznik i serwera aplikacji w tej samej domenie
1. W usłudze Active Directory, przejdź do **narzędzia** > **użytkownicy i komputery**.
2. Wybierz serwer z uruchomionym łącznikiem usługi.
3. Kliknij prawym przyciskiem myszy i wybierz **właściwości** > **delegowania**.
4. Wybierz **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. 
5. Wybierz **Użyj dowolnego protokołu uwierzytelniania**.
6. W obszarze **usług, do których to konto może przedstawiać delegowane poświadczenia** dodać wartość dla tożsamością SPN serwera aplikacji. Dzięki temu łącznika serwera Proxy aplikacji na personifikowanie użytkowników w AD dla aplikacji zdefiniowanej na liście.

   ![Zrzut ekranu okna właściwości SVR łącznika](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Łącznik i serwera aplikacji w różnych domenach
1. Aby uzyskać listę wymagań wstępnych dotyczących pracy z ograniczonego delegowania protokołu Kerberos między domenami, zobacz [ograniczonego delegowania protokołu Kerberos w domenach](https://technet.microsoft.com/library/hh831477.aspx).
2. Użyj `principalsallowedtodelegateto` właściwości na serwerze łącznika, aby włączyć serwer Proxy aplikacji delegować do serwera łącznika. Serwer aplikacji `sharepointserviceaccount` i delegowania server `connectormachineaccount`. Dla systemu Windows 2012 R2 należy użyć następującego kodu, na przykład:

```powershell
$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
```

`sharepointserviceaccount` może to być konto komputera dodatki Service Pack lub konto usługi, w którym jest uruchomiona pula aplikacji dodatki Service Pack.

## <a name="configure-single-sign-on"></a>Konfigurowanie logowania jednokrotnego 
1. Opublikowanie aplikacji zgodnie z instrukcji przedstawionych w temacie [publikowania aplikacji za pomocą serwera Proxy aplikacji](application-proxy-add-on-premises-application.md). Upewnij się, że wybrano **usługi Azure Active Directory** jako **metoda uwierzytelniania wstępnego**.
2. Gdy aplikacja zostanie wyświetlony na liście aplikacji dla przedsiębiorstw, zaznacz go i kliknij **logowanie jednokrotne**.
3. Ustaw tryb logowania pojedynczego **zintegrowane uwierzytelnianie Windows**.  
4. Wprowadź **główna nazwa usługi aplikacji wewnętrznej** serwera aplikacji. W tym przykładzie nazwy SPN dla opublikowanych aplikacji jest http/www.contoso.com. Tę nazwę SPN, musi znajdować się na liście usług, do których łącznik może przedstawiać delegowane poświadczenia. 
5. Wybierz **delegowana tożsamość logowania** dla łącznika do używania w imieniu użytkowników. Aby uzyskać więcej informacji, zobacz [Praca z różnych lokalnych i tożsamości w chmurze](#working-with-different-on-premises-and-cloud-identities)

   ![Konfiguracja zaawansowanych aplikacji](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Logowanie Jednokrotne do aplikacji innych niż Windows

Przepływ delegowania protokołu Kerberos na serwerze Proxy aplikacji usługi Azure AD rozpoczyna się, gdy usługa Azure AD uwierzytelnia użytkownika w chmurze. Po odebraniu żądania lokalnego łącznika serwera Proxy aplikacji usługi Azure AD wystawia bilet protokołu Kerberos w imieniu użytkownika przez interakcję z lokalnej usługi Active Directory. Ten proces jest określany jako protokołu Kerberos ograniczone delegowanie (ograniczonego delegowania protokołu Kerberos). W następnej fazie wysłaniu żądania do aplikacji wewnętrznej bazy danych z tego biletu protokołu Kerberos. 

Istnieje kilka protokołów, które definiują sposób wysyłania takich żądań. Należy oczekiwać, że większość serwerów Windows bez negocjowania z SPNEGO. Ten protokół jest obsługiwana na serwer Proxy aplikacji usługi Azure AD, ale jest domyślnie wyłączona. Serwer może być skonfigurowany dla SPNEGO lub standardowa ograniczonego delegowania protokołu Kerberos, ale nie oba.

Jeśli skonfigurujesz maszynie łącznika dla SPNEGO upewnij się, czy innych łączników w tej grupie łącznika konfigurowane są również SPNEGO. Aplikacje oczekiwano standardowa ograniczonego delegowania protokołu Kerberos należy kierowane za pośrednictwem innych łączników, które nie są skonfigurowane dla SPNEGO.
 

Aby włączyć SPNEGO:

1. Otwórz wiersz polecenia, która działa jako administrator.
2. W wierszu polecenia, uruchom następujące polecenia na serwery łącznika, które wymagają SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Aby uzyskać więcej informacji na temat protokołu Kerberos, zobacz [wszystkie chcesz wiedzieć o Kerberos ograniczone delegowanie (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Aplikacje inne niż Windows zazwyczaj nazw użytkownika lub nazwy konta SAM zamiast domeny adresów e-mail. Taka sytuacja ma zastosowanie do aplikacji, należy skonfigurować pole tożsamości delegowanej logowania nawiązać tożsamości w chmurze usługi tożsamości aplikacji. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Praca z różnych lokalnych i tożsamości w chmurze
Serwer Proxy aplikacji przyjęto założenie, że użytkownicy mają dokładnie ta sama tożsamość w chmurze i lokalnych. Jeśli nie jest tak, możesz nadal używać ograniczonego delegowania protokołu Kerberos dla logowania jednokrotnego. Konfigurowanie **delegowana tożsamość logowania** dla każdej aplikacji określić tożsamość będzie używana podczas przeprowadzania rejestracji jednokrotnej.  

Ta funkcja umożliwia wiele organizacji, które mają różne lokalne i tożsamości w chmurze miał logowania jednokrotnego z chmury do aplikacji lokalnych bez wymagania od użytkowników wprowadzić inne nazwy użytkownika i hasła. Dotyczy to również organizacjom który:

* Wewnętrznie mają kilka domen (joe@us.contoso.com, joe@eu.contoso.com) i jednej domeny w chmurze (joe@contoso.com).
* Wewnętrznie mają nazwy domeny bez obsługi routingu (joe@contoso.usa) i prawne jeden w chmurze.
* Nie używaj nazw domen wewnętrznie (Jan)
* Użyj innych aliasów, lokalnie i w chmurze. Na przykład joe-johns@contoso.com programu vs. joej@contoso.com  

Dzięki serwerowi Proxy aplikacji możesz wybrać tożsamość do użycia w celu uzyskania biletu protokołu Kerberos. To ustawienie jest na aplikację. Niektóre z tych opcji nadają się dla systemów, które nie akceptują format adresu e-mail, inne są zaprojektowane dla alternatywnej nazwy logowania.

![Zrzut ekranu parametrów tożsamości delegowanej logowania](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Jeśli delegowana tożsamość logowania jest używany, wartość nie muszą być unikatowe dla wszystkich domen i lasów w Twojej organizacji. Aby uniknąć tego problemu, należy publikowania aplikacji, te dwa razy, używając dwóch różnych grup łącznika. Ponieważ każda aplikacja ma odbiorców innego użytkownika, musisz dołączyć swoje łączniki do innej domeny.

### <a name="configure-sso-for-different-identities"></a>Konfigurowanie logowania jednokrotnego dla różnych tożsamości
1. Skonfiguruj ustawienia Azure AD Connect, więc tożsamość główna jest adres e-mail (poczta). To jest wykonywane jako część procesu dostosowywanie, zmieniając **główna nazwa użytkownika** pola w ustawieniach synchronizacji. Te ustawienia określają, jak użytkownicy logują się do usługi Office 365, Windows10 urządzeń i inne aplikacje, które używają usługi Azure AD jako ich magazynu tożsamości.  
   ![Identyfikowanie zrzut ekranu użytkowników — lista rozwijana główna nazwa użytkownika](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. W ustawieniach konfiguracji aplikacji dla aplikacji, które chcesz zmodyfikować, wybierz **delegowana tożsamość logowania** ma być używany:

   * Nazwa główna użytkownika (na przykład joe@contoso.com)
   * Alternatywna nazwa główna użytkownika (na przykład joed@contoso.local)
   * Część nazwy głównej nazwy użytkownika (na przykład Jan)
   * Część nazwy alternatywnej nazwy głównej nazwy użytkownika (na przykład joed)
   * Nazwa konta SAM lokalnych (zależy od konfiguracji kontrolera domeny)

### <a name="troubleshooting-sso-for-different-identities"></a>Rozwiązywanie problemów z logowania jednokrotnego dla różnych tożsamości
Jeśli występuje błąd w procesie logowania jednokrotnego, pojawia się w dzienniku zdarzeń maszyny łącznik zgodnie z objaśnieniem w [Rozwiązywanie problemów](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Jednak w niektórych przypadkach żądanie zostanie pomyślnie wysłane do aplikacji zaplecza podczas tej aplikacji odpowiedzi w różnych odpowiedzi HTTP. Rozwiązywanie problemów z tych przypadkach na początek, sprawdzając numer zdarzenia 24029 na maszynie łącznika w dzienniku zdarzeń w sesji serwera Proxy aplikacji. Tożsamość użytkownika, którego użyto dla celów delegacji pojawią się w polu "user" w ramach szczegółów zdarzenia. Aby włączyć funkcję dziennika sesji, należy wybrać **Pokaż analityczne i debugowania dzienniki** w menu Widok Podgląd zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

* [Jak skonfigurować aplikację serwera Proxy aplikacji usługi do korzystania z ograniczonego delegowania protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Rozwiązywanie problemów z serwerem Proxy aplikacji](application-proxy-troubleshoot.md)


Aby zapoznać się z najnowszymi informacjami i aktualizacjami, zobacz [blog dotyczący serwera proxy aplikacji](https://blogs.technet.com/b/applicationproxyblog/)

