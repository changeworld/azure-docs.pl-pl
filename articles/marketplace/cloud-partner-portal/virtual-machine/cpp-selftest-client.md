---
title: Klient autotestu do wstępnej weryfikacji maszyny wirtualnej | Azure Marketplace
description: Jak utworzyć klienta autotestu do wstępnej weryfikacji obrazu maszyny wirtualnej dla portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: fb568400cb60f108303909353bfa703e98ab6157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286425"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Tworzenie klienta autotestu w celu wstępnej weryfikacji obrazu maszyny wirtualnej platformy Azure

Użyj tego artykułu jako przewodnika do tworzenia usługi klienta, która korzysta z interfejsu API autotestu. Za pomocą interfejsu API autotestu można wstępnie sprawdzić poprawność maszyny wirtualnej (VM), aby upewnić się, że spełnia ona najnowsze wymagania dotyczące publikowania w portalu Azure Marketplace. Ta usługa klienta umożliwia przetestowanie maszyny Wirtualnej przed przesłaniem oferty certyfikacji firmy Microsoft.

## <a name="development-and-testing-overview"></a>Omówienie rozwoju i testowania

W ramach procesu autotestu utworzysz klienta lokalnego, który łączy się z witryną Azure Marketplace, aby sprawdzić poprawność maszyny wirtualnej uruchomionej w ramach subskrypcji platformy Azure. Maszyna wirtualna może być uruchomiona w systemie operacyjnym Windows lub Linux.

Klient lokalny uruchamia skrypt, który uwierzytelnia się za pomocą interfejsu API autotestu, wysyła informacje o połączeniu i odbiera wyniki testów.

Kroki wysokiego poziomu tworzenia klienta autotestu są następujące:

1. Wybierz dzierżawę usługi Azure Active Directory (AD) dla aplikacji.
2. Zarejestruj aplikację kliencką.
3. Utwórz token dla aplikacji usługi Azure AD klienta.
4. Przekaż token do interfejsu API autotestu.

Po utworzeniu klienta, można przetestować go na maszynie wirtualnej.

### <a name="self-test-client-authorization"></a>Autoryzacja klienta autotestu

Na poniższym diagramie pokazano, jak działa autoryzacja dla usługi do obsługi wywołań przy użyciu poświadczeń klienta (udostępniony klucz tajny lub certyfikat).)

