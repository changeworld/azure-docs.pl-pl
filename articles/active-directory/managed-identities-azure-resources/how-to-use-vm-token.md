---
title: Uzyskiwanie tokenu dostępu za pomocą tożsamości zarządzanych na maszynie wirtualnej — Azure AD
description: Instrukcje krok po kroku i przykłady dotyczące używania tożsamości zarządzanych dla zasobów platformy Azure na maszynach wirtualnych w celu uzyskania tokenu dostępu OAuth.
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
ms.openlocfilehash: a58103bad3914bd0c0c6e70f8e3d2882271e1070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049208"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure w celu uzyskania tokenu dostępu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

Ten artykuł zawiera różne przykłady kodu i skryptów do pozyskiwania tokenów, a także wskazówki dotyczące ważnych tematów, takich jak obsługa wygaśnięcia tokenu i błędy HTTP. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz użyć przykładów programu Azure PowerShell w tym artykule, należy zainstalować najnowszą wersję [programu Azure PowerShell.](/powershell/azure/install-az-ps)


> [!IMPORTANT]
> - Cały przykładowy kod/skrypt w tym artykule zakłada, że klient jest uruchomiony na maszynie wirtualnej z zarządzanymi tożsamościami dla zasobów platformy Azure. Użyj funkcji "Połącz" maszyny wirtualnej w witrynie Azure portal, aby zdalnie połączyć się z maszyną wirtualną. Aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu portalu Azure](qs-configure-portal-windows-vm.md)lub jednego z artykułów wariantu (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu SDK platformy Azure). 

> [!IMPORTANT]
> - Granica zabezpieczeń tożsamości zarządzanych dla zasobów platformy Azure jest zasobem, na który jest używany. Wszystkie kod/skrypty uruchomione na maszynie wirtualnej można żądać i pobierać tokeny dla wszystkich tożsamości zarządzanych dostępnych na nim. 

## <a name="overview"></a>Omówienie

