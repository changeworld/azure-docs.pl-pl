---
title: Interfejs API modułu zbierającego dane HTTP monitora platformy Azure | Dokumenty firmy Microsoft
description: Za pomocą interfejsu API modułu zbierającego dane HTTP monitora platformy Azure można dodać dane POST JSON do obszaru roboczego usługi Log Analytics od dowolnego klienta, który może wywołać interfejs API REST. W tym artykule opisano sposób korzystania z interfejsu API i zawiera przykłady sposobu publikowania danych przy użyciu różnych języków programowania.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: f12e9e90b99a055945c34398ff5351334c344253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666756"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Wysyłanie danych dziennika do usługi Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)
W tym artykule pokazano, jak używać interfejsu API modułu zbierającego dane HTTP do wysyłania danych dziennika do usługi Azure Monitor z klienta interfejsu API REST.  Opisano w nim sposób formatowania danych zebranych przez skrypt lub aplikację, dołączanie ich do żądania i zezwalanie na to żądanie przez usługę Azure Monitor.  Przykłady są dostarczane dla programów PowerShell, C#i Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Interfejs API modułu zbierającego dane HTTP monitora platformy Azure jest w publicznej wersji zapoznawczej.

## <a name="concepts"></a>Pojęcia
Za pomocą interfejsu API modułu zbierającego dane HTTP można wysyłać dane dziennika do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor od dowolnego klienta, który może wywołać interfejs API REST.  Może to być element runbook w usłudze Azure Automation, który zbiera dane zarządzania z platformy Azure lub innej chmury lub może to być alternatywny system zarządzania, który używa usługi Azure Monitor do konsolidacji i analizowania danych dziennika.

Wszystkie dane w obszarze roboczym usługi Log Analytics są przechowywane jako rekord o określonym typie rekordu.  Dane są formatuj do wysłania do interfejsu API modułu zbierającego dane HTTP jako wiele rekordów w usłudze JSON.  Podczas przesyłania danych w repozytorium tworzony jest pojedynczy rekord dla każdego rekordu w ładunku żądania.


