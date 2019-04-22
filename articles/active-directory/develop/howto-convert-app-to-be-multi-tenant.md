---
title: Jak utworzyć aplikację, która może zalogować dowolnego użytkownika usługi Azure AD
description: Pokazuje sposób tworzenia aplikacji z wieloma dzierżawami, która może zalogować użytkownika z dzierżawami usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: jmprieur, lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2054a873d73bce7048ef9e48adabf3fb5279df9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500400"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Instrukcje: Logowanie dowolnego użytkownika usługi Azure Active Directory za pomocą wzorca aplikacji wielodostępnych

Jeśli oferujesz oprogramowanie jako usługa (SaaS) aplikacji dla wielu organizacji, można skonfigurować aplikację, aby akceptować logowania z dzierżawami usługi Azure Active Directory (Azure AD). Ta konfiguracja jest nazywana *wprowadzania Twojej aplikacji wielodostępnych*. Użytkownicy w dowolnej dzierżawy usługi Azure AD będą mogli logować się do aplikacji po wyrażanie zgody na korzystanie z tego konta z aplikacją.

Jeśli masz istniejącą aplikację, która ma swój własny system konta lub obsługuje inne rodzaje logowania z innych dostawców rozwiązań w chmurze, dodanie usługi Azure AD Zaloguj się za pomocą dowolnej dzierżawy jest proste. Po prostu Zarejestruj swoją aplikację, Dodaj kod, zaloguj się przy użyciu protokołu OAuth2, OpenID Connect lub SAML oraz umieszczać [przycisku "Sign in with Microsoft"] [ AAD-App-Branding] w aplikacji.

> [!NOTE]
> W tym artykule przyjęto założenie, że znasz już Kompilowanie aplikacji pojedynczej dzierżawy usługi Azure AD. Jeśli nie masz, uruchomić przy użyciu jednego z przewodników Szybki Start na [strony głównej przewodnik dla deweloperów][AAD-Dev-Guide].

Istnieją cztery proste kroki, aby przekonwertować aplikacji w aplikacji wielodostępnej usługi Azure AD:

