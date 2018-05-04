---
title: Jak używać zarządzane usługi tożsamość Azure maszyny Wirtualnej można uzyskać tokenu dostępu
description: Krok po kroku tokenu dostępu instrukcje i przykłady dotyczące uzyskania OAuth przy użyciu Instalatora MSI maszyny Wirtualnej Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 360e395743dcf4b4bae98a756f6483dd0d269775
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Sposób użycia tokenu nabycia Azure VM zarządzane usługi tożsamości (MSI) 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

Ten artykuł zawiera różne przykłady kodu i skrypt nabycia token, a także wskazówki dotyczące ważnych tematów, takich jak obsługa wygaśnięcia tokenu i błędów HTTP. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz używać programu Azure PowerShell przykłady w tym artykule, należy zainstalować najnowszą wersję [programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Wszystkie próbki kodu skryptu w tym artykule przyjęto założenie, klient jest uruchomiony na maszynie wirtualnej za pomocą tożsamości zarządzanych usługi. Funkcja maszyny Wirtualnej "Połącz" w portalu Azure, aby zdalnie nawiązać połączenia z maszyną Wirtualną. Aby uzyskać więcej informacji na temat włączania MSI w maszynie Wirtualnej, zobacz [skonfigurować maszyny Wirtualnej zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure](qs-configure-portal-windows-vm.md), lub jednego z artykułów variant (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu SDK platformy Azure). 

> [!IMPORTANT]
> - Granicy zabezpieczeń z tożsamością usługi zarządzania jest zasób, który jest używany na. Wszystkie kodu/skrypty uruchamiania na maszynie wirtualnej można zażądać i pobranie tokenów dla dowolnego zarządzanego tożsamość usługi dostępne w nim. 

## <a name="overview"></a>Przegląd

Aplikacja kliencka mogą żądać tożsamości usługi zarządzane [token dostępu tylko do aplikacji](../develop/active-directory-dev-glossary.md#access-token) do uzyskiwania dostępu do zasobu. Token jest [oparte na nazwy głównej usługi MSI](overview.md#how-does-it-work). Tak jest niepotrzebna dla klienta w celu zarejestrowania się w celu uzyskania tokenu dostępu w ramach własnej nazwy głównej usługi. Token jest odpowiednie do użycia jako tokenu elementu nośnego w [service-to-service wymaga poświadczeń klienta wymagające](../develop/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Uzyskaj token za pośrednictwem protokołu HTTP](#get-a-token-using-http) | Szczegóły protokół dla punktu końcowego tokena MSI |
| [Uzyskaj token za pomocą języka C#](#get-a-token-using-c) | Przykład użycia punkt końcowy MSI REST w kliencie C# |
| [Uzyskaj token korzystania z rzeczywistym użyciem](#get-a-token-using-go) | Przykład użycia punkt końcowy MSI REST z klienta z rzeczywistym użyciem |
| [Uzyskaj token za pomocą programu Azure PowerShell](#get-a-token-using-azure-powershell) | Przykład użycia punkt końcowy MSI REST w kliencie programu PowerShell |
| [Uzyskaj token przy użyciu programu CURL](#get-a-token-using-curl) | Przykład użycia punkt końcowy MSI REST w kliencie Bash/CURL |
| [Obsługa buforowania tokenu](#handling-token-caching) | Wskazówki dotyczące obsługi tokenów dostępu wygasły |
| [Obsługa błędów](#error-handling) | Wskazówki dotyczące obsługi błędów HTTP zwrócony z punktu końcowego tokena MSI |
| [Identyfikatory zasobów dla usług Azure](#resource-ids-for-azure-services) | Skąd uzyskać identyfikatorów zasobów dla obsługiwanych usług platformy Azure |

## <a name="get-a-token-using-http"></a>Uzyskaj token za pośrednictwem protokołu HTTP 

Podstawowe interfejsu uzyskania tokenu dostępu jest oparta na REST, udostępnienie jej do klienta aplikacji uruchomionych na maszynie Wirtualnej, która może wykonywać wywołania REST protokołu HTTP. Jest to podobne do modelu programowania usługi Azure AD, z wyjątkiem klient używa punktu końcowego na maszynie wirtualnej (vs Azure AD punktu końcowego).

Przykładowe żądanie przy użyciu punktu końcowego usługi Azure wystąpienie metadanych usługi (IMDS) *(zalecane)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Zlecenie HTTP wskazująca, że chcesz pobrać dane z punktu końcowego. W takim przypadku tokenu dostępu protokołu OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Punkt końcowy MSI dla wystąpienia usługi metadanych. |
| `api-version`  | Parametr ciągu zapytania, wskazujący wersję interfejsu API dla punktu końcowego IMDS. Użyj interfejsu API w wersji `2018-02-01` lub nowszej. |
| `resource` | Parametr ciągu zapytania, wskazującą aplikację identyfikator URI zasobu docelowego. Jest także wyświetlany w `aud` oświadczeń (odbiorcy) wystawionego tokenu. W tym przykładzie żądania tokenu dostępu usługi Azure Resource Manager, do którego ma identyfikator URI aplikacji z https://management.azure.com/. |
| `Metadata` | Pola nagłówka żądania HTTP, wymagane przez Instalatora MSI jako ograniczenie przed atakiem serwera po stronie żądania Międzywitrynowego (SSRF). Musi mieć ustawioną wartość "prawda", w małe litery.

Przykładowe żądanie przy użyciu punktu końcowego rozszerzenia maszyny Wirtualnej zarządzane tożsamości usługi (MSI) *(aby przestarzałe)*:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Zlecenie HTTP wskazująca, że chcesz pobrać dane z punktu końcowego. W takim przypadku tokenu dostępu protokołu OAuth. | 
| `http://localhost:50342/oauth2/token` | MSI punktu końcowego, gdzie 50342 jest domyślnym portem i można go skonfigurować. |
| `resource` | Parametr ciągu zapytania, wskazującą aplikację identyfikator URI zasobu docelowego. Jest także wyświetlany w `aud` oświadczeń (odbiorcy) wystawionego tokenu. W tym przykładzie żądania tokenu dostępu usługi Azure Resource Manager, do którego ma identyfikator URI aplikacji z https://management.azure.com/. |
| `Metadata` | Pola nagłówka żądania HTTP, wymagane przez Instalatora MSI jako ograniczenie przed atakiem serwera po stronie żądania Międzywitrynowego (SSRF). Musi mieć ustawioną wartość "prawda", w małe litery.


Przykładowa odpowiedź:

```
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
| `access_token` | Żądany dostęp token. Podczas wywoływania metody zabezpieczonych interfejsu API REST, token jest osadzony w `Authorization` pola nagłówka żądania jako token "bearer", umożliwiając interfejsu API do uwierzytelniania obiektu wywołującego. | 
| `refresh_token` | Nie są używane przez Instalatora MSI. |
| `expires_in` | Liczba sekund, przez które token dostępu w dalszym ciągu był prawidłowy, zanim wygaśnie od czasu wydania. Czas wystawiania można znaleźć w tokenie `iat` oświadczeń. |
| `expires_on` | Zakres czasu wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada tokenu `exp` oświadczenia). |
| `not_before` | Timespan, gdy token dostępu obowiązuje i mogą być akceptowane. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada tokenu `nbf` oświadczenia). |
| `resource` | Żądany zasób token dostępu został dla odpowiadający identyfikatorowi `resource` żądania parametr ciągu zapytania. |
| `token_type` | Typ tokenu, który jest "Bearer" tokenu dostępu, co oznacza, że zasób pozwala uzyskiwać dostęp do elementu nośnego tego tokenu. |

## <a name="get-a-token-using-c"></a>Uzyskaj token za pomocą języka C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
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

## <a name="get-a-token-using-go"></a>Uzyskaj token korzystania z rzeczywistym użyciem

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
    
    // Create HTTP request for MSI token to access Azure Resource Manager
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

    // Call MSI /token endpoint
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

W poniższym przykładzie pokazano sposób użycia punkt końcowy MSI REST z klientem programu PowerShell:

1. Należy uzyskać token dostępu.
2. Użyj tokenu dostępu wywołania interfejsu REST API usługi Azure Resource Manager w celu uzyskania informacji o maszynie Wirtualnej. Pamiętaj zastąpić Twojego Identyfikatora subskrypcji, nazwa grupy zasobów i nazwy maszyny wirtualnej dla `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, i `<VM-NAME>`odpowiednio.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Przykład o tym, jak można przeanalizować tokenu dostępu z odpowiedzi:
```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Uzyskaj token przy użyciu programu CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Przykład o tym, jak można przeanalizować tokenu dostępu z odpowiedzi:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="token-caching"></a>Buforowanie tokenu

Podczas używany podsystem zarządzane tożsamości usługi (MSI) (rozszerzenia maszyny Wirtualnej IMDS/MSI) pamięci podręcznej tokenów, zalecamy wdrożenie buforowaniem tokena w kodzie. W związku z tym należy przygotować się na scenariuszach, w którym zasobu wskazuje, czy token utracił ważność. 

Powoduje w locie wywołań do usługi Azure AD, tylko gdy:
- Chybienia pamięci podręcznej występuje ze względu na nie tokenu w pamięci podręcznej podsystemu MSI
- buforowany token utracił ważność

## <a name="error-handling"></a>Obsługa błędów

Punkt końcowy zarządzane tożsamość usługi sygnalizuje błędów za pomocą kodu stanu pola nagłówka komunikatu odpowiedzi HTTP, jako 4xx lub 5xx błędów:

| Kod stanu | Przyczyny błędu | Sposób obsługi |
| ----------- | ------------ | ------------- |
| 429 zbyt wiele żądań. |  Osiągnięto limit przepustnicy IMDS. | Spróbuj ponownie z wykładniczego wycofywania. Zobacz poniższe wskazówki. |
| Błąd 4xx w żądaniu. | Co najmniej jeden z parametrów żądania jest niepoprawne. | Nie próbuj ponownie.  Sprawdź, czy szczegóły błędu, aby uzyskać więcej informacji.  błędy 4xx są błędy czasu projektowania.|
| 5xx Błąd przejściowy z usługi. | Podsystem MSI lub Azure Active Directory zwróciła błąd przejściowy. | Jest bezpieczne ponowić próbę po odczekaniu co najmniej 1 sekundę.  Możesz ponowić próbę zbyt szybko lub zbyt częstym IMDS i/lub Azure AD mogą zwracać błąd limitu szybkości (429).|
| Nie można odnaleźć 404. | Aktualizuje IMDS punktu końcowego. | Spróbuj ponownie z Expontential wycofywania. Zobacz poniższe wskazówki. |
| timeout | Aktualizuje IMDS punktu końcowego. | Spróbuj ponownie z Expontential wycofywania. Zobacz poniższe wskazówki. |

Jeśli wystąpi błąd, odpowiednich treści odpowiedzi HTTP zawiera JSON z szczegóły błędu:

| Element | Opis |
| ------- | ----------- |
| error   | Identyfikator błędu. |
| error_description | Pełen opis błędu. **Opisy błędów można zmienić w dowolnym momencie. Nie zapisuj kod, który gałęzie na podstawie wartości w opisie błędu.**|

### <a name="http-response-reference"></a>Odwołanie odpowiedzi HTTP

W tej sekcji omówiono odpowiedzi może zawierać błąd. A "200 OK" stan to pomyślnej odpowiedzi, a token dostępu jest zawarty w treści odpowiedzi JSON, w elemencie ' access_token '.

| Kod stanu | Błąd | Opis błędu | Rozwiązanie |
| ----------- | ----- | ----------------- | -------- |
| 400 Niewłaściwe żądanie | invalid_resource | AADSTS50001: Aplikacja o nazwie *\<URI\>* nie znaleziono dzierżawy o nazwie  *\<identyfikator DZIERŻAWCY\>*. Może to nastąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub zgodę na każdy użytkownik w dzierżawie. Żądanie uwierzytelniania prawdopodobnie została wysłana do niewłaściwej dzierżawy. \ | (Tylko w systemie Linux) |
| 400 Niewłaściwe żądanie | bad_request_102 | Nie określono nagłówka wymagane metadane | Albo `Metadata` pola nagłówka żądania brakuje żądania lub jest niepoprawnie sformatowana. Wartość musi być określona jako `true`, w małe litery. Zobacz sekcję "przykładowe żądanie" w [powyższej sekcji REST](#rest) przykład.|
| 401 nieautoryzowane | unknown_source | Nieznane źródło  *\<identyfikatora URI\>* | Sprawdź, żądanie HTTP GET identyfikatora URI jest prawidłowo sformatowany. `scheme:host/resource-path` Części muszą być określone jako `http://localhost:50342/oauth2/token`. Zobacz sekcję "przykładowe żądanie" w [powyższej sekcji REST](#rest) przykład.|
|           | invalid_request | Żądania brakuje wymaganego parametru, obejmuje niepoprawna wartość parametru, zawiera więcej niż raz parametr lub w przeciwnym razie jest nieprawidłowo sformułowany. |  |
|           | unauthorized_client | Klient nie ma uprawnień do żądania tokenu dostępu przy użyciu tej metody. | Przyczyną żądanie, które nie zostały Użyj lokalnego sprzężenia zwrotnego, aby wywołać rozszerzenie, lub na maszynie Wirtualnej, która nie ma poprawnie skonfigurowany Instalatora MSI. Zobacz [skonfigurować maszyny Wirtualnej zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure](qs-configure-portal-windows-vm.md) Jeśli potrzebujesz pomocy w konfiguracji maszyny Wirtualnej. |
|           | access_denied | Właściciel zasobu lub autoryzacji serwer odrzucił żądanie. |  |
|           | unsupported_response_type | Serwer autoryzacji nie obsługuje uzyskiwania tokenu dostępu przy użyciu tej metody. |  |
|           | invalid_scope | Żądany zakres jest nieprawidłowy, nieznany lub źle skonstruowany. |  |
| 500 Wewnętrzny błąd serwera | nieznane | Nie można pobrać tokenu z usługi Active directory. Szczegółowe informacje można znaleźć w dziennikach w  *\<ścieżka pliku\>* | Sprawdź, czy włączono MSI w maszynie Wirtualnej. Zobacz [skonfigurować maszyny Wirtualnej zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure](qs-configure-portal-windows-vm.md) Jeśli potrzebujesz pomocy w konfiguracji maszyny Wirtualnej.<br><br>Sprawdź także, czy żądanie HTTP GET identyfikatora URI jest prawidłowo sformatowane, szczególnie zasób, którego identyfikator URI określony w ciągu zapytania. Zobacz sekcję "przykładowe żądanie" w [powyższej sekcji REST](#rest) przykład lub [uwierzytelniania pomocy technicznej usługi Azure AD z usług Azure](services-support-msi.md) listę usług i ich odpowiednich identyfikatorów zasobów.

## <a name="retry-guidance"></a>Spróbuj ponownie wskazówki 

Ograniczenia przepustowości limity mają zastosowanie do liczby wywołań do punktu końcowego IMDS. Po przekroczeniu progu ograniczania przepustowości punktu końcowego IMDS ogranicza żadnych dalszych żądań przepustnicy w czasie działania. W tym okresie punktu końcowego IMDS zwróci kod stanu HTTP 429 ("jest zbyt wiele żądań"), i Niepowodzenie żądania. 

Ponów próbę zalecamy następujących strategii: 

| **Strategia ponawiania prób** | **Ustawienia** | **Wartości** | **Jak to działa** |
| --- | --- | --- | --- |
|ExponentialBackoff |Liczba ponownych prób<br />Minimalna liczba wycofań<br />Maksymalna liczba wycofań<br />Różnica w liczbie wycofań<br />Pierwsze szybkie ponowienie |5<br />0 sek.<br />60 sek.<br />2 sek.<br />false |Próba 1 — opóźnienie 0 sek.<br />Próba 2 — opóźnienie ok. 2 sek.<br />Próba 3 — opóźnienie ok. 6 sek.<br />Próba 4 — opóźnienie ok. 14 sek.<br />Próba 5 — opóźnienie ok. 30 sek. |

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług Azure

Zobacz [uwierzytelniania pomocy technicznej usługi Azure AD z usług Azure](services-support-msi.md) listę zasobów, które obsługują usługi Azure AD i zostały przetestowane msi, a ich odpowiednich identyfikatorów zasobów.


## <a name="related-content"></a>Zawartość pokrewna

- Aby włączyć MSI w maszynie Wirtualnej platformy Azure, zobacz [skonfigurować maszyny Wirtualnej zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure](qs-configure-portal-windows-vm.md).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.








