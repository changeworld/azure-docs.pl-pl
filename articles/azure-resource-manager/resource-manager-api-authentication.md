---
title: Azure Active Directory uwierzytelnianie i Menedżer zasobów | Microsoft Docs
description: Przewodnik dewelopera służący do uwierzytelniania za pomocą interfejsu API Azure Resource Manager i Azure Active Directory do integracji aplikacji z innymi subskrypcjami platformy Azure.
author: dushyantgill
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: dugill
ms.openlocfilehash: 033f3ca9ca79903f884c625dc694b06a3e4fd04c
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263009"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Korzystanie z interfejsu API uwierzytelniania Menedżer zasobów do uzyskiwania dostępu do subskrypcji

Jeśli jesteś deweloperem oprogramowania, który musi utworzyć aplikację, która zarządza zasobami platformy Azure klienta, w tym artykule przedstawiono sposób uwierzytelniania za pomocą interfejsów API Azure Resource Manager i uzyskiwania dostępu do zasobów w innych subskrypcjach.

Aplikacja może uzyskiwać dostęp do Menedżer zasobów interfejsów API na kilka sposobów:

1. **Dostęp użytkownika i aplikacji**: dla aplikacji, które uzyskują dostęp do zasobów dla zalogowanego użytkownika. Ta metoda działa w przypadku aplikacji, takich jak aplikacje sieci Web i narzędzia wiersza polecenia, które dotyczą tylko "interaktywnego zarządzania" zasobów platformy Azure.
2. **Dostęp tylko do aplikacji**: dla aplikacji, które uruchamiają usługi demonów i zaplanowane zadania. Tożsamość aplikacji uzyskuje bezpośredni dostęp do zasobów. Ta metoda działa w przypadku aplikacji wymagających długoterminowego bezobsługowego dostępu do platformy Azure.

Ten artykuł zawiera instrukcje krok po kroku dotyczące tworzenia aplikacji, która wykorzystuje obie te metody autoryzacji. Pokazano, jak wykonać każdy krok przy użyciu interfejsu API REST C#lub. Kompletna aplikacja ASP.NET MVC jest dostępna pod adresem [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Co robi aplikacja internetowa

Aplikacja sieci Web:

1. Zaloguj się do użytkownika platformy Azure.
2. Prosi użytkownika o przyznanie aplikacji sieci Web dostępu do Menedżer zasobów.
3. Pobiera token dostępu użytkownika i aplikacji na potrzeby uzyskiwania dostępu do Menedżer zasobów.
4. Używa tokenu (od kroku 3) do przypisywania nazwy głównej usługi aplikacji do roli w subskrypcji. Ten krok daje aplikacji długoterminowy dostęp do subskrypcji.
5. Pobiera token dostępu tylko do aplikacji.
6. Używa tokenu (od kroku 5) do zarządzania zasobami w ramach subskrypcji za pośrednictwem Menedżer zasobów.

Oto przepływ aplikacji sieci Web.

