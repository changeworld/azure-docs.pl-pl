---
title: Zarządzane tożsamości
description: Dowiedz się, jak zarządzane tożsamości działają w Azure App Service i Azure Functions, jak skonfigurować tożsamość zarządzaną i wygenerować token dla zasobu zaplecza.
author: mattchenderson
ms.topic: article
ms.date: 10/30/2019
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 6fa8e560dc50859fc0501dde8109ddc7cbd596b8
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688629"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Jak używać tożsamości zarządzanych do App Service i Azure Functions

> [!Important] 
> Tożsamości zarządzane dla App Service i Azure Functions nie będą działać zgodnie z oczekiwaniami, jeśli Twoja aplikacja jest migrowana między subskrypcjami/dzierżawcami. Aplikacja będzie musiała uzyskać nową tożsamość, którą można wykonać, wyłączając i ponownie włączając funkcję. Zobacz [usuwanie tożsamości](#remove) poniżej. Zasoby podrzędne również muszą mieć zaktualizowane zasady dostępu do korzystania z nowej tożsamości.

W tym temacie pokazano, jak utworzyć zarządzaną tożsamość dla App Service i Azure Functions aplikacji oraz jak używać jej do uzyskiwania dostępu do innych zasobów. Tożsamość zarządzana z usługi Azure Active Directory umożliwia aplikacji łatwy dostęp do innych zasobów chronionych przez usługę AAD, takich jak usługa Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowizacji ani rotacji żadnych wpisów tajnych. Aby uzyskać więcej informacji o tożsamościach zarządzanych w usłudze AAD, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

Aplikacja może mieć przyznane dwa typy tożsamości: 
- **Tożsamość przypisana do systemu** jest powiązana z aplikacją i jest usuwana, jeśli aplikacja zostanie usunięta. Aplikacja może mieć tylko jedną tożsamość przypisaną do systemu.
- **Tożsamość przypisana przez użytkownika** to autonomiczny zasób platformy Azure, który można przypisać do aplikacji. Aplikacja może mieć wiele tożsamości przypisanych do użytkownika.

## <a name="adding-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu

Utworzenie aplikacji z tożsamością przypisaną przez system wymaga, aby w aplikacji była ustawiona dodatkowa właściwość.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, musisz najpierw utworzyć aplikację w zwykły sposób, a następnie włączyć tę funkcję.

1. Utwórz aplikację w portalu, jak zwykle. Przejdź do niej w portalu.

2. W przypadku korzystania z aplikacji funkcji przejdź do **opcji funkcje platformy**. W przypadku innych typów aplikacji przewiń w dół do grupy **ustawień** w okienku nawigacji po lewej stronie.

3. Wybierz pozycję **tożsamość**.

4. W ramach karty **przypisanej do systemu** Przełącz pozycję **stan** na wartość **włączone**. Kliknij przycisk **Save** (Zapisz).

    ![Tożsamość zarządzana w App Service](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć polecenia `az webapp identity assign` w stosunku do istniejącej aplikacji. Dostępne są trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj go" znajdującego się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 lub nowsza), jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia. 

Poniższe kroki przeprowadzą Cię przez proces tworzenia aplikacji sieci Web i przypisywania jej tożsamości przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć aplikację:

    ```azurecli-interactive
    az login
    ```
2. Utwórz aplikację sieci Web przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów użycia interfejsu wiersza polecenia w App Service, zobacz [App Service przykłady interfejsu wiersza polecenia](../app-service/samples-cli.md):

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

Poniższe kroki przeprowadzą Cię przez proces tworzenia aplikacji sieci Web i przypisywania jej tożsamości przy użyciu Azure PowerShell:

1. W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

2. Utwórz aplikację sieci Web przy użyciu Azure PowerShell. Aby uzyskać więcej przykładów użycia Azure PowerShell z App Service, zobacz [App Service przykładów programu PowerShell](../app-service/samples-powershell.md):

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

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z szablonu Azure Resource Manager

Szablon Azure Resource Manager może służyć do automatyzowania wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej o wdrażaniu do App Service i funkcji, zobacz [Automatyzowanie wdrażania zasobów w App Service](../app-service/deploy-complex-application-predictably.md) i [Automatyzowanie wdrażania zasobów w Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Każdy zasób typu `Microsoft.Web/sites` można utworzyć za pomocą tożsamości, dołączając następującą właściwość w definicji zasobu:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Aplikacja może mieć w tym samym czasie zarówno tożsamość przypisana do systemu, jak i przypisanych do użytkownika. W takim przypadku Właściwość `type` będzie `SystemAssigned,UserAssigned`

Dodanie typu przypisanego do systemu informuje platformę Azure, aby utworzył tożsamość aplikacji i zarządzać nią.

Na przykład aplikacja sieci Web może wyglądać następująco:
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

Gdy witryna zostanie utworzona, ma następujące dodatkowe właściwości:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Gdzie `<TENANTID>` i `<PRINCIPALID>` są zastępowane identyfikatorami GUID. Właściwość tenantId identyfikuje dzierżawcę usługi AAD, do której należy tożsamość. PrincipalId jest unikatowym identyfikatorem nowej tożsamości aplikacji. W usłudze AAD nazwa główna usługi ma taką samą nazwę, która została nadana App Service lub Azure Functions wystąpieniem.


## <a name="adding-a-user-assigned-identity"></a>Dodawanie tożsamości przypisanej do użytkownika

Utworzenie aplikacji z tożsamością przypisaną przez użytkownika wymaga utworzenia tożsamości, a następnie dodania jej identyfikatora zasobu do konfiguracji aplikacji.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Najpierw należy utworzyć zasób tożsamości przypisany przez użytkownika.

1. Utwórz zasób tożsamości zarządzanej przez użytkownika zgodnie z [tymi instrukcjami](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Utwórz aplikację w portalu, jak zwykle. Przejdź do niej w portalu.

3. W przypadku korzystania z aplikacji funkcji przejdź do **opcji funkcje platformy**. W przypadku innych typów aplikacji przewiń w dół do grupy **ustawień** w okienku nawigacji po lewej stronie.

4. Wybierz pozycję **tożsamość**.

5. Na karcie **przypisane przez użytkownika** kliknij przycisk **Dodaj**.

6. Wyszukaj utworzoną wcześniej tożsamość i wybierz ją. Kliknij pozycję **Add** (Dodaj).

    ![Tożsamość zarządzana w App Service](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z szablonu Azure Resource Manager

Szablon Azure Resource Manager może służyć do automatyzowania wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej o wdrażaniu do App Service i funkcji, zobacz [Automatyzowanie wdrażania zasobów w App Service](../app-service/deploy-complex-application-predictably.md) i [Automatyzowanie wdrażania zasobów w Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Każdy zasób typu `Microsoft.Web/sites` można utworzyć za pomocą tożsamości, dołączając następujący blok w definicji zasobu, zastępując `<RESOURCEID>` IDENTYFIKATORem zasobu żądanej tożsamości:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Aplikacja może mieć w tym samym czasie zarówno tożsamość przypisana do systemu, jak i przypisanych do użytkownika. W takim przypadku Właściwość `type` będzie `SystemAssigned,UserAssigned`

Dodanie typu przypisanego przez użytkownika oznacza, że platforma Azure będzie używać tożsamości przypisanej do użytkownika określonej dla danej aplikacji.

Na przykład aplikacja sieci Web może wyglądać następująco:
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

Gdy witryna zostanie utworzona, ma następujące dodatkowe właściwości:
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

Gdzie `<PRINCIPALID>` i `<CLIENTID>` są zastępowane identyfikatorami GUID. PrincipalId jest unikatowym identyfikatorem tożsamości, która jest używana do administrowania usługą AAD. ClientId jest unikatowym identyfikatorem nowej tożsamości aplikacji, która jest używana do określania tożsamości, która ma być używana podczas wywołań środowiska uruchomieniowego.


## <a name="obtaining-tokens-for-azure-resources"></a>Uzyskiwanie tokenów dla zasobów platformy Azure

Aplikacja może używać swojej tożsamości do uzyskiwania tokenów do innych zasobów chronionych przez usługi AAD, takich jak Azure Key Vault. Te tokeny reprezentują aplikację, która uzyskują dostęp do zasobu, a nie do określonego użytkownika aplikacji. 

> [!IMPORTANT]
> Może być konieczne skonfigurowanie zasobu docelowego, aby zezwalać na dostęp z poziomu aplikacji. Na przykład, Jeśli zażądasz tokenu do Key Vault, musisz się upewnić, że dodano zasady dostępu zawierające tożsamość aplikacji. W przeciwnym razie wywołania Key Vault będą odrzucane, nawet jeśli zawierają token. Aby dowiedzieć się więcej o tym, które zasoby obsługują tokeny Azure Active Directory, zobacz [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Istnieje prosty protokół REST umożliwiający uzyskanie tokenu w App Service i Azure Functions. Można go używać dla wszystkich aplikacji i języków. W przypadku niektórych platform .NET i Java zestaw Azure SDK zapewnia streszczenie za pośrednictwem tego protokołu i ułatwia lokalne środowisko programistyczne.

### <a name="using-the-rest-protocol"></a>Korzystanie z protokołu REST

Aplikacja z zarządzaną tożsamością ma zdefiniowane dwie zmienne środowiskowe:

- MSI_ENDPOINT — adres URL usługi tokenu lokalnego.
- MSI_SECRET — nagłówek służący do ograniczania ataków z wykorzystaniem fałszerstwa żądań po stronie serwera (SSRF). Wartość jest obracana przez platformę.

**MSI_ENDPOINT** jest lokalnym adresem URL, z którego aplikacja może żądać tokenów. Aby uzyskać token dla zasobu, wprowadź żądanie HTTP GET do tego punktu końcowego, w tym następujące parametry:

> |Nazwa parametru|Podczas|Opis|
> |-----|-----|-----|
> |zasoby|Zapytanie|Identyfikator URI zasobu usługi AAD zasobu, dla którego ma zostać uzyskany token. Może to być jedna z [usług platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) lub dowolny inny identyfikator URI zasobu.|
> |wersja interfejsu API|Zapytanie|Wersja interfejsu API tokenu, który ma być używany. "2017-09-01" jest obecnie jedyną obsługiwaną wersją.|
> |wpis tajny|Nagłówek|Wartość zmiennej środowiskowej MSI_SECRET. Ten nagłówek jest używany, aby pomóc w ograniczeniu ataków SSRF (po stronie serwera).|
> |clientid|Zapytanie|(Opcjonalnie, chyba że zostanie przypisany przez użytkownika) Identyfikator tożsamości przypisanej do użytkownika, który ma być używany. W przypadku pominięcia zostanie użyta tożsamość przypisana do systemu.|

> [!IMPORTANT]
> Jeśli próbujesz uzyskać tokeny dla tożsamości przypisanych przez użytkownika, musisz uwzględnić Właściwość `clientid`. W przeciwnym razie usługa tokenów podejmie próbę uzyskania tokenu dla tożsamości przypisanej do systemu, która może być lub nie istnieje.

Pomyślne odpowiedź 200 OK zawiera treść JSON o następujących właściwościach:

> |Nazwa właściwości|Opis|
> |-------------|----------|
> |access_token|Żądany token dostępu. Wywoływana usługa sieci Web może używać tego tokenu do uwierzytelniania w usłudze sieci Web otrzymującej.|
> |expires_on|Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0: 0Z UTC do czasu wygaśnięcia. Ta wartość służy do określenia okresu istnienia buforowanych tokenów.|
> |zasoby|Identyfikator URI identyfikatora aplikacji dla usługi sieci Web odbiorczej.|
> |token_type|Wskazuje wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak. Aby uzyskać więcej informacji o tokenach okaziciela, zobacz [Framework uwierzytelniania OAuth 2,0: użycie tokenu okaziciela (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Ta odpowiedź jest taka sama jak [odpowiedź na żądanie tokenu dostępu między usługą AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> Zmienne środowiskowe są konfigurowane podczas pierwszego uruchomienia procesu, więc po włączeniu tożsamości zarządzanej dla aplikacji może być konieczne ponowne uruchomienie aplikacji lub wdrożenie jej kodu przed `MSI_ENDPOINT` i `MSI_SECRET` są dostępne dla kodu.

### <a name="rest-protocol-examples"></a>Przykłady protokołu REST

Przykładowe żądanie może wyglądać następująco:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

I Przykładowa odpowiedź może wyglądać następująco:

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

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!TIP]
> W przypadku języków .NET można także samodzielnie użyć [Microsoft. Azure. Services. AppAuthentication](#asal) zamiast tego żądania.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2017-09-01", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource));
    request.Headers.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await _client.SendAsync(request);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=2017-09-01`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version=2017-09-01"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="asal"></a>Korzystanie z biblioteki Microsoft. Azure. Services. AppAuthentication dla platformy .NET

W przypadku aplikacji i funkcji platformy .NET Najprostszym sposobem pracy z zarządzaną tożsamością jest pakiet Microsoft. Azure. Services. AppAuthentication. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze deweloperskim przy użyciu konta użytkownika z programu Visual Studio, [interfejsu wiersza polecenia platformy Azure](/cli/azure)lub zintegrowanego uwierzytelniania Active Directory. Aby uzyskać więcej informacji na temat lokalnych opcji tworzenia w tej bibliotece, zobacz [Dokumentacja Microsoft. Azure. Services. AppAuthentication]. W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do [programu Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) oraz innych niezbędnych pakietów NuGet do aplikacji. Poniższy przykład używa także [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)kluczy.

2. Dodaj następujący kod do aplikacji, modyfikując go jako docelowy dla poprawnego zasobu. Ten przykład przedstawia dwa sposoby pracy z Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Aby dowiedzieć się więcej na temat Microsoft. Azure. Services. AppAuthentication i wykonywanych przez niego operacji, zobacz artykuł [Dokumentacja Microsoft. Azure. Services. AppAuthentication] , a [App Service i Magazyn kluczy za pomocą przykładu MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Korzystanie z zestawu Azure SDK dla języka Java

W przypadku aplikacji i funkcji języka Java Najprostszym sposobem pracy z zarządzaną tożsamością jest [zestaw Azure SDK dla języka Java](https://github.com/Azure/azure-sdk-for-java). W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołanie do [biblioteki zestawu Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure). W przypadku projektów Maven można dodać ten fragment kodu do sekcji `dependencies` pliku pliku pom projektu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Użyj obiektu `AppServiceMSICredentials` na potrzeby uwierzytelniania. Ten przykład pokazuje, jak można użyć tego mechanizmu do pracy z Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove"></a>Usuwanie tożsamości

Tożsamość przypisana przez system można usunąć, wyłączając funkcję przy użyciu portalu, programu PowerShell lub interfejsu wiersza polecenia w taki sam sposób, jak został utworzony. Tożsamości przypisane do użytkownika można usuwać pojedynczo. Aby usunąć wszystkie tożsamości, w protokole szablonu REST/ARM można to zrobić, ustawiając typ na "none" (brak):

```json
"identity": {
    "type": "None"
}
```

Usunięcie tożsamości przypisanej do systemu w ten sposób spowoduje również usunięcie jej z usługi AAD. Tożsamości przypisane do systemu są również automatycznie usuwane z usługi AAD po usunięciu zasobu aplikacji.

> [!NOTE]
> Istnieje również ustawienie aplikacji, które można ustawić, WEBSITE_DISABLE_MSI, co spowoduje jedynie wyłączenie usługi tokenów lokalnych. Jednak opuszcza tożsamość, a funkcja narzędzi nadal będzie wyświetlać tożsamość zarządzaną jako "on" lub "Enabled". W związku z tym nie zaleca się używania tego ustawienia.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Bezpieczny dostęp SQL Database przy użyciu tożsamości zarządzanej](app-service-web-tutorial-connect-msi.md)

[Dokumentacja Microsoft. Azure. Services. AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
