---
title: Samodzielnie przetestować klienta w celu wstępnego zweryfikowania maszyny wirtualnej — Portal Azure Marketplace | Dokumentacja firmy Microsoft
description: Jak utworzyć samodzielnie przetestować klienta wstępnie weryfikowania obraz maszyny wirtualnej w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pbutlerm
ms.openlocfilehash: ae01b0fb088035240e670c16d4d457d8abda1bfa
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848935"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Tworzenie testu automatycznego klienta w celu wstępnego zweryfikowania obrazów maszyn wirtualnych platformy Azure

Skorzystaj z tego artykułu jako przewodnika tworzenia usługi klienta, który wykorzystuje interfejs API automatycznego testu. Interfejs API samodzielnie przetestować umożliwia wstępnego zweryfikowania maszynę wirtualną (VM), aby upewnić się, że spełnia on wymagania publikacji najnowsze portalu Azure Marketplace. Ta usługa klienta umożliwia przetestowanie Maszynę wirtualną, przed przesłaniem oferty do certyfikacji firmy Microsoft.

## <a name="development-and-testing-overview"></a>Programowanie i testowanie — omówienie

W ramach procesu samodzielnie przetestować należy utworzyć klienta lokalnego, który nawiązuje połączenie z portalu Azure Marketplace, aby sprawdzić poprawność maszyny Wirtualnej działającej w ramach subskrypcji platformy Azure. Maszyna wirtualna może działać system operacyjny Windows lub Linux.

Lokalny klient uruchamia skrypt, który uwierzytelnia się za pomocą interfejsu API samodzielnie przetestować, wysyła informacje o połączeniu i otrzymuje wyniki testu.

Dostępne są następujące ogólne kroki tworzenia samodzielnie przetestować klienta:

1. Wybierz dzierżawę usługi Azure Active Directory (AD) dla aplikacji.
2. Rejestrowanie aplikacji klienta.
3. Utwórz token dla aplikacji klienckiej usługi Azure AD.
4. Przekaż token do automatycznego testu interfejsu API.

Po utworzeniu klienta, można je przetestować na maszynie Wirtualnej.

### <a name="self-test-client-authorization"></a>Samodzielnie przetestować autoryzacji klienta

Na poniższym diagramie przedstawiono, jak działa autoryzacji dla wywołań usług przy użyciu poświadczeń klienta (wspólne hasło lub certyfikat).

