---
title: Dziennika modułów zbierających dane HTTP analizy interfejsu API | Dokumentacja firmy Microsoft
description: Interfejs API modułów zbierających dane HTTP Analytics dziennika umożliwia dodawanie danych POST JSON do repozytorium analizy dzienników za pomocą dowolnego klienta, który można wywołać interfejsu API REST. W tym artykule opisano sposób użycia interfejsu API i zawiera przykłady publikowania danych przy użyciu różnych języków programowania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 1125cdb5b1cc6829345c71537582816d020edc53
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133023"
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Wysyłanie danych do analizy dzienników przy użyciu protokołu HTTP danych modułu zbierającego interfejsu API (w publicznej wersji zapoznawczej)
W tym artykule przedstawiono sposób wysyłania danych do analizy dzienników z klienta interfejsu API REST za pomocą interfejsu API modułów zbierających dane HTTP.  Przedstawiono sposób formatowania danych zbieranych przez skrypt lub aplikację, dołączyć go w żądaniu i mieć tego żądania uprawnień przez analizy dzienników.  Przykłady są dostępne dla programu PowerShell, C# i Python.

> [!NOTE]
> Interfejs API dziennika analizy HTTP danych modułu zbierającego jest w wersji zapoznawczej.

## <a name="concepts"></a>Pojęcia
Interfejs API modułów zbierających dane HTTP służy do wysyłania danych do analizy dzienników z dowolnego klienta, który można wywołać interfejsu API REST.  Może to być element runbook automatyzacji Azure, która gromadzi zarządzania danych z platformy Azure lub innej chmurze lub może być system zarządzania alternatywne, używaną do konsolidacji i analizowanie danych analizy dzienników.

Wszystkie dane w repozytorium analizy dzienników są przechowywane jako rekord typu określonego rekordu.  Formatowanie danych do wysyłania do interfejsu API modułów zbierających dane HTTP jako wielu rekordów w formacie JSON.  Podczas przesyłania danych pojedynczego rekordu jest tworzony w repozytorium dla każdego rekordu w ładunku żądania.


