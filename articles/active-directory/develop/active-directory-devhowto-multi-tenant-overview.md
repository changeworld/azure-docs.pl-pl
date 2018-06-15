---
title: Sposób tworzenia aplikacji, które można zarejestrować się w każdy użytkownik usługi Azure AD
description: Pokazuje, jak utworzyć aplikację wielodostępne, której można zalogować użytkownika z dowolnej dzierżawy usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2018
ms.author: celested
ms.reviewer: elisol
ms.custom: aaddev
ms.openlocfilehash: fd02cde6327cb929d1b4c0c2e3d430d64645ca26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157375"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Jak zarejestrować każdy użytkownik usługi Azure Active Directory przy użyciu wzorca wielodostępnych aplikacji
Jeśli oferujesz oprogramowania jako usługi aplikacji dla wielu organizacji, można skonfigurować aplikację do akceptowania logowania z dowolnej dzierżawy usługi Azure Active Directory (AD). Ta konfiguracja jest nazywana tworzenie dzierżawy usługi aplikacji. Użytkownicy w dowolnej dzierżawy usługi Azure AD będą mogli logować się do aplikacji po zgodę swojego konta za pomocą aplikacji. 

Jeśli masz istniejącej aplikacji, która ma swój własny system konta lub inne rodzaje logowania z innych dostawców chmury obsługuje dodawanie usługi Azure AD logowania z dowolnej dzierżawy jest proste. Po prostu rejestrowanie aplikacji, Dodaj kod logowania za pomocą protokołu OAuth2, OpenID Connect lub SAML i umieść [przycisk "Logowania w with Microsoft"] [ AAD-App-Branding] w aplikacji.

> [!NOTE] 
> W tym artykule przyjęto założenie, że znasz już tworzenia aplikacji pojedynczej dzierżawy dla usługi Azure AD. Jeśli nie masz, należy rozpocząć od jednego z poradniki Szybki Start na [strony głównej przewodnik dewelopera][AAD-Dev-Guide].

Istnieją cztery prostych czynności w celu konwertowania aplikacji na aplikację usługi Azure AD wielodostępne:

