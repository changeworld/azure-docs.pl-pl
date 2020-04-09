---
title: Tworzenie aplikacji loguj się w usługach Azure AD
titleSuffix: Microsoft identity platform
description: Pokazuje, jak utworzyć aplikację z wieloma dzierżawami, która może logować się do użytkownika z dowolnej dzierżawy usługi Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/17/2020
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: f22ecb13284eaf6fb2a833791b5563351ca19147
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884090"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Jak: Logowanie dowolnego użytkownika usługi Azure Active Directory przy użyciu wzorca aplikacji z wieloma dzierżawcami

Jeśli oferujesz aplikację Software as a Service (SaaS) wielu organizacjom, możesz skonfigurować aplikację tak, aby akceptowała logowania z dowolnej dzierżawy usługi Azure Active Directory (Azure AD). Ta konfiguracja jest wywoływana *co aplikacja wielu dzierżawców*. Użytkownicy w dowolnej dzierżawie usługi Azure AD będą mogli zalogować się do aplikacji po zgodę na użycie konta w aplikacji.

Jeśli masz istniejącą aplikację, która ma własny system kont lub obsługuje inne rodzaje logowania od innych dostawców chmury, dodawanie logowania usługi Azure AD z dowolnej dzierżawy jest proste. Wystarczy zarejestrować aplikację, dodać kod logowania za pośrednictwem OAuth2, OpenID Connect lub SAML i umieścić [przycisk "Zaloguj się za pomocą firmy Microsoft"][AAD-App-Branding] w aplikacji.

> [!NOTE]
> W tym artykule założono, że jesteś już zaznajomiony z tworzeniem aplikacji pojedynczej dzierżawy dla usługi Azure AD. Jeśli nie, zacznij od jednego z przewodników szybki start na [stronie głównej przewodnika dla deweloperów][AAD-Dev-Guide].

Istnieją cztery proste kroki, aby przekonwertować aplikację do aplikacji azure ad wielu dzierżawców:

