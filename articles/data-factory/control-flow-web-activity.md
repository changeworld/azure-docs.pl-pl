---
title: Działania w usłudze Azure Data Factory w sieci Web | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działanie internetowe, jednego z działań przepływu sterowania obsługiwanych przez usługę Data Factory umożliwia wywołanie punktu końcowego REST z potoku.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: shlo
ms.openlocfilehash: 7edaa4c673c2cb94dc5bd0245ce66c9fe6a7dd3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764292"
---
# <a name="web-activity-in-azure-data-factory"></a>Działanie internetowe w usłudze Azure Data Factory
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
name | Nazwa działania w sieci web | String | Tak
— typ | Musi być równa **WebActivity**. | String | Tak
method | Metoda interfejsu API REST dla docelowego punktu końcowego. | ciąg. <br/><br/>Obsługiwane typy: POBIERZ "PUBLIKUJ", "PUT" | Yes
url | Docelowy punkt końcowy i ścieżki | Ciąg lub wyrażenie obiektu resultType ciągu. Działanie przekroczy limit czasu na 1 minutę z powodu błędu, jeśli nie otrzymasz odpowiedzi z punktu końcowego. | Yes
Nagłówki | Nagłówki, które są wysyłane do żądania. Na przykład, aby ustawić język i typ żądania: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Ciąg (lub wyrażenie obiektu resultType ciągu) | Tak, wymagany jest nagłówek Content-type. `"headers":{ "Content-Type":"application/json"}`
Treść | Reprezentuje ładunek, które są wysyłane do punktu końcowego.  | Ciąg lub wyrażenie obiektu resultType ciągu. <br/><br/>Wyświetlić schemat ładunek żądania w [schematu ładunku żądania](#request-payload-schema) sekcji. | Wymagane dla metod POST/PUT.
uwierzytelnianie | Metoda uwierzytelniania do wywoływania punktu końcowego. Obsługiwane typy to "Basic lub ClientCertificate." Aby uzyskać więcej informacji, zobacz [uwierzytelniania](#authentication) sekcji. Jeśli uwierzytelnianie nie jest wymagane, należy wykluczyć tej właściwości. | Ciąg (lub wyrażenie obiektu resultType ciągu) | Nie
datasets | Lista zestawów danych jest przekazywana do punktu końcowego. | Tablica odwołań do zestawu danych. Może być pustą tablicą. | Tak
linkedServices | Lista usług połączonych jest przekazywana do punktu końcowego. | Tablica odwołań do połączonej usługi. Może być pustą tablicą. | Yes

> [!NOTE]
> Punkty końcowe REST, które wywołuje działanie internetowe musi zwracać odpowiedzi typu JSON. Działanie przekroczy limit czasu na 1 minutę z powodu błędu, jeśli nie otrzymasz odpowiedzi z punktu końcowego.

W poniższej tabeli przedstawiono wymagania dotyczące zawartości JSON:

| Typ wartości | Treść żądania | Treść odpowiedzi |
|---|---|---|
|Obiekt JSON | Obsługiwane | Obsługiwane |
|Tablica JSON | Obsługiwane <br/>(Obecnie tablice notacji JSON nie działają, w wyniku błędu. Poprawka jest w toku). | Nieobsługiwane |
| Wartość JSON | Obsługiwane | Nieobsługiwane |
| Typ spoza JSON | Nieobsługiwane | Nieobsługiwane |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>Brak
Jeśli nie jest wymagane uwierzytelnianie, nie ma właściwości "uwierzytelnianie".

### <a name="basic"></a>Podstawowa
Określ nazwę użytkownika i hasło do użycia z uwierzytelnianiem podstawowym.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>certyfikat klienta
Określ zawartość algorytmem Base64 pliku PFX i hasło.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Tożsamość zarządzana

Określ identyfikator uri zasobu, dla którego zostanie żądana tokenu dostępu za pomocą tożsamości zarządzanej przez usługę data factory. Aby wywołać interfejs API usługi Azure Resource Management, należy użyć `https://management.azure.com/`. Aby uzyskać więcej informacji na temat zarządzanych tożsamości działania zobacz [zarządzanych tożsamości dla zasobów platformy Azure, strona przeglądu](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>Schemat ładunku żądania
Korzystając z metody POST/PUT właściwości treści reprezentuje ładunek, które są wysyłane do punktu końcowego. Usługi połączone i zestawy danych można przekazać jako część ładunku. Poniżej przedstawiono schematu dla ładunku:

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
W tym przykładzie działanie internetowe w potoku wywołuje punktu końcowego REST. Przekazuje on połączonej usługi Azure SQL i zestaw danych usługi Azure SQL do punktu końcowego. Punkt końcowy REST używa parametrów połączenia usługi Azure SQL do łączenia się z serwerem Azure SQL i zwraca nazwę wystąpienia programu SQL server.

### <a name="pipeline-definition"></a>Definicji potoku

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

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwanych przez usługę Data Factory:

- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
