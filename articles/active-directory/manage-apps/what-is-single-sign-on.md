---
title: Logowanie jednokrotne do aplikacji — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak wybrać metodę logowania jednokrotnego podczas konfigurowania aplikacji w usłudze Azure Active Directory (Azure AD). Użyj logowania jednokrotnego, aby użytkownicy nie musieli zapamiętywać haseł dla każdej aplikacji i upraszczać administrowanie zarządzaniem kontem.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f46bcf412403d8f911e484e12a9d1f421b1666f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366074"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)

Logowanie jednokrotne (Logowanie jednokrotne) zwiększa bezpieczeństwo i wygodę, gdy użytkownicy logują się do aplikacji w usłudze Azure Active Directory (Azure AD). W tym artykule opisano metody logowania jednokrotnego i pomaga wybrać najbardziej odpowiednią metodę logowania jednokrotnego podczas konfigurowania aplikacji.

- **Dzięki logowi jednokrotnemu**użytkownicy logują się raz za pomocą jednego konta, aby uzyskać dostęp do urządzeń przyłączonych do domeny, zasobów firmy, aplikacji oprogramowania jako usługi (SaaS) i aplikacji sieci Web. Po zalogowaniu użytkownik może uruchamiać aplikacje z portalu usługi Office 365 lub panelu dostępu usługi Azure AD MyApps. Administratorzy mogą centralizować zarządzanie kontami użytkowników i automatycznie dodawać lub usuwać dostęp użytkowników do aplikacji na podstawie członkostwa w grupie.

- **Bez logowania jednokrotnego**użytkownicy muszą pamiętać hasła specyficzne dla aplikacji i logować się do każdej aplikacji. Pracownicy działu IT muszą tworzyć i aktualizować konta użytkowników dla każdej aplikacji, takiej jak Office 365, Box i Salesforce. Użytkownicy muszą zapamiętać swoje hasła, a także poświęcić czas na zalogowanie się do każdej aplikacji.

## <a name="choosing-a-single-sign-on-method"></a>Wybieranie metody logowania jednokrotnego

Istnieje kilka sposobów konfigurowania aplikacji do logowania jednokrotnego. Wybór metody logowania jednokrotnego zależy od sposobu skonfigurowania aplikacji do uwierzytelniania.

- Aplikacje w chmurze mogą używać metod OpenID Connect, OAuth, SAML, opartych na hasłach, połączonych lub wyłączonych metod logowania jednokrotnego. 
- Aplikacje lokalne mogą używać opartych na hasłach zintegrowanych uwierzytelniania systemu Windows, opartych na nagłówku, połączonych lub wyłączonych metod logowania jednokrotnego. Wybory lokalne działają, gdy aplikacje są skonfigurowane dla serwera proxy aplikacji.

Ten schemat blokowy pomaga zdecydować, która metoda logowania jednokrotnego jest najlepsza dla Twojej sytuacji.

