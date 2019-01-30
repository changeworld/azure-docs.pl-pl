---
title: Zaloguj się Analytics interfejs API modułu zbierającego dane HTTP | Dokumentacja firmy Microsoft
description: Interfejs API modułu zbierającego dane HTTP analizy dziennika umożliwia dodawanie danych POST JSON do repozytorium usługi Log Analytics za pomocą dowolnego klienta, który można wywołać interfejsu API REST. W tym artykule opisano sposób korzystania z interfejsu API i zawiera przykłady dotyczące publikowania danych przy użyciu różnych języków programowania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: bwren
ms.openlocfilehash: 9fe25821d5a234326570b1681807c6f9dfd6ffc8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211104"
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Wysyłanie danych do usługi Log Analytics przy użyciu interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)
W tym artykule pokazano, jak używać interfejsu API modułu zbierającego dane HTTP do wysyłania danych do usługi Log Analytics z klienta interfejsu API REST.  Przedstawiono sposób formatowania danych zbieranych przez skrypt lub aplikację, uwzględnić go w żądaniu i ma to żądanie autoryzacji przez usługę Log Analytics.  Przykłady są udostępniane dla programu PowerShell, C# i Python.

> [!NOTE]
> Interfejs API modułu zbierającego dane HTTP analizy dziennika jest w publicznej wersji zapoznawczej.

## <a name="concepts"></a>Pojęcia
Za pomocą interfejsu API modułu zbierającego dane HTTP do wysyłania danych do usługi Log Analytics za pomocą dowolnego klienta, który można wywołać interfejs API REST.  Może to być element runbook w usłudze Azure Automation, która gromadzi zarządzania danymi z platformy Azure lub w innej chmurze albo go może być to system zarządzania alternatywne, korzystającą z usługi Log Analytics w celu konsolidacji i analizowania danych.

Wszystkie dane w repozytorium usługi Log Analytics jest przechowywana jako rekord o typie określonego rekordu.  Formatowania danych będzie wysyłać do interfejsu API modułu zbierającego dane HTTP jako wiele rekordów w formacie JSON.  Po przesłaniu danych pojedynczego rekordu jest tworzony w repozytorium dla każdego rekordu w ładunku żądania.