1. [Aktualizuj rejestrację aplikacji w taki sposób, aby się z wieloma dzierżawcami](#update-registration-to-be-multi-tenant)
2. [Zaktualizuj kod w celu wysyłania żądań do / Common punktu końcowego](#update-your-code-to-send-requests-to-common)
3. [Zaktualizuj swój kod obsługi wielu wartości wystawcy](#update-your-code-to-handle-multiple-issuer-values)
4. [Zrozumienie zgody użytkownika i administratora i wprowadź zmiany odpowiedni kod](#understand-user-and-admin-consent)

Spójrzmy na poszczególnych kroków w artykule. Możesz też przejść bezpośrednio do [tej listy przykłady wielodostępnych][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Aktualizowanie rejestracji jako wielodostępnych

Rejestracje interfejsu API i aplikacji sieci web w usłudze Azure AD są domyślnie pojedynczej dzierżawy. Aby włączyć rejestrację wielodostępne, wyszukując **wielu gośćmi** przełączyć się **właściwości** rejestrację aplikacji w okienku [witryny Azure portal] [ AZURE-portal] i ustawieniem dla niego **tak**.

Przed aplikacji wielodostępnych, usługa Azure AD wymaga identyfikator URI Identyfikatora aplikacji aplikacji mogą być globalnie unikatowa. Identyfikator URI identyfikatora aplikacji jest jednym ze sposobów, w jaki aplikacja jest identyfikowana w komunikatach protokołu. W przypadku aplikacji pojedynczej dzierżawy wystarczy, aby identyfikator URI identyfikatora aplikacji był unikatowy w obrębie tej dzierżawy. W przypadku aplikacji wielodostępnej ten identyfikator musi być globalnie unikatowy, dzięki czemu usługa Azure AD będzie mogła znaleźć aplikację we wszystkich dzierżawach. Globalna unikatowość jest wymuszana poprzez wymaganie, aby identyfikator URI identyfikatora aplikacji miał nazwę hosta, która jest zgodna ze zweryfikowaną domeną dzierżawy usługi Azure AD.

Domyślnie aplikacje utworzone w witrynie Azure portal mają globalnie unikatowy identyfikator URI aplikacji ustaw przy tworzeniu aplikacji, ale możesz zmienić tę wartość. Na przykład, jeśli nazwa Twojej dzierżawy został contoso.onmicrosoft.com, a następnie prawidłowy identyfikator URI Identyfikatora aplikacji będzie `https://contoso.onmicrosoft.com/myapp`. Jeśli zweryfikowaną domenę dzierżawy `contoso.com`, a następnie prawidłowy identyfikator URI aplikacji będą również `https://contoso.com/myapp`. Jeśli identyfikator URI identyfikatora aplikacji nie jest zgodny z tym wzorcem, ustawienie aplikacji jako aplikacji wielodostępnej zakończy się niepowodzeniem.

> [!NOTE]
> Rejestracje klienta natywnego także [aplikacji platforma tożsamości firmy Microsoft](./active-directory-appmodel-v2-overview.md) są wielodostępne domyślnie. Nie trzeba podejmować żadnych działań do tych rejestracji aplikacji wielodostępnych.

## <a name="update-your-code-to-send-requests-to-common"></a>Zaktualizuj kod w celu wysyłania żądań do/Common

W przypadku aplikacji pojedynczej dzierżawy żądań logowania są wysyłane do dzierżawy logowania punktu końcowego. Na przykład dla contoso.onmicrosoft.com punkt końcowy będzie: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Żądania wysyłane do punktu końcowego dzierżawcy mogą logować użytkowników (lub gości) w tej dzierżawie, do aplikacji w tej dzierżawie.

Za pomocą aplikacji z wieloma dzierżawami aplikacji nie może ustalić na początku dzierżawy, jakie użytkownik jest, więc nie mogą wysyłać żądania do punktu końcowego dzierżawcy. Zamiast tego żądania są wysyłane do punktu końcowego, który multipleksuje dla dzierżaw wszystkie usługi Azure AD: `https://login.microsoftonline.com/common`

Gdy platforma tożsamości usługi Microsoft otrzyma żądanie na / Common punktu końcowego, jego loguje się użytkownik i, w konsekwencji odnajduje dzierżawy, który użytkownik pochodzi z. / Endpoint wspólnego pracuje ze wszystkimi protokołów uwierzytelniania, obsługiwanych przez usługę Azure AD:  OpenID Connect, OAuth 2.0, SAML 2.0 i WS-Federation.

Odpowiedź logowania do aplikacji, następnie zawiera token reprezentujący użytkownika. Wartość wystawcy w tokenie informuje aplikację dzierżawy, jakie użytkownik pochodzi. Gdy odpowiedzi zwraca znajdujący punktu końcowego, wartość wystawcy w tokenie odnosi się do dzierżawy użytkownika.

> [!IMPORTANT]
> / Wspólnego punktu końcowego nie dzierżawę i nie jest wystawcy, jest po prostu multiplekser. Używając/Common logikę w aplikacji w taki sposób, aby sprawdzał poprawność tokenów musi zostać zaktualizowane, aby wziąć pod uwagę.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Zaktualizuj swój kod obsługi wielu wartości wystawcy

Aplikacji internetowych i internetowych interfejsów API odbierania i sprawdzania poprawności tokenów z platformą Microsoft identity.

> [!NOTE]
> Natywne aplikacje klienckie żądania i odbierania tokenów z platformą Microsoft identity, to zrobią wysyłać je do interfejsów API, w którym są prawidłowe. Aplikacje natywne nie weryfikują tokeny i musisz potraktować je jako nieprzezroczysty.

Przyjrzyjmy się w jaki sposób aplikacja weryfikuje tokeny otrzymuje z platformą Microsoft identity. Aplikacja jednej dzierżawy trwa zwykle wartości punktu końcowego, takie jak:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

i używa go do utworzenia metadanych adres URL (w tym przypadku OpenID Connect), takich jak:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Aby pobrać dwóch najistotniejsze informacje, które są używane do sprawdzania poprawności tokenów: dzierżawy podpisywania kluczy i wartości wystawcy. Każda dzierżawa usługi Azure AD ma wartość unikatowego wystawcy formularza:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

gdy wartość GUID jest identyfikator dzierżawy w dzierżawie w wersji safe zmiany nazwy. Jeśli wybierzesz pod powyższym linkiem metadanych dla `contoso.onmicrosoft.com`, zobaczysz tę wartość wystawcy w dokumencie.

Gdy aplikacja jednej dzierżawy weryfikuje token, sprawdza podpis tokenu względem klucze podpisywania z dokumentu metadanych. Ten test pozwala ją, aby upewnić się, że wartość wystawcy w tokenie jest zgodna ze strukturą, która została znaleziona w dokumencie metadanych.

Ponieważ / wspólnego punktu końcowego nie odnoszą się do dzierżawy i nie jest wystawcy, sprawdź wartość wystawcy w metadanych dla/common ma z szablonem adresu URL zamiast rzeczywistej wartości:

    https://sts.windows.net/{tenantid}/

W związku z tym, aplikacji z wieloma dzierżawami nie można sprawdzić poprawności tokenów po prostu, dopasowując wartości wystawcy metadanych za pomocą `issuer` wartość w tokenie. Aplikacja wielodostępna musi logikę do określania wartości wystawcy, które są prawidłowe i nie są oparte na część Identyfikatora dzierżawy wartości wystawcy. 

Na przykład, jeśli aplikacja wielodostępna umożliwia tylko logowania z określonym dzierżawcy, którzy utworzyli konto usługi, następnie powinien sprawdzić wartość wystawcy lub `tid` wartości w tokenie, aby upewnić się, że dzierżawa znajduje się w ich listę subskrybentów oświadczenia. Jeśli aplikacja wielodostępna dotyczy osób i tylko nie decyzje dotyczące dowolnego dostępu oparte na dzierżaw, następnie go zignorować wartości wystawcy całkowicie.

W [przykłady wielodostępnych][AAD-Samples-MT], sprawdzania poprawności wystawcy jest wyłączona, aby włączyć wszystkie dzierżawy usługi Azure AD zalogować się.

## <a name="understand-user-and-admin-consent"></a>Zrozumienie zgody użytkowników i administratorów

Dla użytkownika do logowania do aplikacji w usłudze Azure AD aplikacja musi być reprezentowana w dzierżawie użytkownika. Dzięki temu organizacji do wykonywania czynności takich jak stosowanie zasad unikatowe, gdy użytkownicy z dzierżawy Zaloguj się do aplikacji. Rejestracja aplikacji pojedynczej dzierżawy, jest proste; jest ten, który się dzieje po zarejestrowaniu aplikacji w [witryny Azure portal][AZURE-portal].

W przypadku aplikacji wielodostępnych wstępnej rejestracji aplikacji znajduje się w dzierżawie usługi Azure AD używany przez dewelopera. Po zalogowaniu się użytkownika z innej dzierżawy do aplikacji po raz pierwszy usługi Azure AD pyta, czy je do wyrażenia zgody na uprawnienia wymagane przez aplikację. Mogą wyrazić zgodę, a następnie reprezentację aplikacji o nazwie *nazwy głównej usługi* zostanie utworzony w dzierżawie użytkownika i logowania można kontynuować. Delegowanie również jest tworzony w katalogu, który rejestruje zgody użytkownika do aplikacji. Szczegółowe informacje na temat aplikacji w aplikacji i ServicePrincipal obiektów i jak powiązane są ze sobą, [obiekty aplikacji i obiektów nazw głównych usług][AAD-App-SP-Objects].

![Zgoda na aplikacji dla jednowarstwowej][Consent-Single-Tier]

To środowisko zgody dotyczy uprawnień żądany przez aplikację. Platforma tożsamości firmy Microsoft obsługuje dwa rodzaje uprawnień, tylko do aplikacji i delegowanego.

* Uprawnienia delegowane przyznaje aplikacji zrobić zdolność do działania jako zalogowanego użytkownika dla podzbioru czynności użytkownika. Na przykład można przyznać aplikacji delegowane uprawnienia do odczytu kalendarza zalogowanego użytkownika.
* Uprawnienia tylko do aplikacji jest udzielane bezpośrednio do tożsamość aplikacji. Na przykład można udzielić aplikacji uprawnień tylko do aplikacji można odczytać listy użytkowników w dzierżawie, niezależnie od tego, kto jest zalogowany do aplikacji.

Niektóre uprawnienia mogą wyrażono zgodę przez zwykłego użytkownika, a inne wymagają zgody administratora dzierżawy. 

### <a name="admin-consent"></a>zgoda administratora

Uprawnienia dotyczące tylko aplikacji zawsze wymagają zgody administratora dzierżawy. Jeśli Twoja aplikacja żąda uprawnienia tylko do aplikacji, a użytkownik próbuje zalogować się do aplikacji, jest wyświetlany komunikat o błędzie, informujący o tym, że użytkownik nie jest w stanie do wyrażenia zgody.

Niektóre uprawnienia delegowane również wymagają zgody administratora dzierżawy. Na przykład możliwość zapisania z powrotem do usługi Azure AD jako zalogowany użytkownik wymaga zgody administratora dzierżawy. Jak uprawnień dotyczących tylko aplikacji Jeśli zwykły użytkownik próbuje zalogować się do aplikacji, która żąda uprawnienia delegowanego, który wymaga zgody administratora aplikacji otrzymuje informację o błędzie. Czy uprawnienie wymaga zgody administratora jest określany przez dewelopera, opublikowane zasobu, która znajduje się w dokumentacji dla zasobu. W dokumentacji uprawnienia [interfejsu API usługi Azure AD Graph] [ AAD-Graph-Perm-Scopes] i [interfejsu API Microsoft Graph] [ MSFT-Graph-permission-scopes] wskazują, uprawnienia, które wymagają administratora wyrażenie zgody.

Jeśli aplikacja używa uprawnień, które wymagają zgody administratora, musisz mieć gest, takich jak przycisk lub łącze, gdzie administrator może zainicjować akcji. Żądania, Twoja aplikacja przesyła ta akcja jest zwykle OAuth2/OpenID Connect żądanie autoryzacji, która obejmuje również `prompt=admin_consent` parametr ciągu zapytania. Gdy administrator wyraził zgodę i nazwy głównej usługi jest tworzony w dzierżawie klienta, kolejne żądania logowania nie ma potrzeby `prompt=admin_consent` parametru. Ponieważ administrator podjęto decyzję, że żądane uprawnienia są dopuszczalne, żaden użytkownik w dzierżawie monit o zgodę od tego momentu.

Administrator dzierżawy może wyłączyć możliwość wyrażania zgody na aplikacje przez zwykłych użytkowników. Jeśli ta funkcja jest wyłączona, zgoda administratora jest zawsze wymagana do używania aplikacji w dzierżawie. Aby przetestować aplikację, za zgodą użytkownika końcowego wyłączone, możesz znaleźć przełącznika konfiguracji w [witryny Azure portal] [ AZURE-portal] w **[ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** sekcji **aplikacje dla przedsiębiorstw**.

`prompt=admin_consent` Parametru używać przez aplikacje, które zażądać uprawnień, które nie wymagają zgody administratora. Przykładem kiedy będzie to używana jest, jeśli aplikacja wymaga środowiska, w którym administrator dzierżawy "zarejestruje się w" jednym czasie, a żadne inne monit o zgodę od tego momentu.

Jeśli aplikacja wymaga zgody administratora, a administrator loguje się bez `prompt=admin_consent` parametru są wysyłane, gdy administrator pomyślnie wyraża zgodę aplikacji będzie stosowana **tylko dla swojego konta użytkownika**. Regularne użytkownicy nadal nie będą mogli zalogować się lub wyrazić zgodę na aplikację. Ta funkcja jest przydatna, jeśli chcesz nadać administratorowi dzierżawy możliwość badania aplikacji przed zezwoleniem na dostęp innym użytkownikom.

> [!NOTE]
> Niektóre aplikacje mają środowisko, w którym zwykli użytkownicy będą mogli początkowo zgody, a później aplikacja może obejmować uprawnienia administratora i żądania, które wymagają zgody administratora. Nie ma możliwości w tym celu z rejestracji aplikacji w wersji 1.0 w usłudze Azure AD obecnie; Jednak przy użyciu punktu końcowego platformy (w wersji 2.0) dla tożsamości firmy Microsoft umożliwia aplikacji, aby zażądać uprawnień w czasie wykonywania, a nie w czasie rejestracji, który umożliwia ten scenariusz. Aby uzyskać więcej informacji, zobacz [punktu końcowego platformy tożsamości firmy Microsoft][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Aplikacje wielowarstwowe i zgody

Aplikacja może mieć wiele warstw, każdy reprezentowany przez własną rejestracji w usłudze Azure AD. Na przykład aplikacji natywnej, która wywołuje interfejs API sieci web lub aplikacji sieci web, wywołuje interfejs API sieci web. W obu przypadkach klient (natywnej aplikacji lub aplikacji sieci web) żąda uprawnień do wywoływania zasobów (interfejsu API sieci web). Klient pomyślnie wyrazić w dzierżawie klienta wszystkie zasoby, do których żąda uprawnień musi już istnieć w dzierżawie klienta. Jeśli ten warunek nie zostanie spełniony, usługi Azure AD zwraca błąd, czy zasób należy najpierw dodać.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Wiele warstw w pojedynczej dzierżawy

Może to być problemem, jeśli Twojej aplikacji logicznych składa się z co najmniej dwóch rejestracje aplikacji, na przykład odrębny klient i zasobów. Jak można uzyskać zasobu do dzierżawy klienta pierwszy? Usługa Azure AD obejmuje takim po włączeniu klient i zasobów wyrazić w jednym kroku. Użytkownik zobaczy łączną sumę liczby uprawnień żądany przez klienta i zasobów na strona zgody użytkownika. Aby włączyć to zachowanie, Rejestracja aplikacji zasobu musi zawierać Identyfikatora aplikacji klienckiej jako `knownClientApplications` w jego [manifest aplikacji][AAD-App-Manifest]. Na przykład:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Jest to zaprezentowane w wielowarstwowej klient natywny, w przypadku wywoływania przykładowym internetowym interfejsie API [powiązana zawartość](#related-content) sekcji na końcu tego artykułu. Poniższy diagram zawiera omówienie wyrażania zgody dla aplikacji wielowarstwowych, zarejestrowany w jednej dzierżawie.

![Zgoda na aplikację wielowarstwową znanych klienta][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Wiele warstw w wielu dzierżaw

Podobne przypadek się dzieje w przypadku różnych warstw w aplikacji są rejestrowane w różnych dzierżawach. Na przykład należy wziąć pod uwagę w przypadku tworzenia aplikacji klienta natywnego, który wywołuje z pakietu Office 365 Exchange Online interfejsu API. Do tworzenia natywnych aplikacji i nowszej dla natywnych aplikacji do uruchamiania w dzierżawie klienta, nazwa główna usługi Exchange Online musi być obecny. W takim przypadku dla deweloperów i klientów musi zakupić usługi Exchange Online do tej jednostki zostały utworzone w ich dzierżawami usługi.

Jeśli jest to interfejs API utworzone przez organizację innych niż Microsoft, dla deweloperów interfejsu API musi umożliwiają klientom zgody aplikacji na swoim klientom dzierżaw. Zalecany projekt jest dla deweloperów innych firm do tworzenia interfejsu API w taki sposób, że może również działać jako klient sieci web w celu zaimplementowania rejestracji. W tym celu:

1. Postępuj zgodnie z wcześniejszych sekcjach, aby upewnić się, że interfejs API implementuje wymagania dotyczące rejestracji/kodu aplikacji wielodostępnych.
2. Oprócz udostępnianie interfejsu API zakresy/role, upewnij się, zawiera rejestracji "Zaloguj się i odczytuj profil użytkownika" uprawnienia (udostępnione domyślnie).
3. Implementowanie strony logowania — w/rejestracją w kliencie sieci web i postępuj zgodnie z [zgody administratora](#admin-consent) wskazówki.
4. Po użytkownik wyraża zgodę na aplikację, jednostki i zgody delegowania łącza do usług są tworzone w ramach ich dzierżawy, a następnie aplikacji natywnej mogą uzyskiwać tokeny interfejsu API.

Poniższy diagram zawiera omówienie wyrażania zgody dla aplikacji wielowarstwowych, zarejestrowanych w różnych dzierżawach.

![Zgoda na wieloosobowa aplikacji wielowarstwowej][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Trwa odwoływanie zgody

Użytkownicy i Administratorzy można odwołać zgody na aplikację, w dowolnym momencie:

* Użytkownicy odwołać dostęp do poszczególnych aplikacji, usuwając je z ich [aplikacje panelu dostępu] [ AAD-Access-Panel] listy.
* Administratorzy odwołać dostęp do aplikacji, usuwając je przy użyciu [aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) części [witryny Azure portal][AZURE-portal].

Jeśli administrator wyraża zgodę na aplikacji dla wszystkich użytkowników w dzierżawie, użytkownicy nie mogą indywidualnie odwołać dostęp. Tylko administrator może odwołać dostęp i tylko dla całej aplikacji.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplikacje wielodostępne i buforowanie tokenów dostępu

Wielodostępne aplikacje można również uzyskać tokenów dostępu w celu wywoływania interfejsów API, które są chronione przez usługę Azure AD. Typowym błędem podczas używania biblioteki uwierzytelniania usługi Active Directory (ADAL) za pomocą aplikacji z wieloma dzierżawami jest początkowo żądania tokenu dla użytkownika za pomocą/Common, otrzymują odpowiedź, a następnie wysłania żądania tokenu służącego kolejnych dla tego samego użytkownika, również przy użyciu/Common. Ponieważ odpowiedzi z usługi Azure AD nie pochodzi z dzierżawy, / wspólne biblioteki ADAL zapisuje w pamięci podręcznej tokenu jako pochodzącej z dzierżawy. Kolejne wywołanie/Common do uzyskania tokenu dostępu dla użytkownika chybień wpisu pamięci podręcznej, a użytkownik jest monitowany, aby zalogować się ponownie. Aby uniknąć, Brak pamięci podręcznej, upewnij się, że kolejne dla już zalogowany użytkownik wywołań do punktu końcowego dzierżawcy.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób tworzenia aplikacji, która może zalogować użytkownika z dzierżawami usługi Azure AD. Po włączeniu pojedynczego logowania jednokrotnego (SSO) między aplikacji i usługi Azure AD, ale też aktualizować aplikacji dostęp do interfejsów API udostępnianych przez zasoby firmy Microsoft, takich jak Office 365. Dzięki temu można zaoferować spersonalizowane środowisko, w aplikacji, takich jak pokazywanie informacji kontekstowych dla użytkowników, takich jak swoje zdjęcie profilowe lub dalej termin z kalendarza. Aby dowiedzieć się więcej o tworzeniu interfejsu API wywołań do usługi Azure AD i Office 365 usług takich jak Exchange, SharePoint, OneDrive, OneNote i uzyskać więcej informacji, odwiedź stronę [interfejsu API Microsoft Graph][MSFT-Graph-overview].

## <a name="related-content"></a>Powiązana zawartość

* [Przykłady aplikacji wielodostępnych][AAD-Samples-MT]
* [Wytyczne dotyczące aplikacji oznaczania marką][AAD-App-Branding]
* [Obiekty aplikacji i obiektów nazw głównych usług][AAD-App-SP-Objects]
* [Integrowanie aplikacji z usługą Azure Active Directory][AAD-Integrating-Apps]
* [Omówienie platformy wyrażania zgody][AAD-Consent-Overview]
* [Zakresy uprawnień interfejsu API Microsoft Graph][MSFT-Graph-permission-scopes]
* [Zakresy uprawnień w usłudze Azure AD Graph API][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
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
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
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
