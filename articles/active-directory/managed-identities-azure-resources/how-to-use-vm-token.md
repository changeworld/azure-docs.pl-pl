---
title: Jak używać zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej w celu pobrania tokenu dostępu
description: Krok po kroku instrukcje i przykłady dotyczące korzystania z zarządzanych tożsamości dla zasobów platformy Azure na maszynach wirtualnych w celu uzyskania tokenu dostępu OAuth.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: abdeb7ce5327db57b8a6ae48fdd8d8c0c81879a7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59258916"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Jak używać zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure w celu pobrania tokenu dostępu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

Ten artykuł zawiera różne przykłady kodu i skryptów do tokenu, a także wskazówki dotyczące ważnych tematów, takich jak obsługa wygaśnięcia tokenu i błędów HTTP. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz użyć przykładów programu Azure PowerShell w tym artykule, należy zainstalować najnowszą wersję [programu Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Wszystkie próbki kodu lub skryptu w tym artykule przyjęto założenie, klient jest uruchomiony na maszynie wirtualnej z zarządzanych tożsamości dla zasobów platformy Azure. Funkcja maszyny wirtualnej "Połącz" w witrynie Azure portal na połączenie zdalne z maszyną wirtualną. Aby uzyskać więcej informacji na temat włączania zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md), lub jeden z tych artykułów variant (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub platformy Azure ZESTAW SDK). 

> [!IMPORTANT]
> - Granicy zabezpieczeń z zarządzanych tożsamości dla zasobów platformy Azure jest zasób, który jest używany na. Wszystkie kodu/skrypty uruchamiania na maszynie wirtualnej może żądać i pobierać tokenów dla zarządzanych tożsamości, dostępne w nim. 

## <a name="overview"></a>Omówienie

