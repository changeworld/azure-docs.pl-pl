---
title: Pobierz dzienniki kontenerów i zdarzeń za pomocą usługi Azure Container Instances
description: Dowiedz się, jak można debugować za pomocą dzienników kontenera i zdarzeń za pomocą usługi Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f286e2136b12a88e65e40f8fb956542233f71715
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369007"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Pobierz dzienniki kontenerów i zdarzenia w usłudze Azure Container Instances

W przypadku kontenerów nieprawidłowo funkcjonującego start, wyświetlając jego dzienników za pomocą [dzienniki kontenerów az][az-container-logs]i przesyłania strumieniowego jego standardowe Wyjście i błąd standardowy [dołączyć az container] [az-container-attach].

## <a name="view-logs"></a>Wyświetlanie dzienników

Aby wyświetlić dzienniki, od kodu aplikacji w kontenerze, można użyć [dzienniki kontenerów az] [ az-container-logs] polecenia.

Oto dane wyjściowe dziennika kontenera opartego na zadaniach przykładu w [uruchamianie konteneryzowanych zadania w usłudze ACI](container-instances-restart-policy.md)po o przekazywani on nieprawidłowy adres URL do przetworzenia:

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Dołączanie strumieni wyjściowych

[Dołączyć az container] [ az-container-attach] polecenie dostarcza informacje diagnostyczne podczas uruchamiania kontenera. Po rozpoczęciu kontenera strumieni STDOUT i STDERR do konsoli lokalnej.

Na przykład poniżej przedstawiono dane wyjściowe z kontenera opartego na zadaniach w [uruchamianie konteneryzowanych zadania w usłudze ACI](container-instances-restart-policy.md)po o podany prawidłowy adres URL pliku duże pole tekstowe do przetworzenia:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Pobierz zdarzenia diagnostyczne

Jeśli kontener nie może pomyślnie wdrożone, należy przejrzeć informacje diagnostyczne, dostarczone przez dostawcę zasobów usługi Azure Container Instances. Aby wyświetlić zdarzenia kontenera, uruchom polecenie [az-container show] [az container show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Dane wyjściowe obejmują podstawowe właściwości kontenera, wraz z wydarzeniami wdrożenia (tutaj pokazane obcięte):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [rozwiązywania typowych problemów z kontenerów i wdrażanie](container-instances-troubleshooting.md) dla usługi Azure Container Instances.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
