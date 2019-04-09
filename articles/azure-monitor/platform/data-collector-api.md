---
title: Usługa Azure Monitor interfejs API modułu zbierającego dane HTTP | Dokumentacja firmy Microsoft
description: Interfejsu API modułu zbierającego dane HTTP monitora platformy Azure umożliwia dodawanie danych POST JSON do obszaru roboczego usługi Log Analytics za pomocą dowolnego klienta, który można wywołać interfejsu API REST. W tym artykule opisano sposób korzystania z interfejsu API i zawiera przykłady dotyczące publikowania danych przy użyciu różnych języków programowania.
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
ms.date: 04/02/2019
ms.author: bwren
ms.openlocfilehash: 9fd65dc0a6d2a5756acd2de7cb46fbf7943a8758
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264099"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Wyślij dane dziennika do usługi Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)
W tym artykule pokazano, jak używać interfejsu API modułu zbierającego dane HTTP do wysyłania dzienników danych do usługi Azure Monitor z klienta interfejsu API REST.  Przedstawiono sposób formatowania danych zbieranych przez skrypt lub aplikację, uwzględnić go w żądaniu i ma to żądanie autoryzacji usługi Azure Monitor.  Przykłady są udostępniane dla programu PowerShell, C# i Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Interfejsu API modułu zbierającego dane HTTP monitora platformy Azure jest dostępna w publicznej wersji zapoznawczej.

## <a name="concepts"></a>Pojęcia
Za pomocą interfejsu API modułu zbierającego dane HTTP do wysłania danych dziennika z obszarem roboczym usługi Log Analytics w usłudze Azure Monitor za pomocą dowolnego klienta, który można wywołać interfejs API REST.  Może to być element runbook w usłudze Azure Automation, która gromadzi zarządzania danymi z platformy Azure lub w innej chmurze albo go może być to system zarządzania alternatywnego, który używa usługi Azure Monitor do konsolidacji i Analizuj dane dzienników.