![Proces autoryzacji klienta](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Samodzielnie przetestować klienta interfejsu API

Interfejs API automatycznego testu zawiera jeden punkt końcowy, który obsługuje tylko metody POST.  Ma ona następującą strukturę.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
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
|  Autoryzacja     |  Ciągu "Bearer xxxx-xxxx-xxxx-xxxxx" zawiera token klienta usługi Azure Active Directory (AD), można utworzyć przy użyciu programu PowerShell.          |
|  DNSName           |  Nazwa DNS maszyny wirtualnej do testowania    |
|  Użytkownik              |  Nazwa użytkownika logowania się do maszyny Wirtualnej         |
|  Hasło          |  Hasło do logowania się do maszyny Wirtualnej          |
|  System operacyjny                |  System operacyjny maszyny wirtualnej: albo `Linux` lub `Windows`          |
|  PortNo            |  Otwórz numer portu do nawiązywania połączenia z maszyną Wirtualną. Numer portu jest zazwyczaj `22` dla systemu Linux i `5986` for Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Korzystanie z interfejsu API

Można używać interfejsu API automatycznego testu, przy użyciu programu PowerShell lub programu cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Korzystanie z interfejsu API w systemu operacyjnego Linux przy użyciu programu PowerShell

Aby wywołać interfejs API w programie PowerShell, wykonaj następujące kroki:

1. Użyj `Invoke-WebRequest` polecenie do wywołania interfejsu API.
2. Ta metoda jest Post i typem zawartości jest JSON, jak pokazano następujące przechwytywania ekranie i przykład kodu.
3. Określ parametry treści w formacie JSON.

Poniższy przykład kodu pokazuje wywołanie interfejsu API programu PowerShell.

```powershell
$accesstoken = “Get token for your Client AAD App”
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```
Poniższy zrzut ekranu przedstawia przykład wywołanie interfejsu API w programie PowerShell.

![Wywoływanie interfejsu API przy użyciu programu PowerShell dla systemu Linux, systemu operacyjnego](./media/stclient-call-api-ps-linuxvm.png)

W poprzednim przykładzie, możesz pobrać za pomocą pliku JSON i analizować je, aby uzyskać następujące informacje:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

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

Poniższy ekran przechwytywania, który pokazuje `$res.Content`, udostępnia szczegóły wyników testu w formacie JSON.

![Wyniki JSON z wywołania programu PowerShell z systemem Linux](./media/stclient-pslinux-rescontent-json.png)

Poniższy zrzut ekranu przedstawia przykład JSON wyniki testów wyświetlane w trybie online przeglądarka JSON (na przykład [upiększanie kodu](https://codebeautify.org/jsonviewer) lub [przeglądarka JSON](https://jsonformatter.org/json-viewer)).

![Wyniki JSON z wywołania programu PowerShell do maszyny Wirtualnej systemu Linux](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Korzystanie z interfejsu API w systemie operacyjnym Windows za pomocą programu PowerShell

Aby wywołać interfejs API w programie PowerShell, wykonaj następujące kroki:

1. Użyj `Invoke-WebRequest` polecenie do wywołania interfejsu API.
2. Ta metoda jest Post i typem zawartości jest JSON, jak pokazano następujące przechwytywania ekranie i przykład kodu.
3. Utwórz parametry treści w formacie JSON.

Poniższy przykład kodu pokazuje wywołanie interfejsu API programu PowerShell.

```powershell
$accesstoken = “Get token for your Client AAD App”
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Poniższy zrzut ekranu przedstawia przykład wywołanie interfejsu API w programie PowerShell.

![Wywoływanie interfejsu API przy użyciu programu PowerShell dla Windows maszyny Wirtualnej](./media/stclient-call-api-ps-windowsvm.png)

W poprzednim przykładzie, możesz pobrać za pomocą pliku JSON i analizować je, aby uzyskać następujące informacje:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

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

Poniższy ekran przechwytywania, który pokazuje `$res.Content`, udostępnia szczegóły wyników testu w formacie JSON.

![Wywołanie Windows wyniki JSON za pomocą programu PowerShell](./media/stclient-pswindows-rescontent-json.png)

Poniższy zrzut ekranu przedstawia wyniki testów wyświetlane w trybie online przeglądarkę kodu JSON.
(na przykład [upiększanie kodu](https://codebeautify.org/jsonviewer), [przeglądarka JSON](https://jsonformatter.org/json-viewer))

![Wyniki JSON z wywołania maszyn wirtualnych Windows PowerShell](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Korzystanie z programu cURL korzystanie z interfejsu API w systemu operacyjnego Linux

Aby wywołać interfejs API za pomocą programu cURL, wykonaj następujące kroki:

1. Użyj polecenia curl, aby wywołać interfejs API.
2. Ta metoda jest Post i typem zawartości jest JSON, jak pokazano w poniższym fragmencie kodu.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

Na poniższym ekranie przedstawiono przykład przy użyciu programu curl do wywołania interfejsu API.

![Wywołanie interfejsu API przy użyciu polecenia curl](./media/stclient-consume-api-curl.png)

Poniższy zrzut ekranu przedstawia wyniki JSON z wywołania programu curl.

![Wyniki JSON z wywołania programu curl](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Wybierz dzierżawę usługi Azure AD dla aplikacji

Gdzie chcesz utworzyć aplikację wykonaj następujące kroki, aby wybierz dzierżawę usługi Azure AD.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Na pasku menu u góry wybierz swoje konto, a następnie w obszarze lista katalogów, wybierz opcję dzierżawy usługi Active Directory, w którym chcesz zarejestrować aplikację. Lub wybierz **katalog i Subskrypcja** ikonę, aby zobaczyć globalnego filtru subskrypcji. Poniższy zrzut ekranu przedstawia przykład tego filtru.

   ![Wybierz filtr subskrypcji](./media/stclient-subscription-filter.png)

3. Na pasku nawigacyjnym po lewej stronie wybierz **wszystkich usług** , a następnie wybierz **usługi Azure Active Directory**.

   W poniższych krokach może być konieczne nazwa dzierżawy (lub nazwę katalogu) lub identyfikator dzierżawy (lub identyfikator katalogu).

   **Aby uzyskać informacje o dzierżawy:**

   W **usługi Azure Active Directory — omówienie**, wyszukaj "Properties", a następnie wybierz **właściwości**. Przy użyciu poniższej zrzut ekranu przedstawia przykład:

   - **Nazwa** — nazwa dzierżawy lub nazwa katalogu
   - **Identyfikator katalogu** -znaleźć właściwości Identyfikatora dzierżawy lub identyfikator katalogu lub użyj paska przewijania.

   ![Strona właściwości w usłudze Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Rejestrowanie aplikacji klienta

Wykonaj następujące kroki, aby zarejestrować aplikację klienta.

1. Na pasku nawigacyjnym po lewej stronie wybierz **wszystkich usług** , a następnie wybierz **rejestracje aplikacji**.
2. W obszarze **rejestracje aplikacji**, wybierz opcję **+ rejestrowanie nowej aplikacji**.
3. W obszarze **Utwórz**, podaj informacje wymagane dla następujących pól:

   - **Nazwa** — Wprowadź przyjazną nazwę dla aplikacji. Na przykład "SelfTestClient".
   - **Typ aplikacji** — wybierz **aplikacji sieci Web/interfejsu API**
   - **Adres URL logowania** — typu "https:\//isvapp.azurewebsites.net/selftest-vm"

4. Wybierz pozycję **Utwórz**.
5. W obszarze **rejestracje aplikacji** lub **zarejestrowana aplikacja**, kopia **identyfikator aplikacji**.

   ![Uzyskiwanie Identyfikatora aplikacji](./media/stclient-app-id.png)

6. Na pasku narzędzi zarejestrowana aplikacja wybierz **ustawienia**.
7. Wybierz **wymagane uprawnienia** konfigurowania uprawnień dla aplikacji.
8. W obszarze **wymagane uprawnienia**, wybierz opcję **+ Dodaj**.
9. W obszarze **dostępu Dodaj interfejs API**, wybierz **wybierz interfejs API**.
10. W obszarze **wybierz interfejs API**, wpisz "Windows klasycznego modelu wdrażania platformy" wyszukiwania dla interfejsu API.
11. W wynikach wyszukiwania wybierz **Windows Azure klasycznego modelu wdrażania** a następnie kliknij przycisk **wybierz**.

    ![Konfigurowanie wielodostępnych aplikacji](./media/stclient-select-api.png)

12. W obszarze **dostępu Dodaj interfejs API**, wybierz **wybierz uprawnienia**.
13. Wybierz **dostęp "Windows Azure Service Management API"**.

    ![Włącz dostęp do interfejsu API dla aplikacji](./media/stclient-enable-api-access.png)

14. Kliknij pozycję **Wybierz**.
15. Wybierz pozycję **Done** (Gotowe).
16. W obszarze **ustawienia**, wybierz opcję **właściwości**.
17. W obszarze **właściwości**, przewiń w dół do **wielodostępnych**. Wybierz **tak**.

    ![Konfigurowanie wielodostępnych aplikacji](./media/stclient-yes-multitenant.png)

18. Wybierz pozycję **Zapisz**.
19. W obszarze **ustawienia**, wybierz opcję **klucze**.
20. Utwórz klucz tajny, wybierając klawisz **opis** pola tekstowego. Skonfiguruj następujące pola:

    - Wpisz nazwę klucza. Na przykład "selftestclient"
    - Na **EXPIRES** listy rozwijanej wybierz pozycję "za 1 rok".
    - Wybierz **Zapisz** do generowania klucza.
    - W obszarze **wartość**, skopiuj klucz.

      >[!Important]
      >Nie można wyświetlić wartość tego klucza, po zamknięciu **klucze** formularza.

    ![Wartość klucza formularza](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Tworzenie tokenu dla aplikacji klienckiej

Do tworzenia i Uzyskaj token za pomocą interfejsu API REST protokołu OAuth, można użyć dowolnej z następujących programów:

- Postman
- cURL w systemie Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Aby utworzyć i uzyskać token przy użyciu narzędzia Postman

 Aby zadać Auth0 tokenów dla dowolnej autoryzowanych aplikacji, należy wykonać operacji POST [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) punkt końcowy z ładunku w następującym formacie:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Następujące parametry są przekazywane w treści żądania:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Następujące parametry są przekazywane w nagłówku żądania:

```
Content-Type: application/x-www-form-urlencoded
```

Poniższy zrzut ekranu przedstawia przykład przy użyciu narzędzia Postman w celu pobrania tokenu.

![Uzyskaj token za pomocą narzędzia Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Aby utworzyć i pobrać tokenu, używając programu cURL w systemie Linux

Aby zadać Auth0 tokenów dla dowolnej autoryzowanych aplikacji, należy wykonać operacji POST [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) punkt końcowy z ładunku w następującym formacie:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Poniższy zrzut ekranu przedstawia przykład użycia polecenia curl, aby uzyskać token.

![Uzyskaj token za pomocą polecenia programu curl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Aby utworzyć i Uzyskaj token za pomocą języka C&#35;

Aby zadać Auth0 tokenów dla dowolnej aplikacji autoryzowanych, należy wykonać operację POST, aby https:\/końcowy /soamtenant.auth0.com/oauth/token z ładunku w następującym formacie:

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

### <a name="to-create-and-get-a-token-using-powershell"></a>Aby utworzyć i Uzyskaj token za pomocą programu PowerShell

Aby zadać Auth0 tokenów dla dowolnej aplikacji autoryzowanych, należy wykonać operację POST, aby https:\/końcowy /soamtenant.auth0.com/oauth/token z ładunku w następującym formacie:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
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

## <a name="pass-the-client-app-token-to-the-api"></a>Przekazywanie tokenu aplikacji interfejsu API klienta

Przekaż token do interfejsu API automatycznego testu, używając następującego kodu w nagłówku autoryzacji:

```powershell
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

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

## <a name="test-your-self-test-client"></a>Testowanie klienta testu automatycznego

Aby przetestować klienta, wykonaj następujące kroki:

1. Wdrażanie maszyny Wirtualnej, którą chcesz przetestować.
2. Wywołaj interfejs API automatycznego testu, przy użyciu Twojego tokenu aplikacji klienckich, do autoryzacji.
3. Pobierz wyniki testu w formacie JSON.

### <a name="test-result-examples"></a>Przykłady wyników testu

Poniższe fragmenty kodu pokazują wyniki testu w formacie JSON.

**Wyniki testu dla maszyny Wirtualnej z systemem Windows:**

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

**Wyniki testu dla maszyny Wirtualnej z systemem Linux:**

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

## <a name="next-steps"></a>Kolejne kroki

Po pomyślnym przetestowaniu maszyn wirtualnych, możesz [opublikować ofertę](./cpp-publish-offer.md).
