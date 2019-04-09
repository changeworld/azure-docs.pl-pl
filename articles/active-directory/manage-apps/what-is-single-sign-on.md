---
title: Logowanie jednokrotne do aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać jedną metodę logowania jednokrotnego, podczas konfigurowania aplikacji w usłudze Azure Active Directory (Azure AD). Użyj logowania jednokrotnego, dzięki czemu użytkownicy nie muszą zapamiętywać hasła do każdej aplikacji i uprościć administrowanie zarządzania kontami.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0357b7f421da753f102d2f05eaf8021cfc74aa2c
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057198"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory

Logowanie jednokrotne (SSO) dodaje zabezpieczenia i wygodę gdy użytkownicy logują się do aplikacji w usłudze Azure Active Directory (Azure AD). W tym artykule opisano pojedynczej metody logowania jednokrotnego i pomaga w wyborze najbardziej odpowiedniej metody logowania jednokrotnego, podczas konfigurowania aplikacji.

- **Za pomocą logowania jednokrotnego**użytkownicy logują się drugi raz z jednego konta na urządzeniach przyłączonych do domeny dostępu do zasobów, oprogramowanie jako usługa (SaaS) aplikacje, firmy i aplikacji sieci web. Po zalogowaniu się użytkownika można uruchamiać aplikacje z poziomu portalu usługi Office 365 lub Azure AD MyApps panelu dostępu. Administratorzy mogą scentralizowane zarządzanie kontami użytkowników i automatycznie dodawała lub usuwała użytkownikom dostęp do aplikacji na podstawie przynależności do grupy.

- **Bez rejestracji jednokrotnej**, użytkownicy muszą zapamiętywać hasła specyficzne dla aplikacji i zaloguj się do każdej aplikacji. Dział IT musi pracowników do tworzenia i aktualizowania konta użytkowników dla każdej aplikacji, takich jak usługi Office 365, Box i Salesforce. Użytkownicy potrzebują do haseł oraz poświęcić czas, aby zalogować się do poszczególnych aplikacji.

## <a name="choosing-a-single-sign-on-method"></a>Wybieranie jednej metody logowania jednokrotnego

Istnieje kilka sposobów, aby skonfigurować aplikację pod kątem logowania jednokrotnego. Wybieranie jednej metody logowania jednokrotnego, zależy od tego, jak aplikacja jest skonfigurowana do uwierzytelniania.

- Aplikacje w chmurze umożliwiają OpenID Connect, OAuth, SAML, opartego na hasłach, połączone lub wyłączone metody logowania jednokrotnego. 
- Aplikacje lokalne uwierzytelniania można użyć opartego na hasłach, zintegrowane Windows opartej na nagłówkach, połączone lub wyłączone metody logowania jednokrotnego. Opcje w środowisku lokalnym działa, gdy aplikacje są skonfigurowane dla serwera Proxy aplikacji.

Ten schemat blokowy pomaga określić, które pojedynczej metody logowania jednokrotnego jest najlepszego w danej sytuacji.