Aplikacja kliencka może żądać zarządzanych tożsamości dla zasobów platformy Azure [token dostępu tylko do aplikacji](../develop/developer-glossary.md#access-token) do uzyskiwania dostępu do danego zasobu. Token jest [na podstawie zarządzanych tożsamości dla jednostki usługi dla zasobów platformy Azure](overview.md#how-does-it-work). W efekcie nie ma potrzeby dla klienta w celu zarejestrowania się w celu uzyskania tokenu dostępu w ramach własnej jednostki usługi. Token jest odpowiedni do użytku jako token elementu nośnego w [service to service wywołuje wymagające poświadczeń klienta](../develop/v1-oauth2-client-creds-grant-flow.md).

|  |  |
| -------------- | -------------------- |
| [Uzyskaj token za pośrednictwem protokołu HTTP](#get-a-token-using-http) | Szczegóły protokołu dla zarządzanych tożsamości dla zasobów platformy Azure token punktu końcowego |
| [Uzyskaj token za pomocą biblioteki Microsoft.Azure.Services.AppAuthentication dla platformy .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Przykładem korzystania z biblioteki Microsoft.Azure.Services.AppAuthentication z klienta programu .NET
| [Uzyskaj token za pomocą języka C#](#get-a-token-using-c) | Przykład użycia zarządzanych tożsamości dla punktu końcowego REST zasobów platformy Azure z klienta języka C# |
| [Uzyskaj token za pomocą języka Java](#get-a-token-using-java) | Przykład użycia zarządzanych tożsamości dla punktu końcowego REST zasobów platformy Azure z klienta języka Java |
| [Uzyskaj token za pomocą języka Go](#get-a-token-using-go) | Przykład użycia zarządzanych tożsamości dla punktu końcowego REST zasobów platformy Azure z klienta z rzeczywistym użyciem |
| [Uzyskaj token za pomocą programu Azure PowerShell](#get-a-token-using-azure-powershell) | Przykład użycia zarządzanych tożsamości dla punktu końcowego REST zasobów platformy Azure w kliencie programu PowerShell |
| [Pobierz token, używając programu CURL](#get-a-token-using-curl) | Przykład użycia zarządzanych tożsamości dla punktu końcowego REST zasobów platformy Azure w kliencie programu Bash/CURL |
| Obsługa buforowania tokenu | Wskazówki dotyczące obsługi tokenów dostępu wygasły |
| [Obsługa błędów](#error-handling) | Wskazówki dotyczące obsługi błędów HTTP zwrócony z zarządzanych tożsamości dla punktu końcowego tokenu zasobów platformy Azure |
| [Identyfikatory zasobów dla usług platformy Azure](#resource-ids-for-azure-services) | Skąd uzyskać identyfikatory zasobów dla obsługiwanych usług platformy Azure |

## <a name="get-a-token-using-http"></a>Uzyskaj token za pośrednictwem protokołu HTTP 

Podstawowe interfejs do uzyskiwania tokenu dostępu jest oparta na REST, udostępnianie w architekturze każda aplikacja kliencka uruchomione na maszynie Wirtualnej, który może wykonywać wywołania REST protokołu HTTP. Jest to podobne do modelu programowania usługi Azure AD, z wyjątkiem klient używa punktu końcowego na maszynie wirtualnej (vs usługi Azure AD punktu końcowego).

Przykładowe żądanie przy użyciu punktu końcowego usługi Azure wystąpienie metadanych usługi (IMDS) *(zalecane)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Czasownik HTTP wskazująca, że chcesz pobrać dane z punktu końcowego. W tym przypadku token dostępu OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Zarządzanych tożsamości dla punktu końcowego zasobów platformy Azure Instance Metadata Service. |
| `api-version`  | Parametr ciągu zapytania, wskazujący wersję interfejsu API dla punktu końcowego IMDS. Użyj wersji interfejsu API `2018-02-01` lub nowszej. |
| `resource` | Parametr ciągu zapytania, wskazującą aplikację identyfikator URI zasobu docelowego. Jest także wyświetlany w `aud` (odbiorcy) oświadczenia w wystawionych tokenów. W tym przykładzie żąda tokenu dostępu do usługi Azure Resource Manager, która zawiera identyfikator URI aplikacji z https://management.azure.com/. |
| `Metadata` | Pola nagłówka żądania HTTP, związanej z zarządzanych tożsamości dla zasobów platformy Azure jako ograniczenie przed atakiem serwera po stronie żądania Międzywitrynowego (SSRF). Ta wartość musi być równa "true", małymi literami. |
| `object_id` | (Opcjonalnie) Parametr ciągu zapytania, wskazujący object_id tożsamości zarządzanej, które Twoim zdaniem token dla. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `client_id` | (Opcjonalnie) Parametr ciągu zapytania, wskazujący client_id z tożsamości zarządzanej, które Twoim zdaniem tokenem. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `mi_res_id` | (Opcjonalnie) Parametr ciągu zapytania, wskazujący mi_res_id (identyfikator zasobu platformy Azure) z tożsamości zarządzanej, które Twoim zdaniem tokenem. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika. |

Przykładowe żądanie przy użyciu zarządzanych tożsamości dla zasobów platformy Azure punktu końcowego maszyny Wirtualnej rozszerzenie *(zaplanowane do wycofania z użycia w styczniu 2019)*:

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Czasownik HTTP wskazująca, że chcesz pobrać dane z punktu końcowego. W tym przypadku token dostępu OAuth. | 
| `http://localhost:50342/oauth2/token` | Zarządzanych tożsamości dla punktu końcowego z zasobów platformy Azure, gdzie 50342 jest domyślnym portem i można konfigurować. |
| `resource` | Parametr ciągu zapytania, wskazującą aplikację identyfikator URI zasobu docelowego. Jest także wyświetlany w `aud` (odbiorcy) oświadczenia w wystawionych tokenów. W tym przykładzie żąda tokenu dostępu do usługi Azure Resource Manager, która zawiera identyfikator URI aplikacji z https://management.azure.com/. |
| `Metadata` | Pola nagłówka żądania HTTP, związanej z zarządzanych tożsamości dla zasobów platformy Azure jako ograniczenie przed atakiem serwera po stronie żądania Międzywitrynowego (SSRF). Ta wartość musi być równa "true", małymi literami.|
| `object_id` | (Opcjonalnie) Parametr ciągu zapytania, wskazujący object_id tożsamości zarządzanej, które Twoim zdaniem token dla. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `client_id` | (Opcjonalnie) Parametr ciągu zapytania, wskazujący client_id z tożsamości zarządzanej, które Twoim zdaniem tokenem. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|

Przykładowa odpowiedź:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Opis |
| ------- | ----------- |
| `access_token` | Token żądanego dostępu. Podczas wywoływania zabezpieczonego interfejsu API REST, token jest osadzony w `Authorization` pola nagłówka żądania jako token "bearer", dzięki czemu interfejs API do uwierzytelniania obiektu wywołującego. | 
| `refresh_token` | Nie używany przez zarządzanych tożsamości dla zasobów platformy Azure. |
| `expires_in` | Liczba sekund, przez które tokenu dostępu w dalszym ciągu jest prawidłowy, zanim wygaśnie od czasu wydania. Czas wystawienia można znaleźć w tokenie `iat` oświadczenia. |
| `expires_on` | Zakres czasu wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odnosi się do tokenu `exp` oświadczenia). |
| `not_before` | Timespan, gdy token dostępu staje się skuteczny i mogą być akceptowane. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odnosi się do tokenu `nbf` oświadczenia). |
| `resource` | Zasób tokenu dostępu zażądano, odpowiadający `resource` żądania parametr ciągu zapytania. |
| `token_type` | Typ tokenu, który jest "Bearer" tokenu dostępu, co oznacza, że zasób można zapewnić dostęp do elementu nośnego tego tokenu. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Uzyskaj token za pomocą biblioteki Microsoft.Azure.Services.AppAuthentication dla platformy .NET

Dla aplikacji platformy .NET i funkcji najprostszy sposób pracy z zarządzanych tożsamości dla zasobów platformy Azure jest za pośrednictwem pakietu Microsoft.Azure.Services.AppAuthentication. Ta biblioteka będzie można również do testowania kodu lokalnie na komputerze deweloperskim, przy użyciu konta użytkownika z programu Visual Studio [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), lub zintegrowane uwierzytelnianie usługi Active Directory. Aby uzyskać więcej informacji na temat opcji lokalny rozwój za pomocą tej biblioteki, zobacz [odwołania Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication). W tej sekcji dowiesz się, jak rozpocząć pracę z biblioteką w kodzie.

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
    
Aby dowiedzieć się więcej na temat Microsoft.Azure.Services.AppAuthentication i operacje, które udostępnia, zobacz [odwołania Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication) i [usługi App Service i magazynu kluczy przy użyciu zarządzanych tożsamości dla zasobów platformy Azure .NET przykładowe](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Uzyskaj token za pomocą języka C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Uzyskaj token za pomocą języka Java

Użyj tego [biblioteki JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) pobierania tokenu przy użyciu języka Java.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Uzyskaj token za pomocą języka Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Uzyskaj token za pomocą programu Azure PowerShell

Poniższy przykład pokazuje sposób użycia zarządzanych tożsamości dla punktu końcowego REST zasobów platformy Azure z klientem programu PowerShell:

1. Uzyskiwanie tokenu dostępu.
2. Wywołania interfejsu REST API usługi Azure Resource Manager i uzyskać informacje na temat maszyny Wirtualnej przy użyciu tokenu dostępu. Pamiętaj zastąpić swoje identyfikator subskrypcji, nazwę grupy zasobów i nazwę maszyny wirtualnej `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, i `<VM-NAME>`, odpowiednio.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Przykład o tym, jak można przeanalizować tokenu dostępu z odpowiedzi:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Pobierz token, używając programu CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Przykład o tym, jak można przeanalizować tokenu dostępu z odpowiedzi:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Buforowanie tokenów

Podczas gdy zarządzanych tożsamości dla podsystemu zasobów platformy Azure używane (IMDS/zarządzanych tożsamości dla zasobów platformy Azure rozszerzenia maszyny Wirtualnej) w pamięci podręcznej tokenów, zalecane jest również implementuje się buforowanie tokenów w kodzie. W rezultacie należy przygotować w scenariuszach, gdzie zasobu wskazuje, że token utracił ważność. 

Tylko wynik w locie wywołań do usługi Azure AD po:
- Trafienia pamięci podręcznej występuje ze względu na brak tokenu w zarządzanych tożsamości dla pamięci podręcznej podsystemu zasobów platformy Azure
- buforowany token wygasł

## <a name="error-handling"></a>Obsługa błędów

Zarządzanych tożsamości dla zasobów platformy Azure punktu końcowego sygnały błędów za pomocą pola Kod stanu elementu nagłówka komunikatu odpowiedzi HTTP jako błędy 4xx lub 5xx:

| Kod stanu | Przyczyna błędu | Sposób obsługi |
| ----------- | ------------ | ------------- |
| 404 Nie znaleziono. | Trwa aktualizowanie IMDS punktu końcowego. | Spróbuj ponownie za pomocą Expontential wycofywania. Zobacz poniższe wskazówki. |
| 429 zbyt wiele żądań. |  Osiągnięto limit ograniczania IMDS. | Spróbuj ponownie z wykorzystaniem wykładniczego wycofywania. Zobacz poniższe wskazówki. |
| Wystąpił błąd 4xx żądania. | Co najmniej jeden z parametrów żądania była nieprawidłowa. | Nie należy wykonywać ponowień.  Sprawdź szczegóły błędu, aby uzyskać więcej informacji.  błędy 4xx są błędy czasu projektowania.|
| Błąd przejściowy 5xx z usługi. | Zarządzanych tożsamości dla podsystemu zasobów platformy Azure lub usługi Azure Active Directory zwrócił błąd przejściowy. | Jest bezpieczne ponowić próbę po odczekaniu co najmniej 1 sekundę.  Jeśli ponowienie próby zbyt szybko lub zbyt często IMDS i/lub Azure AD może zwrócić błąd limitu szybkości (429).|
| timeout | Trwa aktualizowanie IMDS punktu końcowego. | Spróbuj ponownie za pomocą Expontential wycofywania. Zobacz poniższe wskazówki. |

Jeśli wystąpi błąd, odpowiednich treści odpowiedzi HTTP zawiera JSON za pomocą szczegóły błędu:

| Element | Opis |
| ------- | ----------- |
| error   | Identyfikator błędu. |
| error_description | Szczegółowy opis błędu. **Opisy błędów można zmienić w dowolnym momencie. Nie zapisuj kod, który gałęzi na podstawie wartości w opisie błędu.**|

### <a name="http-response-reference"></a>Odwołanie odpowiedzi HTTP

W tej sekcji omówiono możliwy błąd odpowiedzi. Element "200 OK" stan to pomyślnej odpowiedzi i token dostępu jest zawarty w treści odpowiedzi JSON, w elemencie access_token.

| Kod stanu | Błąd | Opis błędu | Rozwiązanie |
| ----------- | ----- | ----------------- | -------- |
| 400 Niewłaściwe żądanie | invalid_resource | AADSTS50001: Aplikacja o nazwie *\<URI\>* nie został znaleziony w dzierżawie o nazwie  *\<TENANT-ID\>*. Może to nastąpić, jeśli nie została zainstalowana przez administratora dzierżawy lub wyraża zgodę na żaden użytkownik w dzierżawie usługi aplikacji. Żądanie uwierzytelniania mogło zostać wysłane do nieprawidłowej dzierżawy. \ | (Tylko system Linux) |
| 400 Niewłaściwe żądanie | bad_request_102 | Nie jest określony nagłówek wymagane metadane | Albo `Metadata` pola nagłówka żądania brakuje żądania lub jest niepoprawnie sformatowana. Wartość musi być określona jako `true`, małymi literami. Zobacz "przykładowe żądanie" w poprzedniej sekcji REST, na przykład.|
| 401 Brak autoryzacji | unknown_source | Nieznane źródło  *\<identyfikatora URI\>* | Sprawdź, żądanie HTTP GET identyfikatora URI jest prawidłowo sformatowany. `scheme:host/resource-path` Część musi być określona jako `http://localhost:50342/oauth2/token`. Zobacz "przykładowe żądanie" w poprzedniej sekcji REST, na przykład.|
|           | invalid_request | Żądania brakuje wymaganego parametru, obejmuje Nieprawidłowa wartość parametru, zawiera więcej niż jeden raz parametr lub w przeciwnym razie jest nieprawidłowo sformułowany. |  |
|           | unauthorized_client | Klient nie ma uprawnień do żądania tokenu dostępu przy użyciu tej metody. | Przyczyną żądania, który został użyty przez Ciebie lokalnego sprzężenie zwrotne do wywoływania rozszerzenia, lub na maszynie Wirtualnej, która nie ma zarządzanych tożsamości dla zasobów platformy Azure są poprawnie skonfigurowane. Zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md) Jeśli potrzebujesz pomocy przy użyciu konfiguracji maszyny Wirtualnej. |
|           | access_denied | Właściciel zasobu lub autoryzacji serwer odrzucił żądanie. |  |
|           | unsupported_response_type | Serwer autoryzacji nie obsługuje uzyskiwania tokenu dostępu przy użyciu tej metody. |  |
|           | invalid_scope | Żądany zakres jest nieprawidłowy, nieznany lub źle skonstruowany. |  |
| 500 Wewnętrzny błąd serwera | nieznane | Nie można pobrać token z usługi Active directory. Szczegółowe informacje można znaleźć w dziennikach w  *\<ścieżki pliku\>* | Sprawdź, czy zarządzanych tożsamości dla zasobów platformy Azure został włączony na maszynie Wirtualnej. Zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md) Jeśli potrzebujesz pomocy przy użyciu konfiguracji maszyny Wirtualnej.<br><br>Sprawdź także, że Twoje żądanie HTTP GET identyfikatora URI jest prawidłowo sformatowany, szczególnie zasób, do którego identyfikatora URI określonego w ciągu zapytania. Zobacz "przykładowe żądanie" w poprzedniej sekcji REST, na przykład lub [usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD](services-support-msi.md) listę usług i ich odpowiednich identyfikatorów zasobów.

## <a name="retry-guidance"></a>Wskazówki dotyczące ponawiania prób 

Zalecane jest aby ponowić próbę, jeśli otrzymasz odpowiedź 404, 429 lub 5xx, kod błędu (zobacz [obsługi błędów](#error-handling) powyżej).

Zastosować limity ograniczania liczby wywołań do IMDS endpoint. Po przekroczeniu progu ograniczania punktu końcowego IMDS ogranicza wszystkie dalsze żądania ograniczenie w czasie działania. W tym okresie IMDS punkt końcowy zostanie zwrócony kod stanu HTTP 429 ("zbyt wiele żądań"), i Niepowodzenie żądania. 

Ponów próbę zalecamy następujących strategii: 

| **Strategia ponawiania prób** | **Ustawienia** | **Wartości** | **Jak to działa** |
| --- | --- | --- | --- |
|ExponentialBackoff |Liczba ponownych prób<br />Minimalna liczba wycofań<br />Maksymalna liczba wycofań<br />Różnica w liczbie wycofań<br />Pierwsze szybkie ponowienie |5<br />0 sek.<br />60 sek.<br />2 sek.<br />false |Próba 1 — opóźnienie 0 sek.<br />Próba 2 — opóźnienie ok. 2 sek.<br />Próba 3 — opóźnienie ok. 6 sek.<br />Próba 4 — opóźnienie ok. 14 sek.<br />Próba 5 — opóźnienie ok. 30 sek. |

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure

Zobacz [usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD](services-support-msi.md) listę zasobów, które obsługują usługę Azure AD i zostały przetestowane z zarządzanych tożsamości dla zasobów platformy Azure oraz ich odpowiednich identyfikatorów zasobów.


## <a name="next-steps"></a>Kolejne kroki

- Aby włączyć zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md).








