---
title: Tożsamość usługi App Service i usługę Azure Functions zarządzanej | Dokumentacja firmy Microsoft
description: Koncepcyjny przewodnik odwołanie i instalacji do obsługi tożsamości usługi zarządzane w usłudze Azure App Service i usługi Azure Functions
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
ms.openlocfilehash: e6aa0d477f94cd5ab087beface65e3a28e5094f5
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936976"
---
# <a name="how-to-use-azure-managed-service-identity-in-app-service-and-azure-functions"></a>Jak używać Azure zarządzanych tożsamość usługi App Service i usługi Azure Functions

> [!NOTE] 
> Usługa aplikacji w systemie Linux i aplikacji sieci Web dla kontenerów aktualnie nie obsługują zarządzane tożsamości usługi.

> [!Important] 
> Zarządzane tożsamości usługi dla aplikacji usługi i usługi Azure Functions nie będzie działać zgodnie z oczekiwaniami, jeśli aplikacja migrowania między subskrypcjami i dzierżawców. Aplikacja musi uzyskać nową tożsamość, można to zrobić przez wyłączenie i ponowne włączenie funkcji. Zobacz [usuwanie tożsamości](#remove) poniżej. Zasoby podrzędne zostaną również musi być zaktualizowane do używania nowej tożsamości zasady dostępu.

W tym temacie przedstawiono sposób utworzenia tożsamości zarządzanych aplikacji usługi aplikacji i usługi Azure Functions aplikacje i jak z niego korzystać, aby uzyskać dostęp do innych zasobów. Tożsamość usługi zarządzanej z usługi Azure Active Directory umożliwia aplikacji łatwo uzyskiwać dostęp do innych chronionych AAD zasoby, takie jak usługi Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure i nie trzeba zapewniać ani obrócić żadnych kluczy tajnych. Więcej informacji o zarządzanych tożsamość usługi, zobacz [omówienie zarządzane tożsamość usługi](../active-directory/managed-service-identity/overview.md).

## <a name="creating-an-app-with-an-identity"></a>Tworzenie aplikacji przy użyciu tożsamości

Tworzenie aplikacji przy użyciu tożsamości wymaga dodatkowych właściwości można ustawić w aplikacji.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość usługi zarządzanej w portalu, zostanie najpierw utworzyć aplikację w zwykły i włączysz tę funkcję.

1. Utwórz aplikację w portalu w zwykły sposób. Przejdź do niego w portalu.

2. Jeśli za pomocą aplikacji funkcji, przejdź do **funkcji platformy**. Dla innych typów aplikacji, przewiń w dół do **ustawienia** grupy na lewym pasku nawigacyjnym.

3. Wybierz **tożsamość usługi zarządzanej**.

4. Przełącznik **zarejestrować w usłudze Azure Active Directory** do **na**. Kliknij pozycję **Zapisz**.

![Zarządzane tożsamości usługi w usłudze App Service](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość usługi zarządzanej przy użyciu wiersza polecenia platformy Azure, musisz użyć `az webapp identity assign` polecenia w odniesieniu do istniejących aplikacji. Są trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [powłoki chmury Azure](../cloud-shell/overview.md) z portalu Azure.
- Użyć osadzonego powłoki chmury Azure za pośrednictwem "Spróbuj on" przycisk, znajdujący się w prawym górnym rogu każdej blok kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 lub nowsza), jeśli wolisz korzystać z konsoli lokalnej interfejsu wiersza polecenia. 

Poniższe kroki przeprowadzi użytkownika przez proces tworzenia aplikacji sieci web i przypisywania jej tożsamość za pomocą interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, w której chcesz wdrożyć aplikację:

    ```azurecli-interactive
    az login
    ```
2. Utwórz aplikację sieci web przy użyciu interfejsu wiersza polecenia. Więcej przykładów dotyczących używania interfejsu wiersza polecenia z usługi aplikacji, zobacz [przykłady interfejsu wiersza polecenia aplikacji usługi](../app-service/app-service-cli-samples.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Uruchom `identity assign` polecenie w celu utworzenia tożsamości dla tej aplikacji:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Poniższe kroki przeprowadzi użytkownika przez proces tworzenia aplikacji sieci web i przypisywania jej tożsamość przy użyciu programu Azure PowerShell:

1. W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

2. Utwórz aplikację sieci web przy użyciu programu Azure PowerShell. Więcej przykładów dotyczących sposobu korzystania z usługi aplikacji Azure PowerShell, zobacz [przykłady środowiska PowerShell usługi aplikacji](../app-service/app-service-powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Uruchom `identity assign` polecenie w celu utworzenia tożsamości dla tej aplikacji:

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Przy użyciu szablonu usługi Azure Resource Manager

Szablon usługi Azure Resource Manager może służyć do automatyzowania wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej o wdrażaniu usługi aplikacji i funkcji, zobacz [automatyzacji wdrażania zasobów w usłudze App Service](../app-service/app-service-deploy-complex-application-predictably.md) i [automatyzacji wdrażania zasobów w usługi Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Dowolnego zasobu typu `Microsoft.Web/sites` można tworzyć przy użyciu tożsamości przez uwzględnienie następujących właściwości w definicji zasobu:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Ta wartość informuje Azure do tworzenia i zarządzania tożsamościami dla aplikacji.

Na przykład aplikacja sieci web może wyglądać następujące czynności:
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

Po utworzeniu witryny ma następujące dodatkowe właściwości:
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Gdzie `<TENANTID>` i `<PRINCIPALID>` są zamieniane na GUID. Właściwość identyfikatora dzierżawcy identyfikuje dzierżawę usługi AAD, jakie należy tożsamości. PrincipalId to unikatowy identyfikator dla nowej tożsamości aplikacji. W usłudze AAD nazwy głównej usługi ma taką samą nazwę, który spowodował wystąpienie usługi aplikacji lub usługi Azure Functions.

## <a name="obtaining-tokens-for-azure-resources"></a>Uzyskiwanie tokenów dla zasobów platformy Azure

Aplikacja może być uzyskiwać tokeny do innych zasobów chronionych przez usługi AAD, takich jak usługi Azure Key Vault jego tożsamość. Tokeny te stanowią aplikacji dostęp do zasobów i nie każdy użytkownik określonych aplikacji. 

> [!IMPORTANT]
> Może być konieczne skonfigurowanie zasobu docelowego, aby zezwolić na dostęp z poziomu aplikacji. Na przykład jeśli żądanie jest token do magazynu kluczy, musisz upewnij się, że dodano zasad dostępu, która obejmuje tożsamości aplikacji. W przeciwnym razie wywołaniami magazynu kluczy zostanie odrzucone, nawet jeśli zawierają one tokenu. Aby dowiedzieć się więcej o zasobach, które obsługuje tokenów tożsamości usługi zarządzane, zobacz [uwierzytelniania pomocy technicznej usługi Azure AD z usług Azure](../active-directory/managed-service-identity/overview.md#which-azure-services-support-managed-service-identity).

Brak prostego protokołu REST do uzyskania tokenu usługi aplikacji i usługi Azure Functions. W przypadku aplikacji .NET biblioteki Microsoft.Azure.Services.AppAuthentication udostępnia abstrakcję za pośrednictwem protokołu i obsługuje środowisko rozwoju lokalnego.

### <a name="asal"></a>Za pomocą biblioteki Microsoft.Azure.Services.AppAuthentication dla platformy .NET

Dla aplikacji .NET oraz funkcji Najprostszym sposobem pracy za pomocą tożsamości zarządzanych usług jest za pomocą pakietu Microsoft.Azure.Services.AppAuthentication. Ta biblioteka będzie pozwalają również do testowania kodu lokalnie na komputerze deweloperskim, przy użyciu konta użytkownika z programu Visual Studio, [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), lub zintegrowane uwierzytelnianie usługi Active Directory. Aby uzyskać więcej informacji na temat opcji wdrożenia lokalnego z tej biblioteki, zobacz [Odwołanie Microsoft.Azure.Services.AppAuthentication]. W tej sekcji przedstawiono, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) pakietów NuGet do aplikacji.

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

Aby dowiedzieć się więcej na temat Microsoft.Azure.Services.AppAuthentication i operacje, takie, zobacz [Odwołanie Microsoft.Azure.Services.AppAuthentication] i [usługi aplikacji i KeyVault z platformą .NET MSI Przykładowe](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Przy użyciu protokołu REST

Za pomocą tożsamości zarządzanych usług aplikacji ma dwie zmienne środowiskowe zdefiniowane:
- MSI_ENDPOINT
- MSI_SECRET

**MSI_ENDPOINT** jest lokalny adres URL, z którego aplikacja może zażądać tokenów. Aby uzyskać token dla zasobu, należy żądanie HTTP GET do tego punktu końcowego, łącznie z następującymi parametrami:

> [!div class="mx-tdBreakAll"]
> |Nazwa parametru|W|Opis|
> |-----|-----|-----|
> |zasób|Zapytanie|Identyfikator URI zasobu zasobu usługi AAD, dla którego mają być uzyskiwane tokenu.|
> |wersja interfejsu API|Zapytanie|Wersja interfejsu API token do użycia. "2017-09-01" jest obecnie jedyna obsługiwana wersja.|
> |wpis tajny|Nagłówek|Wartość zmiennej środowiskowej MSI_SECRET.|


Pomyślne odpowiedź 200 OK zawiera treść JSON z następującymi właściwościami:

> [!div class="mx-tdBreakAll"]
> |Nazwa właściwości|Opis|
> |-------------|----------|
> |access_token|Żądany dostęp token. Wywołanie usługi sieci web umożliwia ten token uwierzytelniania przyjęcia usługi sieci web.|
> |expires_on|Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund z rokiem 1970-01-01T0:0:0Z UTC czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia pamięci podręcznej tokenów.|
> |zasób|Identyfikator URI aplikacji odbierającej usługi sieci web.|
> |token_type|Wskazuje wartość typ tokenu. Jedynym typem, który obsługuje usługę Azure AD jest elementu nośnego. Aby uzyskać więcej informacji dotyczących tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: użycie tokenu elementu nośnego (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).|


Ta odpowiedź jest taka sama jak [odpowiedzi na żądanie tokenu dostępu do usługi AAD](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#service-to-service-access-token-response).

> [!NOTE] 
> Zmienne środowiskowe są ustawiane podczas pierwszego uruchomienia procesu, więc po włączeniu zarządzane tożsamość usługi aplikacji może być konieczne ponowne uruchamianie aplikacji lub wdrożenie przed jego kod `MSI_ENDPOINT` i `MSI_SECRET` są dostępne w kodzie.

### <a name="rest-protocol-examples"></a>Przykłady protokołu REST
Przykładowe żądanie może wyglądać następująco:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
I przykładowa odpowiedź może wyglądać następująco:
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
Aby to żądanie w języku C#:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> W przypadku języków .NET umożliwia także [Microsoft.Azure.Services.AppAuthentication](#asal) zamiast obsługuje tworzenie to żądanie samodzielnie.

W środowisku Node.JS:
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

W programie PowerShell:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Usuwanie tożsamości

Tożsamość może zostać usunięty przez wyłączenie funkcji za pomocą portalu, programu PowerShell lub interfejsu wiersza polecenia w taki sam sposób, że został on utworzony. W protokole szablonu REST/ARM można to zrobić przez ustawienie typu na "Brak":

```json
"identity": {
    "type": "None"
}    
```

Usuwanie tożsamości w ten sposób spowoduje również usunięcie podmiotu zabezpieczeń z usługi AAD. Przypisana przez system tożsamości są automatycznie usuwane z usługi AAD po usunięciu zasobów aplikacji.

> [!NOTE] 
> Brak ustawienia aplikacji, które można ustawić, WEBSITE_DISABLE_MSI, która po prostu wyłącza lokalną usługę tokenu. Jednak pozostawia tożsamość w miejscu i narzędzi w dalszym ciągu będzie MSI jako "on" lub "enabled". W związku z tym nie recommmended jest użycie tego ustawienia.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zabezpieczony dostęp do usługi SQL Database przy użyciu tożsamości usługi zarządzanej](app-service-web-tutorial-connect-msi.md)

[Odwołanie Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