![Omówienie modułu zbierającego dane HTTP](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Utwórz żądanie
Aby użyć interfejsu API modułu zbierającego dane HTTP, należy utworzyć żądanie POST, która zawiera dane do wysłania w JavaScript Object Notation (JSON).  Następne trzy tabele zawierają listę atrybutów, które są wymagane dla każdego żądania. Opisano każdego atrybutu w bardziej szczegółowo w dalszej części tego artykułu.

### <a name="request-uri"></a>Identyfikator URI żądania
| Atrybut | Właściwość |
|:--- |:--- |
| Metoda |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Typ zawartości |application/json |

### <a name="request-uri-parameters"></a>Parametry identyfikatora URI żądania
| Parametr | Opis |
|:--- |:--- |
| CustomerID |Unikatowy identyfikator dla obszaru roboczego usługi Log Analytics. |
| Zasób |Nazwa zasobu interfejsu API: / api/logs. |
| Wersja interfejsu API |Wersja interfejsu API do użycia z tym żądaniem. Obecnie jest 2016-04-01. |

### <a name="request-headers"></a>Nagłówki żądań
| Nagłówek | Opis |
|:--- |:--- |
| Autoryzacja |Podpis autoryzacji. W dalszej części tego artykułu możesz przeczytać o tym, jak utworzyć nagłówek HMAC SHA256. |
| Typ dziennika |Określ typ rekordu jest przesyłane dane. Limit rozmiaru dla tego parametru to 100 znaków. |
| x-ms-date |Data przetwarzania żądania, w formacie RFC 1123. |
| time-generated-field |Nazwa pola danych, które zawiera sygnaturę czasową elementu danych. Jeśli określasz pole, a następnie jej zawartość są używane do **TimeGenerated**. Jeśli to pole nie jest określona, wartość domyślna dla **TimeGenerated** to czas, pobieranym wiadomości. Zawartość pola komunikat powinien być zgodny z formatu ISO 8601 RRRR-MM-Ddtgg. |

## <a name="authorization"></a>Autoryzacja
Każde żądanie do interfejsu API modułu zbierającego dane HTTP analizy dziennika musi zawierać nagłówek autoryzacji. Aby uwierzytelnić żądania, musisz podpisać żądanie z podstawowej lub klucz pomocniczy dla obszaru roboczego, który wysłał żądanie. Następnie przekaż ten podpis jako część żądania.   

Oto format nagłówek autoryzacji:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*Identyfikator obszaru roboczego* jest unikatowym identyfikatorem dla obszaru roboczego usługi Log Analytics. *Podpis* jest [bazujących na skrótach komunikatów uwierzytelniania kodu (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) który jest zbudowany z żądania i następnie obliczane przy użyciu [algorytm SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Następnie możesz zakodować go przy użyciu kodowania Base64.

Użyj tego formatu do zakodowania **SharedKey** ciąg podpisu:

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

W przypadku ciągu podpisu kodować je przy użyciu Algorytm HMAC SHA256 na ciąg kodowany UTF-8, a następnie kodowanie wynik w postaci Base64. Użyj następującego formatu:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Przykłady w kolejnych sekcjach mają przykładowego kodu w celu utworzenia nagłówka autoryzacji.

## <a name="request-body"></a>Treść żądania
Treść wiadomości musi być w formacie JSON. Musi zawierać co najmniej jeden rekord z pary nazw i wartości właściwości w następującym formacie:

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Wiele rekordów ze sobą w pojedynczym żądaniu może batch przy użyciu następującego formatu. Wszystkie rekordy muszą być tego samego typu rekordu.

```json
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
Typ rekordu niestandardowego należy zdefiniować podczas przesyłania danych za pośrednictwem interfejsu API modułu zbierającego dane HTTP analizy dziennika. Obecnie nie można zapisać danych do istniejących typów rekordów, które zostały utworzone przez inne typy danych i rozwiązań. Usługa log Analytics odczytuje dane przychodzące, a następnie tworzy właściwości, które pasują do typów danych, które należy wprowadzić wartości.

Każde żądanie do interfejsu API programu Log Analytics mogą zawierać **typ dziennika** nagłówek o nazwie dla typu rekordu. Sufiks **_CL** jest automatycznie dołączany do nazwy wprowadź odróżniający go od innych typów dziennika jako dziennik niestandardowy. Na przykład, jeśli wprowadzasz nazwę **MyNewRecordType**, usługi Log Analytics jest tworzony rekord o typie **MyNewRecordType_CL**. Pozwala to zagwarantować, że nie istnieją żadne konflikty między nazwami typów, utworzone przez użytkownika i wysłane w bieżących lub przyszłych rozwiązaniach firmy Microsoft.

Aby określić typ danych właściwości, usługi Log Analytics dodaje sufiks nazwy właściwości. Jeśli jakaś właściwość zawiera wartość null, właściwość nie ma tego rekordu. Poniższa tabela zawiera listę właściwości typu danych i odpowiedniego sufiksu:

| Typ danych właściwości | Sufiks |
|:--- |:--- |
| Ciąg |_s |
| Wartość logiczna |_b |
| Podwójne |_d |
| Data/Godzina |_t |
| GUID |_g |

Typ danych, który korzysta z usługi Log Analytics dla każdej właściwości zależy od tego, czy typ rekordu dla nowego rekordu już istnieje.

* Jeśli typ rekordu nie istnieje, usługa Log Analytics utworzony zostaje nowy indeks. Usługa log Analytics używa wnioskowanie o typie JSON można ustalić typu danych dla każdej właściwości w nowym rekordzie.
* Jeśli istnieje typ rekordu, usługi Log Analytics próbuje utworzyć nowy rekord na podstawie istniejącej właściwości. Jeśli typ danych dla właściwości w nowym rekordzie nie jest zgodny i nie można przekonwertować na typ istniejących lub jeśli rekord zawiera właściwość, która nie istnieje, usługa Log Analytics tworzy nową właściwość, którą ma odpowiednie sufiks.

Na przykład, ten wpis przesyłania utworzyć rekord za pomocą trzech właściwości **number_d**, **boolean_b**, i **string_s**:

![Przykładowy rekord 1](media/data-collector-api/record-01.png)

Jeśli następnie przesłane tej następnej pozycji ze wszystkimi wartościami ciągów w formacie, nie zmienić właściwości. Te wartości mogą być konwertowane do istniejących typów danych:

![Przykładowy rekord 2](media/data-collector-api/record-02.png)

Jednak jeśli podejmowana tym przesyłania dalej usługi Log Analytics utworzyć nowe właściwości **boolean_d** i **string_d**. Nie można przekonwertować wartości:

![Przykładowy rekord 3](media/data-collector-api/record-03.png)

Przesłane wcześniej następnie następujący wpis przed utworzeniem typ rekordu, usługi Log Analytics może utworzyć rekord o trzy właściwości **liczba_s**, **boolean_s**, i **string_s**. W tym wpisie początkowej wartości jest w formacie ciągu:

![Przykładowy rekord 4](media/data-collector-api/record-04.png)

## <a name="data-limits"></a>Limity danych
Istnieją pewne ograniczenia wokół danych opublikowane dane analizy dziennika kolekcji interfejs API.

* Maksymalnie 30 MB na wpis do interfejsu API modułu zbierającego dane usługi Log Analytics. Jest to limit rozmiaru dla pojedynczego wpisu. Jeśli wpis danych z jednej, która przekracza 30 MB, należy podzielić dane maksymalnie mniejszych fragmentach o rozmiarze i wysyłać je jednocześnie.
* Maksymalny limit 32 KB dla wartości pól. Jeśli wartość pola jest większa niż 32 KB, dane zostaną obcięte.
* Zalecana maksymalna liczba pól dla danego typu jest 50. Jest to praktyczne ograniczenie z użytecznością i perspektywy środowisko wyszukiwania.  

## <a name="return-codes"></a>Kody powrotne
Kod stanu HTTP 200 oznacza, że żądanie zostało odebrane do przetworzenia. Oznacza to, że operacja została ukończona pomyślnie.

Poniższa tabela zawiera listę pełnego zestawu kodów stanu, które mogą zwracać usługi:

| Kod | Stan | Kod błędu | Opis |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Pomyślnie Zaakceptowano żądania. |
| 400 |Nieprawidłowe żądanie |InactiveCustomer |Obszar roboczy został zamknięty. |
| 400 |Nieprawidłowe żądanie |InvalidApiVersion |Wersja interfejsu API, który określiłeś, nie został rozpoznany przez usługę. |
| 400 |Nieprawidłowe żądanie |InvalidCustomerId |Identyfikator obszaru roboczego jest nieprawidłowy. |
| 400 |Nieprawidłowe żądanie |InvalidDataFormat |Przesłano nieprawidłowy element JSON. Treść odpowiedzi może zawierać więcej informacji na temat sposobu usunięcia błędu. |
| 400 |Nieprawidłowe żądanie |InvalidLogType |Typ dziennika określony zawartej znaków specjalnych ani wartości numeryczne. |
| 400 |Nieprawidłowe żądanie |MissingApiVersion |Nie określono wersji interfejsu API. |
| 400 |Nieprawidłowe żądanie |MissingContentType |Nie określono typu zawartości. |
| 400 |Nieprawidłowe żądanie |MissingLogType |Nie określono wymaganej wartości typu dziennika. |
| 400 |Nieprawidłowe żądanie |UnsupportedContentType |Typ zawartości nie został ustawiony na **application/json**. |
| 403 |Zabroniony |InvalidAuthorization |Nie można uwierzytelnić żądania usługi. Sprawdź, czy klucz połączenia i identyfikator obszaru roboczego są prawidłowe. |
| 404 |Nie znaleziono | | Podany adres URL jest nieprawidłowy albo żądania jest zbyt duży. |
| 429 |Zbyt wiele żądań | | Usługa występuje duże ilości danych z Twojego konta. Ponów próbę żądania później. |
| 500 |Wewnętrzny błąd serwera |UnspecifiedError |W usłudze wystąpił wewnętrzny błąd. Ponów próbę żądania. |
| 503 |Usługa niedostępna |ServiceUnavailable |Usługa jest obecnie odbierać żądań. Prześlij żądanie ponownie. |

## <a name="query-data"></a>Zapytania o dane
Przesyłać zapytania dotyczące danych przesyłanych przez Log Analytics HTTP danych interfejsu API modułu zbierającego, wyszukiwać rekordy z **typu** jest równa **LogType** wartości, który określiłeś, jest dołączany wraz z **_CL**. Na przykład, jeśli użyto **MyCustomLog**, a następnie zwróci wszystkie rekordy z **typu = MyCustomLog_CL**.

>[!NOTE]
> Jeśli obszar roboczy został uaktualniony do [nowych zapytań usługi Log Analytics język](../../azure-monitor/log-query/log-query-overview.md), a następnie powyższe zapytania zmienią się następujące czynności.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Prośby o przykłady
W kolejnych sekcjach znajdują się przykłady dotyczące przesyłania danych do interfejsu API modułu zbierającego dane HTTP analizy dziennika przy użyciu różnych języków programowania.

Dla każdego przykładu wykonaj następujące kroki, aby ustawić zmienne dla nagłówka autoryzacji:

1. W witrynie Azure portal zlokalizuj obszaru roboczego usługi Log Analytics.
2. Wybierz **Zaawansowane ustawienia** i następnie **połączonych źródeł**.
2. Po prawej stronie **identyfikator obszaru roboczego**, wybierz ikonę kopiowania, a następnie wklej identyfikator jako wartość **identyfikator klienta** zmiennej.
3. Po prawej stronie **klucza podstawowego**, wybierz ikonę kopiowania, a następnie wklej identyfikator jako wartość **klucza wspólnego** zmiennej.

Alternatywnie można zmienić zmienne typu dziennika i danych JSON.

### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
$TimeStampField = ""


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
```csharp
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

### <a name="python-2-sample"></a>Przykład języka Python 2
```python
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
- Użyj [interfejsu API wyszukiwania w dzienniku](../../azure-monitor/log-query/log-query-overview.md) do pobierania danych z repozytorium usługi Log Analytics.

- Dowiedz się więcej o tym, jak [tworzenie potoku danych przy użyciu interfejsu API modułu zbierającego dane](../../azure-monitor/platform/create-pipeline-datacollector-api.md) przy użyciu przepływu pracy aplikacji logiki do usługi Log Analytics.
