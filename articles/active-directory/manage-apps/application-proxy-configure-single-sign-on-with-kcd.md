---
title: Logowanie jednokrotne przy użyciu serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Uwzględniono również sposób zapewnienia logowania jednokrotnego przy użyciu serwera Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 506ff0bce0b68b1477f27f913bd3fe119e36cca1
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Ograniczone delegowanie protokołu Kerberos dla rejestracji jednokrotnej do aplikacji przy użyciu serwera Proxy aplikacji

Zapewnia rejestrację jednokrotną dla lokalnych aplikacji opublikowanych przy użyciu serwera Proxy aplikacji, które są zabezpieczone przy użyciu zintegrowanego uwierzytelniania systemu Windows. Te aplikacje wymagają biletu protokołu Kerberos dla dostępu. Serwer Proxy aplikacji używa delegowanie ograniczone protokołu Kerberos (KCD) do obsługi tych aplikacji. 

Można włączyć logowanie jednokrotne do aplikacji przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA) przez nadanie uprawnień łączniki serwera Proxy aplikacji w usłudze Active Directory umożliwiające personifikowanie użytkowników. Łączniki Użyj tego uprawnienia do wysyłania i odbierania tokenów w ich imieniu.

## <a name="how-single-sign-on-with-kcd-works"></a>Jak rejestracji jednokrotnej z KCD działa
Ten diagram opisano przepływ, gdy użytkownik próbuje uzyskać dostępu do aplikacji lokalnych, która używa funkcji IWA.