Aplikacja kliencka może żądać zarządzanych tożsamości dla [tokenu dostępu tylko do](../develop/developer-glossary.md#access-token) zasobów platformy Azure w celu uzyskania dostępu do danego zasobu. Token jest [oparty na tożsamości zarządzanych dla jednostki usługi zasobów platformy Azure.](overview.md#how-does-the-managed-identities-for-azure-resources-work) W związku z tym nie ma potrzeby, aby klient się zarejestrować, aby uzyskać token dostępu w ramach własnej jednostki usługi. Token jest odpowiedni do użycia jako token na okaziciela w [wywołaniach usługi do usługi wymagających poświadczeń klienta.](../develop/v2-oauth2-client-creds-grant-flow.md)

|  |  |
| -------------- | -------------------- |
| [Pobierz token za pomocą protokołu HTTP](#get-a-token-using-http) | Szczegóły protokołu dla tożsamości zarządzanych dla punktu końcowego tokenu zasobów platformy Azure |
| [Pobierz token przy użyciu biblioteki microsoft.Azure.Services.AppAuthentication dla platformy .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Przykład użycia biblioteki microsoft.Azure.Services.AppAuthentication z klienta platformy .NET
| [Pobierz token za pomocą języka C #](#get-a-token-using-c) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta języka C# |
| [Pobierz token za pomocą oprogramowania Java](#get-a-token-using-java) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta java |
| [Pobierz token za pomocą Go](#get-a-token-using-go) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta Go |
| [Pobierz token przy użyciu programu Azure PowerShell](#get-a-token-using-azure-powershell) | Przykład użycia tożsamości zarządzanych dla zasobów platformy Azure dla punktu końcowego REST zasobów platformy Azure z klienta programu PowerShell |
| [Pobierz token za pomocą CURL](#get-a-token-using-curl) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta Bash/CURL |
| Obsługa buforowania tokenów | Wskazówki dotyczące obsługi wygasłych tokenów dostępu |
| [Obsługa błędów](#error-handling) | Wskazówki dotyczące obsługi błędów HTTP zwracanych z tożsamości zarządzanych dla punktu końcowego tokenu zasobów platformy Azure |
| [Identyfikatory zasobów dla usług platformy Azure](#resource-ids-for-azure-services) | Gdzie można uzyskać identyfikatory zasobów dla obsługiwanych usług platformy Azure |

## <a name="get-a-token-using-http"></a>Pobierz token za pomocą protokołu HTTP 

Podstawowy interfejs do uzyskiwania tokenu dostępu jest oparty na REST, dzięki czemu jest dostępny dla dowolnej aplikacji klienckiej uruchomionej na maszynie Wirtualnej, która może nawiązywać wywołania HTTP REST. Jest to podobne do modelu programowania usługi Azure AD, z wyjątkiem klienta używa punktu końcowego na maszynie wirtualnej (w porównaniu z punktem końcowym usługi Azure AD).

Przykładowe żądanie przy użyciu punktu końcowego usługi imds (IMDS) usługi wystąpienia platformy Azure *(zalecane):*

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Zlecenie HTTP, wskazując, że chcesz pobrać dane z punktu końcowego. W takim przypadku token dostępu OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Tożsamości zarządzane dla punktu końcowego zasobów platformy Azure dla usługi metadanych wystąpienia. |
| `api-version`  | Parametr ciągu kwerendy wskazujący wersję interfejsu API dla punktu końcowego IMDS. Użyj wersji `2018-02-01` interfejsu API lub większej. |
| `resource` | Parametr ciągu kwerendy wskazujący identyfikator URI identyfikatora aplikacji zasobu docelowego. Pojawia się również `aud` w (odbiorcy) roszczenia wydanego tokenu. W tym przykładzie żąda tokenu dostępu do usługi Azure `https://management.azure.com/`Resource Manager, który ma identyfikator URI identyfikatora aplikacji . |
| `Metadata` | Pole nagłówka żądania HTTP, wymagane przez tożsamości zarządzane dla zasobów platformy Azure jako ograniczenie ataków fałszerstwa żądań po stronie serwera (SSRF). Ta wartość musi być ustawiona na "true", we wszystkich przypadkach. |
| `object_id` | (Opcjonalnie) Parametr ciągu kwerendy, wskazujący object_id tożsamości zarządzanej, dla której chcesz token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `client_id` | (Opcjonalnie) Parametr ciągu kwerendy, wskazujący client_id tożsamości zarządzanej, dla której chcesz token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `mi_res_id` | (Opcjonalnie) Parametr ciągu kwerendy, wskazujący mi_res_id (Identyfikator zasobu Platformy Azure) tożsamości zarządzanej, dla której chcesz token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika. |

Przykładowe żądanie przy użyciu zarządzanych tożsamości dla zasobów platformy Azure — punkt końcowy rozszerzenia maszyny Wirtualnej *(planowane do wycofania w styczniu 2019 r.):*

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Zlecenie HTTP, wskazując, że chcesz pobrać dane z punktu końcowego. W takim przypadku token dostępu OAuth. | 
| `http://localhost:50342/oauth2/token` | Tożsamości zarządzane dla punktu końcowego zasobów platformy Azure, gdzie 50342 jest portem domyślnym i jest konfigurowalny. |
| `resource` | Parametr ciągu kwerendy wskazujący identyfikator URI identyfikatora aplikacji zasobu docelowego. Pojawia się również `aud` w (odbiorcy) roszczenia wydanego tokenu. W tym przykładzie żąda tokenu dostępu do usługi Azure `https://management.azure.com/`Resource Manager, który ma identyfikator URI identyfikatora aplikacji . |
| `Metadata` | Pole nagłówka żądania HTTP, wymagane przez tożsamości zarządzane dla zasobów platformy Azure jako ograniczenie ataków fałszerstwa żądań po stronie serwera (SSRF). Ta wartość musi być ustawiona na "true", we wszystkich przypadkach.|
| `object_id` | (Opcjonalnie) Parametr ciągu kwerendy, wskazujący object_id tożsamości zarządzanej, dla której chcesz token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `client_id` | (Opcjonalnie) Parametr ciągu kwerendy, wskazujący client_id tożsamości zarządzanej, dla której chcesz token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|

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
| `access_token` | Żądany token dostępu. Podczas wywoływania zabezpieczonego interfejsu API REST `Authorization` token jest osadzony w polu nagłówka żądania jako token "nośnik", umożliwiając interfejsowi API uwierzytelnienie wywołującego. | 
| `refresh_token` | Nie używane przez tożsamości zarządzane dla zasobów platformy Azure. |
| `expires_in` | Liczba sekund token dostępu nadal jest prawidłowa, przed wygaśnięciem, od czasu wystawienia. Czas wystawienia można znaleźć w żądaniu `iat` tokenu. |
| `expires_on` | Czas po wygaśnięciu tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada `exp` żądaniu tokenu). |
| `not_before` | Czas, gdy token dostępu staje się skuteczne i mogą być akceptowane. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada `nbf` żądaniu tokenu). |
| `resource` | Zasób, dla którego zażądano tokenu dostępu, który odpowiada parametrowi `resource` ciągu zapytania żądania. |
| `token_type` | Typ tokenu, który jest token dostępu "Bearer", co oznacza, że zasób może dać dostęp do nośnika tego tokenu. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Pobierz token przy użyciu biblioteki microsoft.Azure.Services.AppAuthentication dla platformy .NET

W przypadku aplikacji i funkcji platformy .NET najprostszym sposobem pracy z tożsamościami zarządzanymi dla zasobów platformy Azure jest pakiet Microsoft.Azure.Services.AppAuthentication. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze deweloperskim przy użyciu konta użytkownika z programu Visual Studio, [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)lub zintegrowanego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji na temat opcji rozwoju lokalnego za pomocą tej biblioteki, zobacz [odwołanie do microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication). W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do pakietów [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet do aplikacji.

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
    
Aby dowiedzieć się więcej o microsoft.Azure.Services.AppAuthentication i operacji, które udostępnia, zobacz [Microsoft.Azure.Services.AppAuthentication odwołania](/azure/key-vault/service-to-service-authentication) i [usługi aplikacji i KeyVault z zarządzanych tożsamości dla zasobów platformy Azure .NET próbki](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet). .

## <a name="get-a-token-using-c"></a>Pobierz token za pomocą języka C #

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

## <a name="get-a-token-using-java"></a>Pobierz token za pomocą oprogramowania Java

Ta [biblioteka JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) służy do pobierania tokenu przy użyciu języka Java.

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

## <a name="get-a-token-using-go"></a>Pobierz token za pomocą Go

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

## <a name="get-a-token-using-azure-powershell"></a>Pobierz token przy użyciu programu Azure PowerShell

W poniższym przykładzie pokazano, jak używać tożsamości zarządzanych dla zasobów platformy Azure punkt końcowy REST z klienta programu PowerShell do:

1. Uzyskaj token dostępu.
2. Użyj tokenu dostępu, aby wywołać interfejs API REST usługi Azure Resource Manager i uzyskać informacje o maszynie Wirtualnej. Pamiętaj, aby zastąpić identyfikator subskrypcji, nazwę grupy zasobów `<SUBSCRIPTION-ID>`i `<RESOURCE-GROUP>`nazwę `<VM-NAME>`maszyny wirtualnej odpowiednio , i , odpowiednio.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Przykład sposobu analizowania tokenu dostępu z odpowiedzi:
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

## <a name="get-a-token-using-curl"></a>Pobierz token za pomocą CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Przykład sposobu analizowania tokenu dostępu z odpowiedzi:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Buforowanie tokenów

Podczas gdy tożsamości zarządzane dla podsystemu zasobów platformy Azure używane (IMDS/tożsamości zarządzane dla rozszerzenia maszyny Wirtualnej zasobów platformy Azure) nie tokeny pamięci podręcznej, zaleca się również zaimplementowanie buforowania tokenów w kodzie. W rezultacie należy przygotować się do scenariuszy, w których zasób wskazuje, że token wygasł. 

Wywołania usługi Azure AD tylko wtedy, gdy:
- brak pamięci podręcznej występuje z powodu braku tokenu w tożsamościach zarządzanych dla pamięci podręcznej podsystemu zasobów platformy Azure
- token w pamięci podręcznej wygasł

## <a name="error-handling"></a>Obsługa błędów

Tożsamości zarządzane dla zasobów platformy Azure sygnalizuje błędy za pośrednictwem pola kodu stanu nagłówka komunikatu odpowiedzi HTTP, jako błędy 4xx lub 5xx:

| Kod stanu | Przyczyna błędu | Jak sobie radzić |
| ----------- | ------------ | ------------- |
| 404 Nie znaleziono. | Punkt końcowy IMDS jest aktualizowany. | Ponów próbę z expontential backoff. Zapoznaj się z poniższymi wskazówkami. |
| 429 Zbyt wiele żądań. |  Osiągnięto limit przepustnicy IMDS. | Ponów próbę przy czym przy próbie przywrócania wykładniczego. Zapoznaj się z poniższymi wskazówkami. |
| 4xx Błąd w żądaniu. | Jeden lub więcej parametrów żądania było niepoprawne. | Nie należy ponowiać próby.  Sprawdź szczegóły błędu, aby uzyskać więcej informacji.  Błędy 4xx to błędy w czasie projektowania.|
| 5xx Błąd przejściowy z serwisu. | Tożsamości zarządzane dla podsystemu zasobów platformy Azure lub usługi Azure Active Directory zwróciły błąd przejściowy. | Można bezpiecznie ponowić próbę po odczekaniu co najmniej 1 sekundy.  Jeśli ponowisz próbę zbyt szybko lub zbyt często, IMDS i/lub usługi Azure AD może zwrócić błąd limitu szybkości (429).|
| timeout | Punkt końcowy IMDS jest aktualizowany. | Ponów próbę z expontential backoff. Zapoznaj się z poniższymi wskazówkami. |

Jeśli wystąpi błąd, odpowiednia treść odpowiedzi HTTP zawiera JSON ze szczegółami błędu:

| Element | Opis |
| ------- | ----------- |
| error   | Identyfikator błędu. |
| error_description | Pełny opis błędu. **Opisy błędów mogą ulec zmianie w dowolnym momencie. Nie należy pisać kodu, który jest gałęzie oparte na wartościach w opisie błędu.**|

### <a name="http-response-reference"></a>Odwołanie do odpowiedzi HTTP

W tej sekcji dokumentuje możliwe odpowiedzi na błędy. Stan "200 OK" jest pomyślną odpowiedzią, a token dostępu znajduje się w treści odpowiedzi JSON w access_token elemencie.

| Kod stanu | Błąd | Opis błędu | Rozwiązanie |
| ----------- | ----- | ----------------- | -------- |
| 400 Zła prośba | invalid_resource | AADSTS50001: Aplikacja o nazwie * \<URI\> * nie została znaleziona w dzierżawie o nazwie * \<\>TENANT-ID*. Może się tak zdarzyć, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub na co wyraziła zgodę dowolnego użytkownika w dzierżawie. Być może wysłano żądanie uwierzytelnienia do niewłaściwej dzierżawy.\ | (Tylko Linux) |
| 400 Zła prośba | bad_request_102 | Nie określono nagłówka wymaganych metadanych | W żądaniu `Metadata` brakuje pola nagłówka żądania lub jest ono niepoprawnie sformatowane. Wartość musi być `true`określona jako , we wszystkich przypadkach. Zobacz "Przykładowe żądanie" w poprzedniej sekcji REST na przykład.|
| 401 Nieautoryzowane | unknown_source | Nieznany identyfikator * \<URI źródła\>* | Sprawdź, czy identyfikator URI żądania HTTP GET jest poprawnie sformatowany. Część `scheme:host/resource-path` musi być `http://localhost:50342/oauth2/token`określona jako . Zobacz "Przykładowe żądanie" w poprzedniej sekcji REST na przykład.|
|           | invalid_request | Żądanie brakuje wymaganego parametru, zawiera nieprawidłową wartość parametru, zawiera parametr więcej niż jeden raz lub jest w inny sposób zniekształcony. |  |
|           | unauthorized_client | Klient nie jest autoryzowany do żądania tokenu dostępu przy użyciu tej metody. | Spowodowane przez żądanie, które nie używało lokalnego sprzężenia zwrotnego do wywołania rozszerzenia lub na maszynie Wirtualnej, która nie ma tożsamości zarządzanych dla zasobów platformy Azure skonfigurowanych poprawnie. Zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal,](qs-configure-portal-windows-vm.md) jeśli potrzebujesz pomocy w konfiguracji maszyny Wirtualnej. |
|           | Access_denied | Właściciel zasobu lub serwer autoryzacji odmówił żądania. |  |
|           | unsupported_response_type | Serwer autoryzacji nie obsługuje uzyskiwania tokenu dostępu przy użyciu tej metody. |  |
|           | invalid_scope | Żądany zakres jest nieprawidłowy, nieznany lub zniekształcony. |  |
| 500 Wewnętrzny błąd serwera | unknown | Nie można pobrać tokenu z usługi Active Directory. Aby uzyskać szczegółowe informacje, zobacz dzienniki w * \<ścieżce pliku\>* | Sprawdź, czy tożsamości zarządzane dla zasobów platformy Azure została włączona na maszynie wirtualnej. Zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal,](qs-configure-portal-windows-vm.md) jeśli potrzebujesz pomocy w konfiguracji maszyny Wirtualnej.<br><br>Sprawdź również, czy identyfikator URI żądania HTTP GET jest poprawnie sformatowany, szczególnie identyfikator URI zasobu określony w ciągu zapytania. Zobacz "Przykładowe żądanie" w poprzedniej sekcji REST na przykład lub [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](services-support-msi.md) dla listy usług i ich odpowiednich identyfikatorów zasobów.

## <a name="retry-guidance"></a>Wskazówki dotyczące ponawiania prób 

Zaleca się ponowić próbę, jeśli otrzymasz kod błędu 404, 429 lub 5xx (zobacz [Obsługa błędów](#error-handling) powyżej).

Limity ograniczania dotyczą liczby wywołań do punktu końcowego IMDS. Po przekroczeniu progu ograniczania przepustowości punkt końcowy IMDS ogranicza wszelkie dalsze żądania, gdy przepustnica jest w mocy. W tym okresie punkt końcowy IMDS zwróci kod stanu HTTP 429 ("Zbyt wiele żądań" i żądania nie powiedzie się. 

Aby ponowić próbę, zalecamy następującą strategię: 

| **Strategia ponawiania prób** | **Ustawienia** | **Wartości** | **Jak to działa** |
| --- | --- | --- | --- |
|ExponentialBackoff |Liczba ponownych prób<br />Minimalna liczba wycofań<br />Maksymalna liczba wycofań<br />Różnica w liczbie wycofań<br />Pierwsze szybkie ponowienie |5<br />0 sek.<br />60 sek.<br />2 sek.<br />false |Próba 1 — opóźnienie 0 sek.<br />Próba 2 — opóźnienie ok. 2 sek.<br />Próba 3 — opóźnienie ok. 6 sek.<br />Próba 4 — opóźnienie ok. 14 sek.<br />Próba 5 — opóźnienie ok. 30 sek. |

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure

Zobacz [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD,](services-support-msi.md) aby uzyskać listę zasobów obsługujących usługę Azure AD i przetestowanych przy użyciu tożsamości zarządzanych dla zasobów platformy Azure i ich odpowiednich identyfikatorów zasobów.


## <a name="next-steps"></a>Następne kroki

- Aby włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu portalu Azure.](qs-configure-portal-windows-vm.md)