Wszystkie dane w obszarze roboczym usługi Log Analytics są przechowywane jako rekord o typie określonego rekordu.  Formatowania danych będzie wysyłać do interfejsu API modułu zbierającego dane HTTP jako wiele rekordów w formacie JSON.  Po przesłaniu danych pojedynczego rekordu jest tworzony w repozytorium dla każdego rekordu w ładunku żądania.


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
| x-ms-AzureResourceId | Identyfikator zasobu usługi Azure resource dane powinny być skojarzone z. Spowoduje to wypełnienie [_ResourceId](log-standard-properties.md#_resourceid) właściwości i zezwala na danych, które mają zostać uwzględnione w [skoncentrowane na zasób](manage-access.md#access-modes) zapytania. Jeśli to pole nie jest określony, dane nie zostaną uwzględnione w zapytaniach skoncentrowane na zasób. |
| time-generated-field | Nazwa pola danych, które zawiera sygnaturę czasową elementu danych. Jeśli określasz pole, a następnie jej zawartość są używane do **TimeGenerated**. Jeśli to pole nie jest określona, wartość domyślna dla **TimeGenerated** to czas, pobieranym wiadomości. Zawartość pola komunikat powinien być zgodny z formatu ISO 8601 RRRR-MM-Ddtgg. |

## <a name="authorization"></a>Autoryzacja
Każde żądanie do interfejsu API modułu zbierającego dane HTTP monitora platformy Azure musi zawierać nagłówek autoryzacji. Aby uwierzytelnić żądania, musisz podpisać żądanie z podstawowej lub klucz pomocniczy dla obszaru roboczego, który wysłał żądanie. Następnie przekaż ten podpis jako część żądania.   

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
Typ rekordu niestandardowego należy zdefiniować podczas przesyłania danych za pośrednictwem interfejsu API modułu zbierającego dane HTTP monitora platformy Azure. Obecnie nie można zapisać danych do istniejących typów rekordów, które zostały utworzone przez inne typy danych i rozwiązań. Usługa Azure Monitor odczytuje przychodzące dane, a następnie tworzy właściwości, które pasują do typów danych, które należy wprowadzić wartości.

Każde żądanie do interfejsu API modułu zbierającego dane mogą zawierać **typ dziennika** nagłówek o nazwie dla typu rekordu. Sufiks **_CL** jest automatycznie dołączany do nazwy wprowadź odróżniający go od innych typów dziennika jako dziennik niestandardowy. Na przykład, jeśli wprowadzasz nazwę **MyNewRecordType**, usługi Azure Monitor jest tworzony rekord o typie **MyNewRecordType_CL**. Pozwala to zagwarantować, że nie istnieją żadne konflikty między nazwami typów, utworzone przez użytkownika i wysłane w bieżących lub przyszłych rozwiązaniach firmy Microsoft.

Aby określić typ danych właściwości, usługi Azure Monitor dodaje sufiks nazwy właściwości. Jeśli jakaś właściwość zawiera wartość null, właściwość nie ma tego rekordu. Poniższa tabela zawiera listę właściwości typu danych i odpowiedniego sufiksu:

| Typ danych właściwości | Sufiks |
|:--- |:--- |
| String |_s |
| Wartość logiczna |_b |
| Double |_d |
| Data/Godzina |_t |
| GUID |_g |

Typ danych, który korzysta z usługi Azure Monitor dla każdej właściwości zależy od tego, czy typ rekordu dla nowego rekordu już istnieje.

* Jeśli typ rekordu nie istnieje, usługa Azure Monitor utworzenie nowej za pomocą wnioskowanie o typie JSON można ustalić typu danych dla każdej właściwości w nowym rekordzie.
* Jeśli typ rekordu istnieje, usługa Azure Monitor próbuje utworzyć nowy rekord na podstawie istniejącej właściwości. Jeśli typ danych dla właściwości w nowym rekordzie nie jest zgodny i nie można przekonwertować na typ istniejących lub jeśli rekord zawiera właściwość, która nie istnieje, usługa Azure Monitor tworzy nową właściwość, którą ma odpowiednie sufiks.

Na przykład, ten wpis przesyłania utworzyć rekord za pomocą trzech właściwości **number_d**, **boolean_b**, i **string_s**:

![Przykładowy rekord 1](media/data-collector-api/record-01.png)

Jeśli następnie przesłane tej następnej pozycji ze wszystkimi wartościami ciągów w formacie, nie zmienić właściwości. Te wartości mogą być konwertowane do istniejących typów danych:

![Przykładowy rekord 2](media/data-collector-api/record-02.png)

Jednak jeśli podejmowana tym przesyłania dalej usługi Azure Monitor utworzyć nowe właściwości **boolean_d** i **string_d**. Nie można przekonwertować wartości:

![Przykładowy rekord 3](media/data-collector-api/record-03.png)

Jeśli następujący wpis jest następnie przesłane, przed utworzeniem typ rekordu, usługi Azure Monitor utworzyć rekord z trzech właściwości **liczba_s**, **boolean_s**, i **string_s**. W tym wpisie początkowej wartości jest w formacie ciągu:

![Przykładowy rekord 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Właściwości zastrzeżone
Następujące właściwości są zarezerwowane i nie należy używać w typie rekordu niestandardowego. Jeśli Twoje ładunek zawiera dowolne z tych nazw właściwości, zostanie zwrócony błąd.

- dzierżawa

## <a name="data-limits"></a>Limity danych
Istnieją pewne ograniczenia wokół danych opublikowane w usłudze Azure Monitor interfejsu API zbierania danych.

* Maksymalnie 30 MB na wpis do interfejsu API modułu zbierającego dane usługi Azure Monitor. Jest to limit rozmiaru dla pojedynczego wpisu. Jeśli wpis danych z jednej, która przekracza 30 MB, należy podzielić dane maksymalnie mniejszych fragmentach o rozmiarze i wysyłać je jednocześnie.
* Maksymalny limit 32 KB dla wartości pól. Jeśli wartość pola jest większa niż 32 KB, dane zostaną obcięte.
* Zalecana maksymalna liczba pól dla danego typu jest 50. Jest to praktyczne ograniczenie z użytecznością i perspektywy środowisko wyszukiwania.  
* Tabela, w obszarze roboczym usługi Log Analytics obsługuje tylko kolumny do 500 (określane jako pole w tym artykule). 
* Maksymalna liczba znaków w nazwie kolumny to 500.

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
Przesyłać zapytania dotyczące danych przesyłanych przez usługi Azure Monitor HTTP danych interfejsu API modułu zbierającego, wyszukiwać rekordy z **typu** jest równa **LogType** wartości, który określiłeś, jest dołączany wraz z **_CL**. Na przykład, jeśli użyto **MyCustomLog**, a następnie zwróci wszystkie rekordy z `MyCustomLog_CL`.

## <a name="sample-requests"></a>Prośby o przykłady
W kolejnych sekcjach znajdują się przykłady dotyczące przesyłania danych do interfejsu API modułu zbierającego dane HTTP monitora platformy Azure przy użyciu różnych języków programowania.

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

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
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

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
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
## <a name="alternatives-and-considerations"></a>Alternatywy i zagadnienia
Interfejsu API modułu zbierającego dane powinny obejmować większość potrzeb można zbierać dane z dowolnych do dzienników platformy Azure, są wystąpienia, gdzie alternatywą może być konieczne pokonanie niektórych ograniczeń interfejsu API. Wszystkie opcje są następujące, główne kwestie dołączone:

| Alternatywna | Opis | Najodpowiedniejsza dla |
|---|---|---|
| [Zdarzenia niestandardowe](https://docs.microsoft.com/en-us/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Natywny zestaw SDK oparte na przyjmowanie danych w usłudze Application Insights | Usługa Application Insights, zwykle Instrumentacji za pomocą zestawu SDK w aplikacji, oferuje możliwość przesyłania danych niestandardowych zdarzeń niestandardowych. | <ul><li> Dane wygenerowane w aplikacji, ale nie są pobierane przez zestaw SDK za pomocą jednego z domyślnych typów danych (ie: żądania, zależności, wyjątki, itp.).</li><li> Dane, które najczęściej są skorelowane z innymi danymi aplikacji w usłudze Application Insights </li></ul> |
| [Interfejs API modułu zbierającego dane](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) w dziennikach w usłudze Azure Monitor | Interfejsu API modułu zbierającego dane w dziennikach monitora platformy Azure jest całkowicie nieograniczony sposób pozyskiwania danych. W tym miejscu można wysyłać żadnych danych sformatowanych w obiekcie JSON. Po wysłaniu będą przetwarzane i dostępne w dzienniki będą skorelowane z innymi danymi w dziennikach lub inne usługi Application Insights danych. <br/><br/> Jest to dość proste przekazać dane jako pliki do obiektu blob usługi Azure Blob z których przetwarzane i przekazany do usługi Log Analytics tych plików. Zobacz [to](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) artykuł, aby przykład implementacji takich potoku. | <ul><li> Dane, które nie są zawsze generowane w obrębie aplikacji zinstrumentowane w ramach usługi Application Insights.</li><li> Przykłady obejmują tabel odnośników i faktów, dane referencyjne, wstępnie zagregowanych danych statystycznych, itp. </li><li> Przeznaczona dla danych, który ma być odsyłaczy względem innych danych usługi Azure Monitor (na przykład usługi Application Insights, inne dzienniki typy danych, usługa Security Center, usługa Azure Monitor, kontenerów lub maszyny wirtualne itp). </li></ul> |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Usługa Azure Data Explorer (ADX) jest platformy danych, która obsługuje analizy usługi Application Insights i Azure Monitor dzienniki. Teraz jest ogólnie już dostępna ("GA"), za pomocą platformy danych w nieprzetworzonej postaci zapewnia pełną elastyczność (ale wymaga pracy związanej z zarządzaniem) za pośrednictwem klastra (RBAC, współczynnik utrzymania schematu, itp.). ADX jest dostępnych wiele [opcji pozyskiwania](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) tym [CSV, TSV i JSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) plików. | <ul><li> Dane, które nie będą zostać skorelowane z wszelkimi innymi danymi, w ramach usługi Application Insights lub dzienniki. </li><li> Danych wymagających zaawansowanych pozyskiwania lub nie jest obecnie dostępna w dzienników monitora platformy Azure możliwości przetwarzania. </li></ul> |


## <a name="next-steps"></a>Kolejne kroki
- Użyj [interfejsu API wyszukiwania w dzienniku](../log-query/log-query-overview.md) do pobierania danych z obszaru roboczego usługi Log Analytics.

- Dowiedz się więcej o tym, jak [tworzenie potoku danych przy użyciu interfejsu API modułu zbierającego dane](create-pipeline-datacollector-api.md) przy użyciu przepływu pracy aplikacji logiki do usługi Azure Monitor.
