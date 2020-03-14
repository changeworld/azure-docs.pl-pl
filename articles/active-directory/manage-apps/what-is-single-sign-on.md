---
title: Logowanie jednokrotne do aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać jedną metodę logowania jednokrotnego, podczas konfigurowania aplikacji w usłudze Azure Active Directory (Azure AD). Użyj logowania jednokrotnego, dzięki czemu użytkownicy nie muszą zapamiętywać hasła do każdej aplikacji i uprościć administrowanie zarządzania kontami.
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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366074"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory

Logowanie jednokrotne (SSO) zwiększa bezpieczeństwo i wygodę, gdy użytkownicy logują się do aplikacji w Azure Active Directory (Azure AD). W tym artykule opisano metody logowania jednokrotnego, które ułatwiają wybranie najbardziej odpowiedniej metody rejestracji jednokrotnej podczas konfigurowania aplikacji.

- **Logowanie jednokrotne**umożliwia użytkownikom logowanie jednokrotne przy użyciu jednego konta, aby uzyskać dostęp do urządzeń przyłączonych do domeny, zasobów firmy, aplikacji jako usługi (SaaS) i aplikacji sieci Web. Po zalogowaniu się użytkownika można uruchamiać aplikacje z poziomu portalu usługi Office 365 lub Azure AD MyApps panelu dostępu. Administratorzy mogą scentralizowane zarządzanie kontami użytkowników i automatycznie dodawała lub usuwała użytkownikom dostęp do aplikacji na podstawie przynależności do grupy.

- **Bez logowania jednokrotnego**użytkownicy muszą pamiętać hasła specyficzne dla aplikacji i zalogować się do każdej aplikacji. Dział IT musi pracowników do tworzenia i aktualizowania konta użytkowników dla każdej aplikacji, takich jak usługi Office 365, Box i Salesforce. Użytkownicy potrzebują do haseł oraz poświęcić czas, aby zalogować się do poszczególnych aplikacji.

## <a name="choosing-a-single-sign-on-method"></a>Wybieranie jednej metody logowania jednokrotnego

Istnieje kilka sposobów, aby skonfigurować aplikację pod kątem logowania jednokrotnego. Wybór metody logowania jednokrotnego zależy od tego, w jaki sposób aplikacja jest skonfigurowana do uwierzytelniania.

- Aplikacje w chmurze mogą korzystać z metod logowania jednokrotnego OpenID Connect Connect, OAuth, SAML, opartych na hasłach, połączonych lub wyłączonych. 
- Aplikacje lokalne uwierzytelniania można użyć opartego na hasłach, zintegrowane Windows opartej na nagłówkach, połączone lub wyłączone metody logowania jednokrotnego. Opcje w środowisku lokalnym działa, gdy aplikacje są skonfigurowane dla serwera Proxy aplikacji.

Ten schemat blokowy pomaga określić, które pojedynczej metody logowania jednokrotnego jest najlepszego w danej sytuacji.