![Proces autoryzacji klienta](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Interfejs API klienta autotestu

Interfejs API autotestu zawiera pojedynczy punkt końcowy, który obsługuje tylko metodę POST.  Ma następującą strukturę.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

W poniższej tabeli opisano pola interfejsu API.


|      Pole         |    Opis    |
|  ---------------   |  ---------------  |
|  Autoryzacja     |  Ciąg "Bearer xxxx-xxxx-xxxx-xxxxx" zawiera token klienta usługi Azure Active Directory (AD), który można utworzyć przy użyciu programu PowerShell.          |
|  Nazwa DNS           |  Nazwa DNS maszyny Wirtualnej do przetestowania    |
|  Użytkownik              |  Nazwa użytkownika do logowania się do maszyny Wirtualnej         |
|  Hasło          |  Hasło do logowania się do maszyny Wirtualnej          |
|  System operacyjny                |  System operacyjny maszyny Wirtualnej: `Linux` albo`Windows`          |
|  PortNo            |  Otwórz numer portu do łączenia się z maszyną wirtualną. Numer portu jest `22` zazwyczaj dla `5986` systemu Linux i Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Korzystanie z interfejsu API

Można używać interfejsu API autotest przy użyciu programu PowerShell lub cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Używanie programu PowerShell do korzystania z interfejsu API w systemie operacyjnym Linux

Aby wywołać interfejs API w programie PowerShell, wykonaj następujące kroki:

1. Użyj `Invoke-WebRequest` polecenia, aby wywołać interfejs API.
2. Metoda jest Post i typ zawartości jest JSON, jak pokazano w poniższym przykładzie kodu i przechwytywania ekranu.
3. Określ parametry obiektu w formacie JSON.

Poniższy przykład kodu pokazuje wywołanie programu PowerShell do interfejsu API.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
Następujące przechwytywanie ekranu pokazuje przykład wywoływania interfejsu API w programie PowerShell.

![Wywołaj interfejs API z programem PowerShell dla systemu operacyjnego Linux](./media/stclient-call-api-ps-linuxvm.png)

Korzystając z poprzedniego przykładu, można pobrać JSON i przeanalizować go, aby uzyskać następujące szczegóły:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

Następujące zrzut ekranu, `$res.Content`który pokazuje , zawiera szczegółowe informacje o wynikach testu w formacie JSON.

![JSON wyniki z wywołania programu PowerShell do systemu Linux](./media/stclient-pslinux-rescontent-json.png)

Następujące zrzut ekranu przedstawia przykład wyników testu JSON wyświetlanych w przeglądarce JSON online (na przykład [Code Beautify](https://codebeautify.org/jsonviewer) lub [JSON Viewer](https://jsonformatter.org/json-viewer)).

![JSON wyniki z wywołania programu PowerShell do maszyny Wirtualnej systemu Linux](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Korzystanie z interfejsu API w systemie operacyjnym Windows za pomocą programu PowerShell

Aby wywołać interfejs API w programie PowerShell, wykonaj następujące kroki:

1. Użyj `Invoke-WebRequest` polecenia, aby wywołać interfejs API.
2. Metoda jest Post i typ zawartości jest JSON, jak pokazano w poniższym przykładzie kodu i przechwytywania ekranu.
3. Utwórz parametry obiektu w formacie JSON.

Poniższy przykład kodu pokazuje wywołanie programu PowerShell do interfejsu API.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

Następujące przechwytywanie ekranu pokazuje przykład wywoływania interfejsu API w programie PowerShell.

![Wywoływanie interfejsu API z programem PowerShell dla maszyny Wirtualnej systemu Windows](./media/stclient-call-api-ps-windowsvm.png)

Korzystając z poprzedniego przykładu, można pobrać JSON i przeanalizować go, aby uzyskać następujące szczegóły:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

Następujące zrzut ekranu, `$res.Content`który pokazuje , zawiera szczegółowe informacje o wynikach testu w formacie JSON.

![JSON wyniki z wywołania programu PowerShell do systemu Windows](./media/stclient-pswindows-rescontent-json.png)

Następujące zrzut ekranu pokazuje wyniki testów wyświetlane w przeglądarce JSON online.
(na przykład [Kod Upiększyć](https://codebeautify.org/jsonviewer), [JSON Viewer](https://jsonformatter.org/json-viewer))

![JSON wyniki z wywołania programu PowerShell do maszyny Wirtualnej systemu Windows](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Użyj cURL, aby korzystać z interfejsu API w systemie operacyjnym Linux

Aby wywołać interfejs API za pomocą cURL, wykonaj następujące kroki:

1. Użyj polecenia curl, aby wywołać interfejs API.
2. Metoda jest Post i typ zawartości jest JSON, jak pokazano w poniższym fragmentie kodu.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

Na poniższym ekranie przedstawiono przykład za pomocą curl do wywołania interfejsu API.

![Wywołanie interfejsu API za pomocą polecenia curl](./media/stclient-consume-api-curl.png)

Następujące przechwytywanie ekranu pokazuje wyniki JSON z wywołania curl.

![JSON wyniki z curl call](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Wybierz dzierżawę usługi Azure AD dla aplikacji

Skorzystaj z poniższych kroków, aby wybrać dzierżawę usługi Azure AD, w której chcesz utworzyć aplikację.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Na górnym pasku menu wybierz swoje konto, a na liście Katalog wybierz dzierżawę usługi Active Directory, w której chcesz zarejestrować aplikację. Możesz też wybrać ikonę **Katalog + Subskrypcja,** aby wyświetlić filtr Subskrypcji globalnej. Następujące zrzut ekranu pokazuje przykład tego filtru.

   ![Wybierz filtr subskrypcji](./media/stclient-subscription-filter.png)

3. Na lewym pasku nawigacyjnym wybierz **pozycję Wszystkie usługi,** a następnie wybierz pozycję **Azure Active Directory**.

   W poniższych krokach może być potrzebna nazwa dzierżawy (lub nazwa katalogu) lub identyfikator dzierżawy (lub identyfikator katalogu).

   **Aby uzyskać informacje o dzierżawie:**

   W **przeglądzie usługi Azure Active Directory**wyszukaj hasło "Właściwości", a następnie wybierz pozycję **Właściwości**. Na przykładzie użycia następującego zrzutu ekranu:

   - **Nazwa** — nazwa dzierżawy lub nazwa katalogu
   - **Identyfikator katalogu** — identyfikator dzierżawy lub identyfikator katalogu lub użyj paska przewijania, aby znaleźć właściwości.

   ![Strona właściwości usługi Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Zarejestruj aplikację kliencką

Aby zarejestrować aplikację kliencką, należy wykonać następujące czynności.

1. Na lewym pasku nawigacyjnym wybierz **pozycję Wszystkie usługi,** a następnie wybierz pozycję **Rejestracje aplikacji**.
2. W obszarze **Rejestracje aplikacji**wybierz **+ Nowa rejestracja aplikacji**.
3. W obszarze **Utwórz**podaj informacje wymagane dla następujących pól:

   - **Nazwa** — wprowadź przyjazną nazwę aplikacji. Na przykład "SelfTestClient".
   - **Typ aplikacji** — wybieranie **aplikacji/interfejsu API w sieci Web**
   - **Adres URL logowania** — typ\/"https: /isvapp.azurewebsites.net/selftest-vm"

4. Wybierz **pozycję Utwórz**.
5. W obszarze **Rejestracje aplikacji** lub **Zarejestrowana aplikacja**skopiuj **identyfikator aplikacji**.

   ![Pobierz identyfikator aplikacji](./media/stclient-app-id.png)

6. Na pasku narzędzi zarejestrowanej aplikacji wybierz pozycję **Ustawienia**.
7. Wybierz **pozycję Wymagane uprawnienia** do konfigurowania uprawnień dla aplikacji.
8. W **obszarze Wymagane uprawnienia**wybierz pozycję + **Dodaj**.
9. W obszarze **Dodaj dostęp do interfejsu API**wybierz pozycję Wybierz interfejs **API**.
10. W obszarze **Wybierz interfejs API**wpisz "Klasyczny model wdrażania systemu Windows Azure", aby wyszukać interfejs API.
11. W wynikach wyszukiwania wybierz **klasyczny model wdrażania systemu Windows Azure,** a następnie kliknij przycisk **Wybierz**.

    ![Konfigurowanie wielu dzierżaw dla aplikacji](./media/stclient-select-api.png)

12. W obszarze **Dodaj dostęp do interfejsu API**wybierz pozycję Wybierz **uprawnienia**.
13. Wybierz **pozycję Dostęp "Interfejs API zarządzania usługami systemu Windows Azure"**.

    ![Włączanie dostępu do interfejsu API dla aplikacji](./media/stclient-enable-api-access.png)

14. Kliknij **pozycję Wybierz**.
15. Wybierz pozycję **Done** (Gotowe).
16. W obszarze **Ustawienia** wybierz pozycję **Właściwości**.
17. W obszarze **Właściwości**przewiń w dół do **wielodostępne**. Wybierz **pozycję Tak**.

    ![Konfigurowanie wielu dzierżaw dla aplikacji](./media/stclient-yes-multitenant.png)

18. Wybierz **pozycję Zapisz**.
19. W obszarze **Ustawienia**wybierz pozycję **Klawisze**.
20. Utwórz klucz tajny, zaznaczając pole **tekstowe** Opis klucza. Skonfiguruj następujące pola:

    - Wpisz nazwę klucza. Na przykład "selftestclient"
    - Na liście rozwijanej **WYGASAS** wybierz "Za 1 rok".
    - Wybierz **pozycję Zapisz,** aby wygenerować klucz.
    - W obszarze **WARTOŚĆ**skopiuj klucz.

      >[!Important]
      >Nie będzie można zobaczyć wartość klucza po zamknięciu **keys** formularza.

    ![Formularz wartości klucza](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Tworzenie tokenu dla aplikacji klienckiej

Do utworzenia i uzyskania tokenu przy użyciu interfejsu API OAuth REST można użyć dowolnego z następujących programów:

- Postman
- cURL w systemie Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Aby utworzyć i uzyskać token za pomocą listonosza

 Aby poprosić Auth0 o tokeny dla dowolnej autoryzowanej aplikacji, wykonaj operację POST do punktu [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) końcowego z ładunkiem w następującym formacie:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Przekaż następujące parametry w treści Żądanie:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Przekaż następujące parametry w nagłówku żądania:

```
Content-Type: application/x-www-form-urlencoded
```

Następujące przechwytywanie ekranu pokazuje przykład za pomocą postmana, aby uzyskać token.

![Zdobądź token z Listonoszem](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Aby utworzyć i uzyskać token przy użyciu cURL w systemie Linux

Aby poprosić Auth0 o tokeny dla dowolnej autoryzowanej aplikacji, wykonaj operację POST do punktu [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) końcowego z ładunkiem w następującym formacie:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Następujące przechwytywanie ekranu pokazuje przykład za pomocą polecenia curl, aby uzyskać token.

![Pobierz token z poleceniem curl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Aby utworzyć i uzyskać token przy użyciu języka C&#35;

Aby poprosić Auth0 o tokeny dla dowolnej autoryzowanej aplikacji, wykonaj\/operację POST na https: /soamtenant.auth0.com/oauth/token punkt końcowy z ładunkiem w następującym formacie:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Aby utworzyć i uzyskać token przy użyciu programu PowerShell

Aby poprosić Auth0 o tokeny dla dowolnej autoryzowanej aplikacji, wykonaj\/operację POST na https: /soamtenant.auth0.com/oauth/token punkt końcowy z ładunkiem w następującym formacie:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>Przekazywanie tokenu aplikacji klienckiej do interfejsu API

Przekaż token do interfejsu API autotestu przy użyciu następującego kodu w nagłówku autoryzacji:

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Przetestuj swojego klienta autotestu

Aby przetestować klienta, wykonaj następujące kroki:

1. Wdrażanie maszyny Wirtualnej, którą chcesz przetestować.
2. Wywołanie interfejsu API autotestu przy użyciu tokenu aplikacji klienckiej do autoryzacji.
3. Pobierz wyniki testu w formacie JSON.

### <a name="test-result-examples"></a>Przykłady wyników testów

Poniższe fragmenty kodu pokazują wyniki testów w formacie JSON.

**Wyniki testów maszyny Wirtualnej systemu Windows:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Wyniki testów maszyny Wirtualnej systemu Linux:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Następne kroki

Po pomyślnym przetestowaniu maszyny wirtualnej platformy Azure można [opublikować ofertę](./cpp-publish-offer.md).
