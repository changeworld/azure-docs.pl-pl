---
title: Azure uwierzytelniania usługi Active Directory i usługi Resource Manager | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera do uwierzytelniania przy użyciu interfejsu API usługi Azure Resource Manager i usługi Azure Active Directory dla integracji aplikacji z innych subskrypcji platformy Azure.
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2017
ms.author: dugill
ms.openlocfilehash: ba2466f58b3af0ef208474adb3e4c7ff184ceccc
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018650"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Użyj Menedżera zasobów uwierzytelniania interfejsu API do dostępu do subskrypcji
## <a name="introduction"></a>Wprowadzenie
Jeśli jesteś deweloper oprogramowania, który musi utworzyć aplikację, która zarządza zasobami Azure klienta, w tym artykule przedstawiono sposób uwierzytelniania za pomocą interfejsów API usługi Azure Resource Manager i uzyskanie dostępu do zasobów w innych subskrypcji.

Aplikację można uzyskać dostęp do interfejsów API Menedżera zasobów kilka sposobów:

1. **Użytkownik i uzyskania dostępu do aplikacji**: dla aplikacji, które uzyskują dostęp do zasobów w imieniu zalogowanego użytkownika. Ta metoda działa w przypadku aplikacji, takich jak aplikacje sieci web i narzędzia wiersza polecenia, które zajmują się tylko "interakcyjne Zarządzanie" zasobów platformy Azure.
2. **Dostęp tylko do aplikacji**: dla aplikacji uruchamianych demon usługi i zaplanowanych zadań. Tożsamości aplikacji otrzymuje bezpośredni dostęp do zasobów. Ta metoda działa w przypadku aplikacji wymagających długoterminowej bezobsługowe dostępu (Instalacja nienadzorowana) na platformie Azure.

Ten artykuł zawiera instrukcje krok po kroku, aby utworzyć aplikację, która jest stosowana w obu tych metod autoryzacji. Widoczny jest sposób wykonania poszczególnych kroków z interfejsu API REST lub C#. Kompletna aplikacja platformy ASP.NET MVC jest dostępna na [ https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense ](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Co to jest aplikacja sieci web
Aplikacja sieci web:

1. Logowania użytkownika w usłudze Azure.
2. Pyta użytkownika w celu przyznania dostępu do aplikacji sieci web do Menedżera zasobów.
3. Pobiera użytkownika + tokenu dostępu aplikacji do uzyskiwania dostępu do Menedżera zasobów.
4. Używa tokenu (z kroku 3) można przypisać nazwy głównej usługi aplikacji do roli w ramach subskrypcji. Ten krok umożliwia długoterminowe dostępu do aplikacji do subskrypcji.
5. Pobiera token dostępu tylko do aplikacji.
6. Używa tokenu (z kroku 5) do zarządzania zasobami w subskrypcji za pomocą Menedżera zasobów.

Oto przepływu aplikacji sieci web.