![Schemat blokowy decyzji dla metody logowania jednokrotnego](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

W poniższej tabeli podsumowano metody logowania jednokrotnego i łącza do bardziej szczegółowych informacji.

| Metoda logowania jednokrotnego | Typy aplikacji | Kiedy stosować |
| :------ | :------- | :----- |
| [OpenID Connect i OAuth](#openid-connect-and-oauth) | tylko chmura | Podczas tworzenia nowej aplikacji należy używać funkcji OpenID Connect i OAuth. Ten protokół upraszcza konfigurację aplikacji, ma łatwe w użyciu sdk i umożliwia aplikacji korzystanie z programu MS Graph.
| [SAML](#saml-sso) | chmura i lokalnie | Wybierz SAML, gdy jest to możliwe dla istniejących aplikacji, które nie używają OpenID Connect lub OAuth. SAML działa dla aplikacji, które uwierzytelniają się przy użyciu jednego z protokołów SAML.|
| [Oparte na hasłach](#password-based-sso) | chmura i lokalnie | Wybierz hasło oparte na aplikacji, gdy aplikacja uwierzytelnia się przy użyciu nazwy użytkownika i hasła. Logowanie jednookrotne oparte na hasłach umożliwia bezpieczne przechowywanie haseł aplikacji i powtarzanie za pomocą rozszerzenia przeglądarki internetowej lub aplikacji mobilnej. Ta metoda używa istniejącego procesu logowania dostarczonego przez aplikację, ale umożliwia administratorowi zarządzanie hasłami. |
| [Połączone](#linked-sign-on) | chmura i lokalnie | Wybierz połączone logowanie, gdy aplikacja jest skonfigurowana do logowania jednokrotnego w innej usłudze dostawcy tożsamości. Ta opcja nie powoduje dodawania logowania jednokrotnego do aplikacji. Jednak aplikacja może już mieć logowanie jednokrotne zaimplementowane przy użyciu innej usługi, takiej jak Usługi federacyjne Active Directory.|
| [Wyłączone](#disabled-sso) | chmura i lokalnie | Wybierz wyłączone logowanie jednokrotne, gdy aplikacja nie jest gotowa do skonfigurowania do logowania jednokrotnego. Ten tryb jest domyślny podczas tworzenia aplikacji.|
| [Zintegrowane uwierzytelnianie systemu Windows (IWA)](#integrated-windows-authentication-iwa-sso) | tylko lokalnie | Wybierz opcję Logowanie jednokrotne IWA dla aplikacji korzystających ze [zintegrowanego uwierzytelniania systemu Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)lub aplikacji obsługujących oświadczenia. W przypadku protokołu IWA łączniki serwera proxy aplikacji używają delegowania ograniczonego protokołu Kerberos (KCD) do uwierzytelniania użytkowników w aplikacji. |
| [Oparte na nagłówku](#header-based-sso) | tylko lokalnie | Użyj logowania jednokrotnego opartego na nagłówku, gdy aplikacja używa nagłówków do uwierzytelniania. Logowanie jednookrotne oparte na nagłówku wymaga usługi PingAccess dla usługi Azure AD. Serwer proxy aplikacji używa usługi Azure AD do uwierzytelniania użytkownika, a następnie przekazuje ruch za pośrednictwem usługi łącznika.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect i OAuth

Podczas tworzenia nowych aplikacji należy używać nowoczesnych protokołów, takich jak OpenID Connect i OAuth, aby uzyskać najlepsze środowisko logowania jednokrotnego dla aplikacji na wielu platformach urządzeń. OAuth umożliwia użytkownikom lub administratorom [udzielenie zgody](configure-user-consent.md) na chronione zasoby, takie jak [Microsoft Graph.](/graph/overview) Zapewniamy łatwe do przyjęcia [SDK](../develop/reference-v2-libraries.md) dla aplikacji, a ponadto aplikacja będzie gotowa do użycia [programu Microsoft Graph.](/graph/overview)

Aby uzyskać więcej informacji, zobacz:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Przewodnik dla deweloperów platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>Logowanie jednokrotne SAML

Dzięki **logowaniu jednokrotnemu SAML**usługa Azure AD uwierzytelnia się w aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD przekazuje informacje logowania do aplikacji za pośrednictwem protokołu połączenia. Za pomocą logowania jednokrotnego opartego na SAML można mapować użytkowników do określonych ról aplikacji na podstawie reguł zdefiniowanych w oświadczeń SAML.

Wybierz saml oparte logowanie jednokrotne, gdy aplikacja obsługuje go.

Logowanie jednokrotne oparte na saml jest obsługiwane dla aplikacji korzystających z dowolnego z tych protokołów:

- SAML 2.0
- WS-Federation

Aby skonfigurować aplikację SaaS dla logowania jednokrotnego opartego na saml, zobacz [Konfigurowanie logowania jednokrotnego opartego na saml](configure-single-sign-on-non-gallery-applications.md). Ponadto wiele aplikacji Software as a Service (SaaS) ma [samouczek specyficzny dla aplikacji,](../saas-apps/tutorial-list.md) który przechodzi przez konfigurację logowania jednokrotnego opartego na saml.

Aby skonfigurować aplikację dla federacji WS, postępuj zgodnie z tymi samymi wskazówkami, aby skonfigurować aplikację dla logowania jednokrotnego opartego na SAML, zobacz [Konfigurowanie logowania jednokrotnego opartego na SAML](configure-single-sign-on-non-gallery-applications.md). W kroku, aby skonfigurować aplikację do korzystania z usługi Azure AD, należy zastąpić adres URL `https://login.microsoftonline.com/<tenant-ID>/wsfed`logowania usługi Azure AD dla punktu końcowego Federacji WS .

Aby skonfigurować aplikację lokalną dla logowania jednokrotnego opartego na saml, zobacz [logowanie jednooznakowe SAML dla aplikacji lokalnych z serwerem proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md).

Aby uzyskać więcej informacji na temat protokołu SAML, zobacz [Protokół SAML jednokrotnego logowania](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Loga logów samosojskich opartych na hasłach

W momencie logowania opartego na hasłach użytkownicy logują się do aplikacji przy użyciu nazwy użytkownika i hasła przy pierwszym dostępie do niej. Po pierwszym zalogowaniu usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji.

Logowanie jednokrotne oparte na hasłach używa istniejącego procesu uwierzytelniania dostarczonego przez aplikację. Po włączeniu logowania jednokrotnego hasła dla aplikacji usługa Azure AD zbiera i bezpiecznie przechowuje nazwy użytkowników i hasła dla aplikacji. Poświadczenia użytkownika są przechowywane w stanie zaszyfrowanym w katalogu.

Wybierz logowanie jednokrotne oparte na hasłach, gdy:

- Aplikacja nie obsługuje protokołu logowania jednokrotnego SAML.
- Aplikacja uwierzytelnia się przy użyciu nazwy użytkownika i hasła zamiast tokenów dostępu i nagłówków.

Logowanie jednookrotne oparte na hasłach jest obsługiwane dla każdej aplikacji chmurowej, która ma stronę logowania opartą na języku HTML. Użytkownik może korzystać z dowolnej z następujących przeglądarek:

- Internet Explorer 11 w systemie Windows 7 lub nowszym
   > [!NOTE]
   > Program Internet Explorer korzysta z ograniczonej pomocy technicznej i nie otrzymuje już nowych aktualizacji oprogramowania. Microsoft Edge jest zalecaną przeglądarką.

- Microsoft Edge w systemie Windows 10 Anniversary Edition lub nowszym
- Microsoft Edge dla systemów iOS i Android
- Intune Managed Browser
- Chrome w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym
- Firefox 26.0 lub nowszy w systemie Windows XP z dodatkiem SP2 lub nowszym oraz w systemie Mac OS X 10.6 lub nowszym

Aby skonfigurować aplikację chmurową do logowania jednokrotnego opartego na [hasłach, zobacz Konfigurowanie logowania jednokrotnego hasła](configure-password-single-sign-on-non-gallery-applications.md).

Aby skonfigurować aplikację lokalną do logowania jednokrotnego za pośrednictwem serwera proxy aplikacji, zobacz [Vaulting haseł dla logowania jednokrotnego za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Jak działa uwierzytelnianie dla loga loga logowanego opartego na hasłach

Aby uwierzytelnić użytkownika do aplikacji, usługa Azure AD pobiera poświadczenia użytkownika z katalogu i wprowadza je do strony logowania aplikacji.  Usługa Azure AD bezpiecznie przekazuje poświadczenia użytkownika za pośrednictwem rozszerzenia przeglądarki sieci Web lub aplikacji mobilnej. Ten proces umożliwia administratorowi zarządzanie poświadczeniami użytkownika i nie wymaga od użytkowników zapamiętywania hasła.

> [!IMPORTANT]
> Poświadczenia są zaciemnione od użytkownika podczas procesu automatycznego logowania. Jednak poświadczenia są wykrywalne przy użyciu narzędzi do debugowania sieci Web. Użytkownicy i administratorzy muszą przestrzegać tych samych zasad zabezpieczeń, tak jak gdyby poświadczenia zostały wprowadzone bezpośrednio przez użytkownika.

### <a name="managing-credentials-for-password-based-sso"></a>Zarządzanie poświadczeniami logowania sytograficznego opartego na hasłach

Hasła dla każdej aplikacji mogą być zarządzane przez administratora usługi Azure AD lub przez użytkowników.

Gdy administrator usługi Azure AD zarządza poświadczeniami:  

- Użytkownik nie musi resetować ani zapamiętywać nazwy użytkownika i hasła. Użytkownik może uzyskać dostęp do aplikacji, klikając na nią w panelu dostępu lub za pośrednictwem podanego linku.
- Administrator może wykonywać zadania zarządzania poświadczeniami. Na przykład administrator może aktualizować dostęp do aplikacji zgodnie z członkostwem w grupie użytkowników i stanem pracownika.
- Administrator może używać poświadczeń administracyjnych w celu zapewnienia dostępu do aplikacji współużytkowane przez wielu użytkowników. Na przykład administrator może zezwolić wszystkim, którzy mają dostęp do aplikacji, na dostęp do mediów społecznościowych lub aplikacji do udostępniania dokumentów.

Gdy użytkownik końcowy zarządza poświadczeniami:

- Użytkownicy mogą zarządzać swoimi hasłami, aktualizując je lub usuwając w razie potrzeby.
- Administratorzy nadal mogą ustawić nowe poświadczenia dla aplikacji.

## <a name="linked-sign-on"></a>Połączone logowanie
Połączone logowanie umożliwia usłudze Azure AD zapewnienie logowania jednokrotnego do aplikacji, która jest już skonfigurowana do logowania jednokrotnego w innej usłudze. Połączona aplikacja może pojawić się użytkownikom końcowym w portalu usługi Office 365 lub portalu Azure AD MyApps. Na przykład użytkownik może uruchomić aplikację skonfigurowaną do logowania jednokrotnego w usługach federacjowych Active Directory 2.0 (AD FS) z portalu usługi Office 365. Dodatkowe raportowanie jest również dostępne dla połączonych aplikacji uruchamianych z portalu usługi Office 365 lub portalu Azure AD MyApps. Aby skonfigurować aplikację do logowania połączonego, zobacz [Konfigurowanie połączonego logowania](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Połączone logowanie do migracji aplikacji

Połączone logowanie może zapewnić spójne środowisko użytkownika podczas migracji aplikacji przez pewien czas. Jeśli migrujesz aplikacje do usługi Azure Active Directory, możesz użyć połączonego logowania, aby szybko opublikować łącza do wszystkich aplikacji, które mają zostać przeniesione.  Użytkownicy mogą znaleźć wszystkie łącza w [portalu MyApps](../user-help/active-directory-saas-access-panel-introduction.md) lub [w usłudze Office 365.](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) Użytkownicy nie będą wiedzieć, że uzyskują dostęp do połączonej aplikacji lub zmigrowanego aplikacji.  

Gdy użytkownik uwierzytelnił się za pomocą połączonej aplikacji, należy utworzyć rekord konta, zanim użytkownik końcowy otrzyma dostęp do logowania jednokrotnego. Inicjowanie obsługi administracyjnej tego rekordu konta może nastąpić automatycznie lub może nastąpić ręcznie przez administratora.

## <a name="disabled-sso"></a>Wyłączone sso

Tryb wyłączenia oznacza, że logowanie jednokrotne nie jest używane dla aplikacji. Gdy logowanie jednokrotne jest wyłączone, użytkownicy mogą być konieczne uwierzytelnienie dwa razy. Najpierw użytkownicy uwierzytelniają się w usłudze Azure AD, a następnie logują się do aplikacji.

Użyj wyłączonego trybu logowania jednokrotnego:

- Jeśli nie jesteś gotowy do integracji tej aplikacji z logiem jednokrotnym usługi Azure AD lub
- Jeśli testujesz inne aspekty aplikacji, lub
- Jako warstwa zabezpieczeń dla aplikacji lokalnej, która nie wymaga od użytkowników uwierzytelniania. Po wyłączeniach użytkownik musi uwierzytelnić.

Należy zauważyć, że jeśli skonfigurowano aplikację dla logowania jednokrotnego inicjowanego przez SAML inicjowanego przez spl i zmienisz tryb logowania jednokrotnego, aby wyłączyć, nie powstrzyma to użytkowników przed logowaniem się do aplikacji poza portalem MyApps. Aby to osiągnąć, należy [wyłączyć możliwość logowania się użytkowników](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Zintegrowane uwierzytelnianie systemu Windows (IWA) — identyfikatory SSO

[Serwer proxy aplikacji](application-proxy.md) udostępnia logowanie jednokrotne (SSO) aplikacjom korzystającym ze [zintegrowanego uwierzytelniania systemu Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)lub aplikacjom obsługującym oświadczenia. Jeśli aplikacja używa protokołu IWA, serwer proxy aplikacji uwierzytelnia się w aplikacji przy użyciu protokołu Kerberos Ograniczone delegowania (KCD). W przypadku aplikacji obsługującej oświadczenia, która ufa usłudze Azure Active Directory, logowanie jednokrotne działa, ponieważ użytkownik został już uwierzytelniony przy użyciu usługi Azure AD.

Wybierz tryb rejestracji jednokrotnej uwierzytelniania systemu Windows, aby zapewnić logowanie jednokrotne w aplikacji lokalnej, która uwierzytelnia się przy użyciu usługi IWA.

Aby skonfigurować aplikację lokalną dla IWA, zobacz [Delegowanie ograniczone kerberos do logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Jak działa logowanie jednokrotne z KCD
Ten diagram wyjaśnia przepływ, gdy użytkownik uzyskuje dostęp do aplikacji lokalnej, która używa IWA.

![Diagram przepływu uwierzytelniania usługi Microsoft Azure AD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Użytkownik wprowadza adres URL, aby uzyskać dostęp do aplikacji lokalnej za pośrednictwem serwera proxy aplikacji.
1. Serwer proxy aplikacji przekierowuje żądanie do usług uwierzytelniania usługi Azure AD w celu wstępnego uwierzytelnienia. W tym momencie usługa Azure AD stosuje wszystkie odpowiednie zasady uwierzytelniania i autoryzacji, takie jak uwierzytelnianie wieloskładnikowe. Jeśli użytkownik zostanie zweryfikowany, usługa Azure AD tworzy token i wysyła go do użytkownika.
1. Użytkownik przekazuje token do serwera proxy aplikacji.
1. Serwer proxy aplikacji sprawdza poprawność tokenu i pobiera nazwę główną użytkownika (UPN) z tokenu. Następnie wysyła żądanie, nazwę UPN i nazwę głównej usługi (SPN) do łącznika za pośrednictwem podwójnie uwierzytelnionego bezpiecznego kanału.
1. Łącznik używa negocjacji protokołu Kerberos Ograniczone delegowanie (KCD) z lokalną usługą AD, personifikując użytkownika, aby uzyskać token Protokołu Kerberos do aplikacji.
1. Usługa Active Directory wysyła token Protokołu Kerberos dla aplikacji do łącznika.
1. Łącznik wysyła oryginalne żądanie do serwera aplikacji przy użyciu tokenu Kerberos odebranego z usługi AD.
1. Aplikacja wysyła odpowiedź do łącznika, który jest następnie zwracany do usługi proxy aplikacji i na koniec do użytkownika.

## <a name="header-based-sso"></a>Samoso oparte na nagłówku

Logowanie jednokrotne oparte na nagłówku działa w przypadku aplikacji, które używają nagłówków HTTP do uwierzytelniania. Ta metoda logowania używa usługi uwierzytelniania innej firmy o nazwie PingAccess. Użytkownik musi tylko uwierzytelnić się w usłudze Azure AD.

Wybierz logowanie jednokrotne oparte na nagłówku, gdy dla aplikacji są skonfigurowane serwery proxy aplikacji i usługi PingAccess.

Aby skonfigurować uwierzytelnianie oparte na nagłówku, zobacz [Uwierzytelnianie oparte na nagłówku dla logowania jednokrotnego za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Co to jest usługa PingAccess dla usługi Azure AD?

Za pomocą funkcji PingAccess dla usługi Azure AD użytkownicy mogą uzyskiwać dostęp do aplikacji korzystających z nagłówków do uwierzytelniania i logowania jednokrotnego. Serwer proxy aplikacji traktuje te aplikacje jak wszystkie inne, używając usługi Azure AD do uwierzytelniania dostępu, a następnie przekazywania ruchu za pośrednictwem usługi łącznika. Po zakończeniu uwierzytelniania usługa PingAccess tłumaczy token dostępu usługi Azure AD na format nagłówka, który jest wysyłany do aplikacji.

Użytkownicy nie zauważą niczego innego, gdy zalogują się, aby korzystać z aplikacji firmowych. Mogą nadal pracować z dowolnego miejsca na dowolnym urządzeniu. Łączniki serwera proxy aplikacji kierują ruch zdalny do wszystkich aplikacji i będą automatycznie równoważenia obciążenia.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Jak uzyskać licencję na pingaccess?

Ponieważ ten scenariusz jest oferowany za pośrednictwem partnerstwa między usługą Azure AD i PingAccess, potrzebujesz licencji dla obu usług. Jednak subskrypcje usługi Azure AD Premium zawierają podstawową licencję PingAccess, która obejmuje maksymalnie 20 aplikacji. Jeśli chcesz opublikować więcej niż 20 aplikacji opartych na nagłówku, możesz uzyskać dodatkową licencję od usługi PingAccess.

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Pokrewne artykuły:
* [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md)
* [Konfigurowanie logowania jednokrotnego opartego na saml](configure-single-sign-on-non-gallery-applications.md)
* [Konfigurowanie logowania jednokrotnego opartego na hasłach](configure-password-single-sign-on-non-gallery-applications.md)
* [Konfigurowanie połączonego logowania](configure-linked-sign-on.md)
* [Wprowadzenie do zarządzania dostępem do aplikacji](what-is-access-management.md)
* Link do pobrania: [Plan wdrażania logowania jednokrotnego](https://aka.ms/SSODeploymentPlan).