![Omówienie modułów zbierających dane HTTP](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>Utwórz żądanie
Aby za pomocą interfejsu API modułów zbierających dane HTTP, należy utworzyć żądania POST, który zawiera dane do wysłania w JavaScript Object Notation (JSON).  Kolejne trzy tabele zawierają listę atrybutów, które są wymagane dla każdego żądania. Opisano każdy atrybut bardziej szczegółowo w dalszej części tego artykułu.

### <a name="request-uri"></a>Identyfikator URI żądania
| Atrybut | Właściwość |
|:--- |:--- |
| Metoda |POST |
| Identyfikator URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Typ zawartości |application/json |

### <a name="request-uri-parameters"></a>Parametry identyfikatora URI żądania
| Parametr | Opis |
|:--- |:--- |
| CustomerID |Unikatowy identyfikator obszaru roboczego analizy dzienników. |
| Zasób |Nazwa zasobu interfejsu API: / api/logs. |
| Wersja interfejsu API |Wersja interfejsu API do używania z tym żądaniem. Obecnie jest 2016-04-01. |

### <a name="request-headers"></a>Nagłówki żądań
| Nagłówek | Opis |
|:--- |:--- |
| Autoryzacja |Podpis autoryzacji. W dalszej części tego artykułu można uzyskać informacje dotyczące sposobu tworzenia nagłówka HMAC SHA256. |
| Typ dziennika |Określ typ rekordu jest przesyłane dane. Typ dziennika obsługuje obecnie tylko znaki alfanumeryczne. Nie obsługuje wartości numeryczne i znaki specjalne. Limit rozmiaru dla tego parametru wynosi 100 znaków. |
| x-ms-date |Żądanie zostało przetworzone, w formacie RFC 1123 Data. |
| time-generated-field |Nazwa pola danych, które zawiera sygnaturę czasową elementu danych. Jeśli określisz pola, a następnie jego zawartość jest używana dla **TimeGenerated**. Jeśli to pole nie zostanie określona, wartością domyślną **TimeGenerated** jest czas, który jest pozyskanych wiadomości. Zawartość pola wiadomości należy wykonać w formacie ISO 8601 RRRR-MM-Ddtgg. |

## <a name="authorization"></a>Autoryzacja
Każde żądanie API modułu zbierającego dane dziennika Analytics HTTP musi zawierać nagłówek uwierzytelnienia. Aby uwierzytelnić żądanie, musisz zalogować się żądanie z serwera podstawowego lub dodatkowego klucza dla obszaru roboczego, który wysłał żądanie. Następnie przekaż tego podpisu, jako część żądania.   

Oto format nagłówka autoryzacji:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* jest unikatowym identyfikatorem dla obszaru roboczego analizy dzienników. *Podpis* jest [Hash-based kodu (metoda HMAC Message Authentication)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) jest tworzony z żądania i następnie obliczane przy użyciu [algorytm SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Następnie należy kodować je przy użyciu kodowania Base64.

Użyj tego formatu do kodowania **SharedKey** podpisu ciągu:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Oto przykład ciągu podpisu:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Jeśli masz ciąg podpisu kodować je przy użyciu Algorytm HMAC SHA256 na ciąg kodowany UTF-8, a następnie kodowanie wynik w postaci Base64. Użyj następującego formatu:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Przykłady w kolejnych sekcjach ma przykładowy kod, aby utworzyć nagłówek autoryzacji.

## <a name="request-body"></a>Treść żądania
Treść komunikatu musi być w formacie JSON. Musi zawierać co najmniej jeden rekord z pary nazw i wartości właściwości w następującym formacie:

```
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Partii z wielu rekordów razem w jednym żądaniu, przy użyciu następującego formatu. Wszystkie rekordy muszą być tego samego typu rekordu.

```
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Typ rekordu i właściwości
Typ niestandardowy rekord należy zdefiniować podczas przesyłania danych za pośrednictwem interfejsu API modułów zbierających dane HTTP analizy dziennika. Obecnie nie można zapisać danych do istniejących typów rekordów, które zostały utworzone przez inne typy danych i rozwiązań. Analiza dzienników odczytuje przychodzących danych, a następnie tworzy właściwości, które pasują do typów danych wartości, które należy wprowadzić.

Każde żądanie do interfejsu API analizy dziennika musi zawierać **typ dziennika** nagłówek o nazwie dla typu rekordu. Sufiks **_CL** jest automatycznie dołączane do nazwy wprowadź odróżniający go od innych typów dziennika jako dziennik niestandardowy. Na przykład wprowadź nazwę **MyNewRecordType**, analizy dzienników tworzy rekord z typem **MyNewRecordType_CL**. Pomaga to zapewnić, że nie występują żadne konflikty między nazwami utworzonych przez użytkownika typu i te w obecne lub przyszłe rozwiązania firmy Microsoft.

Aby określić typ danych właściwości, analizy dzienników dodaje sufiks nazwy właściwości. Jeśli jakaś właściwość zawiera wartość null, właściwość nie jest uwzględniony w tym rekordzie. Poniższa tabela zawiera typ danych właściwości i odpowiedniego sufiksu:

| Typ danych właściwości | Sufiks |
|:--- |:--- |
| Ciąg |_s |
| Wartość logiczna |_b |
| podwójne |_d |
| Data i godzina |_t |
| GUID |_g |

Typ danych, który używa analizy dzienników dla każdej właściwości zależy od tego, czy istnieje już typ rekordu w nowym rekordzie.

* Jeśli nie istnieje typ rekordu, analizy dzienników tworzy nową. Analiza dzienników używa wnioskowanie o typie JSON można ustalić typu danych dla każdej właściwości w nowym rekordzie.
* Jeśli istnieje typ rekordu, analizy dzienników próbuje utworzyć nowy rekord na podstawie istniejącej właściwości. Jeśli typ danych dla właściwości w nowym rekordzie nie odpowiada i nie można przekonwertować na typ istniejący lub jeśli rekord zawiera właściwość, która nie istnieje, analizy dzienników tworzy nowe właściwości, która ma zastosowanie sufiks.

Na przykład utworzyć rekord z trzech właściwości tego wpisu przesyłania **number_d**, **boolean_b**, i **string_s**:

![Przykładowy rekord 1](media/log-analytics-data-collector-api/record-01.png)

Jeśli tego wpisu dalej, następnie przesłane ze wszystkich wartości w formacie ciągów, właściwości nie może zmienić. Te wartości można przekonwertować istniejących typów danych:

![Przykładowy rekord 2](media/log-analytics-data-collector-api/record-02.png)

Jednak jeśli wprowadzono następnie tego przesłania dalej, analizy dzienników utworzyć nowe właściwości **boolean_d** i **string_d**. Nie można przekonwertować wartości:

![Przykładowy rekord 3](media/log-analytics-data-collector-api/record-03.png)

Jeśli następnie przesłane następujący wpis przed utworzeniem typu rekordu, analizy dzienników utworzyć rekord z trzech właściwości **liczba_s**, **boolean_s**, i **string_s**. W tej pozycji początkowej wartości jest sformatowany jako ciąg:

![Przykładowy rekord 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>Limity danych
Istnieją pewne ograniczenia wokół dane publikowane w kolekcji dane analizy dziennika interfejsu API.

* Maksymalnie 30 MB na post API modułów zbierających dane analizy dziennika. Jest to limit rozmiaru dla pojedynczego żądania post. Jeśli po danych z jednej, która przekracza 30 MB, należy rozdzielić maksymalnie mniejsze fragmenty wielkości i wysyłać je jednocześnie.
* Maksymalny limit 32 KB wartości pól. Jeśli wartość pola jest większa niż 32 KB, dane zostaną obcięte.
* Zalecana maksymalna liczba pól dla danego typu jest 50. Jest to limit praktyczne doświadczenie użyteczność i wyszukiwania.  

## <a name="return-codes"></a>Kody powrotne
Kod stanu HTTP 200 oznacza, że otrzymał żądanie do przetworzenia. Oznacza to, że operacja została ukończona pomyślnie.

Poniższa tabela zawiera pełen zestaw kodów stanu, które mogą zwracać usługi:

| Kod | Stan | Kod błędu | Opis |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Pomyślnie zaakceptowano żądanie. |
| 400 |Nieprawidłowe żądanie |InactiveCustomer |Obszar roboczy został zamknięty. |
| 400 |Nieprawidłowe żądanie |InvalidApiVersion |Określona wersja interfejsu API nie został rozpoznany przez usługę. |
| 400 |Nieprawidłowe żądanie |InvalidCustomerId |Określony identyfikator obszaru roboczego jest nieprawidłowy. |
| 400 |Nieprawidłowe żądanie |InvalidDataFormat |Przekazano nieprawidłowy JSON. Treść odpowiedzi może zawierać więcej informacji na temat sposobu rozwiązania błędu. |
| 400 |Nieprawidłowe żądanie |InvalidLogType |Typ dziennika określony zawartych w niej znaków specjalnych ani wartości numeryczne. |
| 400 |Nieprawidłowe żądanie |MissingApiVersion |Nie określono wersji interfejsu API. |
| 400 |Nieprawidłowe żądanie |MissingContentType |Nie określono typu zawartości. |
| 400 |Nieprawidłowe żądanie |MissingLogType |Nie określono typu dziennika wymaganej wartości. |
| 400 |Nieprawidłowe żądanie |UnsupportedContentType |Typ zawartości nie został ustawiony na **application/json**. |
| 403 |Zabroniony |InvalidAuthorization |Usługa nie może uwierzytelnić żądania. Sprawdź, czy klucz połączenia i identyfikator obszaru roboczego są prawidłowe. |
| 404 |Nie znaleziono | | Podany adres URL jest nieprawidłowy albo żądania jest za duży. |
| 429 |Zbyt wiele żądań | | Usługa napotkała dużą liczbę dane z Twojego konta. Ponów żądanie później. |
| 500 |Wewnętrzny błąd serwera |UnspecifiedError |W usłudze wystąpił wewnętrzny błąd. Ponów żądanie. |
| 503 |Usługa niedostępna |ServiceUnavailable |Usługa jest obecnie odbierać żądań. Ponów żądanie. |

## <a name="query-data"></a>Zapytania o dane
Aby danych zapytań przesyłanych przez analityka HTTP danych modułu zbierającego interfejs API dziennika, Wyszukaj rekordy z **typu** jest równa **LogType** wartość, która została określona, dołączony **_CL**. Na przykład jeśli użyto **MyCustomLog**, a następnie zwróci wszystkie rekordy z **typu = MyCustomLog_CL**.

>[!NOTE]
> Jeśli obszaru roboczego został uaktualniony do [języka zapytań nowe analizy dzienników](log-analytics-log-search-upgrade.md), a następnie zmienić powyższym zapytaniu następujące.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Próbki żądań
W kolejnych sekcjach znajdują się przykłady sposobu przesyłania danych do interfejsu API dziennika analizy HTTP danych moduł zbierający przy użyciu różnych języków programowania.

Dla każdej próbki wykonaj następujące kroki, aby ustawić zmienne dla nagłówka autoryzacji:

1. W portalu Azure Znajdź obszaru roboczego analizy dzienników.
2. Wybierz **Zaawansowane ustawienia** , a następnie **połączone źródła**.
2. Po prawej stronie **identyfikator obszaru roboczego**, wybierz ikonę kopiowania, a następnie wklej identyfikator jako wartość **identyfikator klienta** zmiennej.
3. Po prawej stronie **klucza podstawowego**, wybierz ikonę kopiowania, a następnie wklej identyfikator jako wartość **klucz wstępny** zmiennej.

Można również zmienić zmienne typu dziennika i dane JSON.

### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Przykład w języku C#
```
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Przykład 2 języka Python
```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Kolejne kroki
- Użyj [interfejs API dziennika wyszukiwania](log-analytics-log-search-api.md) do pobierania danych z repozytorium analizy dzienników.
