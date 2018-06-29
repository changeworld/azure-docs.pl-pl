---
title: Działania w fabryce danych Azure w sieci Web | Dokumentacja firmy Microsoft
description: Dowiedz się, jak aktywności sieci Web, jeden z działaniami przepływu sterowania obsługiwane przez fabrykę danych umożliwia wywołanie punkt końcowy REST z potoku.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: shlo
ms.openlocfilehash: 71e89828645cadbbbf60527fca9968fd8ed568ff
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059098"
---
# <a name="web-activity-in-azure-data-factory"></a>Działanie sieci Web w fabryce danych Azure
Działanie WebActivity może być używane do wywoływania niestandardowego punktu końcowego REST z potoku usługi Data Factory. Można przekazywać zestawy danych i połączone usługi do zużycia i dostępu przez działanie. 

## <a name="syntax"></a>Składnia

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa działania sieci web | Ciąg | Yes
type | Należy wybrać opcję **WebActivity**. | Ciąg | Yes
method | Metoda interfejsu API REST dla docelowego punktu końcowego. | Ciąg. <br/><br/>Obsługiwane typy: "GET", "POST", "PUT" | Yes
url | Docelowy punkt końcowy i ścieżki | Ciąg (lub wyrażenie o wartości resultType ciągu). Działanie będzie limitu czasu na 1 minutę z powodu błędu, jeśli otrzymasz odpowiedź z punktu końcowego. | Yes
nagłówki | Nagłówki, które są wysyłane do żądania. Na przykład, aby ustawić język i typ na żądanie: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Ciąg (lub wyrażenie o wartości resultType ciągu) | Tak, nagłówek Content-type jest wymagany. `"headers":{ "Content-Type":"application/json"}`
treść | Reprezentuje ładunek, które są wysyłane do punktu końcowego.  | Ciąg (lub wyrażenie o wartości resultType ciągu). <br/><br/>Zobacz Schemat ładunku żądania w [schematu ładunku żądania](#request-payload-schema) sekcji. | Wymagany w przypadku metody POST/PUT.
uwierzytelnianie | Metoda uwierzytelniania używana do wywoływania punktu końcowego. Obsługiwane typy to "Basic lub ClientCertificate." Aby uzyskać więcej informacji, zobacz [uwierzytelniania](#authentication) sekcji. Jeśli uwierzytelnianie nie jest wymagane, należy wykluczyć tę właściwość. | Ciąg (lub wyrażenie o wartości resultType ciągu) | Nie
Zbiory danych | Lista zestawów danych jest przekazywana do punktu końcowego. | Tablica odwołań do zestawu danych. Może być pustą tablicę. | Yes
linkedServices | Lista usług połączonych przekazany do punktu końcowego. | Tablica odwołań do połączonej usługi. Może być pustą tablicę. | Yes

> [!NOTE]
> Punkty końcowe REST, które wywołuje aktywności sieci web musi zwracać odpowiedzi typu JSON. Działanie będzie limitu czasu na 1 minutę z powodu błędu, jeśli otrzymasz odpowiedź z punktu końcowego.

W poniższej tabeli przedstawiono wymagania dotyczące zawartość JSON:

| Typ wartości | Treść żądania | Treść odpowiedzi |
|---|---|---|
|Obiekt JSON | Obsługiwane | Obsługiwane |
|Tablica JSON | Obsługiwane <br/>(Obecnie tablice notacji JSON nie działają w wyniku błędu Poprawka jest w toku.) | Nieobsługiwane |
| Wartość JSON | Obsługiwane | Nieobsługiwane |
| Typ spoza JSON | Nieobsługiwane | Nieobsługiwane |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>Brak
Jeśli uwierzytelnianie nie jest wymagane, nie ma właściwości "uwierzytelnianie".

### <a name="basic"></a>Podstawowa
Określ nazwę użytkownika i hasło w celu korzystania z uwierzytelniania podstawowego. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certyfikat klienta
Określ algorytmem Base64 zawartość pliku PFX oraz hasło. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>Schemat ładunku żądania
Korzystając z metody POST/PUT, właściwości body reprezentuje ładunek, które są wysyłane do punktu końcowego. Połączone usługi i zestawy danych można przekazać jako częścią ładunku. Oto schemat ładunku: 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>Przykład
W tym przykładzie działanie sieci web w potoku wywołuje punkt końcowy REST. Przekazuje ono Azure połączoną usługą SQL i zestawu danych Azure SQL do punktu końcowego. Punkt końcowy REST używa parametrów połączenia Azure SQL w celu połączenia z serwerem Azure SQL i zwraca nazwę wystąpienia programu SQL server. 

### <a name="pipeline-definition"></a>Definicja potoku

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Wartości parametrów potoku

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Kod punkt końcowy usługi sieci Web

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych: 

- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
