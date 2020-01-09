---
title: Użyj interfejsów API REST do wykonania ciągłej integracji/ciągłego wdrażania Azure Stream Analytics na urządzeniach IoT Edge
description: Dowiedz się, jak wdrożyć ciągłą integrację i wdrażanie potoku usługi Azure Stream Analytics przy użyciu interfejsów API REST.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 78f2e06947c2b81ffe5e6cd8a88438db4dabf158
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426419"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementowanie ciągłej integracji/ciągłego wdrażania Stream Analytics na IoT Edge przy użyciu interfejsów API

Aby umożliwić ciągłej integracji i ciągłego wdrażania dla zadań usługi Azure Stream Analytics przy użyciu interfejsów API REST. Ten artykuł zawiera przykłady, w których interfejsy API i sposobu ich używania. Interfejsy API REST nie są obsługiwane w usłudze Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Wywoływanie interfejsów API w różnych środowiskach

Interfejsy API REST można wywołać z systemów Linux i Windows. Poniższe polecenia są przykładem poprawna składnia wywołania interfejsu API. Określone użycie interfejsu API będzie opisane w kolejnych sekcjach tego artykułu.

### <a name="linux"></a>Linux

Dla systemu Linux, możesz użyć `Curl` lub `Wget` poleceń:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Dla Windows za pomocą programu Powershell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Tworzenie zadania usługi ASA na urządzeniach brzegowych 
 
Aby utworzyć zadanie usługi Stream Analytics, wywołaj metodę PUT przy użyciu interfejsu API analizy Stream.

|Metoda|Adres URL żądania|
|------|-----------|
|PUT|https://management.azure.com/subscriptions/{**Identyfikator subskrypcji**} /resourcegroups/ {**Nazwa grupy zasobów**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**Nazwa zadania**}? api-version = 2017-04-01-preview|
 
Przykład użycia polecenia **curl**:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Przykład treści żądania w formacie JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu API](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Publikowanie pakietu Edge 
 
Aby opublikować zadania usługi Stream Analytics w usłudze IoT Edge, należy wywołać metody POST, przy użyciu interfejsu API publikowanie pakietu Edge.

|Metoda|Adres URL żądania|
|------|-----------|
|POST|https://management.azure.com/subscriptions/{**subscriptionid**} /resourceGroups/ {**resourcegroupname**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**jobname**} / publishedgepackage? api-version = 2017-04-01 - (wersja zapoznawcza)|

Ta operacja asynchroniczna zwraca stan 202 do czasu zadania zostały pomyślnie opublikowane. Lokalizacja nagłówek odpowiedzi zawiera identyfikator URI używany do pobierania stanu procesu. Po uruchomieniu procesu wywołania do identyfikatora URI w nagłówku location zwraca stan 202. Po zakończeniu procesu identyfikatora URI w nagłówku location zwraca stan 200. 

Przykład pakietu Edge publikowania za pomocą wywołania **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Po wprowadzeniu wywołanie metody POST, należy oczekiwać odpowiedzi o pustej treści. Wyszukaj adres URL znajduje się w NAGŁÓWKU odpowiedzi i zapisz go w celu dalszego użycia.
 
Przykład adres URL z nagłówek odpowiedzi:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Oczekiwanie na jednej do dwóch minut przed uruchomieniem następujące polecenie, aby wykonywanie wywołania interfejsu API z adresem URL znalezione w NAGŁÓWKU odpowiedzi. Spróbuj ponownie wykonać polecenie, jeśli nie otrzymasz odpowiedzi 200.
 
Przykład wywołania interfejsu API za pomocą zwrócony adres URL z **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Odpowiedź zawiera informacje, które należy dodać do skryptu wdrażania krawędzi. Poniższe przykłady pokazują, co manifest potrzebnych do zbierania informacji i gdzie można dodać go we wdrożeniu.
 
Treść odpowiedzi przykładowych po opublikowaniu pomyślnie:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Przykład Manifest wdrożenia: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Po przeprowadzeniu konfiguracji manifestu wdrażania, można znaleźć [modułów wdrożenia usługi Azure IoT Edge przy użyciu wiersza polecenia platformy Azure](../iot-edge/how-to-deploy-modules-cli.md) dla wdrożenia.


## <a name="next-steps"></a>Następne kroki 
 
* [Azure Stream Analytics w usłudze IoT Edge](stream-analytics-edge.md)
* [Usługa ASA na samouczek dotyczący usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Tworzenie zadań usługi Stream Analytics Edge przy użyciu narzędzi programu Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
