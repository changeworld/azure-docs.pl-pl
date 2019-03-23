---
title: Uwierzytelnianie usługi Active Directory systemu Azure i usługi Resource Manager | Dokumentacja firmy Microsoft
description: Przewodnik dla deweloperów do uwierzytelniania za pomocą interfejsu API usługi Azure Resource Manager i Azure Active Directory w celu integracji aplikacji z innych subskrypcji platformy Azure.
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
ms.date: 3/22/2019
ms.author: dugill
ms.openlocfilehash: 5144a35dd695ce30f4a7ff940f0bca7e6ba9d23c
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372555"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Interfejs API uwierzytelniania Użyj usługi Resource Manager do dostępu do subskrypcji

Jeśli jesteś programistą, kto chce utworzyć aplikację, która zarządza zasobami platformy Azure dla klientów, w tym artykule pokazano, jak przy użyciu interfejsów API usługi Azure Resource Manager i uzyskać dostęp do zasobów w innych subskrypcjach.

Twoja aplikacja może uzyskiwać dostęp z interfejsów API usługi Resource Manager, kilka sposobów:

1. **Dostęp użytkownika i aplikacji**: w przypadku aplikacji uzyskujących dostęp do zasobów dla zalogowanego użytkownika. Ta metoda działa w przypadku aplikacji, takich jak aplikacje sieci web i narzędzia wiersza polecenia, które zajmują się tylko "zarządzania interakcyjnego" zasobów platformy Azure.
2. **Dostęp tylko do aplikacji**: dla aplikacji uruchamianych usługi demona i zaplanowane zadania. Tożsamości aplikacji udzielany jest bezpośredni dostęp do zasobów. Ta metoda działa w przypadku aplikacje, które muszą długoterminowe bezobsługowego (Instalacja nienadzorowana) korzystanie z platformy Azure.

Ten artykuł zawiera instrukcje krok po kroku, aby utworzyć aplikację, która stosuje oba te metody autoryzacji. Pokazuje, jak to zrobić każdego kroku, korzystając z interfejsu API REST lub C#. Kompletna aplikacja platformy ASP.NET MVC znajduje się w temacie [ https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense ](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-the-web-app-does"></a>Jak działa aplikacja sieci web

Aplikacja sieci web:

1. Logowania użytkownikiem platformy Azure.
2. Pyta użytkownika w celu przyznania dostępu do aplikacji sieci web do usługi Resource Manager.
3. Pobiera użytkownika i token dostępu aplikacji do uzyskiwania dostępu do usługi Resource Manager.
4. Przypisuje nazwę główną usługi aplikacji do roli w subskrypcji przy użyciu tokenu (z kroku 3). Ten krok zapewnia długoterminowe dostęp do aplikacji do subskrypcji.
5. Pobiera token dostępu tylko do aplikacji.
6. Używa tokenu (z kroku 5) do zarządzania zasobami w ramach subskrypcji za pomocą usługi Resource Manager.

Poniżej przedstawiono przepływ aplikacji sieci web.