![Przepływ uwierzytelniania usługi Resource Manager](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Jako użytkownik identyfikator subskrypcji podanie subskrypcję, której chcesz użyć:

![Podaj identyfikator subskrypcji](./media/resource-manager-api-authentication/sample-ux-1.png)

Wybierz konto, które ma być używane do logowania.

![Wybierz konto](./media/resource-manager-api-authentication/sample-ux-2.png)

Wprowadź swoje poświadczenia.

![Podaj poświadczenia](./media/resource-manager-api-authentication/sample-ux-3.png)

Udziel aplikacji uprawnień dostępu do Twojej subskrypcji platformy Azure:

![Udzielanie dostępu](./media/resource-manager-api-authentication/sample-ux-4.png)

Zarządzanie subskrypcjami połączone:

![Łączenie subskrypcji](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Zarejestruj aplikację
Przed rozpoczęciem kodowania, należy zarejestrować aplikacji sieci web z usługi Azure Active Directory (AD). Rejestracja aplikacji tworzy centralnej tożsamości aplikacji w usłudze Azure AD. Przechowuje podstawowe informacje o aplikacji, takich jak identyfikator klienta OAuth, adresy URL odpowiedzi i poświadczenia, których aplikacja korzysta z uwierzytelniania i dostępu do interfejsów API usługi Azure Resource Manager. Rejestracja aplikacji rejestruje także różne delegowane uprawnienia, których aplikacja wymaga podczas uzyskiwania dostępu do APIs firmy Microsoft w imieniu użytkownika.

Ponieważ aplikacja uzyskuje dostęp do innych subskrypcji, należy go skonfigurować jako aplikacji wielodostępnej. Aby przekazać sprawdzania poprawności, podaj domeny skojarzonych z usługą Azure Active Directory. Aby wyświetlić domeny skojarzone z usługą Azure Active Directory, zaloguj się do portalu.

Poniższy przykład pokazuje, jak zarejestrować aplikacji przy użyciu programu Azure PowerShell. Musi mieć najnowszą wersję programu Azure PowerShell, aby to polecenie mogło działać (sierpnia 2016).

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true

Aby zalogować się jako aplikacji usługi AD, należy identyfikator aplikacji i hasło. Aby wyświetlić identyfikator aplikacji jest zwracana z poprzedniego polecenia, należy użyć:

    $app.ApplicationId

Poniższy przykład pokazuje, jak zarejestrować aplikacji przy użyciu wiersza polecenia platformy Azure.

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Wyniki zawierają identyfikator aplikacji, należy podczas uwierzytelniania, co aplikacja.

### <a name="optional-configuration---certificate-credential"></a>Konfiguracja opcjonalna — certyfikat poświadczeń
Usługi Azure AD obsługuje również certyfikat poświadczeń dla aplikacji: utwórz samopodpisany certyfikat, przechowywać klucz prywatny i Dodaj klucz publiczny do rejestracji aplikacji usługi Azure AD. W przypadku uwierzytelniania aplikacji wysyła małych ładunku do usługi Azure AD podpisany przy użyciu klucza prywatnego, i usługi Azure AD sprawdza podpis przy użyciu klucza publicznego, który został zarejestrowany.

Dla informacji o tworzeniu aplikacji AD przy użyciu certyfikatu znajduje się w temacie [użycia programu Azure PowerShell do tworzenia nazwy głównej usługi dostępu do zasobów](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) lub [Użyj wiersza polecenia platformy Azure można utworzyć nazwy głównej usługi dostępu do zasobów](resource-group-authenticate-service-principal-cli.md) .

## <a name="get-tenant-id-from-subscription-id"></a>Uzyskanie Identyfikatora dzierżawy z Identyfikatorem subskrypcji
Do żądania tokenu, który może służyć do wywołania usługi Resource Manager, aplikacja musi znać identyfikator dzierżawy dzierżawy usługi Azure AD, obsługującym subskrypcji platformy Azure. Prawdopodobnie użytkownicy wiedzą, ich identyfikatorów subskrypcji, ale nie będzie wiadomo, ich dzierżawy identyfikatorów dla usługi Azure Active Directory. Uzyskanie Identyfikatora dzierżawy przez użytkownika, należy poprosić użytkownika dla identyfikatora subskrypcji. Podaj subskrypcji IDENTYFIKATORA podczas wysyłania żądania dotyczące subskrypcji:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Żądanie kończy się niepowodzeniem, ponieważ użytkownik nie zarejestrował jeszcze, ale można pobrać Identyfikatora dzierżawy z odpowiedzi. W tym wyjątku pobrać identyfikator dzierżawcy z wartości nagłówka odpowiedzi dla **WWW-Authenticate**. Zobacz to implementacja [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) metody.

## <a name="get-user--app-access-token"></a>Pobierz użytkownika + tokenu dostępu do aplikacji
Aplikacja przekierowuje użytkownika do usługi Azure AD z OAuth 2.0 autoryzować żądania — uwierzytelnianie poświadczeń użytkownika i wrócić kod autoryzacji. Aplikacja korzysta z kodu autoryzacji, Uzyskaj token dostępu dla Menedżera zasobów. [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) metoda tworzy żądania autoryzacji.

W tym artykule przedstawiono żądania interfejsu API REST do uwierzytelnienia użytkownika. Umożliwia także biblioteki pomocnika do uwierzytelniania w kodzie. Aby uzyskać więcej informacji o tych bibliotek, zobacz [bibliotek uwierzytelniania usługi Azure Active Directory](../active-directory/active-directory-authentication-libraries.md). Aby uzyskać instrukcje dotyczące integrowania zarządzania tożsamością w aplikacji, zobacz [przewodnik dewelopera usługi Azure Active Directory](../active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Żądanie uwierzytelniania (OAuth 2.0)
Wystawiać Open ID Connect/OAuth2.0 autoryzacji żądania do punktu końcowego autoryzacji usługi Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Parametry ciągu zapytania, które są dostępne dla tego żądania są opisane w [kod autoryzacji żądania](../active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) artykułu.

Poniższy przykład przedstawia sposób żądania autoryzacji OAuth2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Usługi Azure AD uwierzytelnia użytkownika, a w razie potrzeby pyta użytkownika można udzielić uprawnienia do aplikacji. Zwraca kod autoryzacji do adresu URL odpowiedzi aplikacji. W zależności od żądanego response_mode, usługi Azure AD albo odsyła dane w ciągu zapytania lub jako dane post.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Żądanie uwierzytelniania (Open ID Connect)
Jeśli chcesz nie tylko dostęp do usługi Azure Resource Manager w imieniu użytkownika, ale również umożliwiać użytkownikowi zalogować się do aplikacji przy użyciu konta usługi Azure AD, należy wydać Open ID autoryzować żądania połączenia. Z Open ID Connect aplikacja odbiera żądaniu z usługi Azure AD, która aplikacji można używać do logowania użytkownika.

Parametry ciągu zapytania, które są dostępne dla tego żądania są opisane w [wysłanie żądania rejestrowania](../active-directory/develop/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) artykułu.

Oto przykład Open ID Connect żądania jest:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Usługi Azure AD uwierzytelnia użytkownika, a w razie potrzeby pyta użytkownika można udzielić uprawnienia do aplikacji. Zwraca kod autoryzacji do adresu URL odpowiedzi aplikacji. W zależności od żądanego response_mode, usługi Azure AD albo odsyła dane w ciągu zapytania lub jako dane post.

Przykład Open ID Connect odpowiedzi to:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Żądania tokenu (OAuth2.0 Code Grant Flow)
Teraz, gdy aplikacja otrzymał kod autoryzacji z usługi Azure AD, nadszedł czas na Uzyskaj token dostępu usługi Azure Resource Manager.  Opublikuj OAuth2.0 Code Grant Token żądania do punktu końcowego usługi Azure AD tokenu:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry ciągu zapytania, które są dostępne dla tego żądania są opisane w [kodu autoryzacji](../active-directory/develop/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) artykułu.

W poniższym przykładzie przedstawiono żądania tokenu grant kodu z poświadczeniami hasło:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Podczas pracy z poświadczeń certyfikatu, Utwórz tokenu Web JSON (JWT) i znak (RSA SHA256) przy użyciu klucza prywatnego poświadczeń certyfikatu aplikacji. Typy oświadczeń dla tokenu są wyświetlane w [oświadczeń tokenu JWT](../active-directory/develop/active-directory-protocols-oauth-code.md#jwt-token-claims). Aby informacje, zobacz [kod biblioteki uwierzytelniania Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) do podpisywania tokenów JWT potwierdzenia klienta.

Zobacz [specyfikację Open ID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) szczegółowe informacje dotyczące uwierzytelniania klientów.

W poniższym przykładzie przedstawiono żądania tokenu grant kodu z poświadczeniami certyfikatu:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Oto przykład odpowiedzi dla kodu umożliwić token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Obsługa odpowiedzi tokenu grant kodu
Odpowiedź oznaczająca Powodzenie tokenu zawiera (użytkownik + aplikacji) tokenu dostępu dla usługi Azure Resource Manager. Aplikacja używa ten token dostępu, aby otworzyć Menedżera zasobów w imieniu użytkownika. Okres istnienia tokenów dostępu wystawiony przez usługę Azure AD to jedna godzina. Jest mało prawdopodobne, że aplikację sieci web musi odnawiać (użytkownik + aplikacji) tokenu dostępu. Jeśli konieczne do odnawiania tokenu dostępu, należy użyć token odświeżania, która aplikacja otrzymuje w odpowiedzi tokenu. Opublikuj OAuth2.0 Token żądania do punktu końcowego usługi Azure AD tokenu:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry używane z żądaniem odświeżania są opisane w [odświeżanie tokenu dostępu](../active-directory/develop/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

Poniższy przykład przedstawia użycie odświeżanie tokenu:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Tokeny odświeżania może służyć do uzyskania nowego tokenów dostępu dla usługi Azure Resource Manager, nie są one odpowiednie dla dostęp w trybie offline przez aplikację. Okres istnienia tokenów odświeżania jest ograniczona, a tokeny odświeżania są powiązane z użytkownikiem. Jeśli użytkownik opuszcza organizację, aplikacji, używając tokenu odświeżania utracenia dostępu. Ta metoda nie jest odpowiedni dla aplikacji, które są używane przez zespoły do zarządzania zasobami platformy Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Sprawdź, czy użytkownik może przypisać dostęp do subskrypcji
Teraz aplikacja ma token, aby uzyskać dostępu do usługi Azure Resource Manager w imieniu użytkownika. Następnym krokiem jest także łączenie aplikacji do subskrypcji. Po nawiązaniu połączenia aplikacji można zarządzać te subskrypcje, nawet wtedy, gdy użytkownik nie jest obecny (długoterminowej dostęp w trybie offline).

Dla każdej subskrypcji nawiązać wywołać [uprawnienia listy Resource Manager](https://docs.microsoft.com/rest/api/authorization/permissions) interfejsu API w celu określenia, czy użytkownik ma uprawnienia zarządzania dla subskrypcji.

[UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) metoda przykładowej aplikacji ASP.NET MVC implementuje tego wywołania.

Poniższy przykład pokazuje, jak zażądać uprawnień użytkownika w subskrypcji. 83cfe939-2402-4581-b761-4f59b0a041e4 jest identyfikator subskrypcji.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Przykładem odpowiedzi, aby uzyskać uprawnienia użytkownika w subskrypcji jest:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Uprawnienia interfejsu API zwraca wiele uprawnień. Wszystkie uprawnienia składają się z dozwolonych akcji (**akcje**) i niedopuszczalne akcje (**notactions**). Jeśli akcja jest obecna w dozwolonych akcji żadnych uprawnień i nie znajduje się w niezatwierdzonych akcje te uprawnienia, użytkownik może wykonać tej operacji. **Microsoft.Authorization/RoleAssignments/Write** jest akcja udziela praw zarządzania dostępu. Aplikację należy przeanalizować wyniku uprawnienia do wyszukania dopasowanie wyrażenia regularnego w ciągu tej akcji w **akcje** i **notactions** każdego uprawnień.

## <a name="get-app-only-access-token"></a>Uzyskaj token dostępu tylko do aplikacji
Teraz wiesz, czy użytkownik może przypisać dostęp do subskrypcji platformy Azure. Następne kroki są:

1. Przypisz odpowiednie role RBAC do tożsamości aplikacji w ramach subskrypcji.
2. Sprawdzanie poprawności przypisania dostępu przez wykonanie zapytania dotyczącego aplikacji uprawnienia subskrypcji lub uzyskiwania dostępu do usługi Resource Manager przy użyciu tokenu tylko do aplikacji.
3. Zapisz połączenie w strukturze danych aplikacji "połączonych subskrypcji" - utrwalanie identyfikator subskrypcji.

Oto bliżej w pierwszym kroku. Aby przypisać odpowiednie role RBAC tożsamości aplikacji, należy określić:

* Identyfikator obiektu tożsamości aplikacji w usłudze Active Directory Azure użytkownika
* Identyfikator roli RBAC, wymaganych przez aplikację dla tej subskrypcji

Podczas uwierzytelniania użytkownika z usługi Azure AD aplikacja tworzy obiekt główną usługi dla aplikacji w tej usłudze Azure AD. Azure umożliwia role RBAC ma być przypisany do nazwy główne usług uzyskać bezpośredni dostęp do odpowiednie aplikacje na zasobów platformy Azure. Ta akcja jest dokładnie, co chcesz zrobić. Zapytania interfejsu API programu Graph usługi AD platformy Azure w celu ustalenia identyfikatora nazwy głównej usługi aplikacji w zalogowanego użytkownika przez usługi Azure AD.

Masz tylko token dostępu usługi Azure Resource Manager — należy uzyskać nowy token dostępu do wywołania interfejsu API programu Azure AD Graph. Każda aplikacja sieci Web w usłudze Azure AD ma uprawnienia do tworzenia zapytań własną obiekt główny usługi, więc token dostępu tylko do aplikacji jest wystarczająca.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Uzyskaj token dostępu tylko do aplikacji interfejsu API Azure AD Graph
Aby uwierzytelniać swoją aplikację i uzyskać token do interfejsu API Azure AD Graph, wysłać żądania tokenu przepływu OAuth2.0 przyznania poświadczeń klienta do punktu końcowego tokenu usługi Azure AD (**https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) metody przykładowej aplikacji ASP.net MVC pobiera dostęp tylko do aplikacji token dla interfejsu API programu Graph dla platformy .NET przy użyciu biblioteki uwierzytelniania usługi Active Directory.

Parametry ciągu zapytania, które są dostępne dla tego żądania są opisane w [żądania tokenu dostępu](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) artykułu.

Oto przykład żądania tokenu przyznania poświadczeń klienta:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Oto przykład odpowiedzi dla poświadczeń klienta umożliwić token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Pobrania ObjectId nazwy głównej usługi aplikacji w użytkownika usługi Azure AD
Teraz, użyj tokenu na dostęp tylko do aplikacji zapytania [podmiotów zabezpieczeń usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API w celu określenia Identyfikatora obiektu w katalogu, nazwy głównej usługi aplikacji.

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) metoda przykładowej aplikacji ASP.net MVC implementuje tego wywołania.

Poniższy przykład pokazuje, jak żądanie nazwy głównej usługi aplikacji. a0448380-c346-4f9f-b897-c18733de9394 jest identyfikator klienta aplikacji.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

W poniższym przykładzie przedstawiono odpowiedzi na żądanie usługi aplikacji głównej

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Pobierz identyfikator roli Azure RBAC
Aby przypisać odpowiednie role RBAC do nazwy głównej usługi, należy określić identyfikator roli Azure RBAC.

Odpowiednią rolę RBAC dla aplikacji:

* Jeśli aplikacja monitoruje tylko subskrypcja, bez wprowadzania żadnych zmian, wymaga tylko czytnika uprawnienia dla tej subskrypcji. Przypisz **czytnika** roli.
* Jeśli aplikacja Azure subskrypcji, Tworzenie/modyfikowanie/usuwanie jednostek wymaga jednego uprawnienia współautora.
  * Aby zarządzać określonego typu zasobu, Przypisz role współautora określonych zasobów (Współautor·maszyny·wirtualnej, współautora sieci wirtualnych, współautora konta magazynu itp.)
  * Aby zarządzać dowolnego typu zasobu, należy przypisać **współautora** roli.

Przypisania roli dla aplikacji są widoczne dla użytkowników, dlatego wybierz wymagane najmniejszych uprawnień.

Wywołanie [definicji roli Menedżera zasobów interfejsu API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) Aby wyświetlić listę wszystkich ról Azure RBAC i wyszukiwania, a następnie iteracja wyników można znaleźć definicji odpowiednią rolę według nazwy.

[GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) metoda przykładowej aplikacji ASP.net MVC implementuje tego wywołania.

W poniższym przykładzie żądania pokazuje, jak można pobrać identyfikatora roli Azure RBAC. 09cbd307-aa71-4aca-b346-5f253e6e3ebb jest identyfikator subskrypcji.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Odpowiedź znajduje się w następującym formacie:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Nie trzeba wywołać tego interfejsu API w sposób ciągły. Po określeniu dobrze znany identyfikator GUID definicji roli, można utworzyć identyfikator definicji roli jako:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Poniżej przedstawiono identyfikatory często używane wbudowane role:

| Rola | Identyfikator GUID |
| --- | --- |
| Czytelnik |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Współautor |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Współautor maszyny wirtualnej |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Współautor sieci wirtualnej |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Współautor konta magazynu |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Współautor witryny sieci Web |de139f84-1756-47ae-9be6-808fbbe84772 |
| Współautor planów sieci Web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Współautor serwera SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Współautor bazy danych SQL |9b7fa17d-e63e-47B0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Przypisz rolę RBAC do aplikacji
Masz wszystkie elementy potrzebne do przypisać odpowiednie role RBAC do Twojej nazwy głównej usługi za pomocą [Resource Manager utworzyć przypisanie roli](https://docs.microsoft.com/rest/api/authorization/roleassignments) interfejsu API.

[GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) metoda przykładowej aplikacji ASP.net MVC implementuje tego wywołania.

Oto przykład żądania do przypisania roli RBAC do aplikacji:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

W żądaniu używane są następujące wartości:

| Identyfikator GUID | Opis |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |Identyfikator subskrypcji |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |Identyfikator nazwy głównej usługi aplikacji |
| acdd72a7-3385-48ef-bd42-f606fba81ae7 |Identyfikator roli czytnika |
| 4f87261d-2816-465d-8311-70a27558df4c |Nowy identyfikator guid utworzone dla nowe przypisanie roli |

Odpowiedź znajduje się w następującym formacie:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Uzyskaj dostęp tylko do aplikacji token dla usługi Azure Resource Manager
Aby sprawdzić poprawność tej aplikacji ma żądany dostęp do subskrypcji, wykonaj zadania testowego dla subskrypcji przy użyciu tokenu tylko do aplikacji.

Aby uzyskać token dostępu tylko do aplikacji, wykonaj instrukcje z sekcji [Uzyskaj token dostępu tylko do aplikacji interfejsu API Azure AD Graph](#app-azure-ad-graph), podając inną wartość dla parametru zasobów:

    https://management.core.windows.net/

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metody przykładowej aplikacji ASP.NET MVC pobiera dostęp tylko do aplikacji token dla Menedżera zasobów Azure dla platformy .net przy użyciu biblioteki uwierzytelniania usługi Active Directory.

#### <a name="get-applications-permissions-on-subscription"></a>Uzyskać uprawnienia aplikacji w subskrypcji
Aby sprawdzić, czy aplikacja ma odpowiednie prawa dostępu na subskrypcji platformy Azure, może także wywołać [uprawnienia menedżera zasobów](https://docs.microsoft.com/rest/api/authorization/permissions) interfejsu API. Ta metoda jest podobna do sposób można stwierdzić, czy użytkownik ma uprawnienia dostępu do zarządzania dla subskrypcji. Teraz, wymaga jednak uprawnień interfejsu API przy użyciu tokenu dostępu tylko do aplikacji, odebrany w poprzednim kroku.

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metoda przykładowej aplikacji ASP.NET MVC implementuje tego wywołania.

## <a name="manage-connected-subscriptions"></a>Zarządzanie subskrypcjami połączonych
Gdy odpowiednie role RBAC jest przypisany do głównej na subskrypcję usługi aplikacji, aplikacja może zachować monitorowania/zarządzanie przy użyciu tokenów dostępu tylko do aplikacji dla usługi Azure Resource Manager.

Jeśli właściciel subskrypcji usuwa przypisanie roli aplikacji za pomocą portalu lub narzędzia wiersza polecenia, aplikacji nie będzie już mógł uzyskać dostępu do tej subskrypcji. W takim przypadku należy powiadomienia użytkownika Przerwano połączenie z subskrypcją z poza aplikacją i nadaj im opcję "Napraw" połączenie. "Napraw" czy ponownie utworzyć przypisania roli, który został usunięty w trybie offline.

Tak samo, jak włączono użytkownik mógł się połączyć subskrypcje aplikacji musi zezwolić użytkownikowi na odłączyć za subskrypcje. Z dostępu administracyjnego punktu widzenia Odłącz oznacza usunięcie przypisania roli mającą nazwę główną usługi aplikacji dla tej subskrypcji. Opcjonalnie każdy stan aplikacji dla subskrypcji mogą zostać usunięte za.
Tylko użytkownicy mający uprawnienie zarządzania dla tej subskrypcji są w stanie rozłączenia subskrypcji.

[Metody RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) platformy ASP.net MVC przykładowej aplikacji implementuje tego wywołania.

To wszystko — użytkownicy mogą teraz łatwo połączyć i zarządzać ich subskrypcjami platformy Azure z aplikacją.