![Omówienie modułu zbierającego dane HTTP](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Tworzenie żądania
Aby użyć interfejsu API modułu zbierającego dane HTTP, należy utworzyć żądanie POST, które zawiera dane do wysłania w notacji obiektu JavaScript (JSON).  Następne trzy tabele lista atrybutów, które są wymagane dla każdego żądania. Opisujemy każdy atrybut bardziej szczegółowo w dalszej części artykułu.

### <a name="request-uri"></a>Identyfikator URI żądania
| Atrybut | Właściwość |
|:--- |:--- |
| Metoda |POST |
| Identyfikator URI |https://\<identyfikator klienta\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Typ zawartości |application/json |

### <a name="request-uri-parameters"></a>Żądania parametrów URI
| Parametr | Opis |
|:--- |:--- |
| CustomerID |Unikatowy identyfikator obszaru roboczego usługi Log Analytics. |
| Zasób |Nazwa zasobu interfejsu API: /api/logs. |
| Wersja interfejsu API |Wersja interfejsu API do użycia z tym żądaniem. Obecnie jest to 2016-04-01. |

### <a name="request-headers"></a>Nagłówki żądań
| Nagłówek | Opis |
|:--- |:--- |
| Autoryzacja |Podpis autoryzacji. W dalszej części artykułu można przeczytać o tym, jak utworzyć nagłówek HMAC-SHA256. |
| Typ dziennika |Określ typ rekordu przesyłanych danych. Może zawierać tylko litery, cyfry i znaki podkreślenia (_) i nie może przekraczać 100 znaków. |
| x-ms-data |Data przetworzenia żądania w formacie RFC 1123. |
| x-ms-AzureResourceId | Identyfikator zasobu platformy Azure, z pomocą których powinny być skojarzone dane. Spowoduje to wypełnia [właściwość _ResourceId](log-standard-properties.md#_resourceid) i umożliwia uwzględnienie danych w kwerendach [kontekstowych zasobów.](design-logs-deployment.md#access-mode) Jeśli to pole nie jest określone, dane nie zostaną uwzględnione w kwerendach kontekstowych zasobów. |
| pole wygenerowane przez czas | Nazwa pola w danych, które zawiera sygnaturę czasową elementu danych. Jeśli określisz pole, jego zawartość jest używana dla **funkcji TimeGenerated**. Jeśli to pole nie jest określone, ustawieniem domyślnym dla **timegenerated** jest czas, w który wiadomość jest naleśniana. Zawartość pola wiadomości powinna być zgodna z formatem ISO 8601 YYYY-MM-DDThh:mm:ssZ. |

## <a name="authorization"></a>Autoryzacja
Każde żądanie do interfejsu API modułu zbierającego dane HTTP usługi Azure Monitor musi zawierać nagłówek autoryzacji. Aby uwierzytelnić żądanie, należy podpisać żądanie za pomocą klucza podstawowego lub pomocniczego dla obszaru roboczego, który jest złożeniem żądania. Następnie przekaż ten podpis jako część żądania.   

Oto format nagłówka autoryzacji:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*Identyfikator obszaru roboczego* jest unikatowym identyfikatorem obszaru roboczego usługi Log Analytics. *Podpis* jest [kodem uwierzytelniania wiadomości opartym na skrótach (HMAC),](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) który jest konstruowany z żądania, a następnie obliczany przy użyciu [algorytmu SHA256.](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx) Następnie należy zakodować go przy użyciu kodowania Base64.

Ten format służy do kodowania ciągu podpisu **SharedKey:**

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

Jeśli masz ciąg podpisu, zakoduj go przy użyciu algorytmu HMAC-SHA256 na ciągu zakodowanym w uIO, a następnie zakoduj wynik jako Base64. Użyj tego formatu:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Przykłady w następnych sekcjach mają przykładowy kod ułatwiające tworzenie nagłówka autoryzacji.

## <a name="request-body"></a>Treść żądania
Treść wiadomości musi być w JSON. Musi zawierać jeden lub więcej rekordów z nazwą właściwości i parami wartości w następującym formacie. Nazwa właściwości może zawierać tylko litery, cyfry i podkreślenie (_).

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

Można partii wiele rekordów razem w jednym żądaniu przy użyciu następującego formatu. Wszystkie rekordy muszą być tego samego typu rekordu.

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
Typ rekordu niestandardowego można zdefiniować podczas przesyłania danych za pośrednictwem interfejsu API modułu zbierającego dane HTTP monitora platformy Azure. Obecnie nie można zapisywać danych do istniejących typów rekordów, które zostały utworzone przez inne typy danych i rozwiązania. Usługa Azure Monitor odczytuje dane przychodzące, a następnie tworzy właściwości, które pasują do typów danych wprowadzonych wartości.

Każde żądanie do interfejsu API modułu zbierającego dane musi zawierać nagłówek **typu dziennika** o nazwie typu rekordu. Sufiks **_CL** jest automatycznie dołączany do wprowadzonej nazwy, aby odróżnić go od innych typów dzienników jako dziennika niestandardowego. Na przykład po wprowadzeniu nazwy **MyNewRecordType**, Usługa Azure Monitor utworzy rekord o **typie MyNewRecordType_CL**. Pomaga to zapewnić, że nie ma konfliktów między nazwami typów utworzonymi przez użytkownika a nazwami wysyłanych w bieżących lub przyszłych rozwiązaniach firmy Microsoft.

Aby zidentyfikować typ danych właściwości, usługa Azure Monitor dodaje sufiks do nazwy właściwości. Jeśli właściwość zawiera wartość null, właściwość nie jest uwzględniona w tym rekordzie. W tej tabeli wymieniono typ danych właściwości i odpowiadający im sufiks:

| Typ danych właściwości | Sufiks |
|:--- |:--- |
| Ciąg |_s |
| Wartość logiczna |_b |
| Double |_d |
| Data/godzina |_t |
| Identyfikator GUID (przechowywany jako ciąg znaków) |_g |

Typ danych używany przez usługę Azure Monitor dla każdej właściwości zależy od tego, czy typ rekordu dla nowego rekordu już istnieje.

* Jeśli typ rekordu nie istnieje, usługa Azure Monitor tworzy nowy przy użyciu wnioskowania o typie JSON w celu określenia typu danych dla każdej właściwości dla nowego rekordu.
* Jeśli typ rekordu istnieje, usługa Azure Monitor próbuje utworzyć nowy rekord na podstawie istniejących właściwości. Jeśli typ danych dla właściwości w nowym rekordzie nie jest zgodny i nie można go przekonwertować na istniejący typ lub jeśli rekord zawiera właściwość, która nie istnieje, usługa Azure Monitor tworzy nową właściwość, która ma odpowiedni sufiks.

Na przykład ten wpis przesyłania utworzy rekord z trzema właściwościami: **number_d**, **boolean_b**i **string_s:**

![Przykładowy rekord 1](media/data-collector-api/record-01.png)

Jeśli następnie przesłano ten następny wpis, ze wszystkimi wartościami sformatowanymi jako ciągi, właściwości nie ulegną zmianie. Wartości te można przekonwertować na istniejące typy danych:

![Przykładowy rekord 2](media/data-collector-api/record-02.png)

Ale jeśli następnie dokonałeś następnego przesłania, usługa Azure Monitor utworzy nowe właściwości **boolean_d** i **string_d**. Tych wartości nie można przekonwertować:

![Przykładowy rekord 3](media/data-collector-api/record-03.png)

Jeśli następnie zostanie przesłany następujący wpis, przed utworzeniem typu rekordu usługa Azure Monitor utworzy rekord z trzema właściwościami, **number_s** **, boolean_s**i **string_s**. W tym wpisie każda z wartości początkowych jest sformatowana jako ciąg:

![Przykładowy rekord 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Właściwości zarezerwowane
Następujące właściwości są zarezerwowane i nie powinny być używane w niestandardowym typie rekordu. Zostanie wyświetlony błąd, jeśli ładunek zawiera dowolną z tych nazw właściwości.

- Dzierżawy

## <a name="data-limits"></a>Limity danych
Istnieją pewne ograniczenia dotyczące danych opublikowanych w interfejsie API zbierania danych usługi Azure Monitor.

* Maksymalnie 30 MB na post w interfejsie API modułu zbierającego dane usługi Azure Monitor. Jest to limit rozmiaru dla pojedynczego posta. Jeśli dane z jednego wpisu, który przekracza 30 MB, należy podzielić dane do mniejszych fragmentów o rozmiarze i wysłać je jednocześnie.
* Maksymalnie 32 KB limit dla wartości pól. Jeśli wartość pola jest większa niż 32 KB, dane zostaną obcięty.
* Zalecana maksymalna liczba pól dla danego typu wynosi 50. Jest to praktyczny limit z punktu widzenia użyteczności i wyszukiwania.  
* Tabela w obszarze roboczym usługi Log Analytics obsługuje tylko maksymalnie 500 kolumn (określanych jako pole w tym artykule). 
* Maksymalna liczba znaków dla nazwy kolumny wynosi 500.

## <a name="return-codes"></a>Kody powrotne
Kod stanu HTTP 200 oznacza, że żądanie zostało odebrane do przetworzenia. Oznacza to, że operacja została ukończona pomyślnie.

W tej tabeli wymieniono pełny zestaw kodów stanu, które usługa może zwrócić:

| Code | Stan | Kod błędu | Opis |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Żądanie zostało pomyślnie zaakceptowane. |
| 400 |Zła prośba |Nieaktywny Klient |Obszar roboczy został zamknięty. |
| 400 |Zła prośba |InvalidApiVersion (Wersja inwersji) |Określona wersja interfejsu API nie została rozpoznawana przez usługę. |
| 400 |Zła prośba |Nieprawidłowy identyfikator zdyskonuszeń |Określony identyfikator obszaru roboczego jest nieprawidłowy. |
| 400 |Zła prośba |InvalidDataFormat |Zgłoszono nieprawidłowy JSON. Treść odpowiedzi może zawierać więcej informacji na temat sposobu rozwiązania błędu. |
| 400 |Zła prośba |Typ nieprawidłowegologa |Określony typ dziennika zawierał znaki specjalne lub numeryczne. |
| 400 |Zła prośba |MissingApiVersion (MissingApiVersion) |Nie określono wersji interfejsu API. |
| 400 |Zła prośba |Braktytytyp |Typ zawartości nie został określony. |
| 400 |Zła prośba |BraklogType |Nie określono wymaganego typu dziennika wartości. |
| 400 |Zła prośba |Nieobsługiwał typ treści |Typ zawartości nie został ustawiony na **application/json**. |
| 403 |Forbidden |InvalidAuthorization (In invalidAuthorization) |Usługa nie może uwierzytelnić żądania. Sprawdź, czy identyfikator obszaru roboczego i klucz połączenia są prawidłowe. |
| 404 |Nie znaleziono | | Podany adres URL jest niepoprawny lub żądanie jest za duże. |
| 429 |Zbyt wiele żądań | | Usługa ma dużą ilość danych z Twojego konta. Ponów próbę żądania później. |
| 500 |Błąd serwera wewnętrznego |NieokreślonyError |Usługa napotkała błąd wewnętrzny. Ponów próbę żądania. |
| 503 |Usługa niedostępna |SerwisDostępny |Usługa jest obecnie niedostępna do odbierania żądań. Ponów próbę złożenia wniosku. |

## <a name="query-data"></a>Zapytania o dane
Aby zbadać dane przesłane przez interfejs API modułu zbierającego dane HTTP usługi Azure Monitor, wyszukaj rekordy o **typie** równym określonej wartości **LogType,** dołączane do **_CL**. Na przykład, jeśli używasz **MyCustomLog**, to `MyCustomLog_CL`chcesz zwrócić wszystkie rekordy z .

## <a name="sample-requests"></a>Przykładowe żądania
W następnych sekcjach znajdziesz przykłady sposobu przesyłania danych do interfejsu API modułu zbierającego dane HTTP usługi Azure Monitor przy użyciu różnych języków programowania.

Dla każdego przykładu wykonaj następujące czynności, aby ustawić zmienne nagłówka autoryzacji:

1. W witrynie Azure portal znajdź obszar roboczy usługi Log Analytics.
2. Wybierz **pozycję Ustawienia zaawansowane,** a następnie **pozycję Połączone źródła**.
2. Po prawej stronie **identyfikatora obszaru roboczego**wybierz ikonę kopiowania, a następnie wklej identyfikator jako wartość zmiennej **Identyfikator klienta.**
3. Po prawej stronie **klucza podstawowego**zaznacz ikonę kopiowania, a następnie wklej identyfikator jako wartość zmiennej **Klucz udostępniony.**

Alternatywnie można zmienić zmienne dla typu dziennika i danych JSON.

### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
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

### <a name="python-2-sample"></a>Przykład Pythona 2
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
## <a name="alternatives-and-considerations"></a>Alternatywy i rozważania
Podczas gdy interfejs API modułu zbierającego dane powinien obejmować większość potrzeb do zbierania danych w postaci swobodnej do dzienników platformy Azure, istnieją wystąpienia, w których może być wymagana alternatywa w celu przezwyciężenia niektórych ograniczeń interfejsu API. Wszystkie opcje są następujące, główne zagadnienia zawarte:

| Alternatywnych | Opis | Najlepiej nadaje się do |
|---|---|---|
| [Zdarzenia niestandardowe:](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties)natywne pozyskiwania oparte na kole sdk w usłudze Application Insights | Usługa Application Insights, zazwyczaj instrumentowane za pośrednictwem zestawu SDK w aplikacji, oferuje możliwość wysyłania danych niestandardowych za pośrednictwem zdarzeń niestandardowych. | <ul><li> Dane, które są generowane w aplikacji, ale nie są pobierane przez zestaw SDK za pośrednictwem jednego z domyślnych typów danych (żądania, zależności, wyjątki itd.</li><li> Dane, które są najczęściej skorelowane z innymi danymi aplikacji w usłudze Application Insights </li></ul> |
| Interfejs API modułu zbierającego dane w dziennikach monitora platformy Azure | Interfejs API modułu zbierającego dane w dziennikach monitora platformy Azure to całkowicie otwarty sposób pozyskiwania danych. W tym miejscu można wysłać wszystkie dane sformatowane w obiekcie JSON. Po wysłaniu zostanie przetworzony i dostępny w dziennikach, aby być skorelowane z innymi danymi w dziennikach lub z innymi danymi usługi Application Insights. <br/><br/> Jest dość łatwe do przekazania danych jako pliki do obiektu blob platformy Azure, z którego te pliki będą przetwarzane i przekazywane do usługi Log Analytics. Zobacz [ten](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) artykuł, aby uzyskać przykładową implementację takiego potoku. | <ul><li> Dane, które niekoniecznie są generowane w ramach aplikacji instrumentowane w usłudze Application Insights.</li><li> Przykłady obejmują tabele odnośników i faktów, dane referencyjne, wstępnie zagregowane statystyki itd. </li><li> Przeznaczone dla danych, które będą odsyłacze względem innych danych usługi Azure Monitor (usługa Application Insights, inne typy danych dzienników, Centrum zabezpieczeń, usługa Azure Monitor dla kontenerów/maszyn wirtualnych itd.). </li></ul> |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Usługa Azure Data Explorer (ADX) to platforma danych, która zasila usługi Application Insights Analytics i dzienniki usługi Azure Monitor. Teraz ogólnie dostępne ("GA"), przy użyciu platformy danych w postaci nieprzetworzonej zapewnia pełną elastyczność (ale wymaga narzutu zarządzania) nad klastrem (RBAC, wskaźnik przechowywania, schemat i tak dalej). ADX udostępnia wiele [opcji pozyskiwania,](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) w tym [pliki CSV, TSV i JSON.](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) | <ul><li> Dane, które nie będą skorelowane z innymi danymi w obszarze Usługa Application Insights lub Dzienniki. </li><li> Dane wymagające zaawansowanych możliwości pozyskiwania lub przetwarzania nie są obecnie dostępne w dziennikach usługi Azure Monitor. </li></ul> |


## <a name="next-steps"></a>Następne kroki
- Użyj [interfejsu API wyszukiwania dzienników,](../log-query/log-query-overview.md) aby pobrać dane z obszaru roboczego usługi Log Analytics.

- Dowiedz się więcej o [tworzeniu potoku danych za pomocą interfejsu API modułu zbierającego dane](create-pipeline-datacollector-api.md) przy użyciu przepływu pracy aplikacji logiki w usłudze Azure Monitor.