1. [Zaktualizuj rejestrację aplikacji się wieloma dzierżawcami](#update-registration-to-be-multi-tenant)
2. [Zaktualizuj kod do wysyłania żądań do / Common punktu końcowego](#update-your-code-to-send-requests-to-common)
3. [Zaktualizuj swój kod obsługi wielu wartości wystawcy](#update-your-code-to-handle-multiple-issuer-values)
4. [Zrozumienie zgody użytkownika i administratora i zmienić odpowiedni kod](#understanding-user-and-admin-consent)

Przyjrzyjmy się każdego kroku szczegółowo. Można także przejść bezpośrednio do [tej listy próbek wielodostępne][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Zaktualizuj rejestrację się wieloma dzierżawcami
Domyślnie rejestracji aplikacji/interfejsu API sieci web w usłudze Azure AD są pojedynczej dzierżawy. Umożliwia rejestrację wielodostępne znajdując **wielu dzierżawcza** Włącz **właściwości** okienko Twojej rejestracji aplikacji w [portalu Azure] [ AZURE-portal] i ustawieniem dla niego **tak**.

Przed aplikacji wielodostępnych, usługa Azure AD wymaga aplikacji mogą być globalnie unikatowy identyfikator URI aplikacji. Identyfikator URI aplikacji jest jednym ze sposobów, który aplikacja zostanie zidentyfikowana w wiadomości protokołu. Dla aplikacji pojedynczej dzierżawy jest wystarczająca dla identyfikator URI aplikacji być unikatowe w obrębie tej dzierżawy. Aplikacji wielodostępnych musi być globalnie unikatowe dzięki usłudze Azure AD można znaleźć aplikacji we wszystkich dzierżawców. Globalne unikatowości jest wymuszana przez wymaganie identyfikator URI aplikacji ma nazwę hosta pasującą zweryfikowanej domeny dzierżawy usługi Azure AD. Domyślnie aplikacje utworzone za pośrednictwem portalu Azure mają globalnie unikatowy identyfikator URI aplikacji ustawiona przy tworzeniu aplikacji, ale można zmienić tę wartość.

Na przykład, jeśli nazwę dzierżawy został contoso.onmicrosoft.com, a następnie prawidłowy identyfikator URI aplikacji będzie `https://contoso.onmicrosoft.com/myapp`. Gdyby dzierżawy zweryfikowanej domeny `contoso.com`, również będą prawidłowy identyfikator URI aplikacji, a następnie `https://contoso.com/myapp`. Jeśli identyfikator URI aplikacji nie będzie zgodna z tego wzorca ustawienia aplikacji, ponieważ wielodostępne zakończy się niepowodzeniem.

> [!NOTE] 
> Rejestracje klienta natywnego oraz [aplikacji v2](./active-directory-appmodel-v2-overview.md) są wielodostępne domyślnie. Nie musisz podejmować żadnych działań do tych aplikacji rejestracji wielu dzierżawców.

## <a name="update-your-code-to-send-requests-to-common"></a>Zaktualizuj kod do wysyłania żądań do/Common
W aplikacji pojedynczej dzierżawy żądań logowania są wysyłane do dzierżawcy logowania punktu końcowego. Na przykład dla contoso.onmicrosoft.com będzie punktu końcowego: `https://login.microsoftonline.com/contoso.onmicrosoft.com`

Żądania wysyłane do punktu końcowego dzierżawcy zalogować się użytkownicy (lub gości) w tej dzierżawie do aplikacji w tej dzierżawie. Z aplikacją wielodostępnych aplikacji nie może ustalić góry dzierżawy, jakie użytkownik ma, więc nie można wysłać żądania do punktu końcowego dzierżawcy. Zamiast tego żądania są wysyłane do punktu końcowego, który multiplexes między dzierżaw wszystkie usługi Azure AD: `https://login.microsoftonline.com/common`

Jeśli usługi Azure AD odbiera żądanie na / Common punktu końcowego, jego loguje się użytkownik i, w rezultacie, odnajduje dzierżawy, którym użytkownik jest z. / Wspólnego punktu końcowego współpracuje z wszystkie protokoły obsługiwane przez usługę Azure AD: OpenID Connect, OAuth 2.0 SAML 2.0 i WS-Federation.

Odpowiedź logowania do aplikacji, następnie zawiera token reprezentujący użytkownika. Wartości wystawcy tokenu informuje aplikacji dzierżawy, jakie użytkownik jest z. Gdy zwraca odpowiedź znajdujący punktu końcowego, odpowiada wartości wystawcy tokenu użytkownika dzierżawcy. 

> [!IMPORTANT]
> / Wspólnego punktu końcowego nie jest dzierżawcy i nie jest wystawcę, jest tylko multiplekser. Używając/Common logikę w aplikacji do sprawdzania poprawności tokenów musi zostać zaktualizowany do to uwzględniać. 

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Zaktualizuj swój kod obsługi wielu wartości wystawcy
Aplikacje sieci Web i interfejsów API sieci web odbierają i sprawdzania poprawności tokenów z usługi Azure AD. 

> [!NOTE]
> Aplikacje klienckie natywnego żądania i odbierać tokeny od usługi Azure AD, w tym celu ich wysłania do interfejsów API, w którym są weryfikowane. Natywnych aplikacji nie sprawdzania poprawności tokenów i należy je traktować jako przezroczystości.

Zobaczmy, w jaki sposób aplikacja weryfikuje tokeny odbiera z usługi Azure AD. Stosowanie pojedynczej dzierżawy zwykle przyjmuje wartość punktu końcowego, takie jak:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

i używa go do konstruowania adresu URL metadanych (w tym przypadku OpenID Connect), takich jak:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Aby pobrać dwóch najistotniejsze informacje służące do sprawdzania poprawności tokenów: dzierżawcy podpisywania kluczy i wartości wystawcy. Każda dzierżawa usługi Azure AD ma wartość unikatowy wystawcy formularza:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

wartości identyfikatora GUID w przypadku zmiany nazwy bezpieczna wątkowo wersja identyfikatora dzierżawy dzierżawcy. W przypadku wybrania poprzedni element metadanych dla `contoso.onmicrosoft.com`, ta wartość wystawcy dokumentu.

Stosowanie pojedynczej dzierżawy weryfikuje token, sprawdza zgodność podpisu aplikacji tokenu przed klucze podpisywania dokumentu metadanych. Ten test pozwala na upewnij się, że wartości wystawcy tokenu jest zgodna ze strukturą, które odnaleziono w dokumencie metadanych.

Ponieważ / wspólnego punktu końcowego dzierżawcy nie odpowiada i nie jest wystawcy należy zbadać wartości wystawcy metadanych dla / wspólnej składa się z szablonem adresu URL zamiast rzeczywista wartość:

    https://sts.windows.net/{tenantid}/

W związku z tym aplikacji wielodostępnych nie można sprawdzić poprawności tokenów porównując tylko wartości wystawcy w metadanych z `issuer` wartość w tokenie. Aplikacja wielodostępne musi logiki podjęcie decyzji, które wartości wystawcy, są prawidłowe i nie są oparte na część Identyfikatora dzierżawy wartości wystawcy. 

Na przykład jeśli aplikacja wielodostępne zezwala tylko logowania z określonym dzierżawców, którzy utworzyli konto usługi, następnie powinien sprawdzić wartości wystawcy lub `tid` wartości w tokenie, aby się upewnić, że tej dzierżawy jest na liście abonentów oświadczenia. Aplikacji wielodostępnych tylko dotyczy osób, nie decyzje żadnych dostępu oparte na dzierżaw następnie zignorowanie wartości wystawcy całkowicie.

W [przykłady wielodostępne][AAD-Samples-MT], sprawdzania poprawności wystawcy jest wyłączone, aby włączyć wszystkie dzierżawy usługi Azure AD do logowania.

## <a name="understanding-user-and-admin-consent"></a>Opis użytkowników i zgody administratora
Aby użytkownik mógł zalogować się do aplikacji w usłudze Azure AD aplikacja musi być reprezentowana w dzierżawie użytkownika. Dzięki temu wykonywanie czynności, takich jak zastosować unikatowych zasad podczas ich dzierżawy logowania do aplikacji w organizacji. W przypadku aplikacji pojedynczej dzierżawy tej rejestracji jest proste; jest to jeden, która jest wywoływana podczas rejestrowania aplikacji w [portalu Azure][AZURE-portal].

Dla wielodostępnych aplikacji początkowej rejestracji aplikacji znajduje się w dzierżawie usługi Azure AD używane przez dewelopera. Po zalogowaniu użytkownika z innej dzierżawy do aplikacji po raz pierwszy, usługi Azure AD monituje użytkownika o zgodę uprawnień wymaganych przez aplikację. Jeśli ich zgody, a następnie wywołuje reprezentację aplikacji *nazwy głównej usługi* jest tworzony w dzierżawie użytkownika i można kontynuować logowania. Delegowanie również jest tworzony w katalogu, który rejestruje zgody użytkownika do aplikacji. Szczegółowe informacje o aplikacji obiektów aplikacji i ServicePrincipal, i jak są one powiązane ze sobą, zobacz [obiekty aplikacji i nazwy głównej usługi][AAD-App-SP-Objects].

![Wyrażenia zgody na jednowarstwową aplikacji][Consent-Single-Tier] 

To środowisko zgody zależy od uprawnień wymaganych przez aplikację. Usługi Azure AD obsługuje dwa rodzaje uprawnienia tylko do aplikacji i delegowanego.

* Delegowane uprawnienia przyznaje aplikacji, które może wykonywać możliwość działania jako zalogowanego użytkownika dla podzbioru czynności użytkownika. Na przykład można udzielić aplikacji delegowane uprawnienia do odczytu kalendarza zalogowanego użytkownika.
* Uprawnienia tylko do aplikacji otrzymuje bezpośrednio do odpowiedniej tożsamości aplikacji. Na przykład można udzielić aplikacji uprawnienia tylko do aplikacji, można odczytać listy użytkowników w dzierżawie, niezależnie od tego, który jest zalogowany do aplikacji.

Niektóre uprawnienia można zgodę na przez zwykłego użytkownika, a inne wymagają zgody administratora dzierżawy. 

### <a name="admin-consent"></a>Zgoda administratora
Uprawnienia tylko do aplikacji zawsze Wymagaj zgody administratora dzierżawy. Jeśli aplikacja żąda uprawnienia tylko do aplikacji, a użytkownik próbuje zalogować się do aplikacji, jest wyświetlany komunikat o błędzie informujący, że użytkownik nie jest w stanie zgody.

Niektóre delegowane uprawnienia również wymagać zgody administratora dzierżawy. Na przykład możliwość zapisywania zwrotnego z usługą Azure AD jako zalogowany użytkownik wymaga zgody administratora dzierżawy. W przypadku zwykłego użytkownika próbuje zalogować się do aplikacji, która żąda uprawnień delegowanych, które wymaga zgody administratora, takich jak uprawnienia tylko do aplikacji, aplikacji odbierze wystąpił błąd. Określa, czy uprawnienia wymaga zgody administratora jest określane przez dewelopera opublikowane zasobu, którą można znaleźć w dokumentacji dla zasobu. Dokumentację uprawnienia [interfejsu API usługi Azure AD Graph] [ AAD-Graph-Perm-Scopes] i [interfejsu API programu Microsoft Graph] [ MSFT-Graph-permision-scopes] wskazać uprawnienia, które wymagają administratora oznacza zgodę.

Jeśli aplikacja używa uprawnienia, które wymagają zgody administratora, należy mieć gestu, takich jak przycisk lub łącze którym administrator może zainicjować akcji. Żądanie aplikacji wysyła dla tej akcji jest zwykle OAuth2/OpenID Connect autoryzacji żądania, które obejmuje również `prompt=admin_consent` parametr ciągu zapytania. Po zgodził administratora i nazwy głównej usługi jest tworzony w dzierżawie klienta, nie ma potrzeby kolejnych żądań logowania `prompt=admin_consent` parametru. Ponieważ administrator zdecydował się, że żądane uprawnienia są dopuszczalne, nie innych użytkowników w dzierżawie monit o zgodę od tej pory.

Administrator dzierżawy może wyłączyć przez regularne użytkownikom wyrazić zgodę na aplikacje. Jeśli ta funkcja jest wyłączona, zgody administratora jest zawsze wymagane dla aplikacji do użycia w dzierżawie. Jeśli chcesz przetestować aplikację za zgodą użytkownika końcowego wyłączone, możesz znaleźć przełącznik konfiguracji w [portalu Azure] [ AZURE-portal] w **[ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** w obszarze **aplikacje dla przedsiębiorstw**.

`prompt=admin_consent` Parametru można również przez aplikacje, które zażądać uprawnień, które nie wymagają zgody administratora. Przykładem po tym będzie służyć jest, jeśli aplikacja wymaga środowisko, w którym administrator dzierżawy "zarejestrowaniu" jeden czasie, a nie inne użytkownicy otrzymają monit o zgodę od tego momentu.

Jeśli aplikacja wymaga zgody administratora, a administrator loguje się bez `prompt=admin_consent` parametru wysyłane, gdy administrator pomyślnie zgadza się na aplikacji będą miały zastosowania **tylko dla swojego konta użytkownika**. Regularne użytkownicy nadal nie będą mogli zalogować się lub wyrażenia zgody na aplikację. Ta funkcja jest przydatna, jeśli chcesz nadać możliwość Eksploruj aplikacji przed zezwoleniem na innym użytkownikom dostęp administratora dzierżawy.

> [!NOTE]
> Niektóre aplikacje mają środowisko, w którym regularne użytkownicy będą mogli początkowo zgody, a później aplikacja może obejmować uprawnienia administratora i żądania, które wymagają zgody administratora. Nie istnieje sposób w tym celu z v1 rejestracji aplikacji w usłudze Azure AD dzisiaj; Jednak przy użyciu punktu końcowego v2 umożliwia aplikacji, aby zażądać uprawnień w czasie wykonywania, a nie w czasie rejestracji, który umożliwia ten scenariusz. Aby uzyskać więcej informacji, zobacz [punktu końcowego v2][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Aplikacje wielowarstwowe i zgody
Aplikacja może mieć wielu warstw, każdy reprezentowany przez jego własnej rejestracji w usłudze Azure AD. Na przykład natywnych aplikacji, która wywołuje interfejs API sieci web lub aplikacji sieci web która wywołuje interfejs API sieci web. W obu przypadkach klient (aplikacji sieci web lub aplikacji natywnej) żąda uprawnień do wywoływania zasobów (interfejs API sieci web). Klient pomyślnie można zgodę na klienta dzierżawy wszystkie zasoby, do których żąda uprawnień musi już istnieć w dzierżawy przez klienta. Jeśli ten warunek nie jest spełniony, usługi Azure AD zwraca błąd czy zasobu należy najpierw dodać.

**Konfiguracja wielu warstw w pojedynczej dzierżawy**

Może to być problem, jeśli aplikacja logicznej składa się z dwóch lub więcej rejestracji aplikacji, na przykład oddzielnych klienta i zasobów. Jak można uzyskać zasobu do dzierżawy klienta pierwszy? Usługi Azure AD obejmuje przypadek przez włączenie klienta i zasobów być zgodę w jednym kroku. Użytkownik widzi łączną sumę uprawnień żądany przez klienta i zasobów na stronie zgody. Aby włączyć to zachowanie, Rejestracja aplikacji zasobu musi zawierać identyfikator aplikacji klienta jako `knownClientApplications` w jego [manifest aplikacji][AAD-App-Manifest]. Na przykład:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

To jest przedstawiona w klientami wielowarstwowych wywoływanie przykładowy interfejs API sieci web w [związane z zawartością](#related-content) sekcji na końcu tego artykułu. Następujący diagram zawiera omówienie zgody dla aplikacji wielowarstwowych zarejestrowane w pojedynczej dzierżawy.

![Wyrażenia zgody na aplikację wielowarstwową znane klienta][Consent-Multi-Tier-Known-Client] 

**Konfiguracja wielu warstw w wielu dzierżawców**

Podobne przypadku się stanie w przypadku różnych warstw aplikacji są rejestrowane w różnym dzierżawcom. Rozważmy na przykład w przypadku tworzenia aplikacji klientami, która wywołuje interfejs API z Online Exchange Office 365. Aby opracować natywnego aplikacji, a później do natywnej aplikacji do uruchamiania w dzierżawie klienta, główną usługi Exchange Online musi być obecny. W takim przypadku deweloperów i klient musi zakupić usługi Exchange Online dla podmiotu zabezpieczeń mogą być tworzone w swoich dzierżaw usługi. 

W przypadku interfejsu API utworzony przez organizację innych niż Microsoft developer interfejsu API musi umożliwiają klientom zgody aplikacji do ich klientom dzierżaw. Jest zalecana dla deweloperów innych firm do tworzenia interfejsu API w taki sposób, aby również może działać jako klient sieci web do implementowania rejestracji. W tym celu:

1. Postępuj zgodnie z wcześniejszych sekcjach, aby upewnić się, że interfejs API zaimplementowano wymagania rejestracji/kod wielodostępnych aplikacji.
2. Oprócz udostępnianie role/zakresy interfejsie API, upewnij się, obejmuje rejestracji "Zaloguj się i odczytuj profil użytkownika" uprawnienia usługi Azure AD (domyślnie dostępne).
3. Implementuje stronę logowania — w/tworzenia konta w kliencie sieci web po [zgody administratora](#admin-consent) wskazówki wcześniejszym opisem.
4. Po wyrażeniu zgody przez użytkownika do aplikacji, usługi łączy delegowania podmiot zabezpieczeń i zgody są tworzone w swojej dzierżawy, a aplikacji natywnej mogą uzyskiwać tokeny dla interfejsu API.

Następujący diagram zawiera omówienie zgody dla aplikacji wielowarstwowych zarejestrowane w różnym dzierżawcom.

![Wyrażenia zgody na wieloosobowa aplikację wielowarstwową][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Cofnięcie zgody
Użytkownicy i Administratorzy mogą wycofać zgodę do aplikacji w dowolnym momencie:

* Użytkownicy odwołać dostęp do poszczególnych aplikacji, usuwając je z ich [aplikacji panelu dostępu] [ AAD-Access-Panel] listy.
* Administratorzy odwołać dostęp do aplikacji, usuwając je z usługą Azure AD przy użyciu [aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) sekcji [portalu Azure][AZURE-portal].

Jeśli administrator zgadza się na aplikacji dla wszystkich użytkowników w dzierżawie, użytkownicy nie mogą indywidualnie odwołać dostęp. Tylko administrator może odwołać dostęp i tylko dla całej aplikacji.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplikacje wielodostępne i buforowanie tokeny dostępu
Aplikacje wielodostępne można również uzyskać tokenów dostępu do wywoływania interfejsów API, które są chronione przez usługę Azure AD. Typowym błędem podczas przy użyciu biblioteki uwierzytelniania usługi Active Directory (ADAL) z aplikacją wielodostępne jest początkowo żądania token dla użytkownika za pomocą/Common, otrzymują odpowiedź, a następnie żądania tokenu kolejnych dla tego samego użytkownika również przy użyciu/Common. Ponieważ odpowiedzi z usługi Azure AD nie pochodzi od dzierżawcy, / wspólne, ADAL buforuje token jako od dzierżawcy. Wpis pamięci podręcznej chybień kolejne wywołanie/Common do uzyskania tokenu dostępu dla użytkownika, a użytkownik jest monitowany o Zaloguj się ponownie. Aby uniknąć, Brak pamięci podręcznej, upewnij się, że wezwań do już zalogowanego użytkownika zostały wprowadzone do punktu końcowego dzierżawcy.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób tworzenia aplikacji, która może zalogować użytkownika z dowolnej dzierżawy usługi Azure AD. Po włączeniu pojedynczego logowania jednokrotnego (SSO) między aplikacji i usługi Azure AD, należy zaktualizować aplikacji dostępu do interfejsów API udostępniane przez Microsoft zasobów, takich jak usługi Office 365. Dzięki temu można oferować spersonalizowane środowisko w aplikacji, takich jak wyświetlanie informacje kontekstowe użytkownikom, jak ich obraz profilu lub ich dalej terminu kalendarza. Aby dowiedzieć się więcej o tworzeniu interfejsu API wywołuje do usługi Azure AD i Office 365 z usług takich jak Exchange, SharePoint, OneDrive, OneNote, planowania, Excel i więcej, odwiedź stronę [interfejsu API programu Microsoft Graph][MSFT-Graph-overview].

## <a name="related-content"></a>Zawartość pokrewna
* [Przykłady aplikacji z wieloma dzierżawcami][AAD-Samples-MT]
* [Znakowanie wytyczne dotyczące aplikacji][AAD-App-Branding]
* [Obiekty aplikacji i głównej usługi][AAD-App-SP-Objects]
* [Integrowanie aplikacji z usługą Azure Active Directory][AAD-Integrating-Apps]
* [Omówienie struktury zgody][AAD-Consent-Overview]
* [Zakresy uprawnień interfejsu API programu Microsoft Graph][MSFT-Graph-permision-scopes]
* [Zakresy uprawnień w usłudze Azure AD Graph API][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