![Wybierz metodę rejestracji jednokrotnej](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Poniższa tabela zawiera podsumowanie pojedynczej metody logowania jednokrotnego i linki, aby uzyskać więcej szczegółów.

| Metoda rejestracji jednokrotnej | Typy aplikacji | Kiedy stosować |
| :------ | :------- | :----- |
| [OpenID Connect i OAuth](#openid-connect-and-oauth) | Tylko w chmurze | Podczas tworzenia nowej aplikacji za pomocą protokołu OpenID Connect i OAuth. Ten protokół upraszcza konfigurację aplikacji, jest łatwy w użyciu zestawy SDK i pozwala aplikacji używać MS Graph.
| [SAML](#saml-sso) | w chmurze i lokalnych | Wybierz SAML, w miarę możliwości istniejących aplikacji, które nie korzystają z protokołu OpenID Connect i OAuth. SAML działa w przypadku aplikacji, które przeprowadzają uwierzytelnianie przy użyciu jednej z protokołów języka SAML.|
| [Oparte na haśle](#password-based-sso) | w chmurze i lokalnych | Wybierz opartego na hasłach, gdy aplikacja uwierzytelnia się za pomocą nazwy użytkownika i hasła. Oparte na hasłach logowanie jednokrotne umożliwia bezpieczną aplikację przechowywanie i powtarzanie haseł przy użyciu rozszerzenia przeglądarki sieci web lub aplikacji mobilnej. Ta metoda wykorzystuje istniejący proces logowania udostępniany przez aplikację, ale umożliwia administratorowi Zarządzanie hasłami. |
| [Połączone](#linked-sso) | w chmurze i lokalnych | Wybierz połączone logowanie jednokrotne, gdy aplikacja jest skonfigurowana na potrzeby logowania jednokrotnego w innej usłudze dostawcy tożsamości. Ta opcja nie powoduje dodania logowanie jednokrotne do aplikacji. Jednak aplikacja może już logowanie jednokrotne implementowane za pomocą innej usługi, takie jak Active Directory Federation Services.|
| [Disabled (Wyłączony)](#disabled-sso) | w chmurze i lokalnych | Wybierz wyłączone rejestracji jednokrotnej, jeśli aplikacja nie jest gotowe do skonfigurowania dla logowania jednokrotnego. Użytkownicy muszą wprowadzić swoją nazwę użytkownika i hasło, za każdym razem, gdy ich uruchomić tę aplikację.|
| [Zintegrowane uwierzytelnianie systemu Windows](#integrated-windows-authentication-iwa-sso) | tylko lokalnie | Wybierz IWA logowania jednokrotnego dla aplikacji, które używają [zintegrowane Windows Authentication (Zintegrowane)](/aspnet/web-api/overview/security/integrated-windows-authentication), lub aplikacji obsługujących oświadczenia. IWA łączników serwera Proxy aplikacji usługi użytku delegowanie ograniczone protokołu Kerberos (KCD) do uwierzytelniania użytkowników w aplikacji. | 
| [Na podstawie nagłówka](#header-based-sso) | tylko lokalnie | Jeśli aplikacja używa nagłówków do uwierzytelniania, należy użyć opartej na nagłówkach logowania jednokrotnego. Opartej na nagłówkach logowanie jednokrotne wymaga oprogramowanie PingAccess dla usługi Azure AD. Serwer Proxy aplikacji używa usługi Azure AD można uwierzytelnić użytkownika, a następnie przekazuje ruch za pośrednictwem usługi łącznika.  | 

## <a name="openid-connect-and-oauth"></a>OpenID Connect i OAuth
Podczas tworzenia nowych aplikacji, należy użyć nowoczesnych protokołów, takich jak OpenID Connect i OAuth, aby osiągnąć najlepsze logowania jednokrotnego dla aplikacji na wielu platformach urządzeń. OAuth pozwala użytkownikom lub administratorom [wyrazić zgody](configure-user-consent.md) dla chronionych zasobów, takich jak [MS Graph](/graph/overview). Firma Microsoft zapewnia proste ustalenie [zestawów SDK](../develop/reference-v2-libraries.md) dla aplikacji, a ponadto aplikacja nie będzie gotowe do użycia [MS Graph](/graph/overview).

Aby uzyskać więcej informacji, zobacz:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [Uwierzytelnianie OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>Logowanie jednokrotne SAML
Za pomocą **SAML logowania jednokrotnego**, usługa Azure AD uwierzytelnia się do aplikacji przy użyciu konta usługi Azure AD. Usługa Azure AD komunikuje się informacji logowania jednokrotnego do aplikacji za pośrednictwem protokołu połączenia. Za pomocą opartej na SAML logowania jednokrotnego można mapować użytkowników do ról aplikacji, na podstawie reguł zdefiniowanych w swoje oświadczenia języka SAML.

Wybierz opartej na SAML logowania jednokrotnego, jeśli aplikacja obsługuje tę funkcję.


Opartej na SAML logowania jednokrotnego jest obsługiwana dla aplikacji, które korzystają z dowolnych z tych protokołów:

- SAML 2.0
- WS-Federation

Aby skonfigurować aplikację do opartej na SAML logowania jednokrotnego, zobacz [opartej na SAML skonfigurować logowanie jednokrotne](configure-single-sign-on-portal.md). Wiele oprogramowanie jako usługa (SaaS) aplikacje są także [specyficzne dla aplikacji, samouczek](../saas-apps/tutorial-list.md) , szczegółowy opis konfiguracji opartej na SAML logowania jednokrotnego.

Aby skonfigurować aplikację dla protokołu WS-Federation, wykonaj te same wskazówki, aby skonfigurować aplikację na podstawie protokołu SAML logowania jednokrotnego, zobacz [opartej na SAML skonfigurować logowanie jednokrotne](configure-single-sign-on-portal.md). W kroku, aby skonfigurować aplikację do korzystania z usługi Azure AD, musisz zastąpić adres URL logowania usługi Azure AD dla punktu końcowego protokołu WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Aby uzyskać więcej informacji na temat protokołu SAML, zobacz [pojedynczego logowania jednokrotnego protokołu SAML](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Usługa rejestracji Jednokrotnej z opartego na hasłach
Za pomocą opartego na hasłach logowania jednokrotnego użytkownicy zalogować się do aplikacji przy użyciu nazwy użytkownika i hasła podczas pierwszego uzyskiwania dostępu do. Po pierwsze logowanie jednokrotne usługi Azure AD zawiera nazwę użytkownika i hasło do aplikacji. 

Na podstawie hasła logowania jednokrotnego używa istniejącego procesu uwierzytelniania udostępniany przez aplikację. Po włączeniu hasła logowania jednokrotnego dla aplikacji usługi Azure AD zbiera i OS x bezpiecznie przechowa nazwy użytkownika i hasła dla aplikacji. Poświadczenia użytkownika są przechowywane w stanie zaszyfrowane w katalogu. 

Wybierz opartego na hasłach pojedynczego logowania jednokrotnego po:

- Aplikacja nie obsługuje pojedynczego logowania jednokrotnego protokołu SAML.
- Aplikacja uwierzytelnia się za pomocą nazwy użytkownika i hasło zamiast tokenami dostępu i nagłówki.

Oparte na hasłach logowanie jednokrotne jest obsługiwana dla dowolnej aplikacji opartej na chmurze, który jest oparty na języku HTML strony logowania. Użytkownik może użyć dowolnej z poniższych przeglądarek:

- Internet Explorer 11 w systemie Windows 7 lub nowszy
- Microsoft Edge w systemie Windows 10 Anniversary Edition lub nowszy
- Dla programu Chrome w Windows 7 lub nowszy, a system MacOS x lub nowszym
- Firefox 26.0 lub nowszej dla systemu Windows XP SP2 lub nowszy i w systemie Mac OS X 10.6 lub nowszej

Aby skonfigurować dla opartego na hasłach logowania jednokrotnego aplikacji w chmurze, zobacz [skonfigurować wniosek o hasło logowania jednokrotnego](application-sign-in-problem-password-sso-gallery.md#configure-the-application-for-password-single-sign-on).

Aby skonfigurować aplikację lokalną pod kątem logowania jednokrotnego za pośrednictwem serwera Proxy aplikacji, zobacz [hasło vaulting dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Jak działa uwierzytelnianie oparte na hasłach logowania jednokrotnego

W celu uwierzytelnienia użytkownika do aplikacji usługi Azure AD umożliwia pobranie poświadczeń użytkownika z katalogu i wejścia stronie logowania jednokrotnego dla aplikacji.  Usługi Azure AD bezpiecznie przekazuje poświadczenia użytkownika przy użyciu rozszerzenia przeglądarki sieci web lub aplikacji mobilnej. Ten proces umożliwia administratorowi Zarządzanie poświadczeniami użytkownika i nie wymaga użytkownikom na zapamiętywanie hasła.

> [!IMPORTANT]
> Poświadczenia są zaciemniony w plikach od użytkownika podczas zautomatyzowanego procesu logowania. Poświadczenia są jednak wykrywalny przy użyciu narzędzia do debugowania w sieci web. Użytkownicy i Administratorzy muszą wykonać te same zasady zabezpieczeń tak, jakby zostały wprowadzone poświadczenia bezpośrednio przez użytkownika.

### <a name="managing-credentials-for-password-based-sso"></a>Zarządzanie poświadczeniami opartego na hasłach logowania jednokrotnego

Hasła do każdej aplikacji albo można zarządzać przez administratora usługi Azure AD lub przez użytkowników.

Gdy poświadczenia zarządza administrator usługi Azure AD:  

- Użytkownik nie musi zresetować lub Zapamiętaj nazwę użytkownika i hasło. Użytkownikowi dostęp do aplikacji, klikając je w swoich panelach dostępu lub za pośrednictwem podany link.
- Administrator mogą wykonywać zadania zarządzania, przy użyciu poświadczeń. Na przykład administrator można zaktualizować dostępu do aplikacji, zgodnie z członkostwa w grupach użytkowników i stanu.
- Administrator może użyć poświadczeń administracyjnych, aby zapewnić dostęp do aplikacji współużytkowane przez wielu użytkowników. Na przykład administrator można zezwolić na każdy, kto może uzyskiwać dostęp do aplikacji na dostęp do mediów społecznościowych i aplikacji do udostępniania dokumentu.

Gdy użytkownik końcowy zarządza poświadczeniami:

- Użytkownicy mogą zarządzać swoich haseł, aktualizowanie i usuwanie ich odpowiednio do potrzeb. 
- Administratorzy mogą nadal Ustaw nowe poświadczenia dla aplikacji.


## <a name="linked-sso"></a>Połączona usługa rejestracji Jednokrotnej
Połączone logowanie umożliwia usłudze Azure AD w celu zapewnienia logowania jednokrotnego do aplikacji, która jest już skonfigurowane logowanie jednokrotne w innej usłudze. Połączonych aplikacji może znajdować się użytkownikom końcowym w portalu usługi Office 365 lub portalu usługi Azure AD MyApps. Na przykład użytkownik może uruchomić aplikację, która jest skonfigurowana na potrzeby logowania jednokrotnego w Active Directory Federation Services 2.0 (AD FS) z portalu usługi Office 365. Dodatkowe raportowania jest również dostępna dla połączonych aplikacji, które będą uruchamiane w portalu usługi Office 365 lub portalu usługi Azure AD MyApps. 

### <a name="linked-sso-for-application-migration"></a>Połączone logowanie Jednokrotne do migracji aplikacji

Połączone logowanie Jednokrotne może zapewnić spójne środowisko użytkownika podczas migrowania aplikacji w danym okresie czasu. W przypadku migrowania aplikacji do usługi Azure Active Directory, można użyć połączonego logowania jednokrotnego, szybkie publikowanie łączy do wszystkich aplikacji, które zamierzasz migrować.  Użytkownicy mogą znaleźć wszystkie linki w [portalu MyApps](../user-help/active-directory-saas-access-panel-introduction.md) lub [uruchamiania aplikacji usługi Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Użytkownicy nie będą o tym, że użyteczną połączonych aplikacji lub migrowanych aplikacji.  

Po użytkownik został uwierzytelniony przy użyciu połączonych aplikacji, rekord konta musi zostać utworzona przed użytkownika końcowego podano dostępu rejestracji jednokrotnej. Inicjowanie obsługi administracyjnej ten rekord konta można albo automatycznie są wykonywane lub może być wykonywane ręcznie przez administratora.

## <a name="disabled-sso"></a>Wyłączone logowania jednokrotnego

Wyłączony tryb oznacza, że logowanie jednokrotne nie jest używana dla aplikacji. Gdy logowanie jednokrotne jest wyłączone, użytkownicy może być konieczne dwukrotnego uwierzytelnienia. Po pierwsze uwierzytelnianie użytkowników do usługi Azure AD, a następnie zaloguj aplikacji. 

Używanie wyłączone tryb rejestracji jednokrotnej:

- Jeśli nie jesteś gotowy do integracji aplikacji za pomocą usługi Azure AD logowanie jednokrotne, lub
- Jeśli testujesz inne aspekty aplikacji, lub
- Jako warstwy zabezpieczeń do aplikacji w środowisku lokalnym, który nie wymaga od użytkowników uwierzytelniania. Z wyłączoną użytkownik musi uwierzytelnić. 

## <a name="integrated-windows-authentication-iwa-sso"></a>Zintegrowane uwierzytelnianie Windows (IWA) logowania jednokrotnego

[Serwer Proxy aplikacji](application-proxy.md) umożliwia logowanie jednokrotne (SSO) do aplikacji, które używają [zintegrowane Windows Authentication (Zintegrowane)](/aspnet/web-api/overview/security/integrated-windows-authentication), lub aplikacji obsługujących oświadczenia. Jeśli aplikacja używa IWA, serwer Proxy aplikacji uwierzytelnia się do aplikacji za pomocą delegowanie ograniczone protokołu Kerberos (KCD). Dla aplikacji obsługujących oświadczenia i relacje zaufania usługi Azure Active Directory logowania jednokrotnego działa, ponieważ użytkownik został już uwierzytelniony za pomocą usługi Azure AD.

Wybierz opcję Zintegrowane uwierzytelnianie Windows pojedynczego tryb logowania jednokrotnego:

- Aby zapewnić logowanie jednokrotne do aplikacji w środowisku lokalnym, który uwierzytelnia IWA. 

Aby skonfigurować aplikację lokalną dla IWA, zobacz [ograniczonego delegowania protokołu Kerberos do logowania jednokrotnego do aplikacji przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md). 

### <a name="how-single-sign-on-with-kcd-works"></a>Jak logowanie jednokrotne za pomocą działania ograniczonego delegowania protokołu Kerberos
Ten diagram opisano przepływ, gdy użytkownik uzyskuje dostęp do aplikacji w środowisku lokalnym, która używa IWA.

![Diagram przepływu uwierzytelniania AAD firmy Microsoft](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Użytkownik musi wprowadzić adres URL, aby uzyskać dostęp w aplikacji lokalnej za pośrednictwem serwera Proxy aplikacji.
2. Serwer Proxy aplikacji przekierowuje żądanie do usług uwierzytelniania usługi Azure AD, aby preauthenticate. W tym momencie usługa Azure AD ma zastosowanie wszelkich mających zastosowanie uwierzytelniania i zasady autoryzacji, takie jak uwierzytelnianie wieloskładnikowe. Jeśli użytkownik zostanie zweryfikowana, usługi Azure AD tworzy token i wysyła go do użytkownika.
3. Użytkownik przekazuje token do serwera Proxy aplikacji.
4. Serwer Proxy aplikacji weryfikuje token i pobiera główną nazwę użytkownika (UPN) z tokenu. Żądanie, nazwy UPN i głównej nazwy usługi (SPN) następnie wysyła do łącznika usługi za pośrednictwem uwierzytelnionych dually bezpiecznego kanału.
5. Łącznik używa negocjacji delegowanie ograniczone protokołu Kerberos (KCD) przy użyciu usługi AD, personifikacji użytkownika w celu pobrania tokenu protokołu Kerberos do aplikacji lokalnych.
6. Usługi Active Directory wysyła ten token protokołu Kerberos, do łącznika aplikacji.
7. Łącznik wysyła oryginalne żądanie do serwera aplikacji, za pomocą tokenu protokołu Kerberos, otrzymanego z usługi AD.
8. Aplikacja wysyła odpowiedź do łącznika, która jest zwracana do serwera Proxy aplikacji usługi a na koniec do użytkownika.

## <a name="header-based-sso"></a>Usługa rejestracji Jednokrotnej w opartej na nagłówkach

Opartej na nagłówkach logowania jednokrotnego działa w przypadku aplikacji, które nagłówki HTTP jest używany do uwierzytelniania. Ta metoda logowania jednokrotnego używa usługi uwierzytelniania innej firmy o nazwie PingAccess. Użytkownik musi jedynie do uwierzytelniania w usłudze Azure AD. 

Wybierz opartej na nagłówkach pojedynczego logowania jednokrotnego po:

- Serwer Proxy aplikacji i PingAccess są skonfigurowane dla aplikacji

Aby skonfigurować uwierzytelnianie oparte na nagłówek, zobacz [opartej na nagłówkach uwierzytelniania dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md). 

### <a name="what-is-pingaccess-for-azure-ad"></a>Co to jest oprogramowanie PingAccess dla usługi Azure AD?

Przy użyciu PingAccess dla usługi Azure AD, użytkownicy mogą dostęp i jednym logowanie jednokrotne do aplikacji, które używające nagłówków na potrzeby uwierzytelniania. Serwer Proxy aplikacji traktuje te aplikacje, takie jak każdy inny, za pomocą usługi Azure AD do uwierzytelniania dostępu i następnie przekazywanie ruchu za pośrednictwem usługi łącznika. Po wystąpieniu uwierzytelniania usługi PingAccess tłumaczy token dostępu usługi Azure AD na format nagłówka, które są wysyłane do aplikacji.

Użytkownicy nie będą zauważyć nic innego po zalogowaniu się do użycia aplikacje firmowe. One nadal pracować z dowolnego miejsca na dowolnym urządzeniu. Łączniki serwera Proxy aplikacji bezpośrednie zdalny ruch do wszystkich aplikacji i będą one nadal równoważenia obciążenia automatycznie.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Jak uzyskać licencję na oprogramowanie PingAccess?

Ponieważ ten scenariusz jest oferowana za pośrednictwem powiązania między usługą Azure AD i PingAccess, potrzebujesz licencji dla obu usług. Jednak subskrypcje usługi Azure AD Premium obejmują licencję PingAccess podstawowe, który obejmuje maksymalnie 20 aplikacji. Należy opublikować więcej niż 20 aplikacji opartej na nagłówkach, można uzyskać dodatkową licencję z usługą PingAccess. 

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).


## <a name="related-articles"></a>Pokrewne artykuły:
* [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](../saas-apps/tutorial-list.md)
* [Samouczek dotyczący konfigurowania logowania jednokrotnego](configure-single-sign-on-portal.md)
* [Wprowadzenie do zarządzanie dostępem do aplikacji](what-is-access-management.md)
* Link pobierania: [Plan wdrażania rejestracji jednokrotnej](https://aka.ms/SSODeploymentPlan).