![Przepływ uwierzytelniania Menedżer zasobów](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Jako użytkownik podaj identyfikator subskrypcji, której chcesz użyć:

![Podaj identyfikator subskrypcji](./media/resource-manager-api-authentication/sample-ux-1.png)

Wybierz konto, które ma być używane do logowania.

![Wybierz konto](./media/resource-manager-api-authentication/sample-ux-2.png)

Podaj swoje poświadczenia.

![Podaj poświadczenia](./media/resource-manager-api-authentication/sample-ux-3.png)

Przyznaj aplikacji dostęp do subskrypcji platformy Azure:

![Udzielanie dostępu](./media/resource-manager-api-authentication/sample-ux-4.png)

Zarządzaj połączonymi subskrypcjami:

![Połącz subskrypcję](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Zarejestruj aplikację
Przed rozpoczęciem kodowania Zarejestruj swoją aplikację sieci Web za pomocą Azure Active Directory (AD). Rejestracja aplikacji tworzy centralną tożsamość dla aplikacji w usłudze Azure AD. Zawiera podstawowe informacje o aplikacji, takie jak identyfikator klienta OAuth, adresy URL odpowiedzi i poświadczenia używane przez aplikację do uwierzytelniania Azure Resource Manager interfejsów API i uzyskiwania do nich dostępu. Rejestracja aplikacji rejestruje również różne uprawnienia delegowane wymagane przez aplikację podczas uzyskiwania dostępu do interfejsów API firmy Microsoft dla użytkownika.

Aby zarejestrować aplikację, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../active-directory/develop/quickstart-register-app.md). Nadaj aplikacji nazwę, a następnie wybierz pozycję **konta w dowolnym katalogu organizacyjnym** dla obsługiwanych typów kont. W przypadku adresu URL przekierowania Podaj domenę skojarzoną z Azure Active Directory.

Aby zalogować się jako aplikacja usługi AD, potrzebujesz identyfikatora aplikacji i klucza tajnego. Identyfikator aplikacji jest wyświetlany w omówieniu dla aplikacji. Aby utworzyć wpis tajny i zażądać uprawnień interfejsu API, zobacz [Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do interfejsów API sieci Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Podaj nowy klucz tajny klienta. W obszarze uprawnienia interfejsu API wybierz pozycję **Azure Service Management**. Wybierz pozycję **uprawnienia delegowane** i **user_impersonation**.

### <a name="optional-configuration---certificate-credential"></a>Opcjonalna konfiguracja — poświadczenie certyfikatu
Usługa Azure AD obsługuje również poświadczenia certyfikatów dla aplikacji: tworzysz certyfikat z podpisem własnym, Zachowaj klucz prywatny i Dodaj klucz publiczny do rejestracji aplikacji usługi Azure AD. W przypadku uwierzytelniania aplikacja wysyła mały ładunek do usługi Azure AD podpisany przy użyciu klucza prywatnego, a usługa Azure AD weryfikuje podpis przy użyciu zarejestrowanego klucza publicznego.

Aby uzyskać informacje na temat tworzenia aplikacji usługi AD przy użyciu certyfikatu, zobacz [używanie Azure PowerShell do tworzenia jednostki usługi w celu uzyskania dostępu do zasobów](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) lub [korzystania z interfejsu wiersza polecenia platformy Azure w celu utworzenia jednostki usługi w celu uzyskania dostępu do zasobów](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Uzyskaj identyfikator dzierżawy z identyfikatora subskrypcji
Aby zażądać tokenu, który może służyć do wywoływania Menedżer zasobów, aplikacja musi znać identyfikator dzierżawy dzierżawy usługi Azure AD, która obsługuje subskrypcję platformy Azure. Najprawdopodobniej użytkownicy znają swoje identyfikatory subskrypcji, ale mogą nie znać ich identyfikatorów dzierżawy dla Azure Active Directory. Aby uzyskać identyfikator dzierżawy użytkownika, poproś użytkownika o identyfikator subskrypcji. Podaj ten identyfikator subskrypcji podczas wysyłania żądania dotyczącego subskrypcji:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Żądanie nie powiodło się, ponieważ użytkownik nie zalogował się jeszcze, ale można pobrać identyfikator dzierżawy z odpowiedzi. W tym wyjątku należy pobrać identyfikator dzierżawy z wartości nagłówka odpowiedzi dla **sieci Web — uwierzytelnianie**. Ta implementacja zostanie wyświetlona w metodzie [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Pobierz token dostępu użytkownika i aplikacji
Aplikacja przekierowuje użytkownika do usługi Azure AD za pomocą żądania autoryzacji OAuth 2,0, aby uwierzytelnić poświadczenia użytkownika i uzyskać kod autoryzacji. Aplikacja używa kodu autoryzacji w celu uzyskania tokenu dostępu dla Menedżer zasobów. Metoda [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) tworzy żądanie autoryzacji.

W tym artykule przedstawiono żądania interfejsu API REST w celu uwierzytelnienia użytkownika. Do uwierzytelniania w kodzie można także użyć bibliotek pomocnika. Aby uzyskać więcej informacji na temat tych bibliotek, zobacz [Azure Active Directory biblioteki uwierzytelniania](../active-directory/active-directory-authentication-libraries.md). Aby uzyskać wskazówki dotyczące integrowania zarządzania tożsamościami w aplikacji, zobacz [przewodnik dewelopera Azure Active Directory](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Żądanie uwierzytelnienia (OAuth 2,0)
Wydaj żądanie autoryzacji otwartego połączenia/OAuth 2.0 z autoryzacją do punktu końcowego autoryzacji usługi Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Parametry ciągu zapytania, które są dostępne dla tego żądania, są opisane w artykule [żądanie kodu autoryzacji](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) .

Poniższy przykład pokazuje, jak zażądać autoryzacji OAuth 2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Usługa Azure AD uwierzytelnia użytkownika i w razie potrzeby prosi użytkownika o udzielenie uprawnienia do aplikacji. Zwraca kod autoryzacji do adresu URL odpowiedzi aplikacji. W zależności od żądanego response_mode usługa Azure AD wysyła dane z powrotem do ciągu zapytania lub jako dane post.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Żądanie uwierzytelnienia (połączenie z otwartym IDENTYFIKATORem)
Jeśli nie tylko chcesz uzyskać dostęp do Azure Resource Manager dla użytkownika, ale również zezwolisz użytkownikom na logowanie się do aplikacji przy użyciu konta usługi Azure AD, wystaw żądanie autoryzacji za pomocą programu Open ID Connect. Przy użyciu programu Open ID Connect aplikacja otrzymuje także id_token z usługi Azure AD, za pomocą którego aplikacja może zalogować użytkownika.

Parametry ciągu zapytania, które są dostępne dla tego żądania, są opisane w artykule [Wysyłanie żądania logowania](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) .

Przykład żądania połączenia z otwartym IDENTYFIKATORem:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Usługa Azure AD uwierzytelnia użytkownika i w razie potrzeby prosi użytkownika o udzielenie uprawnienia do aplikacji. Zwraca kod autoryzacji do adresu URL odpowiedzi aplikacji. W zależności od żądanego response_mode usługa Azure AD wysyła dane z powrotem do ciągu zapytania lub jako dane post.

Przykładem odpowiedzi:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Żądanie tokenu (przepływ przyznania kodu OAuth 2.0)
Teraz, gdy aplikacja otrzymała kod autoryzacji z usługi Azure AD, należy uzyskać token dostępu dla Azure Resource Manager.  Opublikuj żądanie tokenu grantu Code protokołu OAuth 2.0 do punktu końcowego tokenu usługi Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry ciągu zapytania, które są dostępne dla tego żądania, są opisane w artykule [użycie kodu autoryzacji](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

Poniższy przykład przedstawia żądanie tokenu grantu Code z poświadczeniami hasła:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Podczas pracy z poświadczeniami certyfikatów Utwórz token sieci Web JSON (JWT) i Sign (RSA SHA256) przy użyciu klucza prywatnego poświadczenie certyfikatu aplikacji. Kompilowanie tego tokenu jest wyświetlane w [przepływie poświadczeń klienta](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with-a-certificate).

Aby uzyskać szczegółowe informacje na temat uwierzytelniania klientów, zobacz [specyfikację programu Open ID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) .

Poniższy przykład przedstawia żądanie tokenu grantu Code z poświadczenia certyfikatu:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Przykładowa odpowiedź dla tokenu grantu Code:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Obsługa odpowiedzi tokenu grantu kodu
Pomyślna odpowiedź tokenu zawiera token dostępu (User + App) dla Azure Resource Manager. Aplikacja używa tego tokenu dostępu, aby uzyskać dostęp do Menedżer zasobów użytkownika. Okres istnienia tokenów dostępu wystawionych przez usługę Azure AD to jedna godzina. Jest mało prawdopodobne, że aplikacja sieci Web musi odnowić token dostępu (użytkownik + aplikacja). Jeśli musi odnowić token dostępu, należy użyć tokenu odświeżania, który aplikacja otrzymuje w odpowiedzi tokenu. Opublikuj żądanie tokenu OAuth 2.0 w punkcie końcowym tokenu usługi Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry, które mają być używane z żądaniem odświeżania, są opisane w [odświeżeniu tokenu dostępu](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

Poniższy przykład pokazuje, jak używać tokenu odświeżania:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Tokeny odświeżania mogą służyć do uzyskiwania nowych tokenów dostępu dla Azure Resource Manager, ale nie są odpowiednie dla dostępu do trybu offline przez aplikację. Okres istnienia tokenów odświeżania jest ograniczony, a tokeny odświeżania są powiązane z użytkownikiem. Jeśli użytkownik opuści organizację, aplikacja używająca tokenu odświeżania utraci dostęp. Takie podejście nie jest odpowiednie dla aplikacji, które są używane przez zespoły do zarządzania zasobami platformy Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Sprawdź, czy użytkownik może przypisywać dostęp do subskrypcji
Aplikacja ma teraz token umożliwiający dostęp do Azure Resource Manager użytkownika. Następnym krokiem jest połączenie aplikacji z subskrypcją. Po nawiązaniu połączenia aplikacja może zarządzać tymi subskrypcjami nawet wtedy, gdy użytkownik nie jest obecny (dostęp długoterminowy w trybie offline).

Dla każdej subskrypcji, aby nawiązać połączenie, Wywołaj interfejs API [uprawnień listy Menedżer zasobów](https://docs.microsoft.com/rest/api/authorization/permissions) , aby określić, czy użytkownik ma prawa dostępu do usługi dla subskrypcji.

Metoda [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) aplikacji przykładowej ASP.NET MVC implementuje to wywołanie.

Poniższy przykład pokazuje, jak zażądać uprawnień użytkownika do subskrypcji. 83cfe939-2402-4581-b761-4f59b0a041e4 jest IDENTYFIKATORem subskrypcji.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Przykładem odpowiedzi na uzyskanie uprawnień użytkownika do subskrypcji jest:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Interfejs API uprawnień zwraca wiele uprawnień. Każde uprawnienie składa się z dozwolonych akcji (akcji) i niedozwolonych**akcji (** **noactions**). Jeśli akcja jest obecna w dozwolonych akcjach dowolnego uprawnienia i nie występuje w niedozwolonych akcjach tego uprawnienia, użytkownik może wykonać tę akcję. **Microsoft. Authorization/RoleAssignments/Write** to akcja, która przyznaje prawa do zarządzania dostępem. Aplikacja musi analizować wynik uprawnień, aby wyszukać dopasowanie wyrażenia regularnego dla tego ciągu akcji w **akcjach** i **nienaruszonych** poszczególnych uprawnień.

## <a name="get-app-only-access-token"></a>Pobierz token dostępu tylko do aplikacji
Teraz wiesz, czy użytkownik może przypisywać dostęp do subskrypcji platformy Azure. Następne kroki są następujące:

1. Przypisz odpowiednią rolę RBAC do tożsamości aplikacji w ramach subskrypcji.
2. Sprawdź poprawność przypisania dostępu, wykonując zapytania dotyczące uprawnienia aplikacji do subskrypcji lub poprzez dostęp do Menedżer zasobów przy użyciu tokenu tylko aplikacja.
3. Rejestrowanie połączenia w strukturze danych aplikacji "połączone subskrypcje" — utrwalanie identyfikatora subskrypcji.

Przyjrzyjmy się bliżej pierwszego kroku. Aby przypisać odpowiednią rolę RBAC do tożsamości aplikacji, należy określić:

* Identyfikator obiektu tożsamości aplikacji w Azure Active Directory użytkownika
* Identyfikator roli RBAC wymaganej przez aplikację w subskrypcji

Gdy aplikacja uwierzytelnia użytkownika z usługi Azure AD, tworzy obiekt jednostki usługi dla aplikacji w usłudze Azure AD. System Azure umożliwia przypisywanie ról RBAC do jednostek usługi w celu udzielenia bezpośredniego dostępu do odpowiednich aplikacji w zasobach platformy Azure. Ta akcja to dokładnie to, co chcesz zrobić. Wykonaj zapytanie dotyczące interfejs API programu Graph usługi Azure AD, aby określić identyfikator jednostki usługi w aplikacji w usłudze Azure AD zalogowanego użytkownika.

Masz tylko token dostępu dla Azure Resource Manager — potrzebujesz nowego tokenu dostępu do wywoływania interfejs API programu Graph usługi Azure AD. Każda aplikacja w usłudze Azure AD ma uprawnienia do wykonywania zapytań względem własnego obiektu głównego usługi, więc wystarczy token dostępu tylko do aplikacji.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Uzyskiwanie tokenu dostępu tylko do aplikacji dla usługi Azure AD interfejs API programu Graph

Aby uwierzytelnić aplikację i uzyskać token do usługi Azure AD interfejs API programu Graph, wystawiaj poświadczenia klienta Przydziel żądanie tokenu usługi OAuth 2.0 do punktu końcowego tokenu Azure AD (**https: \//login. microsoftonline. com/{directory_domain_name}/OAuth2/token**).

Metoda [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) aplikacji przykładowej ASP.NET MVC pobiera token dostępu tylko do aplikacji dla interfejs API programu Graph przy użyciu Active Directory Authentication Library dla platformy .NET.

Parametry ciągu zapytania, które są dostępne dla tego żądania, są opisane w artykule [żądanie tokenu dostępu](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) .

Przykładowe żądanie dotyczące tokenu przydzielenia poświadczeń klienta:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Przykładowa odpowiedź dla tokenu przydzielania poświadczeń klienta:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Pobieranie identyfikatora obiektu nazwy głównej usługi aplikacji w usłudze Azure AD użytkownika
Teraz Użyj tokenu dostępu tylko do aplikacji, aby wykonać zapytanie do interfejsu API jednostek [usługi Azure AD Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) w celu ustalenia identyfikatora obiektu nazwy głównej usługi aplikacji w katalogu.

Metoda [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) aplikacji przykładowej ASP.NET MVC implementuje to wywołanie.

Poniższy przykład pokazuje, jak zażądać nazwy głównej usługi aplikacji. a0448380-c346-4f9f-b897-c18733de9394 jest IDENTYFIKATORem klienta aplikacji.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

Poniższy przykład przedstawia odpowiedź na żądanie dla nazwy głównej usługi aplikacji

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Uzyskaj identyfikator roli RBAC platformy Azure
Aby przypisać odpowiednią rolę RBAC do nazwy głównej usługi, należy określić identyfikator roli RBAC platformy Azure.

Właściwa rola RBAC dla swojej aplikacji:

* Jeśli aplikacja monitoruje tylko subskrypcję bez wprowadzania żadnych zmian, wymaga tylko uprawnień czytelnika do subskrypcji. Przypisz rolę **czytelnika** .
* Jeśli Twoja aplikacja zarządza subskrypcją platformy Azure, tworzenie/modyfikowanie/usuwanie jednostek wymaga jednego z uprawnień współautora.
  * Aby zarządzać określonym typem zasobu, przypisz role współautor specyficzne dla zasobów (współautor maszyny wirtualnej, współautor Virtual Network, współautor konta magazynu itp.)
  * Aby zarządzać dowolnym typem zasobu, przypisz rolę **współautor** .

Przypisanie roli dla aplikacji jest widoczne dla użytkowników, więc wybierz uprawnienie najmniej wymagane.

Wywołaj [interfejs API definicji roli Menedżer zasobów](https://docs.microsoft.com/rest/api/authorization/roledefinitions) , aby wyświetlić listę wszystkich ról RBAC platformy Azure, a następnie wykonać iterację w wyniku, aby znaleźć definicję roli według nazwy.

Metoda [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) aplikacji przykładowej ASP.NET MVC implementuje to wywołanie.

W poniższym przykładzie żądania pokazano, jak uzyskać identyfikator roli RBAC platformy Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb jest IDENTYFIKATORem subskrypcji.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Odpowiedź ma następujący format:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Nie ma potrzeby ciągłego wywoływania tego interfejsu API. Po ustaleniu dobrze znanego identyfikatora GUID definicji roli można utworzyć identyfikator definicji roli jako:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Poniżej przedstawiono identyfikatory często używanych wbudowanych ról:

| Rola | IDENT |
| --- | --- |
| Czytelnik |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Współautor |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Współautor maszyny wirtualnej |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Współautor Virtual Network |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Współautor konta magazynu |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Współautor witryny sieci Web |de139f84-1756-47ae-9be6-808fbbe84772 |
| Współautor planu sieci Web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Współautor SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Współautor bazy danych SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Przypisywanie roli RBAC do aplikacji
Wszystko, co musisz zrobić, aby przypisać odpowiednią rolę RBAC do nazwy głównej usługi przy użyciu [Menedżer zasobów tworzenia interfejsu API przypisywania ról](https://docs.microsoft.com/rest/api/authorization/roleassignments) .

Metoda [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) aplikacji przykładowej ASP.NET MVC implementuje to wywołanie.

Przykładowe żądanie przypisania roli RBAC do aplikacji:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

W żądaniu są używane następujące wartości:

| Identyfikator GUID | Opis |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |Identyfikator subskrypcji |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |Identyfikator obiektu jednostki usługi aplikacji |
| b24988ac-6180-42a0-ab88-20f7382dd24c |Identyfikator roli współautor |
| 4f87261d-2816-465d-8311-70a27558df4c |nowy identyfikator GUID utworzony dla nowego przypisania roli |

Odpowiedź ma następujący format:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Uzyskaj token dostępu tylko do aplikacji dla Azure Resource Manager
Aby sprawdzić, czy aplikacja może uzyskać dostęp do subskrypcji, wykonaj zadanie testowe w subskrypcji przy użyciu tokenu tylko aplikacja.

Aby uzyskać token dostępu tylko do aplikacji, postępuj zgodnie z instrukcjami podanymi w sekcji [Uzyskiwanie tokenu dostępu tylko do aplikacji dla usługi Azure AD interfejs API programu Graph](#app-azure-ad-graph)z inną wartością parametru zasobu:

    https://management.core.windows.net/

Metoda [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) aplikacji przykładowej ASP.NET MVC pobiera token dostępu tylko do aplikacji dla Azure Resource Manager przy użyciu Active Directory Authentication Library dla platformy .NET.

#### <a name="get-applications-permissions-on-subscription"></a>Pobierz uprawnienia aplikacji do subskrypcji
Aby sprawdzić, czy aplikacja może uzyskać dostęp do subskrypcji platformy Azure, możesz również wywołać interfejs API [uprawnień Menedżer zasobów](https://docs.microsoft.com/rest/api/authorization/permissions) . Takie podejście jest podobne do sposobu ustalenia, czy użytkownik ma prawa do zarządzania dostępem do subskrypcji. Jednak ten czas należy wywołać interfejs API uprawnień z tokenem dostępu tylko do aplikacji otrzymanym w poprzednim kroku.

Metoda [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) aplikacji przykładowej ASP.NET MVC implementuje to wywołanie.

## <a name="manage-connected-subscriptions"></a>Zarządzanie połączonymi subskrypcjami
Po przypisaniu odpowiedniej roli RBAC do nazwy głównej usługi aplikacji w ramach subskrypcji aplikacja może zachować monitorowanie/zarządzanie przy użyciu tokenów dostępu tylko do aplikacji dla Azure Resource Manager.

Jeśli właściciel subskrypcji usunie przypisanie roli aplikacji przy użyciu portalu lub narzędzi wiersza polecenia, aplikacja nie będzie już mogła uzyskiwać dostępu do tej subskrypcji. W takim przypadku należy powiadomić użytkownika, że połączenie z subskrypcją zostało pobrane spoza aplikacji i nadawać im opcję "Napraw" połączenie. "Naprawa" spowoduje ponowne utworzenie przypisania roli, które zostało usunięte w trybie offline.

Po włączeniu użytkownika w celu połączenia subskrypcji z aplikacją należy zezwolić użytkownikowi na rozłączanie subskrypcji. Z punktu widzenia zarządzania dostępem Odłącz oznacza to usunięcie przypisania roli, którego nazwa główna usługi aplikacji ma w subskrypcji. Opcjonalnie można również usunąć wszystkie Stany aplikacji dla subskrypcji.
Tylko użytkownicy z uprawnieniem do zarządzania dostępem w ramach subskrypcji mogą rozłączać subskrypcję.

[Metoda RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) aplikacji przykładowej ASP.NET MVC implementuje to wywołanie.

Oznacza to, że użytkownicy mogą teraz łatwo łączyć swoje subskrypcje platformy Azure z Twoją aplikacją i zarządzać nimi.
