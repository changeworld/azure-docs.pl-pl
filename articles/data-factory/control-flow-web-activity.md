---
title: Aktywność sieci Web w Azure Data Factory
description: Dowiedz się, w jaki sposób można użyć działania sieci Web, jednego z działań przepływu sterowania obsługiwanego przez Data Factory, aby wywołać punkt końcowy REST z potoku.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 5929d4edac53b2be87e168b527034c5a473f154f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678169"
---
# <a name="web-activity-in-azure-data-factory"></a>Aktywność sieci Web w Azure Data Factory
Działanie WebActivity może być używane do wywoływania niestandardowego punktu końcowego REST z potoku usługi Data Factory. Można przekazywać zestawy danych i połączone usługi do zużycia i dostępu przez działanie.

> [!NOTE]
> Działanie sieci Web może wywoływać tylko publicznie uwidocznione adresy URL. Nie jest obsługiwana w przypadku adresów URL hostowanych w prywatnej sieci wirtualnej.

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

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa działania sieci Web | Ciąg | Tak
type | Musi być ustawiona na **webactivity**. | Ciąg | Tak
method | Metoda interfejsu API REST dla docelowego punktu końcowego. | parametry. <br/><br/>Obsługiwane typy: "GET", "POST", "PUT" | Tak
url | Docelowy punkt końcowy i ścieżka | Ciąg (lub wyrażenie z typem ResultType ciągu). Działanie zostanie przekroczenie limitu czasu na 1 minutę z błędem, jeśli nie otrzyma odpowiedzi z punktu końcowego. | Tak
Nagłówka | Nagłówki wysyłane do żądania. Na przykład, aby ustawić język i typ na żądanie: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Ciąg (lub wyrażenie z typem ResultType ciągu) | Tak, nagłówek Content-Type jest wymagany. `"headers":{ "Content-Type":"application/json"}`
jednostce | Reprezentuje ładunek, który jest wysyłany do punktu końcowego.  | Ciąg (lub wyrażenie z typem ResultType ciągu). <br/><br/>Zobacz schemat ładunku żądania w sekcji [schematu ładunku żądania](#request-payload-schema) . | Wymagane dla metod POST/PUT.
uwierzytelnianie | Metoda uwierzytelniania używana do wywoływania punktu końcowego. Obsługiwane typy to "podstawowa" lub "ClientCertificate". Aby uzyskać więcej informacji, zobacz sekcję [uwierzytelnianie](#authentication) . Jeśli uwierzytelnianie nie jest wymagane, Wyklucz tę właściwość. | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie
zestawów danych | Lista zestawów danych przeniesiona do punktu końcowego. | Tablica odwołań do zestawu danych. Może być pustą tablicą. | Tak
LinkedServices | Lista połączonych usług przeniesiona do punktu końcowego. | Tablica odwołań do połączonych usług. Może być pustą tablicą. | Tak

> [!NOTE]
> Punkty końcowe REST, które wywołuje działanie sieci Web, muszą zwracać odpowiedź typu JSON. Działanie zostanie przekroczenie limitu czasu na 1 minutę z błędem, jeśli nie otrzyma odpowiedzi z punktu końcowego.

W poniższej tabeli przedstawiono wymagania dotyczące zawartości JSON:

| Typ wartości | Treść żądania | Treść odpowiedzi |
|---|---|---|
|Obiekt JSON | Obsługiwane | Obsługiwane |
|Tablica JSON | Obsługiwane <br/>(W obecnym czasie tablice JSON nie działają w wyniku błędu. Poprawka jest w toku.) | Nieobsługiwane |
| Wartość JSON | Obsługiwane | Nieobsługiwane |
| Typ inny niż JSON | Nieobsługiwane | Nieobsługiwane |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>Brak
Jeśli uwierzytelnianie nie jest wymagane, nie należy uwzględniać właściwości "Authentication".

### <a name="basic"></a>Podstawowa
Określ nazwę użytkownika i hasło, które mają być używane z uwierzytelnianiem podstawowym.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certyfikat klienta
Określ zawartość pliku PFX i hasło w formacie base64.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Tożsamość zarządzana

Określ identyfikator URI zasobu, dla którego będzie wymagany token dostępu przy użyciu tożsamości zarządzanej fabryki danych. Aby wywołać interfejs API zarządzania zasobami platformy Azure, użyj `https://management.azure.com/`. Aby uzyskać więcej informacji na temat sposobu działania tożsamości zarządzanych, zobacz [stronę Omówienie zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>Schemat ładunku żądania
W przypadku używania metody POST/PUT Właściwość Body reprezentuje ładunek, który jest wysyłany do punktu końcowego. W ramach ładunku można przekazać połączone usługi i zestawy danych. Oto schemat ładunku:

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
W tym przykładzie działanie sieci Web w potoku wywołuje punkt końcowy REST. Przekazuje ona połączoną usługę Azure SQL i zestaw danych usługi Azure SQL do punktu końcowego. Punkt końcowy REST używa parametrów połączenia usługi Azure SQL w celu nawiązania połączenia z serwerem Azure SQL i zwraca nazwę wystąpienia programu SQL Server.

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

### <a name="web-service-endpoint-code"></a>Kod punktu końcowego usługi sieci Web

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

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory:

- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