![Schemat blokowy decyzji dla metody rejestracji jednokrotnej](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Poniższa tabela zawiera podsumowanie pojedynczej metody logowania jednokrotnego i linki, aby uzyskać więcej szczegółów.

| Metoda rejestracji jednokrotnej | Typy aplikacji | Kiedy stosować |
| :------ | :------- | :----- |
| [OpenID Connect Connect and OAuth](#openid-connect-and-oauth) | tylko w chmurze | Użyj OpenID Connect Connect i OAuth podczas tworzenia nowej aplikacji. Ten protokół upraszcza konfigurację aplikacji, ma łatwe w użyciu zestawy SDK i umożliwia aplikacji korzystanie z programu MS Graph.
| [SAML](#saml-sso) | w chmurze i lokalnych | Wybierz pozycję SAML wszędzie tam, gdzie to możliwe dla istniejących aplikacji, które nie używają OpenID Connect Connect lub OAuth. Protokół SAML działa w przypadku aplikacji, które uwierzytelniają się przy użyciu jednego z protokołów SAML.|
| [Oparte na hasłach](#password-based-sso) | w chmurze i lokalnych | Wybierz opcję hasła, gdy aplikacja jest uwierzytelniana przy użyciu nazwy użytkownika i hasła. Oparte na hasłach logowanie jednokrotne umożliwia bezpieczną aplikację przechowywanie i powtarzanie haseł przy użyciu rozszerzenia przeglądarki sieci web lub aplikacji mobilnej. Ta metoda wykorzystuje istniejący proces logowania udostępniany przez aplikację, ale umożliwia administratorowi Zarządzanie hasłami. |
| [Połączone](#linked-sign-on) | w chmurze i lokalnych | Wybierz pozycję dołączone logowanie, gdy aplikacja jest skonfigurowana pod kątem logowania jednokrotnego w innej usłudze dostawcy tożsamości. Ta opcja nie powoduje dodania logowanie jednokrotne do aplikacji. Jednak aplikacja może już logowanie jednokrotne implementowane za pomocą innej usługi, takie jak Active Directory Federation Services.|
| [Disabled (Wyłączone)](#disabled-sso) | w chmurze i lokalnych | Wybierz pozycję wyłączone Logowanie jednokrotne, jeśli aplikacja nie jest gotowa do skonfigurowania do rejestracji jednokrotnej. Ten tryb jest wartością domyślną podczas tworzenia aplikacji.|
| [Zintegrowane uwierzytelnianie systemu Windows (IWA)](#integrated-windows-authentication-iwa-sso) | tylko lokalnie | Wybierz pozycję IWA Logowanie jednokrotne dla aplikacji korzystających ze [zintegrowanego uwierzytelniania systemu Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)lub aplikacji obsługujących oświadczenia. W przypadku IWA łączniki serwera proxy aplikacji używają ograniczonego delegowania protokołu Kerberos (KCD) do uwierzytelniania użytkowników w aplikacji. |
| [Na podstawie nagłówka](#header-based-sso) | tylko lokalnie | Jeśli aplikacja używa nagłówków do uwierzytelniania, należy użyć opartej na nagłówkach logowania jednokrotnego. Logowanie jednokrotne oparte na nagłówkach wymaga PingAccess dla usługi Azure AD. Serwer Proxy aplikacji używa usługi Azure AD można uwierzytelnić użytkownika, a następnie przekazuje ruch za pośrednictwem usługi łącznika.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect Connect and OAuth

Podczas opracowywania nowych aplikacji używaj nowoczesnych protokołów, takich jak OpenID Connect Connect i OAuth, aby osiągnąć najlepsze środowisko logowania jednokrotnego dla aplikacji na wielu platformach urządzeń. Uwierzytelnianie OAuth umożliwia użytkownikom lub administratorom [udzielenie zgody](configure-user-consent.md) na chronione zasoby, takie jak [Microsoft Graph](/graph/overview). Zapewniamy łatwy do przyjęcia [zestawy SDK](../develop/reference-v2-libraries.md) dla aplikacji, a ponadto aplikacja będzie gotowa do użycia [Microsoft Graph](/graph/overview).

Aby uzyskać więcej informacji, zobacz:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Microsoft Identity platform Developer Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>Logowanie jednokrotne SAML

Za pomocą **logowania jednokrotnego SAML**usługa Azure AD uwierzytelnia się w aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD komunikuje się informacji logowania jednokrotnego do aplikacji za pośrednictwem protokołu połączenia. Korzystając z logowania jednokrotnego opartego na protokole SAML, można mapować użytkowników na określone role aplikacji na podstawie reguł zdefiniowanych w oświadczeniach SAML.

Wybierz Logowanie jednokrotne oparte na protokole SAML, gdy aplikacja je obsługuje.

Opartej na SAML logowania jednokrotnego jest obsługiwana dla aplikacji, które korzystają z dowolnych z tych protokołów:

- SAML 2.0
- WS-Federation

Aby skonfigurować aplikację SaaS dla logowania jednokrotnego opartego na protokole SAML, zobacz Konfigurowanie logowania jednokrotnego [opartego na protokole SAML](configure-single-sign-on-non-gallery-applications.md). Ponadto wiele aplikacji oprogramowania jako usługi (SaaS) ma [samouczek specyficzny dla aplikacji](../saas-apps/tutorial-list.md) , który przeprowadzi Cię przez proces konfigurowania logowania jednokrotnego opartego na protokole SAML.

Aby skonfigurować aplikację dla protokołu WS-Federation, postępuj zgodnie z tymi samymi wskazówkami, aby skonfigurować aplikację dla logowania jednokrotnego opartego na protokole SAML, zobacz Konfigurowanie logowania jednokrotnego [opartego na protokole SAML](configure-single-sign-on-non-gallery-applications.md). W kroku, aby skonfigurować aplikację do korzystania z usługi Azure AD, musisz zastąpić adres URL logowania usługi Azure AD dla `https://login.microsoftonline.com/<tenant-ID>/wsfed`punktu końcowego usługi WS-Federation.

Aby skonfigurować aplikację lokalną na potrzeby logowania jednokrotnego opartego na protokole SAML, zobacz Rejestrowanie jednokrotne [SAML dla aplikacji lokalnych przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md).

Aby uzyskać więcej informacji na temat protokołu SAML, zobacz Logowanie jednokrotne [protokołu SAML](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Usługa rejestracji Jednokrotnej z opartego na hasłach

Logowanie oparte na haśle polega na zalogowaniu się do aplikacji przy użyciu nazwy użytkownika i hasła podczas pierwszego uzyskiwania do nich dostępu. Po pierwszym zalogowaniu usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji.

Na podstawie hasła logowania jednokrotnego używa istniejącego procesu uwierzytelniania udostępniany przez aplikację. Po włączeniu hasła logowania jednokrotnego dla aplikacji usługi Azure AD zbiera i OS x bezpiecznie przechowa nazwy użytkownika i hasła dla aplikacji. Poświadczenia użytkownika są przechowywane w stanie zaszyfrowane w katalogu.

Wybierz Logowanie jednokrotne oparte na haśle, gdy:

- Aplikacja nie obsługuje protokołu SAML logowania jednokrotnego.
- Aplikacja uwierzytelnia się za pomocą nazwy użytkownika i hasło zamiast tokenami dostępu i nagłówki.

Oparte na hasłach logowanie jednokrotne jest obsługiwana dla dowolnej aplikacji opartej na chmurze, który jest oparty na języku HTML strony logowania. Użytkownik może użyć dowolnej z poniższych przeglądarek:

- Internet Explorer 11 w systemie Windows 7 lub nowszy
   > [!NOTE]
   > Program Internet Explorer ma ograniczoną obsługę i nie otrzymuje już nowych aktualizacji oprogramowania. Zalecana przeglądarka to Microsoft Edge.

- Microsoft Edge w systemie Windows 10 w wersji rocznicowej lub nowszej
- Microsoft Edge dla systemów iOS i Android
- Intune Managed Browser
- Dla programu Chrome w Windows 7 lub nowszy, a system MacOS x lub nowszym
- Firefox 26.0 lub nowszej dla systemu Windows XP SP2 lub nowszy i w systemie Mac OS X 10.6 lub nowszej

Aby skonfigurować aplikację w chmurze na potrzeby logowania jednokrotnego opartego na hasłach, zobacz [Konfigurowanie logowania](configure-password-single-sign-on-non-gallery-applications.md)jednokrotnego przy użyciu hasła.

Aby skonfigurować aplikację lokalną do logowania jednokrotnego za pomocą serwera proxy aplikacji, zobacz Przechowywanie [haseł w celu logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Jak uwierzytelnianie jest wykonywane na podstawie hasła Logowanie jednokrotne

W celu uwierzytelnienia użytkownika w aplikacji usługa Azure AD Pobiera poświadczenia użytkownika z katalogu i umieszcza je na stronie logowania aplikacji.  Usługi Azure AD bezpiecznie przekazuje poświadczenia użytkownika przy użyciu rozszerzenia przeglądarki sieci web lub aplikacji mobilnej. Ten proces umożliwia administratorowi Zarządzanie poświadczeniami użytkownika i nie wymaga użytkownikom na zapamiętywanie hasła.

> [!IMPORTANT]
> Poświadczenia są ukrywane podczas procesu automatycznego logowania. Poświadczenia są jednak wykrywalny przy użyciu narzędzia do debugowania w sieci web. Użytkownicy i Administratorzy muszą wykonać te same zasady zabezpieczeń tak, jakby zostały wprowadzone poświadczenia bezpośrednio przez użytkownika.

### <a name="managing-credentials-for-password-based-sso"></a>Zarządzanie poświadczeniami opartego na hasłach logowania jednokrotnego

Hasła do każdej aplikacji albo można zarządzać przez administratora usługi Azure AD lub przez użytkowników.

Gdy poświadczenia zarządza administrator usługi Azure AD:  

- Użytkownik nie musi zresetować lub Zapamiętaj nazwę użytkownika i hasło. Użytkownikowi dostęp do aplikacji, klikając je w swoich panelach dostępu lub za pośrednictwem podany link.
- Administrator mogą wykonywać zadania zarządzania, przy użyciu poświadczeń. Na przykład administrator można zaktualizować dostępu do aplikacji, zgodnie z członkostwa w grupach użytkowników i stanu.
- Administrator może użyć poświadczeń administracyjnych, aby zapewnić dostęp do aplikacji współużytkowane przez wielu użytkowników. Na przykład administrator można zezwolić na każdy, kto może uzyskiwać dostęp do aplikacji na dostęp do mediów społecznościowych i aplikacji do udostępniania dokumentu.

Gdy użytkownik końcowy zarządza poświadczeniami:

- Użytkownicy mogą zarządzać swoich haseł, aktualizowanie i usuwanie ich odpowiednio do potrzeb.
- Administratorzy mogą nadal Ustaw nowe poświadczenia dla aplikacji.

## <a name="linked-sign-on"></a>Logowanie połączone
Połączone logowanie umożliwia usłudze Azure AD w celu zapewnienia logowania jednokrotnego do aplikacji, która jest już skonfigurowane logowanie jednokrotne w innej usłudze. Połączonych aplikacji może znajdować się użytkownikom końcowym w portalu usługi Office 365 lub portalu usługi Azure AD MyApps. Na przykład użytkownik może uruchomić aplikację, która jest skonfigurowana na potrzeby logowania jednokrotnego w Active Directory Federation Services 2.0 (AD FS) z portalu usługi Office 365. Dodatkowe raportowania jest również dostępna dla połączonych aplikacji, które będą uruchamiane w portalu usługi Office 365 lub portalu usługi Azure AD MyApps. Aby skonfigurować aplikację do logowania połączonego, zobacz [Konfigurowanie połączenia połączonego](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Logowanie połączone do migracji aplikacji

Połączone logowanie może zapewnić spójny interfejs użytkownika podczas migrowania aplikacji w określonym czasie. Jeśli migrujesz aplikacje do Azure Active Directory, możesz użyć połączenia połączonego, aby szybko publikować linki do wszystkich aplikacji, które mają zostać zmigrowane.  Użytkownicy mogą znaleźć wszystkie linki w [portalu webapps](../user-help/active-directory-saas-access-panel-introduction.md) lub [uruchamiania aplikacji pakietu Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Użytkownicy nie będą o tym, że użyteczną połączonych aplikacji lub migrowanych aplikacji.  

Po użytkownik został uwierzytelniony przy użyciu połączonych aplikacji, rekord konta musi zostać utworzona przed użytkownika końcowego podano dostępu rejestracji jednokrotnej. Inicjowanie obsługi administracyjnej ten rekord konta można albo automatycznie są wykonywane lub może być wykonywane ręcznie przez administratora.

## <a name="disabled-sso"></a>Wyłączone logowania jednokrotnego

Wyłączony tryb oznacza, że logowanie jednokrotne nie jest używana dla aplikacji. Gdy logowanie jednokrotne jest wyłączone, użytkownicy może być konieczne dwukrotnego uwierzytelnienia. Po pierwsze uwierzytelnianie użytkowników do usługi Azure AD, a następnie zaloguj aplikacji.

Używanie wyłączone tryb rejestracji jednokrotnej:

- Jeśli nie jesteś gotowy do integracji aplikacji za pomocą usługi Azure AD logowanie jednokrotne, lub
- Jeśli testujesz inne aspekty aplikacji, lub
- Jako warstwy zabezpieczeń do aplikacji w środowisku lokalnym, który nie wymaga od użytkowników uwierzytelniania. Z wyłączoną użytkownik musi uwierzytelnić.

Należy pamiętać, że jeśli skonfigurowano aplikację dla logowania jednokrotnego opartego na protokole SAML z usługą SP i zmienisz tryb logowania jednokrotnego na wyłączony, nie uniemożliwi to użytkownikom podpisywania aplikacji poza portalem aplikacji. Aby to osiągnąć, należy [wyłączyć możliwość logowania się użytkowników](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Zintegrowane uwierzytelnianie Windows (IWA) logowania jednokrotnego

[Serwer proxy aplikacji](application-proxy.md) umożliwia logowanie jednokrotne do aplikacji korzystających ze [zintegrowanego uwierzytelniania systemu Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)lub aplikacji obsługujących oświadczenia. Jeśli aplikacja używa IWA, serwer Proxy aplikacji uwierzytelnia się do aplikacji za pomocą delegowanie ograniczone protokołu Kerberos (KCD). Dla aplikacji obsługujących oświadczenia i relacje zaufania usługi Azure Active Directory logowania jednokrotnego działa, ponieważ użytkownik został już uwierzytelniony za pomocą usługi Azure AD.

Wybierz pozycję zintegrowane uwierzytelnianie systemu Windows tryb logowania jednokrotnego, aby zapewnić Logowanie jednokrotne do aplikacji lokalnej, która jest uwierzytelniana za pomocą IWA.

Aby skonfigurować aplikację lokalną dla usługi IWA, zobacz [ograniczone delegowanie protokołu Kerberos na potrzeby logowania jednokrotnego do aplikacji przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Jak logowanie jednokrotne za pomocą działania ograniczonego delegowania protokołu Kerberos
Ten diagram opisano przepływ, gdy użytkownik uzyskuje dostęp do aplikacji w środowisku lokalnym, która używa IWA.

![Diagram przepływu uwierzytelniania Microsoft Azure AD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Użytkownik wprowadza adres URL w celu uzyskania dostępu do lokalnej aplikacji za pomocą serwera proxy aplikacji.
1. Serwer Proxy aplikacji przekierowuje żądanie do usług uwierzytelniania usługi Azure AD, aby preauthenticate. W tym momencie usługa Azure AD ma zastosowanie wszelkich mających zastosowanie uwierzytelniania i zasady autoryzacji, takie jak uwierzytelnianie wieloskładnikowe. Jeśli użytkownik zostanie zweryfikowana, usługi Azure AD tworzy token i wysyła go do użytkownika.
1. Użytkownik przekazuje token do serwera Proxy aplikacji.
1. Serwer Proxy aplikacji weryfikuje token i pobiera główną nazwę użytkownika (UPN) z tokenu. Żądanie, nazwy UPN i głównej nazwy usługi (SPN) następnie wysyła do łącznika usługi za pośrednictwem uwierzytelnionych dually bezpiecznego kanału.
1. Łącznik używa negocjacji ograniczonego delegowania protokołu Kerberos (KCD) z lokalną usługą AD, personifikując użytkownika w celu uzyskania tokenu Kerberos do aplikacji.
1. Usługi Active Directory wysyła ten token protokołu Kerberos, do łącznika aplikacji.
1. Łącznik wysyła oryginalne żądanie do serwera aplikacji, za pomocą tokenu protokołu Kerberos, otrzymanego z usługi AD.
1. Aplikacja wysyła odpowiedź do łącznika, która jest zwracana do serwera Proxy aplikacji usługi a na koniec do użytkownika.

## <a name="header-based-sso"></a>Usługa rejestracji Jednokrotnej w opartej na nagłówkach

Opartej na nagłówkach logowania jednokrotnego działa w przypadku aplikacji, które nagłówki HTTP jest używany do uwierzytelniania. Ta metoda logowania jednokrotnego używa usługi uwierzytelniania innej firmy o nazwie PingAccess. Użytkownik musi jedynie do uwierzytelniania w usłudze Azure AD.

Wybierz pozycję Logowanie jednokrotne oparte na nagłówkach, gdy serwer proxy aplikacji i PingAccess są skonfigurowane dla aplikacji.

Aby skonfigurować uwierzytelnianie oparte na nagłówkach, zobacz [uwierzytelnianie oparte na nagłówkach logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Co to jest oprogramowanie PingAccess dla usługi Azure AD?

Przy użyciu PingAccess dla usługi Azure AD, użytkownicy mogą dostęp i jednym logowanie jednokrotne do aplikacji, które używające nagłówków na potrzeby uwierzytelniania. Serwer Proxy aplikacji traktuje te aplikacje, takie jak każdy inny, za pomocą usługi Azure AD do uwierzytelniania dostępu i następnie przekazywanie ruchu za pośrednictwem usługi łącznika. Po wystąpieniu uwierzytelniania usługi PingAccess tłumaczy token dostępu usługi Azure AD na format nagłówka, które są wysyłane do aplikacji.

Użytkownicy nie będą zauważyć nic innego po zalogowaniu się do użycia aplikacje firmowe. One nadal pracować z dowolnego miejsca na dowolnym urządzeniu. Łączniki serwera Proxy aplikacji bezpośrednie zdalny ruch do wszystkich aplikacji i będą one nadal równoważenia obciążenia automatycznie.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Jak uzyskać licencję na oprogramowanie PingAccess?

Ponieważ ten scenariusz jest oferowany przez partnerstwo między usługą Azure AD i PingAccess, potrzebujesz licencji dla obu usług. Jednak subskrypcje Azure AD — wersja Premium obejmują podstawową licencję PingAccess, która obejmuje maksymalnie 20 aplikacji. Należy opublikować więcej niż 20 aplikacji opartej na nagłówkach, można uzyskać dodatkową licencję z usługą PingAccess.

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Pokrewne artykuły
* [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md)
* [Konfigurowanie logowania jednokrotnego opartego na protokole SAML](configure-single-sign-on-non-gallery-applications.md)
* [Konfigurowanie logowania jednokrotnego opartego na hasłach](configure-password-single-sign-on-non-gallery-applications.md)
* [Konfigurowanie logowania połączonego](configure-linked-sign-on.md)
* [Wprowadzenie do zarządzania dostępem do aplikacji](what-is-access-management.md)
* Link do pobierania: [plan wdrożenia z logowaniem](https://aka.ms/SSODeploymentPlan)jednokrotnym.