1. [Aktualizowanie rejestracji aplikacji, aby była wielodostępna](#update-registration-to-be-multi-tenant)
2. [Aktualizowanie kodu w celu wysyłania żądań do /common endpoint](#update-your-code-to-send-requests-to-common)
3. [Aktualizowanie kodu w celu obsługi wielu wartości wystawcy](#update-your-code-to-handle-multiple-issuer-values)
4. [Zrozumienie zgody użytkownika i administratora oraz wprowadzenie odpowiednich zmian w kodzie](#understand-user-and-admin-consent)

Przyjrzyjmy się szczegółowo każdemu krokowi. Można również przejść bezpośrednio do [przykładu Tworzenie wielodostępczej aplikacji sieci web SaaS, która wywołuje program Microsoft Graph przy użyciu usługi Azure AD i OpenID Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Aktualizuj rejestrację, aby była wielodostępna

Domyślnie rejestracje aplikacji sieci Web/interfejsu API w usłudze Azure AD są pojedynczą dzierżawą. Rejestrację można wprowadzić jako wielodostępną, znajdując przełącznik **Obsługiwane typy kont** w okienku **Uwierzytelnianie** rejestracji aplikacji w [portalu Azure][AZURE-portal] i ustawiając ją na Konta w dowolnym **katalogu organizacyjnym.**

Przed aplikacją można zrobić wielu dzierżawców, usługa Azure AD wymaga identyfikatora URI identyfikatora aplikacji, aby być globalnie unikatowy. Identyfikator URI identyfikatora aplikacji jest jednym ze sposobów, w jaki aplikacja jest identyfikowana w komunikatach protokołu. W przypadku aplikacji pojedynczej dzierżawy wystarczy, aby identyfikator URI identyfikatora aplikacji był unikatowy w obrębie tej dzierżawy. W przypadku aplikacji wielodostępnej ten identyfikator musi być globalnie unikatowy, dzięki czemu usługa Azure AD będzie mogła znaleźć aplikację we wszystkich dzierżawach. Globalna unikatowość jest wymuszana poprzez wymaganie, aby identyfikator URI identyfikatora aplikacji miał nazwę hosta, która jest zgodna ze zweryfikowaną domeną dzierżawy usługi Azure AD.

Domyślnie aplikacje utworzone za pośrednictwem witryny Azure portal mają globalnie unikatowy identyfikator URI aplikacji ustawiony na tworzenie aplikacji, ale można zmienić tę wartość. Na przykład, jeśli nazwa dzierżawy została contoso.onmicrosoft.com, a następnie prawidłowy `https://contoso.onmicrosoft.com/myapp`identyfikator URI identyfikatora aplikacji będzie . Jeśli dzierżawa miała zweryfikowaną `contoso.com`domenę , to prawidłowy identyfikator `https://contoso.com/myapp`URI identyfikatora aplikacji będzie również . Jeśli identyfikator URI identyfikatora aplikacji nie jest zgodny z tym wzorcem, ustawienie aplikacji jako aplikacji wielodostępnej zakończy się niepowodzeniem.

> [!NOTE]
> Rejestracje klientów natywnych, a także [aplikacje platformy tożsamości firmy Microsoft](./active-directory-appmodel-v2-overview.md) są domyślnie wielodostępne. Nie trzeba podejmować żadnych działań, aby te rejestracje aplikacji wielu dzierżawców.

## <a name="update-your-code-to-send-requests-to-common"></a>Aktualizowanie kodu w celu wysyłania żądań do /common

W aplikacji pojedynczej dzierżawy żądania logowania są wysyłane do punktu końcowego logowania dzierżawcy. Na przykład dla contoso.onmicrosoft.com punkt końcowy będzie: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Żądania wysyłane do punktu końcowego dzierżawy można zalogować użytkowników (lub gości) w tej dzierżawie do aplikacji w tej dzierżawie.

W przypadku aplikacji z wieloma dzierżawcami aplikacja nie wie z góry, z jakiej dzierżawy pochodzi użytkownik, więc nie można wysyłać żądań do punktu końcowego dzierżawy. Zamiast tego żądania są wysyłane do punktu końcowego, który multipleksuje wszystkich dzierżaw usługi Azure AD:`https://login.microsoftonline.com/common`

Gdy platforma tożsamości firmy Microsoft odbiera żądanie w /common punkt końcowy, podpisuje użytkownika i, w konsekwencji, odnajduje, z której dzierżawy jest użytkownik. /common punkt końcowy współpracuje ze wszystkimi protokołami uwierzytelniania obsługiwanymi przez usługę Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 i WS-Federation.

Odpowiedź logowania do aplikacji następnie zawiera token reprezentujący użytkownika. Wartość wystawcy w tokenie informuje aplikację, z jakiej dzierżawy jest użytkownik. Gdy odpowiedź zwraca z /common punktu końcowego, wartość wystawcy w tokenie odpowiada dzierżawy użytkownika.

> [!IMPORTANT]
> /common punkt końcowy nie jest dzierżawcą i nie jest wystawcą, to tylko multiplekser. Podczas korzystania z /common logiki w aplikacji do sprawdzania poprawności tokenów musi zostać zaktualizowany, aby wziąć to pod uwagę.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Aktualizowanie kodu w celu obsługi wielu wartości wystawcy

Aplikacje sieci Web i interfejsy API sieci Web odbierają i weryfikują tokeny z platformy tożsamości firmy Microsoft.

> [!NOTE]
> Podczas gdy natywne aplikacje klienckie żądają i odbierają tokeny z platformy tożsamości firmy Microsoft, robią to, aby wysłać je do interfejsów API, gdzie są sprawdzane. Aplikacje macierzyste nie sprawdzają poprawności tokenów i muszą traktować je jako nieprzezroczyste.

Przyjrzyjmy się, jak aplikacja sprawdza poprawność tokenów, które otrzymuje z platformy tożsamości firmy Microsoft. Pojedyncza aplikacja dzierżawy zwykle przyjmuje wartość punktu końcowego, taką jak:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

i używa go do konstruowania adresu URL metadanych (w tym przypadku OpenID Connect), takich jak:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

, aby pobrać dwa krytyczne informacje, które są używane do sprawdzania poprawności tokenów: klucze podpisywania dzierżawy i wartość wystawcy. Każda dzierżawa usługi Azure AD ma unikatową wartość wystawcy formularza:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

gdzie wartość guid jest zmień nazwę wersji identyfikatora dzierżawy dzierżawy. Jeśli wybierzesz poprzednie łącze metadanych dla `contoso.onmicrosoft.com`, możesz zobaczyć tę wartość wystawcy w dokumencie.

Gdy pojedyncza aplikacja dzierżawy sprawdza token, sprawdza podpis tokenu względem kluczy podpisywania z dokumentu metadanych. Ten test umożliwia upewnienie się, że wartość wystawcy w tokenie jest zgodna z tą, która została znaleziona w dokumencie metadanych.

Ponieważ /common punkt końcowy nie odpowiada dzierżawcy i nie jest wystawcą, podczas badania wartości wystawcy w metadanych dla /common ma szablonu adresu URL zamiast rzeczywistej wartości:

    https://sts.windows.net/{tenantid}/

W związku z tym aplikacja z wieloma dzierżawami nie może sprawdzić poprawności tokenów `issuer` tylko przez dopasowanie wartości wystawcy w metadanych do wartości w tokenie. Aplikacja wielodostępna wymaga logiki, aby zdecydować, które wartości wystawcy są prawidłowe, a które nie są oparte na części identyfikatora dzierżawy wartości wystawcy. 

Na przykład jeśli aplikacja z wieloma dzierżawami zezwala tylko na logowanie od określonych dzierżawców, którzy zarejestrowali `tid` się w swojej usłudze, należy sprawdzić wartość wystawcy lub wartość oświadczenia w tokenie, aby upewnić się, że dzierżawa znajduje się na ich liście subskrybentów. Jeśli aplikacja wielodostępna zajmuje się tylko osobami i nie podejmuje żadnych decyzji o dostępie na podstawie dzierżawy, może całkowicie zignorować wartość wystawcy.

W [przykładach z wieloma dzierżawcami][AAD-Samples-MT]sprawdzanie poprawności wystawcy jest wyłączone, aby umożliwić dowolnej dzierżawie usługi Azure AD zalogowanie się.

## <a name="understand-user-and-admin-consent"></a>Zrozumienie zgody użytkownika i administratora

Aby użytkownik zalogował się do aplikacji w usłudze Azure AD, aplikacja musi być reprezentowana w dzierżawie użytkownika. Dzięki temu organizacja może wykonywać takie czynności, jak stosowanie unikatowych zasad, gdy użytkownicy z dzierżawy logują się do aplikacji. W przypadku aplikacji pojedynczej dzierżawy ta rejestracja jest prosta; jest to ten, który dzieje się podczas rejestracji aplikacji w [witrynie Azure portal][AZURE-portal].

W przypadku aplikacji z wieloma dzierżawcami początkowa rejestracja aplikacji jest mieszkana w dzierżawie usługi Azure AD używanej przez dewelopera. Gdy użytkownik z innej dzierżawy loguje się do aplikacji po raz pierwszy, usługa Azure AD prosi o zgodę na uprawnienia wymagane przez aplikację. Jeśli wyrazi zgodę, w dzierżawie użytkownika zostanie utworzona reprezentacja aplikacji o nazwie *jednostki usługi,* a logowanie może być kontynuowane. Delegowanie jest również tworzony w katalogu, który rejestruje zgodę użytkownika do aplikacji. Aby uzyskać szczegółowe informacje na temat aplikacji application's Application i ServicePrincipal obiektów i jak odnoszą się one do siebie, zobacz [Application obiektów i usług głównych obiektów][AAD-App-SP-Objects].

![Ilustruje zgodę na aplikację jednowarstwową][Consent-Single-Tier]

Na to środowisko zgody mają wpływ uprawnienia wymagane przez aplikację. Platforma tożsamości firmy Microsoft obsługuje dwa rodzaje uprawnień, tylko do aplikacji i delegowane.

* Uprawnienie delegowane daje aplikacji możliwość działania jako zalogowany użytkownik dla podzbioru rzeczy, które użytkownik może zrobić. Na przykład można udzielić aplikacji delegowane uprawnienia do odczytu podpisanego w kalendarzu użytkownika.
* Uprawnienie tylko do aplikacji jest przyznawane bezpośrednio do tożsamości aplikacji. Na przykład można udzielić aplikacji tylko do aplikacji uprawnienia do odczytu listy użytkowników w dzierżawie, niezależnie od tego, kto jest zalogowany do aplikacji.

Niektóre uprawnienia mogą być zgód przez zwykłego użytkownika, podczas gdy inne wymagają zgody administratora dzierżawy. 

### <a name="admin-consent"></a>zgoda administratora

Uprawnienia dotyczące tylko aplikacji zawsze wymagają zgody administratora dzierżawy. Jeśli aplikacja żąda uprawnienia tylko do aplikacji, a użytkownik próbuje zalogować się do aplikacji, zostanie wyświetlony komunikat o błędzie informujący, że użytkownik nie może wyrazić zgody.

Niektóre uprawnienia delegowane wymagają również zgody administratora dzierżawy. Na przykład możliwość zapisu z powrotem do usługi Azure AD jako zalogowany użytkownik wymaga zgody administratora dzierżawy. Podobnie jak uprawnienia tylko do aplikacji, jeśli zwykły użytkownik próbuje zalogować się do aplikacji, która żąda delegowanej zgody, która wymaga zgody administratora, aplikacja odbiera błąd. Czy uprawnienie wymaga zgody administratora jest określana przez dewelopera, który opublikował zasób i można znaleźć w dokumentacji zasobu. Dokumentacja uprawnień [dla interfejsu API programu Microsoft Graph][MSFT-Graph-permission-scopes] wskazuje, które uprawnienia wymagają zgody administratora.

Jeśli aplikacja używa uprawnień, które wymagają zgody administratora, musisz mieć gest, taki jak przycisk lub łącze, w którym administrator może zainicjować akcję. Żądanie, które aplikacja wysyła dla tej akcji jest zwykłym żądaniem autoryzacji OAuth2/OpenID Connect, które zawiera również parametr ciągu `prompt=admin_consent` zapytania. Gdy administrator wyraził zgodę, a podmiot usługi zostanie utworzony w dzierżawie klienta, `prompt=admin_consent` kolejne żądania logowania nie wymagają parametru. Ponieważ administrator zdecydował, że żądane uprawnienia są dopuszczalne, żaden inny użytkownik w dzierżawie nie jest monitowany o zgodę od tego momentu do przodu.

Administrator dzierżawy może wyłączyć możliwość wyrażania zgody na aplikacje przez zwykłych użytkowników. Jeśli ta funkcja jest wyłączona, zgoda administratora jest zawsze wymagana do używania aplikacji w dzierżawie. Jeśli chcesz przetestować aplikację z wyłączeną zgodą użytkownika końcowego, możesz znaleźć przełącznik konfiguracji w [witrynie Azure portal][AZURE-portal] w sekcji **[Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** w obszarze **Aplikacje przedsiębiorstwa**.

Parametr `prompt=admin_consent` może być również używany przez aplikacje, które żądają uprawnień, które nie wymagają zgody administratora. Przykładem, kiedy to będzie używany jest, jeśli aplikacja wymaga środowiska, w którym administrator dzierżawy "rejestruje się" jeden raz, a żaden inny użytkownik nie są monitowani o zgodę od tego momentu.

Jeśli aplikacja wymaga zgody administratora, a `prompt=admin_consent` administrator loguje się bez wysyłanego parametru, gdy administrator pomyślnie wyrazi zgodę na aplikację, będzie on aplikować **tylko do swojego konta użytkownika**. Zwykli użytkownicy nadal nie będą mogli się zalogować ani wyrazić zgody na aplikację. Ta funkcja jest przydatna, jeśli chcesz dać administratorowi dzierżawy możliwość eksplorowania aplikacji przed zezwoleniem innym użytkownikom na dostęp.

> [!NOTE]
> Niektóre aplikacje chcą środowiska, w którym zwykli użytkownicy są w stanie wyrazić zgodę początkowo, a później aplikacja może obejmować administratora i żądania uprawnień, które wymagają zgody administratora. Nie ma sposobu, aby to zrobić z rejestracją aplikacji w wersji 1.0 w usłudze Azure AD dzisiaj; jednak przy użyciu punktu końcowego platformy tożsamości firmy Microsoft (w wersji 2.0) umożliwia aplikacjom żądanie uprawnień w czasie wykonywania, a nie w czasie rejestracji, co umożliwia ten scenariusz. Aby uzyskać więcej informacji, zobacz [punkt końcowy platformy tożsamości firmy Microsoft][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Zgoda i aplikacje wielowarstwowe

Aplikacja może mieć wiele warstw, z których każda jest reprezentowana przez własną rejestrację w usłudze Azure AD. Na przykład natywna aplikacja, która wywołuje internetowy interfejs API lub aplikacja sieci web, która wywołuje internetowy interfejs API. W obu tych przypadkach klient (aplikacja natywna lub aplikacja sieci web) żąda uprawnień do wywołania zasobu (internetowego interfejsu API). Aby klient został pomyślnie zezwolony na dzierżawę klienta, wszystkie zasoby, do których żąda uprawnień, muszą już istnieć w dzierżawie klienta. Jeśli ten warunek nie jest spełniony, usługa Azure AD zwraca błąd, który należy najpierw dodać zasób.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Wiele warstw w jednej dzierżawie

Może to być problem, jeśli aplikacja logiczna składa się z dwóch lub więcej rejestracji aplikacji, na przykład oddzielny klient i zasób. Jak najpierw uzyskać zasób do dzierżawy klienta? Usługa Azure AD obejmuje ten przypadek, umożliwiając klientowi i zasobowi wyrażenie zgody w jednym kroku. Użytkownik widzi sumę uprawnień wymaganych zarówno przez klienta, jak i zasobu na stronie zgody. Aby włączyć to zachowanie, rejestracja aplikacji zasobu musi zawierać identyfikator `knownClientApplications` aplikacji klienta jako w [manifeście aplikacji.][AAD-App-Manifest] Przykład:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Jest to wykazane w wielowarstwowym natywnym kliencie wywołującym przykład interfejsu API sieci web w sekcji [Powiązana zawartość](#related-content) na końcu tego artykułu. Poniższy diagram zawiera omówienie zgody na wielowarstwową aplikację zarejestrowaną w jednej dzierżawie.

![Ilustruje zgodę na wielowarstwową znaną aplikację kliencką][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Wiele warstw w wielu dzierżawach

Podobny przypadek ma miejsce, jeśli różne warstwy aplikacji są zarejestrowane w różnych dzierżawców. Rozważmy na przykład przypadek tworzenia natywnej aplikacji klienckiej, która wywołuje interfejs API usługi Office 365 Exchange Online. Aby opracować aplikację macierzystą, a później dla aplikacji natywnej do uruchomienia w dzierżawie klienta, jednostki usługi Exchange Online musi być obecny. W takim przypadku deweloper i klient musi zakupić usługę Exchange Online dla jednostki usługi, które mają zostać utworzone w ich dzierżaw.

Jeśli jest to interfejs API utworzony przez organizację inną niż Microsoft, deweloper interfejsu API musi zapewnić swoim klientom sposób wyrażenia zgody na aplikację do dzierżawców swoich klientów. Zalecany projekt jest dla dewelopera innej firmy do tworzenia interfejsu API, tak aby mógł również działać jako klient sieci web do zaimplementowania rejestracji. W tym celu:

1. Postępuj zgodnie z wcześniejszymi sekcjami, aby upewnić się, że interfejs API implementuje wymagania dotyczące rejestracji/kodu aplikacji z wieloma dzierżawami.
2. Oprócz uwidaczniania zakresów/ról interfejsu API upewnij się, że rejestracja zawiera uprawnienie "Zaloguj się i odczytuj profil użytkownika" (domyślnie podane).
3. Zaimplementuj stronę logowania/rejestracji w kliencie sieci web i postępuj zgodnie ze wskazówkami dotyczącymi [zgody administratora.](#admin-consent)
4. Gdy użytkownik wyraża zgodę na aplikację, dublowana usługa i łącza delegowania zgody są tworzone w ich dzierżawy, a natywna aplikacja może uzyskać tokeny dla interfejsu API.

Poniższy diagram zawiera omówienie zgody na wielowarstwową aplikację zarejestrowaną w różnych dzierżawach.

![Ilustruje zgodę na wielopoziomową aplikację wielopartyjną][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Cofnięcie zgody

Użytkownicy i administratorzy mogą w dowolnym momencie cofnąć zgodę na aplikację:

* Użytkownicy cofają dostęp do poszczególnych aplikacji, usuwając je z listy [Aplikacje panelu dostępu.][AAD-Access-Panel]
* Administratorzy cofają dostęp do aplikacji, usuwając je za pomocą sekcji [Aplikacje przedsiębiorstwa](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) w [witrynie Azure portal][AZURE-portal].

Jeśli administrator wyraża zgodę na aplikację dla wszystkich użytkowników w dzierżawie, użytkownicy nie mogą odwołać dostępu indywidualnie. Tylko administrator może odwołać dostęp i tylko dla całej aplikacji.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplikacje dla wielu dzierżawców i tokeny dostępu do buforowania

Aplikacje z wieloma dzierżawami mogą również uzyskać tokeny dostępu do wywoływania interfejsów API, które są chronione przez usługę Azure AD. Typowym błędem podczas korzystania z biblioteki uwierzytelniania usługi Active Directory (ADAL) z aplikacją wielodostępną jest początkowo żądanie tokenu dla użytkownika korzystającego z /common, otrzymanie odpowiedzi, a następnie żądanie kolejnego tokenu dla tego samego użytkownika również przy użyciu /common. Ponieważ odpowiedź z usługi Azure AD pochodzi z dzierżawy, nie /common, ADAL buforuje token jako pochodzący z dzierżawy. Kolejne wywołanie /common, aby uzyskać token dostępu dla użytkownika nie przyjmuje wpisu pamięci podręcznej, a użytkownik jest monitowany o ponowne zalogowanie się. Aby uniknąć braku pamięci podręcznej, upewnij się, że kolejne wywołania już zalogowanego użytkownika są dokonywane do punktu końcowego dzierżawy.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak utworzyć aplikację, która może zalogować się w użytkowniku z dowolnej dzierżawy usługi Azure AD. Po włączeniu logowania jednokrotnego (SSO) między aplikacją a usługą Azure AD można również zaktualizować aplikację, aby uzyskać dostęp do interfejsów API udostępniane przez zasoby firmy Microsoft, takie jak Office 365. Dzięki temu można zaoferować spersonalizowane środowisko w aplikacji, takie jak wyświetlanie informacji kontekstowych dla użytkowników, takich jak ich zdjęcie profilowe lub ich następnego terminu kalendarza. Aby dowiedzieć się więcej o nawiązywać połączenia interfejsu API z usługami Azure AD i Office 365, takimi jak Exchange, SharePoint, OneDrive, OneNote i inne, odwiedź [witrynę Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Zawartość pokrewna

* [Przykład aplikacji dla wielu dzierżawców](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Wytyczne dotyczące oznaczania aplikacji marką][AAD-App-Branding]
* [Obiekty aplikacji i obiekty głównej usługi][AAD-App-SP-Objects]
* [Integrating applications with Azure Active Directory][AAD-Integrating-Apps] (Integrowanie aplikacji za pomocą usługi Azure Active Directory)
* [Przegląd ram zgody][AAD-Consent-Overview]
* [Zakresy uprawnień interfejsu API programu Microsoft Graph][MSFT-Graph-permission-scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://docs.microsoft.com/samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
