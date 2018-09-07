---
title: Zarządzane tożsamości usługi App Service i Azure Functions | Dokumentacja firmy Microsoft
description: Koncepcyjny Przewodnik instalacji i odwołania do obsługi tożsamości usługi zarządzanej w usłudze Azure App Service i Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/25/2018
ms.author: mahender
ms.openlocfilehash: c7a819f987de41ba7705d21bb6de95475cd3f9c8
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027190"
---
# <a name="how-to-use-azure-managed-service-identity-in-app-service-and-azure-functions"></a>Jak używać usługi Azure tożsamości usługi zarządzanej w usłudze App Service i Azure Functions

> [!NOTE] 
> Usługa App Service w systemie Linux a Web App for Containers aktualnie nie obsługują tożsamości usługi zarządzanej.

> [!Important] 
> Tożsamość usługi zarządzanej dla usługi App Service i Azure Functions nie będzie działać zgodnie z oczekiwaniami, jeśli aplikacja jest migrowana subskrypcji/dzierżawy. Aplikacja musi uzyskać nową tożsamość może odbywać się przez wyłączenie i ponowne włączenie tej funkcji. Zobacz [usuwanie tożsamości](#remove) poniżej. Zasoby podrzędne musisz również mieć zasady dostępu zaktualizowana w celu używania nowej tożsamości.

W tym temacie dowiesz się, jak utworzyć tożsamość zarządzaną aplikację, dla aplikacji usługi App Service i Azure Functions i jak z niej korzystać, aby uzyskać dostęp do innych zasobów. Tożsamości usługi zarządzanej w usłudze Azure Active Directory umożliwia aplikacji łatwo uzyskiwać dostęp do innych zasobów chronionych przez usługi AAD, takich jak usługi Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure i nie wymaga obsługi administracyjnej ani Obróć jakichkolwiek kluczy tajnych. Aby uzyskać więcej informacji o tożsamości usługi zarządzanej, zobacz [Przegląd tożsamości usługi zarządzanej](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="creating-an-app-with-an-identity"></a>Tworzenie aplikacji przy użyciu tożsamości

Tworzenie aplikacji przy użyciu tożsamości wymaga dodatkowych właściwości, należy ustawić na aplikację.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamości usługi zarządzanej w portalu, możesz najpierw utworzyć aplikację w zwykły i włączysz tę funkcję.

1. Utwórz aplikację w portalu, tak jak zwykle. Przejdź do niego w portalu.

2. Jeśli używasz aplikacji funkcji, przejdź do **funkcje platformy**. Dla innych typów aplikacji, przewiń w dół do **ustawienia** grupy w obszarze nawigacji po lewej stronie.

3. Wybierz **tożsamości usługi zarządzanej**.

4. Przełącznik **rejestrowanie w usłudze Azure Active Directory** do **na**. Kliknij pozycję **Zapisz**.

![Zarządzane tożsamości usługi w usłudze App Service](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość usługi zarządzanej, przy użyciu wiersza polecenia platformy Azure, musisz użyć `az webapp identity assign` polecenia w odniesieniu do istniejących aplikacji. Masz trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [usługi Azure Cloud Shell](../cloud-shell/overview.md) w witrynie Azure portal.
- Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 

Poniższe kroki przeprowadzi Cię przez proces tworzenia aplikacji sieci web i przypisywanie jej tożsamości przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta, który jest skojarzony z subskrypcją platformy Azure w ramach której chcesz wdrożyć aplikację:

    ```azurecli-interactive
    az login
    ```
2. Tworzenie aplikacji internetowej przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów użycia interfejsu wiersza polecenia przy użyciu usługi App Service, zobacz [przykłady interfejsu wiersza polecenia aplikacji usługi](../app-service/app-service-cli-samples.md):

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

Poniższe kroki przeprowadzi Cię przez proces tworzenia aplikacji sieci web i przypisywanie jej tożsamości przy użyciu programu Azure PowerShell:

1. W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

2. Tworzenie aplikacji internetowej przy użyciu programu Azure PowerShell. Aby uzyskać więcej przykładów programu Azure PowerShell za pomocą usługi App Service, zobacz [przykłady programu PowerShell usługi aplikacji](../app-service/app-service-powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Uruchom `identity assign` polecenie, aby utworzyć tożsamość dla tej aplikacji:

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Przy użyciu szablonu usługi Azure Resource Manager

Szablon usługi Azure Resource Manager umożliwia automatyzowanie wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej na temat wdrażania usługi App Service i Functions, zobacz [Automatyzowanie wdrażania zasobów w usłudze App Service](../app-service/app-service-deploy-complex-application-predictably.md) i [Automatyzowanie wdrażania zasobów w usłudze Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Dowolny zasób typu `Microsoft.Web/sites` można utworzyć przy użyciu tożsamości, umieszczając następujące właściwości w definicji zasobu:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Oznacza to, Azure do tworzenia i zarządzania tożsamościami dla aplikacji.

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
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Gdzie `<TENANTID>` i `<PRINCIPALID>` są zastępowane identyfikatorów GUID. Właściwość tenantId identyfikuje dzierżawy usługi AAD, jakie należy tożsamości. Identyfikator principalId to unikatowy identyfikator dla nowej tożsamości aplikacji. W ramach usługi AAD nazwa główna usługi ma taką samą nazwę, która udostępniła do swojego wystąpienia usługi App Service lub Azure Functions.

## <a name="obtaining-tokens-for-azure-resources"></a>Uzyskiwanie tokenów dla zasobów platformy Azure

Aplikacja może używać swoją tożsamość, uzyskiwanie tokenów do innych zasobów chronionych za pomocą usługi AAD, takich jak usługi Azure Key Vault. Tokeny te reprezentują aplikacji dostęp do zasobów i nie są ustawiane określonego użytkownika aplikacji. 

> [!IMPORTANT]
> Może być konieczne skonfigurowanie zasób docelowy, aby zezwolić na dostęp z poziomu aplikacji. Na przykład jeśli w przypadku żądania tokenu służącego do usługi Key Vault, musisz upewnij się, że dodano zasady dostępu, które obejmują tożsamość swojej aplikacji. W przeciwnym razie wywołania do usługi Key Vault zostanie odrzucone, nawet jeśli zawierają one tokenu. Aby dowiedzieć się więcej o tym, jakie zasoby obsługują tokenów tożsamości usługi zarządzanej, zobacz [usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication).

Brak prostego protokołu REST w celu uzyskania tokenu w usłudze App Service i Azure Functions. W przypadku aplikacji .NET biblioteki Microsoft.Azure.Services.AppAuthentication udostępnia abstrakcję za pośrednictwem protokołu i obsługuje środowisko rozwoju lokalnego.

### <a name="asal"></a>Korzystanie z biblioteki Microsoft.Azure.Services.AppAuthentication dla platformy .NET

Dla aplikacji platformy .NET i funkcji najprostszy sposób pracy z tożsamości usługi zarządzanej jest za pośrednictwem pakietu Microsoft.Azure.Services.AppAuthentication. Ta biblioteka będzie można również do testowania kodu lokalnie na komputerze deweloperskim, przy użyciu konta użytkownika z programu Visual Studio [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), lub zintegrowane uwierzytelnianie usługi Active Directory. Aby uzyskać więcej informacji na temat opcji lokalny rozwój za pomocą tej biblioteki, zobacz [Odwołanie Microsoft.Azure.Services.AppAuthentication]. W tej sekcji dowiesz się, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) pakiety NuGet do aplikacji.

2.  Dodaj następujący kod do aplikacji:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Aby dowiedzieć się więcej na temat Microsoft.Azure.Services.AppAuthentication i operacje, które udostępnia, zobacz [Odwołanie Microsoft.Azure.Services.AppAuthentication] i [usługi App Service i magazynu kluczy przy użyciu tożsamości usługi Zarządzanej platformy .NET Przykładowe](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Za pośrednictwem protokołu REST

Aplikację przy użyciu tożsamości usługi zarządzanej ma dwie zmienne środowiskowe zdefiniowane:
- MSI_ENDPOINT
- MSI_SECRET

**MSI_ENDPOINT** jest lokalny adres URL, z którego aplikacja może żądać tokenów. Aby uzyskać token dla zasobu, należy żądanie HTTP GET do tego punktu końcowego, łącznie z następującymi parametrami:

> [!div class="mx-tdBreakAll"]
> |Nazwa parametru|W|Opis|
> |-----|-----|-----|
> |zasób|Zapytanie|Zasób usługi AAD identyfikator URI zasobu, dla którego mają być uzyskiwane tokenu.|
> |wersja interfejsu API|Zapytanie|Wersja token interfejsu API do użycia. "2017-09-01" jest obecnie jedyna obsługiwana wersja.|
> |wpis tajny|Nagłówek|Wartość zmiennej środowiskowej MSI_SECRET.|


Pomyślne odpowiedź 200 OK zawiera treść JSON z następującymi właściwościami:

> [!div class="mx-tdBreakAll"]
> |Nazwa właściwości|Opis|
> |-------------|----------|
> |access_token|Token żądanego dostępu. Wywoływania usługi sieci web można użyć tego tokenu do uwierzytelnienia w usłudze sieci web odbierania.|
> |expires_on|Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia tokenów buforowanych.|
> |zasób|Identyfikator URI Identyfikatora aplikacji odbierającej usługi sieci web.|
> |token_type|Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest elementu nośnego. Aby uzyskać więcej informacji na temat tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: użycie tokenu elementu nośnego (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).|


Ta odpowiedź jest taka sama jak [odpowiedzi dla żądania tokenu dostępu do usługi AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE] 
> Zmienne środowiskowe są konfigurowane podczas procesu pierwszego uruchomienia, więc po włączeniu tożsamości usługi zarządzanej aplikacji może być konieczne ponowne uruchomienie aplikacji lub ponownego wdrażania jego kod przed `MSI_ENDPOINT` i `MSI_SECRET` są dostępne w kodzie.

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
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
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

Tożsamość można usunąć, wyłączając funkcję przy użyciu portalu, programu PowerShell lub interfejsu wiersza polecenia w taki sam sposób, że został on utworzony. W protokole szablonu REST/ARM można to zrobić przez ustawienie typu na "None":

```json
"identity": {
    "type": "None"
}    
```

Usuwanie tożsamości w ten sposób spowoduje również usunięcie jednostki z usługi AAD. Tożsamości przypisanych przez system zostaną automatycznie usunięte z usługi AAD, gdy zasób aplikacji zostanie usunięty.

> [!NOTE] 
> Brak ustawienia aplikacji, można ustawić, WEBSITE_DISABLE_MSI, która po prostu wyłącza lokalną usługę tokenu. Jednak pozostawia tożsamości w miejscu i narzędzi w dalszym ciągu będzie MSI jako "włączone" lub "włączone". W rezultacie Użyj tego ustawienia nie jest zalecana.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zabezpieczony dostęp do usługi SQL Database przy użyciu tożsamości usługi zarządzanej](app-service-web-tutorial-connect-msi.md)

[Odwołanie Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
