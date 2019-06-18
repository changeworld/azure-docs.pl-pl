---
title: Zarządzane Przegląd tożsamości — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Koncepcyjny Przewodnik instalacji i odwołania dla zarządzanych tożsamości w usłudze Azure App Service i Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.openlocfilehash: 0942d5ba7b31ddb2c0dec5fe979f1331d1bf3bfd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66136971"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Jak używać zarządzanych tożsamości dla usługi App Service i Azure Functions

> [!NOTE] 
> Obsługa tożsamość zarządzaną w usłudze App Service w systemie Linux i Web App for Containers jest obecnie w wersji zapoznawczej.

> [!Important] 
> Tożsamości zarządzanej dla usługi App Service i Azure Functions nie będzie działać zgodnie z oczekiwaniami, jeśli aplikacja jest migrowana subskrypcji/dzierżawy. Aplikacja musi uzyskać nową tożsamość może odbywać się przez wyłączenie i ponowne włączenie tej funkcji. Zobacz [usuwanie tożsamości](#remove) poniżej. Zasoby podrzędne musisz również mieć zasady dostępu zaktualizowana w celu używania nowej tożsamości.

W tym temacie dowiesz się, jak utworzyć tożsamość zarządzaną w aplikacjach usługi App Service i usługi Azure Functions i jak z niej korzystać, aby uzyskać dostęp do innych zasobów. Tożsamość zarządzaną w usłudze Azure Active Directory umożliwia aplikacji łatwo uzyskiwać dostęp do innych zasobów chronionych przez usługi AAD, takich jak usługi Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure i nie wymaga obsługi administracyjnej ani Obróć jakichkolwiek kluczy tajnych. Aby uzyskać więcej informacji o zarządzanych tożsamości w usłudze AAD, zobacz [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

Aplikacja może otrzymać dwa rodzaje tożsamości: 
- A **tożsamości przypisanych przez system** jest powiązany z aplikacją i zostanie usunięty, jeśli aplikacja zostanie usunięta. Aplikacja może mieć tylko jedną tożsamość przypisaną przez system. Obsługa tożsamości przypisanych przez system jest ogólnie dostępna dla aplikacji Windows. 
- A **tożsamości przypisanych przez użytkownika** jest autonomicznym zasobów platformy Azure, które mogą być przypisane do aplikacji. Aplikacja może mieć wiele tożsamości przypisanych przez użytkownika. Obsługa tożsamości przypisanych przez użytkownika jest w wersji zapoznawczej dla wszystkich typów aplikacji.

## <a name="adding-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanych przez system

Tworzenie aplikacji przy użyciu tożsamości przypisanych przez system wymaga dodatkowych właściwości, należy ustawić na aplikację.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, musisz najpierw utworzyć aplikację w zwykły sposób, a następnie włączyć tę funkcję.

1. Utwórz aplikację w portalu, tak jak zwykle. Przejdź do niej w portalu.

2. Jeśli używasz aplikacji funkcji, przejdź do **funkcje platformy**. Dla innych typów aplikacji, przewiń w dół do **ustawienia** grupy w obszarze nawigacji po lewej stronie.

3. Wybierz **tożsamości zarządzanej**.

4. W ramach **przypisanej w systemie** kartę, Przełącz **stan** do **na**. Kliknij pozycję **Zapisz**.

![Tożsamość zarządzaną w usłudze App Service](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzana przy użyciu wiersza polecenia platformy Azure, musisz użyć `az webapp identity assign` polecenia w odniesieniu do istniejących aplikacji. Masz trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [usługi Azure Cloud Shell](../cloud-shell/overview.md) w witrynie Azure portal.
- Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 

Poniższe kroki przeprowadzi Cię przez proces tworzenia aplikacji sieci web i przypisywanie jej tożsamości przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta, który jest skojarzony z subskrypcją platformy Azure w ramach której chcesz wdrożyć aplikację:

    ```azurecli-interactive
    az login
    ```
2. Tworzenie aplikacji internetowej przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów użycia interfejsu wiersza polecenia przy użyciu usługi App Service, zobacz [przykłady interfejsu wiersza polecenia aplikacji usługi](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Uruchom `identity assign` polecenie, aby utworzyć tożsamość dla tej aplikacji:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższe kroki przeprowadzi Cię przez proces tworzenia aplikacji sieci web i przypisywanie jej tożsamości przy użyciu programu Azure PowerShell:

1. W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

2. Tworzenie aplikacji internetowej przy użyciu programu Azure PowerShell. Aby uzyskać więcej przykładów programu Azure PowerShell za pomocą usługi App Service, zobacz [przykłady programu PowerShell usługi aplikacji](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Uruchom `Set-AzWebApp -AssignIdentity` polecenie, aby utworzyć tożsamość dla tej aplikacji:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Przy użyciu szablonu usługi Azure Resource Manager

Szablon usługi Azure Resource Manager umożliwia automatyzowanie wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej na temat wdrażania usługi App Service i Functions, zobacz [Automatyzowanie wdrażania zasobów w usłudze App Service](../app-service/deploy-complex-application-predictably.md) i [Automatyzowanie wdrażania zasobów w usłudze Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Dowolny zasób typu `Microsoft.Web/sites` można utworzyć przy użyciu tożsamości, umieszczając następujące właściwości w definicji zasobu:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Aplikacja może mieć tożsamości przypisanych przez użytkownika i przypisanych przez system, w tym samym czasie. W tym przypadku `type` będzie właściwości `SystemAssigned,UserAssigned`

Dodawanie typu przypisany systemowo informuje platformy Azure do tworzenia i zarządzania tożsamościami dla aplikacji.

Na przykład aplikacja sieci web może wyglądać następująco:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Gdy witryna jest tworzona, ma następujące dodatkowe właściwości:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Gdzie `<TENANTID>` i `<PRINCIPALID>` są zastępowane identyfikatorów GUID. Właściwość tenantId identyfikuje dzierżawy usługi AAD, jakie należy tożsamości. Identyfikator principalId to unikatowy identyfikator dla nowej tożsamości aplikacji. W ramach usługi AAD nazwa główna usługi ma taką samą nazwę, która udostępniła do swojego wystąpienia usługi App Service lub Azure Functions.


## <a name="adding-a-user-assigned-identity-preview"></a>Dodawanie tożsamości przypisanych przez użytkownika (wersja zapoznawcza)

> [!NOTE] 
> Tożsamości przypisanych przez użytkownika są obecnie dostępne w wersji zapoznawczej. Suwerenne chmury nie są jeszcze obsługiwane.

Tworzenie aplikacji przy użyciu tożsamości przypisanych przez użytkownika wymaga tworzenia tożsamości, a następnie dodaj swój identyfikator zasobu do pliku config aplikacji.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

> [!NOTE] 
> To środowisko portalu jest wdrażana i mogą jeszcze być niedostępne we wszystkich regionach.

Najpierw musisz utworzyć zasób tożsamości przypisanych przez użytkownika.

1. Utwórz zasób przypisanych przez użytkownika z tożsamości zarządzanej zgodnie z opisem w [w instrukcjach](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Utwórz aplikację w portalu, tak jak zwykle. Przejdź do niej w portalu.

3. Jeśli używasz aplikacji funkcji, przejdź do **funkcje platformy**. Dla innych typów aplikacji, przewiń w dół do **ustawienia** grupy w obszarze nawigacji po lewej stronie.

4. Wybierz **tożsamości zarządzanej**.

5. W ramach **użytkownik przypisany (wersja zapoznawcza)** kliknij pozycję **Dodaj**.

6. Wyszukaj tożsamości, która została utworzona wcześniej i zaznacz go. Kliknij pozycję **Add** (Dodaj).

![Tożsamość zarządzaną w usłudze App Service](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Przy użyciu szablonu usługi Azure Resource Manager

Szablon usługi Azure Resource Manager umożliwia automatyzowanie wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej na temat wdrażania usługi App Service i Functions, zobacz [Automatyzowanie wdrażania zasobów w usłudze App Service](../app-service/deploy-complex-application-predictably.md) i [Automatyzowanie wdrażania zasobów w usłudze Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Dowolnego zasobu typu `Microsoft.Web/sites` można utworzyć przy użyciu tożsamości, umieszczając następujący blok w definicji zasobu, zastępując `<RESOURCEID>` o identyfikatorze zasobu żądaną tożsamości:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Aplikacja może mieć tożsamości przypisanych przez użytkownika i przypisanych przez system, w tym samym czasie. W tym przypadku `type` będzie właściwości `SystemAssigned,UserAssigned`

Dodawanie typu przypisanych przez użytkownika i cotells platformy Azure do tworzenia i zarządzania tożsamościami dla aplikacji.

Na przykład aplikacja sieci web może wyglądać następująco:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Gdy witryna jest tworzona, ma następujące dodatkowe właściwości:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Gdzie `<PRINCIPALID>` i `<CLIENTID>` są zastępowane identyfikatorów GUID. Identyfikator principalId to unikatowy identyfikator dla tożsamości, który jest używany dla administracji usługi AAD. Identyfikator ClientID, który jest unikatowy identyfikator aplikacji nowej tożsamości używanej do określania tożsamość do użycia podczas wywołania środowiska uruchomieniowego.


## <a name="obtaining-tokens-for-azure-resources"></a>Uzyskiwanie tokenów dla zasobów platformy Azure

Aplikacja może używać swoją tożsamość, uzyskiwanie tokenów do innych zasobów chronionych za pomocą usługi AAD, takich jak usługi Azure Key Vault. Tokeny te reprezentują aplikacji dostęp do zasobów i nie są ustawiane określonego użytkownika aplikacji. 

> [!IMPORTANT]
> Może być konieczne skonfigurowanie zasób docelowy, aby zezwolić na dostęp z poziomu aplikacji. Na przykład jeśli w przypadku żądania tokenu służącego do usługi Key Vault, musisz upewnij się, że dodano zasady dostępu, które obejmują tożsamość swojej aplikacji. W przeciwnym razie wywołania do usługi Key Vault zostanie odrzucone, nawet jeśli zawierają one tokenu. Aby dowiedzieć się więcej o tym, jakie zasoby obsługują tokenów usługi Azure Active Directory, zobacz [usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Brak prostego protokołu REST w celu uzyskania tokenu w usłudze App Service i Azure Functions. W przypadku aplikacji .NET biblioteki Microsoft.Azure.Services.AppAuthentication udostępnia abstrakcję za pośrednictwem protokołu i obsługuje środowisko rozwoju lokalnego.

### <a name="asal"></a>Korzystanie z biblioteki Microsoft.Azure.Services.AppAuthentication dla platformy .NET

Dla aplikacji platformy .NET i funkcji najprostszy sposób pracy z tożsamości zarządzanej jest za pośrednictwem pakietu Microsoft.Azure.Services.AppAuthentication. Ta biblioteka będzie można również do testowania kodu lokalnie na komputerze deweloperskim, przy użyciu konta użytkownika z programu Visual Studio [wiersza polecenia platformy Azure](/cli/azure), lub zintegrowane uwierzytelnianie usługi Active Directory. Aby uzyskać więcej informacji na temat opcji lokalny rozwój za pomocą tej biblioteki, zobacz [Odwołanie Microsoft.Azure.Services.AppAuthentication]. W tej sekcji dowiesz się, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i innych niezbędnych pakietów NuGet do aplikacji. Poniższym przykładzie użyto także [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Dodaj następujący kod do aplikacji, modyfikując pod kątem odpowiedniego zasobu. Ten przykład przedstawia dwa sposoby pracy z usługą Azure Key Vault:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Aby dowiedzieć się więcej na temat Microsoft.Azure.Services.AppAuthentication i operacje, które udostępnia, zobacz [Odwołanie Microsoft.Azure.Services.AppAuthentication] i [usługi App Service i magazynu kluczy przy użyciu tożsamości usługi Zarządzanej platformy .NET Przykładowe](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Za pośrednictwem protokołu REST

Aplikacji za pomocą tożsamości zarządzanej ma dwie zmienne środowiskowe zdefiniowane:

- MSI_ENDPOINT — adres URL lokalnej usługi tokenu.
- MSI_SECRET — nagłówek, przydatna do zmniejszenia ataków (SSRF) sfałszowaniem żądania po stronie serwera. Wartość jest obracana przez platformę.

**MSI_ENDPOINT** jest lokalny adres URL, z którego aplikacja może żądać tokenów. Aby uzyskać token dla zasobu, należy żądanie HTTP GET do tego punktu końcowego, łącznie z następującymi parametrami:

> |Nazwa parametru|W|Opis|
> |-----|-----|-----|
> |resource|Zapytanie|Zasób usługi AAD identyfikator URI zasobu, dla którego mają być uzyskiwane tokenu. Może to być jeden z [usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) lub innych zasobów identyfikator URI.|
> |api-version|Zapytanie|Wersja token interfejsu API do użycia. "2017-09-01" jest obecnie jedyna obsługiwana wersja.|
> |secret|nagłówek|Wartość zmiennej środowiskowej MSI_SECRET. Tego pliku nagłówkowego jest używana w celu ograniczenia zagrożenia atakami sfałszowaniem (SSRF) żądania po stronie serwera.|
> |clientid|Zapytanie|(Opcjonalnie) Identyfikator tożsamości przypisanych przez użytkownika ma być używany. W przypadku pominięcia jest używana tożsamości przypisanych przez system.|

Pomyślne odpowiedź 200 OK zawiera treść JSON z następującymi właściwościami:

> |Nazwa właściwości|Opis|
> |-------------|----------|
> |access_token|Token żądanego dostępu. Wywoływania usługi sieci web można użyć tego tokenu do uwierzytelnienia w usłudze sieci web odbierania.|
> |expires_on|Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia tokenów buforowanych.|
> |resource|Identyfikator URI Identyfikatora aplikacji odbierającej usługi sieci web.|
> |token_type|Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest elementu nośnego. Aby uzyskać więcej informacji na temat tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: Użycie tokenu elementu nośnego (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Ta odpowiedź jest taka sama jak [odpowiedzi dla żądania tokenu dostępu do usługi AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> Zmienne środowiskowe są konfigurowane podczas procesu pierwszego uruchomienia, więc po włączeniu tożsamości zarządzanej aplikacji, może być konieczne ponowne uruchomienie aplikacji lub ponownego wdrażania jego kod przed `MSI_ENDPOINT` i `MSI_SECRET` są dostępne w kodzie.

### <a name="rest-protocol-examples"></a>Przykłady protokołu REST

Przykładowe żądanie może wyglądać następująco:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

I przykładowej odpowiedzi może wyglądać następująco:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Przykłady kodu

<a name="token-csharp"></a>Do wykonania tego żądania w języku C#:

```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```

> [!TIP]
> W językach .NET, można również użyć [Microsoft.Azure.Services.AppAuthentication](#asal) zamiast tworzenia to żądanie samodzielnie.

<a name="token-js"></a>W środowisku Node.JS:

```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>W programie PowerShell:

```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Usuwanie tożsamości

Można usunąć tożsamości przypisanych przez system, wyłączając funkcję przy użyciu portalu, programu PowerShell lub interfejsu wiersza polecenia w taki sam sposób, że został on utworzony. Oddzielnie można usunąć tożsamości przypisanych przez użytkownika. Aby usunąć wszystkie tożsamości za pomocą protokołu REST/ARM szablonu, można to zrobić przez ustawienie typu na "None":

```json
"identity": {
    "type": "None"
}
```

Usuwanie tożsamości przypisanych przez system w ten sposób spowoduje również usunięcie go z usługi AAD. Tożsamości przypisanych przez system zostaną również automatycznie usunięte z usługi AAD podczas usuwania zasobu aplikacji.

> [!NOTE]
> Brak ustawienia aplikacji, można ustawić, WEBSITE_DISABLE_MSI, która po prostu wyłącza lokalną usługę tokenu. Jednak pozostawia tożsamości w miejscu i narzędzi w dalszym ciągu będzie tożsamości zarządzanej jako "włączone" lub "włączone". W rezultacie Użyj tego ustawienia nie jest zalecane.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dostęp do bazy danych SQL przy użyciu tożsamości zarządzanej](app-service-web-tutorial-connect-msi.md)

[Odwołanie Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