![Przepływ uwierzytelniania usługi Resource Manager](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Jako użytkownik identyfikator subskrypcji przewidują subskrypcję, której chcesz użyć:

![Podaj identyfikator subskrypcji](./media/resource-manager-api-authentication/sample-ux-1.png)

Wybierz konto używane do zalogowania się.

![Wybierz konto](./media/resource-manager-api-authentication/sample-ux-2.png)

Wprowadź swoje poświadczenia.

![Podaj poświadczenia](./media/resource-manager-api-authentication/sample-ux-3.png)

Udzielić aplikacji dostępu do subskrypcji platformy Azure:

![Udzielanie dostępu](./media/resource-manager-api-authentication/sample-ux-4.png)

Zarządzaj swoimi subskrypcjami połączone:

![Łączenie subskrypcji](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Zarejestruj aplikację
Przed rozpoczęciem kodowania, Zarejestruj swoją aplikację sieci web za pomocą usługi Azure Active Directory (AD). Rejestracja aplikacji powoduje utworzenie centralnej tożsamości aplikacji w usłudze Azure AD. Przechowuje podstawowe informacje o swojej aplikacji, takich jak identyfikator klienta OAuth, adresy URL odpowiedzi i poświadczeń używanych przez aplikację do uwierzytelniania i dostępu do interfejsów API usługi Azure Resource Manager. Rejestracja aplikacji rejestruje także różne uprawnienia delegowane, wymaganych przez aplikację podczas uzyskiwania dostępu do firmy Microsoft APIs dla użytkownika.

Ponieważ aplikacja uzyskuje dostęp do innej subskrypcji, należy go skonfigurować jako aplikacji z wieloma dzierżawami. Aby pomyślnie weryfikacji, należy podać domeny skojarzone z usługi Azure Active Directory. Aby wyświetlić domeny skojarzone z usługi Azure Active Directory, zaloguj się do portalu.

Poniższy przykład pokazuje, jak zarejestrować aplikację przy użyciu programu Azure PowerShell. Konieczne jest posiadanie najnowszej wersji programu Azure PowerShell na potrzeby to polecenie mogło działać (sierpnia 2016 r.).

```azurepowershell-interactive
$app = New-AzADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
```

Aby zalogować się jako aplikacji usługi AD, potrzebujesz Identyfikatora aplikacji i hasło. Aby wyświetlić identyfikator aplikacji, która jest zwracana z poprzedniego polecenia, użyj:

```azurepowershell-interactive
$app.ApplicationId
```

Poniższy przykład pokazuje, jak zarejestrować aplikację przy użyciu wiersza polecenia platformy Azure.

```azurecli-interactive
az ad app create --display-name {app name} --homepage https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available-to-other-tenants true
```

Wyniki obejmują identyfikator aplikacji, które są wymagane podczas uwierzytelniania aplikacji.

### <a name="optional-configuration---certificate-credential"></a>Konfiguracja opcjonalna - poświadczenie certyfikatu
Usługa Azure AD obsługuje również poświadczeń certyfikatu dla aplikacji: Utworzenie certyfikatu z podpisem własnym, zachowaj klucz prywatny i Dodaj klucz publiczny do rejestracji aplikacji usługi Azure AD. Do uwierzytelniania Twoja aplikacja przesyła małych ładunku do usługi Azure AD podpisany przy użyciu klucza prywatnego, a usługa Azure AD sprawdza podpis przy użyciu klucza publicznego, który został zarejestrowany.

Aby uzyskać informacje dotyczące tworzenia aplikacji usługi AD za pomocą certyfikatu, zobacz [użyciu programu Azure PowerShell utworzyć nazwę główną usługi dostępu do zasobów](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) lub [interfejsu wiersza polecenia użyj Azure, aby utworzyć jednostkę usługi, aby uzyskiwać dostęp do zasobów](resource-group-authenticate-service-principal-cli.md) .

## <a name="get-tenant-id-from-subscription-id"></a>Pobieranie Identyfikatora dzierżawy z subskrypcji o identyfikatorze
Do żądania tokenu, który może służyć do wywołania usługi Resource Manager, aplikacja musi wiedzieć, identyfikator dzierżawy w dzierżawie usługi Azure AD, który jest hostem subskrypcji platformy Azure. Najbardziej prawdopodobne, że użytkownicy wiedzą, swój identyfikator subskrypcji, ale może nie znają ich dzierżawy identyfikatorów usługi Azure Active Directory. Aby uzyskać identyfikator dzierżawy użytkownika, należy poprosić użytkownika dla identyfikatora subskrypcji. Podaj tę subskrypcję IDENTYFIKATORA podczas wysyłania żądania o subskrypcji:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Żądanie nie powiedzie się, ponieważ użytkownik nie zostało jeszcze zarejestrowane, ale można pobrać Identyfikatora dzierżawy z odpowiedzi. W tym wyjątków, należy pobrać identyfikator dzierżawy z wartość nagłówka odpowiedzi **WWW-Authenticate**. Zostanie wyświetlony w tej implementacji w [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) metody.

## <a name="get-user--app-access-token"></a>Pobierz użytkownika i token dostępu aplikacji
Aplikacja przekierowuje użytkownika do usługi Azure AD przy użyciu protokołu OAuth 2.0 autoryzować żądania — uwierzytelnienia poświadczeń użytkownika i uzyskanie kodu autoryzacji. Aplikacja używa kodu autoryzacji do uzyskania tokenu dostępu dla usługi Resource Manager. [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) metoda tworzy żądanie autoryzacji.

W tym artykule przedstawiono żądań interfejsu API REST w celu uwierzytelnienia użytkownika. Umożliwia także biblioteki pomocnika do uwierzytelniania w kodzie. Aby uzyskać więcej informacji na temat tych bibliotek, zobacz [bibliotek uwierzytelniania usługi Azure Active Directory](../active-directory/active-directory-authentication-libraries.md). Aby uzyskać wskazówki dotyczące integrowania zarządzania tożsamością w aplikacji, zobacz [przewodnik dewelopera usługi Azure Active Directory](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Żądania uwierzytelniania (OAuth 2.0)
Wykonaj Open ID Connect/OAuth 2.0 autoryzować żądania do punktu końcowego autoryzacji usługi Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Parametry ciągu zapytania, które są dostępne dla tego żądania są opisane w [żądania kodu autoryzacji](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) artykułu.

Poniższy przykład pokazuje, jak żądania autoryzacji OAuth 2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Usługa Azure AD uwierzytelnia użytkownika, a jeśli to konieczne, prosi użytkownika o udzielenie uprawnień do aplikacji. Zwraca kod autoryzacji do adresu URL odpowiedzi aplikacji. W zależności od żądanej response_mode, usługi Azure AD albo wysyła z powrotem dane w ciągu zapytania lub w postaci danych post.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Żądania uwierzytelniania (Open ID Connect)
Jeśli chcesz, aby nie tylko dostępu do usługi Azure Resource Manager dla użytkownika, ale również zezwolić użytkownikowi zalogowanie się do aplikacji przy użyciu swojego konta usługi Azure AD, należy wydać Open ID autoryzacji żądania połączenia. Za pomocą Open ID Connect aplikacja odbiera id_token z usługi Azure AD, używanego przez aplikację do logowania użytkownika.

Parametry ciągu zapytania, które są dostępne dla tego żądania są opisane w [wysłać żądanie logowania](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) artykułu.

Przykładowe żądanie Open ID Connect jest:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Usługa Azure AD uwierzytelnia użytkownika, a jeśli to konieczne, prosi użytkownika o udzielenie uprawnień do aplikacji. Zwraca kod autoryzacji do adresu URL odpowiedzi aplikacji. W zależności od żądanej response_mode, usługi Azure AD albo wysyła z powrotem dane w ciągu zapytania lub w postaci danych post.

Przykład odpowiedzi Open ID Connect to:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Żądanie tokenu (OAuth2.0 kodu przepływ przyznawania)
Teraz, gdy aplikacja otrzymała kod autoryzacji z usługi Azure AD, nadszedł czas na uzyskiwanie tokenu dostępu do usługi Azure Resource Manager.  Opublikuj, OAuth 2.0 kodu Grant Token żądania do punktu końcowego usługi Azure AD tokenu:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry ciągu zapytania, które są dostępne dla tego żądania są opisane w [kodu autoryzacji](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) artykułu.

Poniższy przykład przedstawia żądanie dotyczące kodu grant tokenu przy użyciu poświadczeń hasła:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Pracując przy użyciu poświadczeń certyfikatu, Utwórz tokenu Web JSON (JWT) i zaloguj (RSA SHA256), używając klucza prywatnego poświadczeń certyfikatu aplikacji. Tworzenie token ten jest wyświetlany w [przepływ poświadczeń klienta](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with a-certificate).  Aby informacje, zobacz [Active Directory uwierzytelniania biblioteki (.NET) kod](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) do podpisywania tokenów JWT potwierdzenia klienta.

Zobacz [specyfikacji Open ID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) szczegółowe informacje dotyczące uwierzytelniania klienta.

Poniższy przykład przedstawia żądanie dotyczące kodu grant tokenu przy użyciu poświadczeń certyfikatu:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Przykładową odpowiedź dla kodu udzielić tokenu:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Obsługa kodu grant tokenu odpowiedź na żądanie
Odpowiedź oznaczająca Powodzenie tokenu zawiera (użytkowników i aplikacji) token dostępu usługi Azure Resource Manager. Aplikacja używa tego tokenu dostępu, dostęp do usługi Resource Manager przez użytkownika. Okres istnienia tokeny dostępu wystawione przez usługę Azure AD to jedna godzina. Jest mało prawdopodobne, że aplikacja sieci web musi odnowić (użytkowników i aplikacji) token dostępu. Jeśli potrzebny do odnawiania tokenu dostępu, należy użyć tokenu odświeżania, która aplikacja otrzymuje w odpowiedzi tokenu. Opublikuj, OAuth 2.0 Token żądania do punktu końcowego usługi Azure AD tokenu:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry do użycia z tym żądaniem odświeżania są opisane w [odświeżanie tokenu dostępu](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

Poniższy przykład pokazuje, jak używać odświeżanie tokenu:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Chociaż można używać tokenów odświeżania można pobrać nowych tokenów dostępu usługi Azure Resource Manager, nie są one odpowiednie dla dostępu przez aplikację w trybie offline. Okres istnienia tokenów odświeżania jest ograniczona, a tokeny odświeżania są powiązane z użytkownika. Jeśli użytkownik opuszcza organizację, aplikacji, używając tokenu odświeżania traci dostęp. To podejście jest odpowiednie w przypadku aplikacji, które są używane przez zespoły do zarządzania zasobami platformy Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Sprawdź, jeśli użytkownik może przypisać dostęp do subskrypcji
Teraz Twoja aplikacja ma token dostępu do usługi Azure Resource Manager dla użytkownika. Następnym krokiem jest łączenie aplikacji do subskrypcji. Po nawiązaniu połączenia aplikacji można zarządzać te subskrypcje, nawet wtedy, gdy użytkownik nie jest obecny (długoterminowe dostęp w trybie offline).

Dla każdej subskrypcji do łączenia z wywołania [uprawnienia listy usługi Resource Manager](https://docs.microsoft.com/rest/api/authorization/permissions) interfejsu API w celu określenia, czy użytkownik ma uprawnienia zarządzania dla subskrypcji.

[UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) metoda przykładową aplikację platformy ASP.NET MVC zaimplementowano tego wywołania.

Poniższy przykład pokazuje, jak żądania uprawnień użytkownika, w ramach subskrypcji. 83cfe939-2402-4581-b761-4f59b0a041e4 jest identyfikator subskrypcji.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Przykład odpowiedzi, aby uzyskać uprawnienia użytkownika w ramach subskrypcji to:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Uprawnień interfejsu API zwraca wiele uprawnień. Każdego uprawnienia, które składa się z dozwolonych akcji (**akcje**) i niedopuszczalne akcje (**notactions**). Jeśli akcja znajduje się w dozwolonych akcji żadnych uprawnień, a nie znajduje się w niedozwolone akcje tego uprawnienia, użytkownik może wykonywać tę akcję. **Microsoft.Authorization/RoleAssignments/Write** jest akcja, która przyznaje dostęp usługi rights management. Aplikacja musi przeanalizować wynik uprawnienia do wyszukania dopasowanie wyrażenia regularnego w tym ciąg akcji w **akcje** i **notactions** wszystkich uprawnień.

## <a name="get-app-only-access-token"></a>Uzyskaj token dostępu tylko do aplikacji
Teraz wiesz, jeśli użytkownik może przypisać dostęp do subskrypcji platformy Azure. Potrzebne są kolejne kroki:

1. Przypisz odpowiednią rolę RBAC do tożsamości aplikacji w ramach subskrypcji.
2. Sprawdź poprawność przypisania dostępu przy użyciu zapytań dotyczących uprawnień aplikacji w ramach subskrypcji lub uzyskując dostęp do usługi Resource Manager przy użyciu tokenu tylko do aplikacji.
3. Zapisz połączenie w strukturze danych aplikacji "połączonej subskrypcji" — przechowywanie identyfikator subskrypcji.

Przyjrzyjmy się bliżej w pierwszym kroku. Aby przypisać odpowiednie role RBAC do tożsamości aplikacji, należy określić:

* Identyfikator obiektu aplikacji tożsamości użytkownika usługi Azure Active Directory
* Identyfikator roli funkcji RBAC w wymaganych przez aplikację w ramach subskrypcji

Gdy aplikacja uwierzytelnia użytkownika z usługi Azure AD, tworzy obiektu jednostki usługi dla aplikacji w tej usłudze Azure AD. Platforma Azure zezwoli role RBAC, które ma być przypisane do jednostki usługi do udzielania bezpośredni dostęp do odpowiednie aplikacje w zasobach platformy Azure. Ta akcja jest dokładnie, co chcesz zrobić. Azure AD dla zapytania interfejsu API programu Graph usługi Azure AD w celu ustalenia identyfikator jednostki usługi dla aplikacji w zalogowanego użytkownika.

Masz tylko token dostępu usługi Azure Resource Manager — należy nowy token dostępu do wywołania interfejsu API programu Graph usługi Azure AD. Każda aplikacja w usłudze Azure AD ma uprawnienia do tworzenia zapytań własnego obiektu jednostki usługi, więc wystarczy token dostępu tylko do aplikacji.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Uzyskaj token dostępu tylko do aplikacji interfejsu API programu Graph usługi Azure AD

Do uwierzytelniania aplikacji i pobrać tokenu do interfejsu API programu Graph usługi Azure AD, należy wysłać żądanie tokenu przepływu OAuth 2.0 przydział poświadczeń klienta do punktu końcowego tokenu usługi Azure AD (**https:\//login.microsoftonline.com/{directory_domain_name}/OAuth2/Token** ).

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) metoda przykładowej aplikacji platformy ASP.net MVC pobiera tylko do aplikacji token dostępu dla interfejsu API programu Graph przy użyciu biblioteki uwierzytelniania usługi Active Directory dla środowiska .NET.

Parametry ciągu zapytania, które są dostępne dla tego żądania są opisane w [żądania tokenu dostępu](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) artykułu.

Przykładowe żądanie tokenu przydział poświadczeń klienta:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Przykładową odpowiedź dla tokenu przydział poświadczeń klienta:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Pobierz identyfikator obiektu nazwy głównej usługi aplikacji w polu użytkownika usługi Azure AD
Teraz Użyj tokenu dostępu tylko dla aplikacji do wykonywania zapytań [jednostki usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) interfejsu API, aby określić identyfikator obiektu nazwy głównej usługi aplikacji w katalogu.

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) metoda przykładowej aplikacji platformy ASP.net MVC zaimplementowano tego wywołania.

Poniższy przykład pokazuje, jak żądanie nazwy głównej usługi aplikacji. a0448380-c346-4f9f-b897-c18733de9394 jest identyfikator klienta aplikacji.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

W poniższym przykładzie przedstawiono odpowiedzi na żądanie dla aplikacji usługi głównej

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Pobierz identyfikator ról RBAC platformy Azure
Aby przypisać odpowiednie role RBAC do nazwy głównej usługi, należy określić identyfikator rolę RBAC platformy Azure.

Odpowiednią rolę RBAC dla aplikacji:

* Jeśli aplikacja monitoruje tylko subskrypcji, bez wprowadzania żadnych zmian, wymaga tylko uprawnienia czytnika w ramach subskrypcji. Przypisz **czytnika** roli.
* Jeśli aplikacja zarządza Azure subskrypcji, Tworzenie/modyfikowanie/usuwanie jednostek, wymaga jednego uprawnienia współautora.
  * Aby zarządzać określonego typu zasobów, przypisywanie ról określonych zasobów współautora (Współautor maszyny wirtualnej Współautor sieci wirtualnej, współautor konta magazynu, itp.)
  * Aby zarządzać dowolnym typem zasobów, należy przypisać **Współautor** roli.

Przypisanie roli aplikacji jest widoczna dla użytkowników, dlatego wybierz wymagane najmniejszych uprawnień.

Wywołaj [definicji roli Menedżera zasobów interfejsu API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) do listy wszystkich ról RBAC platformy Azure, a następnie przejść przez wyniki w celu znalezienia definicji roli według nazwy.

[GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) metoda przykładową aplikację platformy ASP.net MVC zaimplementowano tego wywołania.

W poniższym przykładzie żądanie pokazano, jak uzyskać identyfikator ról RBAC platformy Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb jest identyfikator subskrypcji.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Odpowiedź znajduje się w następującym formacie:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Nie trzeba wywołać ten interfejs API w sposób ciągły. Po określeniu dobrze znanego identyfikatora GUID definicji roli możesz utworzyć jako identyfikator definicji roli:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Poniżej przedstawiono identyfikatory powszechnie używane wbudowanych ról:

| Rola | Identyfikator GUID |
| --- | --- |
| Czytelnik |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Współautor |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Współautor maszyny wirtualnej |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Virtual Network Contributor |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Współautor konta magazynu |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Współautor witryny sieci Web |de139f84-1756-47ae-9be6-808fbbe84772 |
| Współautor planów sieci Web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Współautor serwera SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Współautor bazy danych SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Przypisz rolę RBAC do aplikacji
Masz wszystko, czego potrzebujesz do przypisania odpowiednich ról RBAC do jednostki usługi przy użyciu [usługi Resource Manager, Utwórz przypisanie roli](https://docs.microsoft.com/rest/api/authorization/roleassignments) interfejsu API.

[GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) metoda przykładową aplikację platformy ASP.net MVC zaimplementowano tego wywołania.

Przykładowe żądanie przypisać rolę RBAC do aplikacji:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

W żądaniu używane są następujące wartości:

| Guid | Opis |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |Identyfikator subskrypcji |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |Identyfikator obiektu nazwy głównej usługi aplikacji |
| b24988ac-6180-42a0-ab88-20f7382dd24c |Identyfikator roli współautora |
| 4f87261d-2816-465d-8311-70a27558df4c |Nowy identyfikator guid utworzone dla nowego przypisania roli |

Odpowiedź znajduje się w następującym formacie:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Uzyskiwanie dostępu tylko do aplikacji tokenu usługi Azure Resource Manager
Do sprawdzania poprawności tej aplikacji można uzyskać dostępu do subskrypcji, wykonaj test zadań w ramach subskrypcji przy użyciu tokenu tylko do aplikacji.

Aby uzyskać token dostępu tylko do aplikacji, wykonaj instrukcje z sekcji [uzyskiwanie tokenów dostępu tylko do aplikacji dla interfejsu API usługi Azure AD Graph](#app-azure-ad-graph), podając inną wartość dla parametru zasobów:

    https://management.core.windows.net/

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metoda przykładowej aplikacji platformy ASP.NET MVC pobiera tylko do aplikacji token dostępu usługi Azure Resource Manager przy użyciu biblioteki uwierzytelniania usługi Active Directory dla środowiska .net.

#### <a name="get-applications-permissions-on-subscription"></a>Uzyskaj uprawnienia aplikacji w ramach subskrypcji
Aby sprawdzić, czy Twoja aplikacja ma dostęp do subskrypcji platformy Azure, mogą również wywołać [uprawnienia menedżera zasobów](https://docs.microsoft.com/rest/api/authorization/permissions) interfejsu API. To podejście jest podobne do sposób można stwierdzić, czy użytkownik ma uprawnienia dostępu do zarządzania dla subskrypcji. Jednak tym razem wywoływać uprawnień interfejsu API przy użyciu tokenu dostępu tylko do aplikacji, otrzymany w poprzednim kroku.

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metoda przykładową aplikację platformy ASP.NET MVC zaimplementowano tego wywołania.

## <a name="manage-connected-subscriptions"></a>Zarządzaj subskrypcjami połączone
Po odpowiednich ról RBAC jest przypisany do jednostki w ramach subskrypcji usługi aplikacji, aplikację można zachować monitorowania/zarządzania nią przy użyciu tokenów dostępu tylko do aplikacji usługi Azure Resource Manager.

Jeśli właściciel subskrypcji spowoduje usunięcie przypisania roli aplikacji za pomocą portalu lub narzędzi wiersza polecenia, aplikacja nie jest już mogli korzystać z tej subskrypcji. W takim przypadku należy powiadomienia użytkownika poza aplikacją Przerwano z połączenia z subskrypcją i nadaj im opcję "Napraw" połączenie. "Napraw" spowodowałoby ponowne przypisanie roli, który został usunięty w trybie offline.

Tak, jak został włączony użytkownik nawiązał połączenie subskrypcje do swojej aplikacji, muszą zezwalać na użytkowników można rozłączyć za subskrypcje. Z dostępu do zarządzania punktu widzenia Odłącz oznacza, że usunięcie przypisania roli, zawierającej nazwy głównej usługi aplikacji w ramach subskrypcji. Opcjonalnie każdy stan w aplikacji dla subskrypcji mogą zostać usunięte zbyt.
Tylko użytkownicy mający uprawnienie do dostępu do zarządzania w ramach subskrypcji można odłączyć subskrypcji.

[Metoda RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) platformy ASP.net MVC przykładową aplikację implementuje tego wywołania.

To wszystko — użytkownicy mogą teraz łatwo połączyć i zarządzają subskrypcjami platformy Azure za pomocą aplikacji.