![Diagram przepływu uwierzytelniania firmy Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Użytkownik musi wprowadzić adres URL, aby uzyskać dostęp do aplikacji lokalnych przy użyciu serwera Proxy aplikacji.
2. Serwer Proxy aplikacji przekierowuje żądanie do usługi uwierzytelniania usługi Azure AD do preauthenticate. W tym momencie usługi Azure AD stosuje odpowiednie uwierzytelnianie, a zasady autoryzacji, takie jak uwierzytelnianie wieloskładnikowe. Jeśli użytkownik jest weryfikowane, usługi Azure AD tworzy token i wysyła go do użytkownika.
3. Użytkownik przekazuje token do serwera Proxy aplikacji.
4. Serwer Proxy aplikacji sprawdza poprawność tokenu i pobiera główną nazwę użytkownika (UPN) z niego, a następnie wysyła żądanie, głównej nazwy użytkownika i nazwy usługi (SPN) z łącznikiem za pośrednictwem uwierzytelnionego dually bezpiecznego kanału.
5. Łącznik przeprowadza negocjowanie delegowanie ograniczone protokołu Kerberos (KCD) z lokalnej usługi AD, personifikacji użytkownika w celu pobrania tokenu protokołu Kerberos do aplikacji.
6. Usługi Active Directory wysyła ten token protokołu Kerberos do łącznika aplikacji.
7. Łącznik wysyła oryginalne żądanie do serwera aplikacji, za pomocą tokenu protokołu Kerberos, otrzymanego z usługi Active Directory.
8. Aplikacja wysyła odpowiedź do łącznik, który jest następnie zwracany do serwera Proxy aplikacji usługi i w końcu dla użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z logowanie jednokrotne dla aplikacji IWA, upewnij się, że środowisko jest gotowe z poniższych ustawień i konfiguracji:

* Aplikacji, takich jak aplikacje sieci Web programu SharePoint są ustawiane do korzystania ze zintegrowanego uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [włączyć obsługę uwierzytelniania Kerberos](https://technet.microsoft.com/library/dd759186.aspx), lub dla programu SharePoint, zobacz [planowanie uwierzytelniania Kerberos w programie SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Wszystkie Twoje aplikacje mają [główne nazwy usług](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Serwer z uruchomioną łącznika i serwera, na którym uruchomiono aplikację są przyłączone do domeny, jest częścią tej samej domenie lub domenach ufających. Aby uzyskać więcej informacji dotyczących przyłączania do domeny, zobacz [przyłączyć komputer do domeny](https://technet.microsoft.com/library/dd807102.aspx).
* Serwera z uruchomionym programem łącznika ma dostęp do odczytu atrybutu TokenGroupsGlobalAndUniversal dla użytkowników. To ustawienie domyślne może negatywnie przez ograniczenie funkcjonalności środowiska zabezpieczeń.

### <a name="configure-active-directory"></a>Konfigurowanie usługi Active Directory
Konfiguracja usługi Active Directory różni się w zależności od tego, czy z łącznika serwera Proxy aplikacji i serwera aplikacji znajdują się w tej samej domenie lub nie.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Łącznik i serwera aplikacji w tej samej domenie
1. W usłudze Active Directory, przejdź do **narzędzia** > **użytkownicy i komputery**.
2. Wybierz serwer z uruchomioną łącznika.
3. Kliknij prawym przyciskiem myszy i wybierz **właściwości** > **delegowania**.
4. Wybierz **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. 
5. W obszarze **usług, do których to konto może okazywać delegowane poświadczenia** dodać wartość tożsamością SPN serwera aplikacji. Dzięki temu łącznika serwera Proxy aplikacji personifikować użytkowników w usłudze AD dla aplikacji zdefiniowanej na liście.

   ![Zrzut ekranu okna właściwości SVR łącznika](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Łącznik i serwera aplikacji w różnych domenach
1. Aby uzyskać listę wymagań wstępnych dotyczących pracy z KCD między domenami, zobacz [ograniczone delegowanie protokołu Kerberos między domenami](https://technet.microsoft.com/library/hh831477.aspx).
2. Użyj `principalsallowedtodelegateto` właściwości na serwerze łącznika, aby włączyć serwer Proxy aplikacji delegować do serwera łącznika. Serwer aplikacji `sharepointserviceaccount` , a serwer delegujące `connectormachineaccount`. W systemie Windows 2012 R2 Użyj tego kodu na przykład:

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

Sharepointserviceaccount może być SPS konta komputera lub konta usługi, na którym uruchomiono SPS puli aplikacji.

## <a name="configure-single-sign-on"></a>Konfigurowanie rejestracji jednokrotnej 
1. Publikowanie aplikacji zgodnie z instrukcjami opisanego w [publikowania aplikacji za pomocą serwera Proxy aplikacji](application-proxy-publish-azure-portal.md). Upewnij się wybrać **usługi Azure Active Directory** jako **metoda uwierzytelniania wstępnego**.
2. Gdy aplikacji zostanie wyświetlony na liście aplikacji przedsiębiorstwa, zaznacz go i kliknij **logowanie jednokrotne**.
3. Ustaw tryb znak pojedynczego **zintegrowane uwierzytelnianie systemu Windows**.  
4. Wprowadź **wewnętrznych aplikacji głównej nazwy usługi** serwera aplikacji. W tym przykładzie nazwa SPN dla opublikowanych aplikacji jest http/www.contoso.com. Tę nazwę SPN musi znajdować się na liście usług, do których łącznik może okazywać delegowane poświadczenia. 
5. Wybierz **delegowane tożsamości logowania** dla łącznika do użycia w imieniu użytkowników. Aby uzyskać więcej informacji, zobacz [Praca z różnych lokalnymi i tożsamościami w chmurze](#Working-with-different-on-premises-and-cloud-identities)

   ![Konfiguracja zaawansowanych aplikacji](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Logowanie Jednokrotne dla aplikacji z systemem innym niż Windows
Przepływ delegowania protokołu Kerberos w serwera Proxy aplikacji usługi Azure AD rozpoczyna się, gdy usługa Azure AD uwierzytelnia użytkowników w chmurze. Gdy żądanie dociera do lokalnej, łącznika serwera Proxy aplikacji usługi Azure AD wystawia biletu protokołu Kerberos w imieniu użytkownika przez interakcji z lokalnej usługi Active Directory. Ten proces jest określany jako protokołu Kerberos ograniczonego delegowania (KCD). W następnej fazy żądanie jest wysyłane do aplikacji zaplecza z tego biletu protokołu Kerberos. Istnieje kilka protokołów, które definiują sposób wysyłania takich żądań. Większość serwerów z systemem innym niż Windows oczekiwać Negotiate/SPNego, która jest teraz obsługiwana na serwera Proxy aplikacji usługi Azure AD.

Aby uzyskać więcej informacji o protokole Kerberos, zobacz [wszystkich chcesz wiedzieć o protokołu Kerberos ograniczonego delegowania (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Aplikacje systemu Windows bez zwykle użytkownika nazwy użytkowników lub nazwy konta SAM zamiast domeny adresów e-mail. Jeśli taka sytuacja ma zastosowanie do aplikacji, należy skonfigurować pole tożsamości logowania delegowanego nawiązywania połączenia z Twoich tożsamości aplikacji tożsamości w chmurze. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Praca z różnych lokalnymi i tożsamościami w chmurze
Serwer Proxy aplikacji założenia, że użytkownicy dokładnie taką samą tożsamość w chmurze i lokalnie. Jeśli nie jest wielkość liter, można nadal używać KCD dla logowania jednokrotnego. Skonfiguruj **delegowane tożsamości logowania** dla każdej aplikacji można określić tożsamości, która będzie używana podczas rejestracji jednokrotnej.  

Ta funkcja umożliwia wiele organizacji, które mają różne lokalnymi i tożsamości w chmurze ma rejestracji Jednokrotnej z chmury do lokalnych aplikacji bez konieczności przez użytkowników wprowadzić różne nazwy użytkowników i hasła. Dotyczy to również organizacjom który:

* Wewnętrznie mają kilka domen (joe@us.contoso.com, joe@eu.contoso.com) i jednej domeny w chmurze (joe@contoso.com).
* Wewnętrznie mają nazwy domeny bez obsługi routingu (joe@contoso.usa) i prawnych w chmurze.
* Nie używaj nazw domen wewnętrznie (Jan)
* Użyj innych aliasów lokalnych i w chmurze. Na przykład joe-johns@contoso.com vs. joej@contoso.com  

Dzięki serwerowi Proxy aplikacji można wybrać tożsamość używaną do uzyskania biletu Kerberos. To ustawienie jest na aplikację. Niektóre z tych opcji nadają się do systemów, które nie akceptują format adresu e-mail, inne są zaprojektowane dla alternatywnej nazwy logowania.

![Zrzut ekranu parametr tożsamości delegowanego logowania](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Jeśli używana jest tożsamość delegowanego logowania, wartość nie muszą być unikatowe dla wszystkich domen i lasów w Twojej organizacji. Aby uniknąć tego problemu, należy publikowania aplikacji, te dwa razy przy użyciu dwóch różnych grup łącznika. Ponieważ każda aplikacja ma odbiorców inny użytkownik, możesz dołączyć jego łączniki do innej domeny.

### <a name="configure-sso-for-different-identities"></a>Konfigurowanie logowania jednokrotnego dla różnych tożsamości
1. Konfigurowanie ustawień usługi Azure AD Connect tożsamość główna jest adres e-mail (poczta). Odbywa się w ramach procesu Dostosuj zmieniając **główna nazwa użytkownika** w ustawieniach synchronizacji. Te ustawienia określają, jak użytkownicy logują się do usługi Office 365, urządzeń Windows10 i inne aplikacje, które używają usługi Azure AD jako magazynu ich tożsamości.  
   ![Identyfikowanie użytkowników zrzut ekranu — Lista rozwijana główna nazwa użytkownika](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. W ustawieniach konfiguracji aplikacji dla aplikacji, które chcesz zmodyfikować, wybierz **delegowane tożsamości logowania** do użycia:

   * Nazwa główna użytkownika (na przykład joe@contoso.com)
   * Alternatywna nazwa główna użytkownika (na przykład joed@contoso.local)
   * Nazwa użytkownika, część główna nazwa użytkownika (na przykład Jan)
   * Część nazwy użytkownika alternatywna nazwa główna użytkownika (na przykład joed)
   * Nazwa konta SAM lokalnymi (zależy od konfiguracji kontrolera domeny)

### <a name="troubleshooting-sso-for-different-identities"></a>Rozwiązywanie problemów z logowania jednokrotnego dla różnych tożsamości
Jeśli występuje błąd w procesie logowania jednokrotnego, wydaje się w dzienniku zdarzeń maszyny łącznika zgodnie z objaśnieniem w [Rozwiązywanie problemów](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Jednak w niektórych przypadkach, żądanie jest pomyślnie wysłane do aplikacji zaplecza podczas odpowiada tej aplikacji w różnych odpowiedzi HTTP. Rozwiązywanie problemów z tych przypadkach należy zacząć od badanie numer zdarzenia 24029 na maszynie łącznika w dzienniku zdarzeń w sesji serwera Proxy aplikacji. Tożsamość użytkownika, którego użyto do delegowania pojawia się w polu "użytkownika" w ramach szczegółów zdarzenia. Aby włączyć dziennik sesji, wybierz **wyświetlanie analityczne i debugowania dzienniki** w menu Widok podglądu zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

* [Jak skonfigurować serwer Proxy aplikacji aplikacji na używanie ograniczone delegowanie protokołu Kerberos](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Rozwiązywanie problemów, które masz problem z serwerem Proxy aplikacji](../active-directory-application-proxy-troubleshoot.md)


Aby zapoznać się z najnowszymi informacjami i aktualizacjami, zobacz [blog dotyczący serwera proxy aplikacji](http://blogs.technet.com/b/applicationproxyblog/)

