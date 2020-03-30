---
title: Używanie interfejsów API REST do wykonywania ciągłej integracji/ciągłego wdrażania dla usługi Azure Stream Analytics w usłudze IoT Edge
description: Dowiedz się, jak zaimplementować potok ciągłej integracji i wdrażania usługi Azure Stream Analytics przy użyciu interfejsów API REST.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 328ca7cd2c6f76095c8334ae6fdb4aa75fbb867d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292003"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementowanie ciągłej integracji/dysków CD dla usługi Stream Analytics w usłudze IoT Edge przy użyciu interfejsów API

Można włączyć ciągłą integrację i wdrażanie dla zadań usługi Azure Stream Analytics przy użyciu interfejsów API REST. W tym artykule przedstawiono przykłady, których interfejsów API do użycia i jak z nich korzystać. Interfejsy API REST nie są obsługiwane w usłudze Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Wywoływanie interfejsów API z różnych środowisk

Interfejsy API REST mogą być wywoływane zarówno z systemu Linux, jak i Windows. Następujące polecenia demonstrują prawidłową składnię wywołania interfejsu API. Określone użycie interfejsu API zostaną opisane w dalszej części tego artykułu.

### <a name="linux"></a>Linux

W przypadku systemu `Curl` Linux `Wget` można używać lub poleceń:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

W systemie Windows użyj programu Powershell: 

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
 
## <a name="create-an-asa-job-on-edge"></a>Tworzenie zadania ASA na krawędzi 
 
Aby utworzyć zadanie usługi Stream Analytics, należy wywołać metodę PUT przy użyciu interfejsu API usługi Stream Analytics.

|Metoda|Adres URL żądania|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Przykład polecenia za pomocą **curl:**

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Przykład treści żądania w JSON:

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
 
Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Publikuj pakiet Krawędzi 
 
Aby opublikować zadanie usługi Stream Analytics w usłudze IoT Edge, należy wywołać metodę POST przy użyciu interfejsu API publikowania pakietu brzegowego.

|Metoda|Adres URL żądania|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Ta operacja asynchronizacyjną zwraca stan 202, dopóki zadanie nie zostanie pomyślnie opublikowane. Nagłówek odpowiedzi lokalizacji zawiera identyfikator URI używany do uzyskania stanu procesu. Gdy proces jest uruchomiony, wywołanie identyfikatora URI w nagłówku lokalizacji zwraca stan 202. Po zakończeniu procesu identyfikator URI w nagłówku lokalizacji zwraca stan 200. 

Przykład wywołania publikowania pakietu Edge przy użyciu **curl:** 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Po nawiązywania wywołania POST należy spodziewać się odpowiedzi z pustym ciałem. Poszukaj adresu URL znajdującego się w adresie HEAD odpowiedzi i zarejestruj go do dalszego wykorzystania.
 
Przykład adresu URL z head odpowiedzi:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
A Zaczekaj od jednej do dwóch minut przed uruchomieniem następującego polecenia, aby wykonać wywołanie interfejsu API z adresem URL znalezionym w head odpowiedzi. Spróbuj ponownie wykonać polecenie, jeśli nie otrzymasz odpowiedzi 200.
 
Przykład nawiązywania wywołania interfejsu API z zwróconym adresem URL z **curl:**

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Odpowiedź zawiera informacje, które należy dodać do skryptu wdrażania usługi Edge. Poniższe przykłady pokazują, jakie informacje należy zebrać i gdzie dodać je w manifeście wdrażania.
 
Przykładowa treść odpowiedzi po pomyślnym opublikowaniu:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Przykład manifestu wdrażania: 

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

Po skonfigurowaniu manifestu wdrażania zapoznaj się [z poleceniami wdrażania modułów usługi Azure IoT Edge z platformą Azure CLI](../iot-edge/how-to-deploy-modules-cli.md) w celu wdrożenia.


## <a name="next-steps"></a>Następne kroki 
 
* [Azure Stream Analytics na urządzeniach IoT Edge](stream-analytics-edge.md)
* [Asa w UIO Edge samouczek](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Tworzenie zadań usługi Stream Analytics Edge przy użyciu narzędzi programu Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
